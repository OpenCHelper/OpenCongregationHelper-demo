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

The demo is hosted on its own [Altervista](https://www.altervista.org) space, as described in the
[Deploy section](https://github.com/OpenCHelper/OpenCongregationHelper#deploy) of OpenCongregationHelper.

1. Create a `DEMO` environment (Settings → Environments) with:

   | Kind | Name | Value |
   | --- | --- | --- |
   | Variable | `FTP_SERVER` | FTP server of the space (e.g. `ftp.example.altervista.org`) |
   | Variable | `FTP_USERNAME` | FTP user of the space |
   | Secret | `FTP_PASSWORD` | FTP password of the space |

2. Create the `.env` file in the root of the space from the
   [`.env.example`](https://github.com/OpenCHelper/OpenCongregationHelper/blob/main/.env.example) of
   OpenCongregationHelper, with the database of the space, and set:

   ```dotenv
   APP_NAME="OpenCongregationHelper (demo)"
   APP_ENV=preview
   APP_DEBUG=false
   APP_URL=https://example.altervista.org
   INITIAL_ROOT_PASSWORD="a-password-used-nowhere-else"
   DEMO_PREFILL_LOGIN_CREDENTIALS=true
   ```

   `APP_ENV` must not be `production`, where the pre-filled login is always disabled (outside production, the
   credentials of the administrator cannot be changed from the application either). The password of
   `INITIAL_ROOT_PASSWORD` is shown to every visitor: never use it anywhere else.

3. Run the workflow manually once (Actions → Deploy the demo → Run workflow).
