# OpenCongregationHelper demo

Public demo of [OpenCongregationHelper](https://github.com/OpenCHelper/OpenCongregationHelper), the open source
territory management system for congregations of Jehovah's Witnesses.

The demo always runs the latest release (pre-releases included), filled with dummy data and with the login page
pre-filled with the credentials of the administrator, so anyone can try every feature right away.

> [!WARNING]
> Everybody can sign in to the demo as administrator, and its data is reset every night: never enter real names,
> addresses or other personal data.

## How it works

[`deploy-demo.yml`](.github/workflows/deploy-demo.yml) deploys the latest release with the deployment workflow of
OpenCongregationHelper itself, resetting the database with fresh dummy data:

- every 6 hours, when a new release has been published since the last deployment;
- every night, to undo the changes of the visitors;
- manually (Actions → Deploy the demo → Run workflow), to deploy the latest release again.

Each deployed release is recorded as a `deployed/<tag>` tag of this repository.

## Setup

The demo is hosted on the `ochdemo` [Altervista](https://www.altervista.org) space, at
<https://ochdemo.altervista.org>, in its `demo/` folder: the release is uploaded as a single archive and unpacked on the
server, and the `.htaccess` of the root of the space routes the requests to it (see
[Hosting the previews on the TEST space](https://github.com/OpenCHelper/OpenCongregationHelper#hosting-the-previews-on-the-test-space)
and [Deploying with `ftp-archive-deploy`](https://github.com/OpenCHelper/OpenCongregationHelper#deploying-with-ftp-archive-deploy)
in OpenCongregationHelper).

1. Create a `DEMO` environment (Settings → Environments) with:

   | Kind | Name | Value |
   | --- | --- | --- |
   | Variable | `FTP_SERVER` | FTP server of the space (`ftp.ochdemo.altervista.org`) |
   | Variable | `FTP_USERNAME` | FTP user of the space |
   | Secret | `FTP_PASSWORD` | FTP password of the space |

2. Create the `SPACE_DEFAULT_ENVIRONMENT` (`demo`, the folder served at <https://ochdemo.altervista.org>) and
   `SPACE_PHP_HANDLER` (the PHP version of the space, e.g. `av-php82`) repository variables (Settings → Secrets and
   variables → Actions → Variables).

3. Create the `demo/.env` file of the space from the
   [`.env.example`](https://github.com/OpenCHelper/OpenCongregationHelper/blob/main/.env.example) of
   OpenCongregationHelper, with the database of the space, and set:

   ```dotenv
   APP_NAME="OpenCongregationHelper (demo)"
   APP_ENV=demo
   APP_DEBUG=false
   APP_URL=https://ochdemo.altervista.org
   INITIAL_ROOT_PASSWORD="a-password-used-nowhere-else"
   DEMO_PREFILL_LOGIN_CREDENTIALS=true
   ```

   `APP_ENV` must not be `production`, where the pre-filled login is always disabled (outside production, the
   credentials of the administrator cannot be changed from the application either). The password of
   `INITIAL_ROOT_PASSWORD` is shown to every visitor: never use it anywhere else.

4. Run the workflow manually once (Actions → Deploy the demo → Run workflow).
