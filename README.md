# TamilMV Downloader - Render Deployment

This project contains the `render.yaml` Blueprint for deploying the `sidmatka/tamilmv-downloader:latest` Docker image natively on [Render](https://render.com).

## Key Differences for Render

1. **No Watchtower:** Render does not give access to the underlying `/var/run/docker.sock`, which means Watchtower cannot be used. Instead, you can use Render's **Deploy Hooks** combined with a free cron service (like cron-job.org) to trigger automatic updates on a schedule (e.g., every Sunday).
2. **Persistent Storage (Disks):** Render handles persistent storage using "Disks". The `render.yaml` defines a 1GB disk mounted to `/app/data`. **Important:** Render's Free tier does *not* support Disks. You will need a paid plan (e.g., Starter plan) to run this with persistent data.
3. **Ports:** Render automatically handles SSL and port forwarding. It maps an external HTTPS URL down to the internal `8282` port using the `PORT` environment variable.

## How to Deploy on Render

### Option 1: Using the Render Dashboard (Manual)
1. Go to the [Render Dashboard](https://dashboard.render.com).
2. Click **New** -> **Web Service**.
3. Select **Deploy an existing image from a registry**.
4. Enter `sidmatka/tamilmv-downloader:latest` as the Image URL and click Next.
5. Set Name: `tamilmv-downloader`
6. Under **Advanced**, add an Environment Variable: `TZ` = `Asia/Kolkata`.
7. Under **Advanced**, add a Disk: Name = `tamilmv-data`, Mount Path = `/app/data`, Size = `1 GB`.
8. Choose your Instance Type (must be a paid tier for Disk support) and click **Create Web Service**.

### Option 2: Using the Blueprint (Infrastructure as Code)
If you push this repository to GitHub/GitLab:
1. Go to the Render Dashboard.
2. Click **New** -> **Blueprint**.
3. Connect your repository containing this `render.yaml` file.
4. Render will automatically provision the Web Service, Environment Variables, and Persistent Disk as defined.

## Accessing the Application

Once deployed, Render will provide a public URL for your service (e.g., `https://tamilmv-downloader-xxxx.onrender.com`).
- **Base URL:** `https://your-app-url.onrender.com`
- **API Documentation:** `https://your-app-url.onrender.com/docs` or `/redoc`

## Automatic Updates (Replacement for Watchtower)
To set up your Sunday automatic update:
1. In your Render Web Service settings, go to **Deploy Hooks**.
2. Copy your unique Deploy Hook URL.
3. Use a free external cron service (like [cron-job.org](https://cron-job.org)) or GitHub Actions to send an HTTP POST request to that URL every Sunday at midnight.
4. Render will automatically pull the newest `sidmatka/tamilmv-downloader:latest` image and roll out the update.
