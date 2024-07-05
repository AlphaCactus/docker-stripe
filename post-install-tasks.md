# Post "docker compose up" - Additional Setup

Once docker is installed and the projects are running, some developers may need to install and run additional development tools. 

## Sync assets
   - Silverstripe 3.x
      - `rsync -avz root@sputnik.designkh.com:/home/<account>/public_html/assets/ assets/` 
   - Silverstripe >= 4.x
      - `rsync -avz root@sputnik.designkh.com:/home/<account>/public_html/public/assets/ public/assets/`
   

## Connect to databases

- Choose and install a Database Client
   - Can use command line if desired.
   - We use Mysql, MariahDB and Postgres.
   - Suggestions
      - DBeaver
         - Free option, lots of features but also cluttered ui. 
      - SQLYog
         - Simple UI, but not free. 
      - PHPStorm/VSCode built in DB Browsers.
- Connection settings:
   - host: localhost
   - port: 3307
   - user: root
   - pass: see `DB_ROOT_PASSWORD` in your `.env` file
    
## Node

Install Node on host machine for running various services, such as grunt.

1. Install NVM
   - Open terminal 
   - `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash`
   - close and reopen terminal 
2. Install latest stable node
   - `nvm install --lts`
3. List installed node versions
   - `nvm ls` 
4. List what version of node will run by default
   - `node --version`
5. Check version of npm
   - `npm -v`
6. Update npm
   - `npm install -g npm@latest` 
   - This won't tell you what it actually did so check new version
   - `npm -v`

## Grunt
Some legacy SCSS code is built with grunt.
1. Install grunt
   - `npm install -g grunt-cli`
2. Install node modules for project that uses grunt
   - change to project folder
   - Install node modules based on package.json
   - `npm install`
   - If there are warnings, run `npm audit fix`
3. If prompted to udpate caniused databse, update it.
   - tbd, what does this prompt look like? 
4. Grunt dev commands
   - `grunt sass`
      - Compile scss into css
   - `grunt postcss`
      - Compare code to caniuse.com, add vendor prefixes, minify 
   - `grunt`
      - watch for changes and run both sass and postcss

## Composer
Composer is a PHP package manager used in many PHP projects.
`composer install` is run automatically by the php container on creation.

### Run composer manually

Use [docker exec](https://docs.docker.com/reference/cli/docker/container/exec/).
 
1. Open a terminal window on your docker host to the docker project folder.
2. Option1: 
   - run: `docker compose exec <service> composer install`
   - `composer install` is the command to run in the container.
   - By default docker compose exec allocates a TTY

3. Option2: run install interactively
   - get container name: `docker compose ps` 
   - run: `docker exec -it <service> composer install`
   - `-it` starts an interactive pseudo-TTY, in case it is needed
   

## Run PHP CLI Scripts

It is not necessary to run any PHP CLI scripts, however it can be very useful for running one-time controller endpoints or simulating cron jobs or systemd units.

### PHP CLI Examples
 
- `docker compose exec <service> php -v`
   - Output the PHP version 
- `docker compose exec <service> php framework/cli-script.php Sandbox/helloWorld`
   - Run the `Sandbox` controller's `helloWorld` endpoint 

### PHP Debugging

By default, php debugging is enabled.
However, thus far it has been hard to distinguish actual performance differences with debugging enabled or disabled.
Luckily, disabling debugging is easy.

**CAUTION! Be sure to disable your debugging client BEFORE doing any docker commands.**
The debugger can cause the docker commands to hang, such as when composer is run, and cause weird problems.

1. Open `compose.yaml` and uncomment the desired `PHP_INI` version.
2. Open terminal to docker environment project folder.
3. Build container: `docker compose build <service>`
4. `docker compose up -d`
