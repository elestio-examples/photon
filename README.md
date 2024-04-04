# Photon CI/CD pipeline

<a href="https://dash.elest.io/deploy?source=cicd&social=dockerCompose&url=https://github.com/elestio-examples/photon"><img src="deploy-on-elestio.png" alt="Deploy on Elest.io" width="180px" /></a>

Deploy Photon server with CI/CD on Elestio

<img src="photon.png" style='width: 100%;'/>
<br/>
<br/>

# Once deployed ...

You can open Photon ADMIN UI here:

    URL: https://[CI_CD_DOMAIN]
    email: admin
    password: [ADMIN_PASSWORD]


# Custom domain instructions (IMPORTANT)

By default, we set up a CNAME on elestio.app domain, but probably you will want to have your domain.

***Step1:*** Add your domain in the Elestio dashboard as explained here:

    https://docs.elest.io/books/security/page/custom-domain-and-automated-encryption-ssltls

***Step2:*** Update the env vars to indicate your custom domain
Open Elestio dashboard > Service overview > click on UPDATE CONFIG button > Env tab
there update `DOMAIN` & `BASE_URL` with your real domain

***Step3:*** You must set your custom domain in `/opt/app/lemmy.hjson` (you can do that with the File editor of VSCode in our tools tab)

***Step4:*** You must reset the Lemmy instance DB, you can do that with those commands, connect over SSH, and run this:

    cd /opt/app;
    docker-compose down;
    rm -rf ./pgdata;
    rm -rf ./pictrs;
    mkdir -p ./pictrs
    chown -R 991:991 ./pictrs
    docker-compose up -d;

You will start over with a fresh instance of Lemmy directly configured with the correct custom domain name and federation will work as expected

# Configure Own Instance

By default, we set it up to display the inbuild Lemmy instance in the UI. However, if you prefer to use other Lemmy service, please follow the instructions below.

### Dedicated Service:
1. Open the Elestio dashboard
2. Go to the Service overview
3. Click on the `Update Config` button.
4. Select the `Env`.
5. Edit `PUBLIC_INSTANCE_URL` with your real domain.
6. Click `Update & Restart` to reflect the changes.

#### CI/CD:
1. Open the Elestio dashboard
2. Go to the Pipeline Overview
3. Go to the `Build & Deploy` tab.
4. Click on the `Environment Variables` tab.
5. Edit `PUBLIC_INSTANCE_URL` with your real domain.
7. Click the button `Apply Changes` to reflect the changes.

The following environment variables can be set to override the default settings:

| Variable                        | Values              | Default Value                          |
| ------------------------------- | ------------------- | -------------------------------------- |
| PUBLIC_INSTANCE_URL             | URL                 | `[CI_CD_DOMAIN]` Our Inbuild Lemmy     |
| PUBLIC_FAVICON                  | URL                 | `/img/logo-background.svg              |
| PUBLIC_LOCK_TO_INSTANCE         | `bool`              | `true` if `PUBLIC_INSTANCE_URL` is set |
| PUBLIC_SSR_ENABLED              | `bool`              | `false`                                |
| PUBLIC_MIGRATE_COOKIE           | `bool`              | `false`                                |
| PUBLIC_THEME                    | system\|dark\|light | system                                 |
| PUBLIC_EXPANDABLE_IMAGES        | `bool`              | true                                   |
| PUBLIC_MARK_READ_POSTS          | `bool`              | true                                   |
| PUBLIC_REVERT_VOTE_COLORS       | `bool`              | false                                  |
| PUBLIC_SHOW_INSTANCES_USER      | `bool`              | false                                  |
| PUBLIC_SHOW_INSTANCES_COMMUNITY | `bool`              | true                                   |
| PUBLIC_SHOW_INSTANCES_COMMENTS  | `bool`              | false                                  |
| PUBLIC_SHOW_COMPACT_POSTS       | `bool`              | false                                  |
| PUBLIC_DEFAULT_FEED_SORT        | `SortType`          | Active                                 |
| PUBLIC_DEFAULT_FEED             | `ListingType`       | Local                                  |
| PUBLIC_DEFAULT_COMMENT_SORT     | `CommentSortType`   | Hot                                    |
| PUBLIC_HIDE_DELETED             | `bool`              | true                                   |
| PUBLIC_HIDE_REMOVED             | `bool`              | false                                  |
| PUBLIC_FULL_WIDTH_LAYOUT        | `bool`              | false                                  |
| PUBLIC_EXPAND_SIDEBAR           | `bool`              | true                                   |
| PUBLIC_DISPLAY_NAMES            | `bool`              | true                                   |
| PUBLIC_NSFW_BLUR                | `bool`              | true                                   |
| PUBLIC_NEW_VOTE_BUTTONS         | `bool`              | false                                  |
| PUBLIC_RANDOM_PLACEHOLDERS      | `bool`              | true                                   |
| PUBLIC_REMOVE_CREDIT            | `bool`              | false                                  |

The values for `SortType`, `ListingType`, and `CommentSortType` are defined by the lemmy-js-client library.
