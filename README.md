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

By default, we set it up to display the `lemmy.ml` instance in the UI. However, if you prefer to use your Lemmy service, please follow the instructions below.

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
