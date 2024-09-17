# Multi Data Center Environment Deployment

This guide will help you deploy a Multi Data Center Environment with one control plane and two data planes.

## Prerequisites

- Git
- Docker
- Tyk Dashboard and MDCB licenses

## Steps

### 1. Clone the Repository

```bash
git clone <repository-url>
```

### 2. Add Licenses

Add the MDCB and Dashboard License in the `.env` file:

```env
TYK_DB_LICENSEKEY=<<TYK_DASHBOARD_LICENSE>>
TYK_MDCB_LICENSE=<<TYK_MDCB_LICENSE>>
```

### 3. Deploy the Control Plane

Deploy the control plane using Docker Compose:

```bash
docker compose -f docker-compose.control.yml up
```

### 4. Bootstrap the Tyk Dashboard

Go to `http://localhost:3000` and bootstrap the Tyk Dashboard.

### 5. Login to the Tyk Dashboard

Login to the Tyk Dashboard and navigate to “users”. Click on your user and copy the OrgID and Access API information.

### 6. Update Environment Variables

Use the values to complete the `.env` variables:

```env
TYK_GW_SLAVEOPTIONS_RPCKEY=<<ORG_ID>>
TYK_GW_SLAVEOPTIONS_APIKEY=<<TYK_DASHBOARD_API_ACCESS_CREDENTIALS>>
```

### 7. Enable Hybrid Architecture

Run the following commands in the command line:

```bash
export DASH_ADMIN_SECRET=12345
export DASH_URL=http://localhost:3000
export ORG_ID=<<ORG_ID>>

curl $DASH_URL/admin/organisations/$ORG_ID -H "Admin-Auth: $DASH_ADMIN_SECRET" | python3 -mjson.tool > myorg.json
```

Edit `myorg.json` to change the `hybrid_enabled` value to `true`:

```json
{
  ...
  "hybrid_enabled": true,
  ...
}
```

Push the changes:

```bash
curl -X PUT $DASH_URL/admin/organisations/$ORG_ID -H "Admin-Auth: $DASH_ADMIN_SECRET" -d @myorg.json
```

### 8. Deploy Data Planes

Deploy two data planes with a Tyk API Gateway, Tyk Pump, and Redis.

### 9. Create a New API

Create a new API to be propagated to both Tyk API Gateways.

### 10. Test the Tyk API Gateways

Check that both Tyk API Gateways are up and running:

- Tyk API Gateway 1: `http://localhost:8090/hello`
- Tyk API Gateway 2: `http://localhost:8091/hello`
```
