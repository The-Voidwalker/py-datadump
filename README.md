# py-datadump
Standalone python script for invoking the DataDump extension on Miraheze

# Installation
Install python on your system (if not already installed). Then simply copy or download the [raw script](https://raw.githubusercontent.com/The-Voidwalker/py-datadump/main/datadump.py) to a file. Configure it (see below) and run.

Note: If you use OAuth1 you will need to install `requests_oauthlib` as an additional dependency. To do this run `pip install requests_oauthlib` or `python -m pip install requests_oauthlib`.

# Configuration
At a minimum, you will need to configure the script to know your username, the hostname of your wiki, and one or more of the authentication methods.

## USER
Set this value equal to whatever your username is. If your username is `Example` you would set the line to:
```py
USER = "Example"
```

## HOSTNAME
Set the full URL to your wiki here. If you are using https://publictestwiki.com, you would set
```py
HOSTNAME = "https://publictestwiki.com"
# OR
HOSTNAME = "https://publictestwiki.com/wiki/Main_page"
```
If in doubt, simply past in the full URL to the homepage of your wiki. NOTE: A value such as `meta.miraheze.org` without the protocol *will fail*.

## DUMPS_DIRECTORY
Set this value to the name of the directory (folder) you want your dumps downloaded to. By default, the script will download the dumps to the current working directory.
```py
# Example for windows
DUMPS_DIRECTORY = r"D:\mywiki\backups"
# Example for linux
DUMPS_DIRECTORY = "/home/user/mywiki/backups"
```

## DUMP_TYPES
Configure this to be a list of the dump types you wish to have generated and downloaded. The available dump types can be viewed from Special:DataDump on your wiki. Be aware that until [T10280](https://phabricator.miraheze.org/T10280) is resolved, it will not be possible to generate image dumps. The default value is for xml dumps only. If you wish to have xml dumps and a managewiki backup, set:
```py
DUMP_TYPES = ['xml', 'managewiki_backup']
```

# Authentication setup
Please bear in mind that anyone who can access the script file will be able to use whatever authentication method you select here to access your account.

Multiple authentication methods can be configured in the same script (though they will all attempt to use the same username). The script will prefer OAuth2, but if that login method fails or is not configured, it will attempt OAuth1. If OAuth1 fails or is not configured, it will then attempt to use the password to login.

It is strongly recommended that you use OAuth (either 2 or 1) and NOT use a password. OAuth applications are limited in the type of access they have to your account, and it is much easier to recover from a breach of OAuth credentials as opposed to your username and password.

## OAUTH2
If you are using this script on Miraheze, you will first need to visit https://meta.miraheze.org/wiki/Special:OAuthConsumerRegistration/propose to begin setting up a new OAuth consumer. Do this while logged into the account you wish to use (this must be what you configured for [USER](#user)).

Begin by givng your consumer application a simple and descriptive name, such as "YourWiki DataDump script". Feel free to leave the Consumer version alone. Be sure to set the OAuth protocol version to 2.0 and provide a description. IMPORTANT: Check the box that says "This consumer is for youse only by YourName." If you do not check this box, you will not be able to use the OAuth application!

Next, scroll down to the section on applicable grants. Ensure that the grant for "Create, view, and delete dumps" is checked. This is the only grant (other than basic rights) that needs to be checked for this script to function. Then, check the acknoledgement checkbox, and propose the consumer.

You should now see a page with the text "Your tokens are:" followed by a client application key, a client application secret, and an access token. Record each of these for future reference, however, the only one we need is the access token. Copy the full access token to the `OAUTH2_TOKEN` configuration.

## OAUTH1
Follow the same procedure as for [OAuth2](#oauth2), except set the OAuth protocol version to 1.0a. The rest of the steps will be the same until you submit the consumer. In this case, you should see a page with the client (consumer) application key, the client (consumer) application secret, an access token, and an access secret. These values correspond directly to the configruation options `OAUTH1_CONSUMER_KEY`, `OAUTH1_CONSUMER_SECRET`, `OAUTH1_ACCESS_TOKEN`, and `OAUTH1_ACCESS_SECRET` respectively.

## PASSWORD
Putting the password for your account into a file is not recommended. If you do, simply make sure that the password you set is the correct one for the [USER](#user) you configured.

# Advanced options
The following are advanced options that likely will not need to be configured for you

## DELETE_EXISTING
If set to `True`, the script will delete existing dumps of the configured [DUMP_TYPES](#dump_types) before generating new ones. If set to `False`, the script will neither delete existing dumps, **nor will it download existing dumps**. This value is `True` by default.

## DELETE_AFTER_DOWNLOAD
If set to `True`, the script will delete the dumps after it finishes downloading them. This value is `False` by default.

## WAIT_FOR_DUMPS
Delay in seconds to wait after queueing a dump generation to wait before attempting to download it. Increase this value if you are unable to download a dump because it has not finised generating yet. The default value for this option is `30`.

## MAX_RETRIES
If the script encounters a connection error (by definition any server response other than 200), the script will make a most this many attempts per request. The default value for this option is `5`.

## RETRY_TIMEOUT
If the script encounters a connection error (by definition any server response other than 200), the script will wait this many seconds before making a new attempt. The default value for this option is `30`.

## ACCEPT_DEFAULT_USERNAME
The only reason you need to set this to True is if your [username](#user) is actually `YOUR USERNAME HERE`. Otherwise, this script will fail thinking the username has not been configured.

## SCRIPT_PATH
DO NOT CHANGE THIS OPTION FOR MIRAHEZE WIKIS.

The value of [$wgScriptPath](https://www.mediawiki.org/wiki/Manual:$wgScriptPath) for the wiki. The default value for this option is `"/w"`.

## API_PATH
DO NOT CHANGE THIS OPTION FOR MIRAHEZE WIKIS.

The location of api.php after the script path. The default value for this option is `"/api.php"`.

For example, if the api of your wiki is located at `https://examplewiki.com/script/api.php`, you would need the following configuration:
```py
HOSTNAME = "https://examplewiki.com"
SCRIPT_PATH = "/script"
API_PATH = "/api.php"
```

## DEFAULT_USER_AGENT
Custom [user agent](https://en.wikipedia.org/wiki/User_agent#Use_in_HTTP) for use by the script. The default value for this option is `"py-datadump script"`.

Consider changing this value to be specific for your wiki such as:
```py
DEFAULT_USER_AGENT = "metawiki py-datadump script"
```
