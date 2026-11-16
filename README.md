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

Since Altervista has no cron, [cron-job.org](https://cron-job.org) calls the scheduler of the demo
(`/api/artisan/schedule/process`) every 5 minutes, from 8:00 to 22:55 (Rome time), Monday to Friday, like the PR
previews of OpenCongregationHelper: every deployment creates or updates its job, in the `(Demo)` folder.

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
   | Secret | `ROOT_PASSWORD` | Password of the administrator of the demo |
   | Secret | `ARTISAN_CRON_TOKEN` | Random token sent by the cron job to the scheduler, e.g. from `openssl rand -hex 32` |
   | Secret | `CRON_JOB_ORG_API_KEY` | API key of [cron-job.org](https://cron-job.org) (Settings → API) |

   The password of the administrator is shown to every visitor on the login page: never use it anywhere else.

   Altervista accepts FTP logins only from the countries allowed in its control panel: allow the countries of the
   GitHub-hosted runners (mostly the United States), or the deployment fails with
   `530 Autenticazione fallita [...] aggiungi la nazione da cui ti stai connettendo tra quelle consentite`.

2. Create the `SPACE_DEFAULT_ENVIRONMENT` (`demo`, the folder served at <https://ochdemo.altervista.org>) and
   `SPACE_PHP_HANDLER` (the PHP version of the space, e.g. `av-php82`) repository variables (Settings → Secrets and
   variables → Actions → Variables).

3. Enable the MySQL database of the space in the Altervista control panel (`my_ochdemo`, with user `ochdemo` and no
   password).

4. Run the workflow manually once (Actions → Deploy the demo → Run workflow).

The `.env` file of the demo is not managed on the server: every deployment writes it from the `dotenv` settings of
[`deploy-demo.yml`](.github/workflows/deploy-demo.yml), with a new `APP_KEY` and the `ROOT_PASSWORD` secret. Its
`APP_ENV` is `demo`: never `production`, where the pre-filled login is always disabled.
