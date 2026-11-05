# Deployment Guide: Farmer Crop Portal (Render & Vercel)

This guide walks you through deploying your full-stack MERN application to **Render** (Backend API) and **Vercel** (Frontend Client) using **MongoDB Atlas**.

---

## Pre-deployment Checklist

- [x] Repository cleaned of tracked `node_modules` and local database files.
- [x] Backend CORS configured to allow Vercel and Render domains.
- [x] `render.yaml` blueprint added to project root.
- [x] `frontend/vercel.json` configured for SPA routing.
- [x] Frontend API base URL sanitized and timeout set for Render cold starts.

---

## Step 1: Push Code to GitHub

Open your terminal in the project directory and push the latest commits to GitHub:

```bash
git push origin main
```

*(If prompted, sign in via browser or enter your GitHub credentials)*

---

## Step 2: MongoDB Atlas Network Access

Render free tier instances have dynamic IP addresses. To allow Render to communicate with your MongoDB Atlas cluster:

1. Log in to [MongoDB Atlas](https://cloud.mongodb.com/).
2. In the left navigation, click **Network Access** under the *Security* section.
3. Click **+ Add IP Address**.
4. Click **Allow Access from Anywhere** (`0.0.0.0/0`).
5. Click **Confirm**.

---

## Step 3: Deploy Backend on Render

1. Log in to [Render](https://dashboard.render.com/).
2. Click **New +** in the top right and select **Web Service**.
3. Choose **Build and deploy from a Git repository** and connect your `arunnalamati/MERN` repository.
4. Fill in the service configuration:
   - **Name**: `farmer-crop-portal-backend` *(or your preferred name)*
   - **Region**: Choose the region closest to your users (e.g., *Singapore*, *Oregon*, or *Frankfurt*)
   - **Branch**: `main`
   - **Root Directory**: `backend`
   - **Runtime**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Instance Type**: `Free`

5. Scroll down to **Environment Variables** and add the following:

   | Key | Value | Description |
   | :--- | :--- | :--- |
   | `NODE_ENV` | `production` | Production environment mode |
   | `PORT` | `10000` | Port used by Render (auto-configured) |
   | `MONGO_URI` | `mongodb+srv://gnyanada3111_db_user:arun1234@cluster0.vjpvxfn.mongodb.net/farmer_crop_portal?retryWrites=true&w=majority` | MongoDB Atlas connection string |
   | `JWT_SECRET` | `super_secure_farmer_crop_portal_jwt_secret_2026_key` | Secret key for JWT signing |
   | `CLIENT_URL` | `*` | Allowed CORS origin (you can update this to your Vercel URL later) |

6. Click **Create Web Service**.
7. Wait 2–3 minutes for the build to finish. Once live, copy your backend URL (e.g., `https://farmer-crop-portal-backend.onrender.com`).
8. Verify it works by opening `https://farmer-crop-portal-backend.onrender.com` in your browser. You should see:
   ```json
   {
     "project": "Farmer Crop Information Portal API",
     "status": "Running",
     "version": "1.0.0"
   }
   ```

---

## Step 4: Deploy Frontend on Vercel

1. Log in to [Vercel](https://vercel.com/dashboard).
2. Click **Add New...** -> **Project**.
3. Select your GitHub repository (`MERN` or `arunnalamati/MERN`) and click **Import**.
4. In the **Configure Project** screen:
   - **Project Name**: `farmer-crop-portal` *(or default)*
   - **Framework Preset**: `Vite` *(auto-detected)*
   - **Root Directory**: Click **Edit** and choose `frontend`. Click **Continue**.
   - **Build Command**: `npm run build` *(default)*
   - **Output Directory**: `dist` *(default)*
   - **Install Command**: `npm install` *(default)*

5. Expand **Environment Variables** and add:

   | Key | Value |
   | :--- | :--- |
   | `VITE_API_URL` | `https://farmer-crop-portal-backend.onrender.com/api` |

   *(Replace with your actual Render backend URL, appending `/api` at the end)*

6. Click **Deploy**.
7. In ~1 minute, your site will be live at `https://farmer-crop-portal.vercel.app`!

---

## Step 5: (Optional) Lock Down CORS on Render

Once your Vercel app is live:
1. Go back to your Render Dashboard -> `farmer-crop-portal-backend` -> **Environment**.
2. Update `CLIENT_URL` from `*` to your actual Vercel URL (e.g. `https://farmer-crop-portal.vercel.app`).
3. Click **Save Changes** (Render will automatically redeploy).

---

## Default Login Credentials (from seed data)

- **Admin Account**: `admin@farmerportal.com` / `admin123`
- **Farmer Account**: `ramesh@farmerportal.com` / `farmer123`
