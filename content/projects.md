+++
title = "Projects"
description = "I do a lot of small projects! These are just a few I'm proud of"
template = "page.html"
+++

<h3 id='fastbin'>FastBin
    <!-- <span class="projlink"><a href="https://fastbin.mastrchef.rocks/">https://fastbin.mastrchef.rocks/</a></span>-->
</h3>
<p>FastBin is a quick project I wrote that provides client-side encryption for text snippets. The name is based off
    HasteBin and PasteBin, but is a pun on "fasten", meaning "to secure."</p>
<p>The backend <a href="https://github.com/galenguyer/FastBin-Server">(source)</a> is super simple. It is just an
    ASP.NET API that inserts the data it receives into a LiteDB database. The only logic it performs is ensuring the
    ID is unique by adding a random character if there is a collision.</p>
<p>The frontend <a href="https://github.com/galenguyer/FastBin-Web">(source)</a> is where the magic happens. There's
    two sides to the app: submission and retrival. When the content is submitted, the first thing to happen is the
    SHA-256 hash of the content is taken. The first 8 characters of this make up the primary ID. It'll also serve as
    the checksum when we're retrieving the data. Next, a random 24-character key is generated. This is used as the
    encryption key for the snippet. The key is <b>never</b> sent to the server, making it nigh on impossible to be
    decrypted. The AES-encrypted data, along with the initial ID, is sent to the API server for storage. The server
    returns the final ID, which may have had some characters added to prevent duplication issues.</p>
<p>Now that we know the final ID, the user is navigated to the link to their snippet. The ID and decryption key are
    stored in the url fragment. The ID is used to fetch the user's encrypted content from the server, but again, the
    decryption key is never seen by the server. Once the encrypted content is retrieved, it is decrypted and
    displayed to the user. The final step to ensure data integrity is to compare the SHA-256 sum of the decrypted
    content with the post ID. If they don't match, we know that somehow, the decryption key has been leaked and the
    content has been tampered with. If they match, we can be reasonably confident the data has not been tampered
    with. Note that this doesn't prove the decryption key hasn't been leaked, just that it hasn't been used to
    tamper with the stored data. The status of this final check is displayed to the user at the top of the page.</p>
<p>In addition to the project being open-source, in order to increase trust in the application, the javascript
    needed to make this work is under 200 lines of javascript. This makes it easy for a user to verify the code
    running on their machine is the same as what is in the github repo, and that it doesn't contain any surprises.
</p>
<p>The final step in this project was dockerizing it. In doing this, I learned how multi-stage builds work, and that
    Alpine Linux uses musl and most other Linux distros use glibc, which are not cross-compatible. The backend API
    is built as a standalone app, nginx is built as a static binary, and the web content is pulled from github.
    These are all moved into the final image, along with an nginx configuration file. By including everything in
    this image, as long as the directory for the LiteDB database is mounted with Docker, the entire application is
    self-contained, with all routes served from the same origin, as expected. The image used is avaiable <a
        href="https://docker.galenguyer.com/repository/chef/fastbin">here,</a> and the Dockerfile and other useful
    docker-related information is <a href="https://github.com/galenguyer/FastBin-Docker">here!</a></p>

<br>

<h3 id='spotify'>Spotify Recent Plays
    <!-- <span class="projlink"><a href="https://galenguyer.com/spotify/">https://galenguyer.com/spotify/</a></span>-->
</h3>
<p>A small project to display the songs I've recently listened to on Spotify. It draws directly from the spotify
    API using an ASP.NET app and displays it using a TypeScript/React app.</p>
<p>The backend API is written in ASP.NET Core. To build it, I started by learning how <a
        href="https://oauth.net/2/">OAuth 2.0</a> works and implementing a client for it from scratch. It
    redirects to the login page, and once the callback token is recieved, it exchanges it for the access and
    refresh tokens. The client also automatically handles refreshing the tokens before they expire. In order to
    prevent someone else from logging in and displaying their data instead, during the login flow I verify that
    the email on the connecting account matches the one in the configuration. If it doesn't, the attempt is
    thrown out.</p>
<p>The backend also scrapes YouTube to provide links to each song on the webpage. YouTube doesn't provide an API
    so I was forced to extract the link from the search page with some regex. Once I get the search result the
    first time, it gets saved so I don't have to spam YouTube with search requests every time someone loads the
    page.</p>
<p>The frontend is my first foray into TypeScript and my second project with React. I chose TypeScript because
    I'm handling complex objects and the type safety is useful. I found some requirements that TypeScript
    imposes annoying at first, but overall, I prefer writing React with TypeScript because of the additional
    safety. Getting Intellisense when using VS Code is a nice bonus as well.</p>
<p>As always, the source is available <a href="https://github.com/galenguyer/SpotifyRecent">on github.</a></p>