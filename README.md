[![logo](https://raw.githubusercontent.com/naqoda/docker-samba/master/logo.jpg)](https://www.samba.org)

# Samba

Samba docker container, based on [dperson/samba](https://github.com/dperson/samba) with performance improvements and option to set user ID when creating a Samba user.

# What is Samba?

Since 1992, Samba has provided secure, stable and fast file and print services
for all clients using the SMB/CIFS protocol, such as all versions of DOS and
Windows, OS/2, Linux and many others.

# How to use this image

By default there are no shares configured, additional ones can be added.

## Hosting a Samba instance

    sudo docker run -it -p 139:139 -p 445:445 -d naqoda/docker-samba

OR set local storage:

    sudo docker run -it --name samba -p 139:139 -p 445:445 \
                -v /path/to/directory:/mount \
                -d naqoda/docker-samba

## Configuration

    sudo docker run -it --rm naqoda/docker-samba -h
    Usage: samba.sh [-opt] [command]
    Options (fields in '[]' are optional, '<>' are required):
        -h          This help
        -i "<path>" Import smbpassword
                    required arg: "<path>" - full file path in container
        -n          Start the 'nmbd' daemon to advertise the shares
        -p          Set ownership and permissions on the shares
        -s "<name;/path>[;browsable;readonly;guest;users;admins]" Configure a share
                    required arg: "<name>;<comment>;</path>"
                    <name> is how it's called for clients
                    <path> path to share
                    NOTE: for the default values, just leave blank
                    [browsable] default:'yes' or 'no'
                    [readonly] default:'yes' or 'no'
                    [guest] allowed default:'yes' or 'no'
                    [users] allowed default:'all' or list of allowed users
                    [admins] allowed default:'none' or list of admin users
        -t ""       Configure timezone
                    possible arg: "[timezone]" - zoneinfo timezone for container
        -u "<username;password;id>"       Add a user
                    required arg: "<username>;<passwd>"
                    <username> for user
                    <password> for user
                    <id> for user (allows to force a user ID)
        -w "<workgroup>"       Configure the workgroup (domain) samba should use
                    required arg: "<workgroup>"
                    <workgroup> for samba

    The 'command' (if provided and valid) will be run instead of samba

ENVIRONMENT VARIABLES (only available with `docker run`)

 * `NMBD` - As above, enable nmbd
 * `TZ` - As above, set a zoneinfo timezone, IE `EST5EDT`
 * `WORKGROUP` - As above, set workgroup

**NOTE**: if you enable nmbd (via `-n` or the `NMBD` environment variable), you
will also want to expose port 137 with `-p 137:137`.

## Examples

Any of the commands can be run at creation with `docker run` or later with
`docker exec -it samba.sh` (as of version 1.3 of docker).

### Setting the Timezone

    sudo docker run -it -p 139:139 -p 445:445 -d naqoda/docker-samba -t EST5EDT

OR using `environment variables`

    sudo docker run -it -e TZ=EST5EDT -p 139:139 -p 445:445 -d naqoda/docker-samba

Will get you the same settings as

    sudo docker run -it --name samba -p 139:139 -p 445:445 -d naqoda/docker-samba
    sudo docker exec -it samba samba.sh -t EST5EDT ls -AlF /etc/localtime
    sudo docker restart samba

### Start an instance creating user 'user1' and share 'data' mapped to local path '/path/to/data':

    sudo docker run -it --name samba -p 139:139 -p 445:445 \
    			-v /path/to/data:/share \
    			-d naqoda/docker-samba \
                -u "user1;password1" \
                -s "data;/share;yes;no;no;user1" 

# User Feedback

## Issues

If you have any problems with or questions about this image, please contact me
through a [GitHub issue](https://github.com/naqoda/docker-samba/issues).