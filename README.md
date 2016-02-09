Git-Flow
========================

(I'm not the creator of this process, I just read this article http://nvie.com/posts/a-successful-git-branching-model about two years ago, and start using it)

## Two main branches

* master - production server is always synchronized with master branch. 

* develop - the main branch for development. Contains last changes for the next release.

## Supporting branches

* Feature branches - for a new features

* Release branches - for a new release. On a stage when develop branch is ready or almost ready we are creating release branch, which can exist some time before go in live. Before release branch is live we can make minor changes

* Hotfix branches - branches for hot fixes (unplanned releases)

### Feature branches

* May branch off from develop

* Must merge back into develop

* Branch naming convention anything except master, develop, release-*, or hotfix-*

* typically exist in developer repos only

### Release branches

* May branch off from develop

* Must merge back into develop and master

* Branch naming convention release-*

### Hotfix branches

* May branch off from master

* Must merge back into develop and master

* Branch naming convention hotfix-*


Versioning with git on a intern server
========================

* install needed aptitude packages
```
aptitude install git-core git-svn gitweb
```

* create dir to store repo
```
mkdir -p /www/git.domain.tld/{htdocs,logs} /www/git.domain.tld/htdocs/git
```

* activate needed apache mods
```
a2enmod dav
a2enmod dav_fs
a2enmod rewrite
a2enmod env
```

* create config for gitweb
```
mcedit /www/git.domain.tld/gitweb.conf

For example

$my_uri = “http://git.domain.tld”; # адрес репозиториев
$site_name = “git.domain.tld”; # название сайта, отображается в заголовке
$projectroot = “/www/git.domain.tld/htdocs/git/”; # путь к репозиториям git на жёстком диске

$git_temp = “/tmp”;
$home_link = $my_uri; # ссылка на «домашнюю страничку»
# $home_text = “indextext.html”; # текст, можно расскоментировать и вставить свой
$projects_list = $projectroot;
$stylesheet = “/gitweb/gitweb.css”;
$logo = “/gitweb/git-logo.png”;
$favicon = “/gitweb/git-favicon.png”;
$projects_list_description_width = 40;

$feature{’pathinfo’}{’default’} = [1];
```

* set up vhost for apache2
```
<VirtualHost *:80>
ServerName git.domain.tld
ServerAlias www.git.domain.tld

ServerAdmin head@coderscamp.ru

DocumentRoot /www/git.domain.tld/htdocs
ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/

DirectoryIndex /cgi-bin/gitweb.cgi

RewriteEngine on
RewriteRule ^/([a-zA-Z0-9_\-]+\/\.git)/?(\?.*)?$ /cgi-bin/gitweb.cgi/$1 [L,PT]

SetEnv GITWEB_CONFIG /www/git.domain.tld/gitweb.conf
Alias /gitweb /usr/share/gitweb/

<Directory /www/git.domain.tld/htdocs>
Options FollowSymLinks
AllowOverride None
Order allow,deny
allow from all
</Directory>

<Location /git>
DAV on
AuthType Basic
AuthName «Git»
AuthUserFile /www/git.domain.tld/passwd.git
<LimitExcept GET HEAD PROPFIND OPTIONS REPORT>
Require valid-user
</LimitExcept>
</Location>

LogLevel warn
ErrorLog /www/git.domain.tld/logs/error.log
CustomLog /www/git.domain.tld/logs/access.log combined
</VirtualHost>
```

* add new user
```
htpasswd -cm /www/git.domain.tld/passwd.git user
```
