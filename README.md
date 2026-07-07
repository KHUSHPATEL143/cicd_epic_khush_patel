# CICD_Epic - Continuous Integration & Infrastructure Knowledge API

CICD_Epic is a production-ready, scalable RESTful API built with Node.js, Express, and MongoDB. It acts as an operations control center serving a dataset of **2,708 continuous integration workflow templates and infrastructure guides**. 

It features built-in document version auditing, execution run simulations (streaming logs & diagnostics metrics), secure JWT authentication, role restrictions, and administrative user CRUD dashboards.

### 📖 API Reference & Postman Documentation
The complete interactive API documentation, detailing parameters, payloads, and response structures for all 49 endpoints, is available here:
👉 **[Postman API Documentation](https://documenter.getpostman.com/view/50839203/2sBY4JwNAP)**

### 🌐 Live Production Deployments
The application is deployed and accessible globally:
* **Frontend Web App:** [https://cicd-epic-khush-patel.vercel.app](https://cicd-epic-khush-patel.vercel.app)
* **Backend API Service (Healthcheck):** [https://cicd-epic-khush-patel.onrender.com/health](https://cicd-epic-khush-patel.onrender.com/health)

---

## 🚀 Local Installation & Running

### Prerequisites
- Node.js (v18 or higher recommended)
- MongoDB Server running locally on port `27017`

### 1. Backend Setup & Configuration
Navigate to the `BACKEND` directory:
```bash
cd BACKEND
```

Install the required dependencies:
```bash
npm install
```

Create/Review the environment file `BACKEND/.env` (pre-configured with local defaults):
```env
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/cicd-epic-db
NODE_ENV=development
JWT_SECRET=supersecretkeyforjwtcicdepicpatel143
JWT_EXPIRES_IN=30d
```

### 2. Seeding the Database
Run the seeder script to clear previous collections and bulk-load the 2,708 continuous integration records enriched with mock analytics stats, along with two default operational credentials:
```bash
npm run seed
```

**Seeded Credentials:**
- **Lead Administrator (Admin):** `admin@cicd-epic.com` (password: `admin123`)
- **Developer (Standard User):** `user@cicd-epic.com` (password: `user123`)

### 3. Starting the Server
Start the development server with automatic file reload:
```bash
npm run dev
```
The API server will listen on `http://localhost:5000/api/v1`.

### 4. Running Validation Tests
To run the self-contained verification test suite asserting all 49 routes, run:
```bash
node scripts/test_endpoints.js
```

---

## 🗄️ Database Modeling & Schema Design

Our database is designed around four Mongoose collections inside MongoDB:

1. **Workflows (`workflows`):** Represents CI/CD actions and scripts. Implements compound text indices on `instruction` and `output` fields for efficient phrase search. Includes counters for views, runs, and ratings.
2. **Workflow Versions (`workflowversions`):** Audits and archives historical mutations to any workflow. Stores `workflowId`, `version` number, contents, change operation type (`create`, `replace`, `patch`), and change timestamps.
3. **Workflow Runs (`workflowruns`):** Simulates a VM/container environment execution logs stream (stdout) and hardware metrics (duration, CPU, memory).
4. **Users (`users`):** Stores credentials, hashed passwords (via pre-save bcrypt triggers), and application roles (`admin`, `user`).

---

## 🧭 API Route Layout & Endpoints (49 Total)

### 1. Authentication & Session Management
- `POST /api/v1/auth/signup` - Register a standard or admin user.
- `POST /api/v1/auth/login` - Validate credentials and return JWT authorization token.
- `GET /api/v1/auth/profile` - Retrieve the current authenticated user's profile details.
- `PUT /api/v1/auth/profile` - Update the current authenticated user's credentials.

### 2. Administrative User Management (Admin Only)
- `GET /api/v1/users` - Retrieve paginated accounts.
- `POST /api/v1/users` - Register a new account.
- `GET /api/v1/users/:id` - Fetch user details.
- `PUT /api/v1/users/:id` - Update user role or password.
- `DELETE /api/v1/users/:id` - Permanently remove a user account.

### 3. Continuous Integration Workflows (Core REST CRUD)
- `GET /api/v1/workflows` - Fetch workflows with advanced filters, projection, sorting, and pagination.
- `POST /api/v1/workflows` - Create a new workflow (creates Version 1).
- `GET /api/v1/workflows/random` - Retrieve a random workflow.
- `GET /api/v1/workflows/latest` - Fetch newest additions.
- `GET /api/v1/workflows/trending` - Fetch most frequently run templates.
- `GET /api/v1/workflows/popular` - Fetch templates with high view counts.
- `GET /api/v1/workflows/recommended` - Fetch templates with high ratings.
- `GET /api/v1/workflows/:id` - Fetch details (auto-increments view counter).
- `PUT /api/v1/workflows/:id` - Replace a workflow (commits a new audit version).
- `PATCH /api/v1/workflows/:id/content` - Partially update workflow properties (commits a new audit version).
- `DELETE /api/v1/workflows/:id` - Permanently remove a workflow and clean up its history logs.
- `PATCH /api/v1/workflows/:id/archive` - Soft-delete (hides from default lookups).
- `PATCH /api/v1/workflows/:id/restore` - Reactivate an archived workflow.
- `GET /api/v1/workflows/:id/versions` - Retrieve version history logs.
- `POST /api/v1/workflows/:id/clone` - Clone a workflow.
- `POST /api/v1/workflows/:id/run` - Trigger a simulated container test run.
- `POST /api/v1/workflows/:id/cancel` - Terminate a running workflow VM.
- `GET /api/v1/workflows/:id/logs` - Fetch stdout log streams.
- `GET /api/v1/workflows/:id/metrics` - Fetch resource usage metrics.
- `GET /api/v1/workflows/history/:id` - Retrieve complete run execution list.

### 4. Infrastructure & Platform Guides (20 Sub-routes)
Query and fetch guides dynamically by category, platform, component, or resource type:
- `GET /api/v1/infra/k8s` - Kubernetes guides.
- `GET /api/v1/infra/docker` - Docker guides.
- `GET /api/v1/infra/helm` - Helm package charts.
- `GET /api/v1/infra/terraform` - IaC configuration guides.
- `GET /api/v1/infra/aws` - Amazon Web Services guides.
- `GET /api/v1/infra/gcp` - Google Cloud Platform guides.
- `GET /api/v1/infra/azure` - Microsoft Azure guides.
- `GET /api/v1/infra/pods` - Pod resources.
- `GET /api/v1/infra/services` - Kubernetes Service guides.
- `GET /api/v1/infra/deployments` - Deployments guides.
- `GET /api/v1/infra/ingress` - Ingress controllers.
- `GET /api/v1/infra/configmaps` - ConfigMap key-value pairs.
- `GET /api/v1/infra/secrets` - Sensitive environment settings.
- `GET /api/v1/infra/volumes` - Storage mounts and PVs.
- `GET /api/v1/infra/networking` - Network Policies.
- `GET /api/v1/infra/autoscaling` - HPA scale rules.
- `GET /api/v1/infra/security` - Policy configurations.
- `GET /api/v1/infra/monitoring` - Prometheus scrape configs.
- `GET /api/v1/infra/logging` - Fluent Bit metrics shipping.
- `GET /api/v1/infra/templates` - Starter config skeletons.