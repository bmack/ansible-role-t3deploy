TYPO3 CMS Deployment via Ansible
================================

A role that uses ansible's deploy_helper (introduced in Ansible 2.0) to ease deploying of TYPO3 CMS projects.

Requirements
------------

A running application server with PHP (and composer if necessary) should be available.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    t3deploy_base_path: /var/www/

The deployment main path. Inside this directory, "releases" and "shared" folders are created, as well as the
"current" link to the project.

    t3deploy_repository_url: ssh://git@github.com/bmack/ansible-role-t3deploy.git
    
Or

    t3_deploy_repository_url: git@github.com:bmack/ansible-role-t3deploy.git

The GIT repository URL to use for cloning the project on the target systems.

    t3deploy_repository_version: master

The GIT repository branch, tag or sha1 to check out on the target system. This is usually different for local, test
and production systems and should be overriden in the group_vars/ or the playbook.yml itself.

    t3deploy_web_directory: web/

Optionally the path to the site root if your TYPO3 project is installed with the composer web path option.
If you don't define the web directory like this in your composer.json project file, you need to set this property
to an empty string:

    "extra": {
        "typo3/cms": {
            "web-dir": "web"
        }
    }

    t3deploy_shared_directories:
      - "{{ t3deploy_web_directory }}fileadmin"
      - "{{ t3deploy_web_directory }}uploads"
      - "{{ t3deploy_web_directory }}anotherdirectory"

The shared directories which will be symlinked from the shared/ folder into the new release on deployment.

    t3deploy_writeable_directories:
      - "{{ t3deploy_web_directory }}typo3temp"

A list of directories that are created if not existant after deploying a new version.
 
    t3deploy_create_index_file: false

Some hosters have problems using symlinks for the index.php file. However TYPO3 symlinks index.php by default
to typo3_src/index.php. If this option is enabled, the typo3_src/index.php file is copied at each deployment and
the symlink is removed.

    t3deploy_composer_enabled: true

Whether the composer dependency manager should run, enabled by default. For instances where TYPO3 is not set up via
composer, disabling this command will result in 

    t3deploy_composer_command: composer.phar

Defines the executable for running the composer install command, if it's not in the PATH of the user already.

    t3deploy_typo3_context: Production

The TYPO3 Context that should be used. Use it in conjunction with EXT:environment.

    t3deploy_cleanup_items:
      - ansible.cfg
      - build/
      - bootstrap.sh
      - README.md
      - Vagrantfile

A list of files and folders in your repository that should be removed from the directory once the deployment is finished.


Example Playbook
----------------

This is how an example project could look like

    - hosts: prod
      vars:
        t3deploy_repository_url: ssh://git@github.com:bmack/myrepository.git
        t3deploy_web_directory: htdocs/
        t3deploy_create_index_file: true
        t3deploy_composer_command: /usr/local/bin/composer.phar
      roles:
        - { role: bmack.t3deploy }
    
    - hosts: test
        t3deploy_typo3_context: Testing
        t3deploy_repository_version: develop
        t3deploy_repository_url: ssh://git@github.com:bmack/myrepository.git
        t3deploy_web_directory: htdocs/
        t3deploy_create_index_file: true
        t3deploy_composer_command: /usr/local/bin/composer.phar
      roles:
        - { role: bmack.t3deploy }

License
-------

MIT

Author Information
------------------

Created by Benjamin Mack for b:13.
