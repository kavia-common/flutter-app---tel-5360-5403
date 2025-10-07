# Environment Configuration

This app uses flutter_dotenv to load runtime configuration from a `.env` file bundled as an asset for local development and CI.

Keys expected in `.env`:
- ENVIRONMENT: development | staging | production
- API_BASE_URL: Base URL for general API endpoints (e.g., https://api.example.com)
- AUTH_BASE_URL: Optional separate base for auth endpoints (e.g., https://auth.example.com). Defaults to API_BASE_URL if unset.
- LOG_NETWORK: Enable HTTP request/response logging (true/false, 1/0, yes/no, on/off)
- FEATURE_USE_SQFLITE_CACHE: Toggle local sqflite cache features (true/false)

.env.example
ENVIRONMENT=development
API_BASE_URL=http://localhost:3000
# AUTH_BASE_URL=http://localhost:3001
LOG_NETWORK=true
FEATURE_USE_SQFLITE_CACHE=true

Where it is used:
- lib/core/config/env.dart: Loads .env and exposes typed getters.
- lib/core/network/api_client.dart:
  - Uses API_BASE_URL for most requests.
  - Uses AUTH_BASE_URL (when provided) for paths starting with /v1/auth/.
  - Adds Authorization: Bearer <token> when a token exists in Preferences.
  - Respects LOG_NETWORK flag to print HTTP logs.
- lib/core/storage/local_db.dart:
  - You may toggle usage at feature level by reading EnvConfig.useSqfliteCache.

Flutter configuration:
- pubspec.yaml includes `.env` under flutter.assets to allow local/CI runs without extra steps.

Bootstrapping:
- AppState.bootstrap() calls EnvConfig.ensureLoaded() at startup to ensure .env is available before other services.

Notes:
- Do not commit secrets into the repository. Use .env for development only. Provide a .env file via CI/CD or local machine.
- In production builds, consider supplying configuration via different mechanisms or using separate .env files per flavor.
