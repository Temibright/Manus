# 🔌 Comprehensive Guide: Building Custom APIs in a WordPress Plugin for Identity & Clearance Services

This guide provides a complete, production-ready architectural manual and code blueprint for building custom REST API endpoints inside a WordPress plugin. It specifically covers integrating third-party services—such as **IPE Clearance**, **NIN Validation**, **Personalization**, and **NIN Identity Search**—with support for multiple request variants and asynchronous status workflows.

---

## 📑 Table of Contents
1. [Architectural Overview](#1-architectural-overview)
2. [WordPress REST API Foundations](#2-wordpress-rest-api-foundations)
3. [Service & Variant Taxonomy](#3-service--variant-taxonomy)
4. [Step-by-Step Plugin Implementation](#4-step-by-step-plugin-implementation)
   - [4.1 Main Plugin Entry Point](#41-main-plugin-entry-point)
   - [4.2 Admin Settings Page (API Key Configuration)](#42-admin-settings-page-api-key-configuration)
   - [4.3 External API Client Class](#43-external-api-client-class)
   - [4.4 REST API Controller Class](#44-rest-api-controller-class)
5. [Handling Variants & Response Parsing](#5-handling-variants--response-parsing)
6. [Frontend & Client-Side Integration (JS Fetch Example)](#6-frontend--client-side-integration-js-fetch-example)
7. [Security, Performance & Best Practices](#7-security-performance--best-practices)

---

## 1. Architectural Overview

When building a WordPress plugin that interfaces with external APIs (like IPE Clearance or NIN Verification), you should **never expose upstream API keys directly to the browser**.

Instead, implement a **Gateway/Proxy Pattern**:

```
[ Frontend Client / Browser ]
           │
           │  1. REST Request + Nonce (AJAX)
           ▼
[ WordPress Site / Plugin Endpoint ]  ───▸ Checks Auth, Nonce, Rate Limits
           │
           │  2. `wp_remote_post()` with Secure API Key Header
           ▼
[ Upstream Service (Robost Tech / IPE API) ]
           │
           │  3. Responds with Status / Demographic Data / Base64 Image
           ▼
[ WordPress Plugin Proxy ]  ───▸ Formats Data / Error Handling
           │
           │  4. Returns WP_REST_Response
           ▼
[ Frontend Client / UI ]
```

### Key Benefits:
- **Security:** Hides upstream API keys in WordPress server configuration or options database.
- **Abstraction:** Provides clean internal endpoints (e.g., `/wp-json/robosttech/v1/validation/submit`) regardless of third-party changes.
- **Status & Cache Management:** Allows caching results via WordPress Transients API to reduce API consumption costs.

---

## 2. WordPress REST API Foundations

WordPress natively includes the **REST API Infrastructure**, initialized on the `rest_api_init` action hook using `register_rest_route()`.

```php
add_action( 'rest_api_init', function () {
    register_rest_route( 'robosttech/v1', '/clearance/submit', array(
        'methods'             => 'POST',
        'callback'            => 'handle_clearance_submission',
        'permission_callback' => 'check_user_permissions',
        'args'                => array(
            'tracking_id' => array(
                'required'          => true,
                'type'              => 'string',
                'sanitize_callback' => 'sanitize_text_field',
            ),
        ),
    ) );
} );
```

---

## 3. Service & Variant Taxonomy

Your plugin handles multiple distinct services, each with specific API endpoints, parameters, and success/error variants:

### Service 1: Validation Flow
* **Variant 1.1: Submit NIN for Validation** (`POST /api/validation`)
  * Parameters: `nin`
  * Status Responses: `approved: true`, `category: "new"`
* **Variant 1.2: Check Validation Status** (`POST /api/validation_status`)
  * States: `status: "sent"`, `status: "processing"`, `approved: true`

### Service 2: Personalization Flow
* **Variant 2.1: Submit Personalization** (`POST /api/personalization`)
  * Parameters: `tracking_id`
* **Variant 2.2: Check Personalization Status** (`POST /api/personalization_status`)
  * States: `processing` vs. `completed` (returns profile object & Base64 `photo`)

### Service 3: NIN Identity Search
* **Variant 3.1: Search by NIN Number** (`POST /api/nin_verify`)
  * Parameters: `nin`
* **Variant 3.2: Search by Phone Number** (`POST /api/nin_phone`)
  * Parameters: `phone`
* **Variant 3.3: Search by Demographic Data** (`POST /api/nin_demo`)
  * Parameters: `firstname`, `lastname`, `middlename`, `gender`, `dateOfBirth`

### Service 4: IPE Clearance Flow
* **Variant 4.1: Request New Clearance** (`POST /api/clearance`)
* **Variant 4.2: Clearance Error Variants**
  * *Duplicate Existing:* Returns HTTP 400 (`exist: true`, `"Already Exist"`)
  * *Previous Rejection:* Returns HTTP 400 (`"Previous Clearance Failed"`)
* **Variant 4.3: Check Clearance Status** (`POST /api/clearance_status`)
  * *Success Variant:* `cleared: true`, `status: "completed"`, `reply: "[CLEARANCE_CODE]"`
  * *Refunded Variant:* `not_cleared: true`, `status: "failed"`, `reply: "NO RECORD FOUND..."`

---

## 4. Step-by-Step Plugin Implementation

Create the following file structure inside your WordPress plugin directory `wp-content/plugins/robosttech-api-gateway/`:

```
robosttech-api-gateway/
├── robosttech-api-gateway.php
├── includes/
│   ├── class-admin-settings.php
│   ├── class-api-client.php
│   └── class-rest-controller.php
└── assets/
    └── js/
        └── api-handler.js
```

### 4.1 Main Plugin Entry Point (`robosttech-api-gateway.php`)

```php
<?php
/**
 * Plugin Name: RobostTech & IPE Clearance API Gateway
 * Description: Integrates IPE Clearance, NIN Validation, Personalization, and Identity Verification APIs into WordPress.
 * Version: 1.0.0
 * Author: RobostTech
 * Text Domain: robosttech-api
 */

if ( ! defined( 'ABSPATH' ) ) {
    exit; // Exit if accessed directly
}

define( 'ROBOSTTECH_PLUGIN_DIR', plugin_dir_path( __FILE__ ) );
define( 'ROBOSTTECH_PLUGIN_URL', plugin_dir_url( __FILE__ ) );

// Load classes
require_once ROBOSTTECH_PLUGIN_DIR . 'includes/class-admin-settings.php';
require_once ROBOSTTECH_PLUGIN_DIR . 'includes/class-api-client.php';
require_once ROBOSTTECH_PLUGIN_DIR . 'includes/class-rest-controller.php';

// Initialize Plugin
add_action( 'plugins_loaded', function() {
    RobostTech_Admin_Settings::get_instance();
    RobostTech_REST_Controller::get_instance();
} );
```

---

### 4.2 Admin Settings Page (`includes/class-admin-settings.php`)

This class creates an admin interface to securely store API Keys and Base URL in WordPress options.

```php
<?php
if ( ! defined( 'ABSPATH' ) ) exit;

class RobostTech_Admin_Settings {
    private static $instance = null;

    public static function get_instance() {
        if ( null === self::$instance ) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    private function __construct() {
        add_action( 'admin_menu', array( $this, 'add_settings_page' ) );
        add_action( 'admin_init', array( $this, 'register_settings' ) );
    }

    public function add_settings_page() {
        add_options_page(
            'RobostTech API Settings',
            'RobostTech API',
            'manage_options',
            'robosttech-settings',
            array( $this, 'render_settings_page' )
        );
    }

    public function register_settings() {
        register_setting( 'robosttech_options_group', 'robosttech_base_url' );
        register_setting( 'robosttech_options_group', 'robosttech_validation_api_key' );
        register_setting( 'robosttech_options_group', 'robosttech_nin_api_key' );
        register_setting( 'robosttech_options_group', 'robosttech_clearance_api_key' );
    }

    public function render_settings_page() {
        ?>
        <div class="wrap">
            <h1>RobostTech & IPE Clearance API Configuration</h1>
            <form method="post" action="options.php">
                <?php
                settings_fields( 'robosttech_options_group' );
                do_settings_sections( 'robosttech_options_group' );
                ?>
                <table class="form-table">
                    <tr>
                        <th scope="row">Base API URL</th>
                        <td><input type="text" name="robosttech_base_url" value="<?php echo esc_attr( get_option( 'robosttech_base_url', 'https://robosttech.com' ) ); ?>" class="regular-text" /></td>
                    </tr>
                    <tr>
                        <th scope="row">Validation / Personalization API Key</th>
                        <td><input type="password" name="robosttech_validation_api_key" value="<?php echo esc_attr( get_option( 'robosttech_validation_api_key' ) ); ?>" class="regular-text" /></td>
                    </tr>
                    <tr>
                        <th scope="row">NIN Search API Key</th>
                        <td><input type="password" name="robosttech_nin_api_key" value="<?php echo esc_attr( get_option( 'robosttech_nin_api_key' ) ); ?>" class="regular-text" /></td>
                    </tr>
                    <tr>
                        <th scope="row">IPE Clearance API Key</th>
                        <td><input type="password" name="robosttech_clearance_api_key" value="<?php echo esc_attr( get_option( 'robosttech_clearance_api_key' ) ); ?>" class="regular-text" /></td>
                    </tr>
                </table>
                <?php submit_button(); ?>
            </form>
        </div>
        <?php
    }
}
```

---

### 4.3 External API Client Class (`includes/class-api-client.php`)

Centralized HTTP client wrapper using `wp_remote_post()` to execute API calls safely.

```php
<?php
if ( ! defined( 'ABSPATH' ) ) exit;

class RobostTech_API_Client {

    /**
     * Sends HTTP POST request to external API endpoint.
     *
     * @param string $endpoint Target path e.g. '/api/validation'
     * @param array  $body     Payload array
     * @param string $api_key  API Key string
     * @return array|WP_Error  Parsed response body or WP_Error object
     */
    public static function request( $endpoint, $body = array(), $api_key = '' ) {
        $base_url = get_option( 'robosttech_base_url', 'https://robosttech.com' );
        $url      = rtrim( $base_url, '/' ) . '/' . ltrim( $endpoint, '/' );

        $headers = array(
            'Content-Type' => 'application/json',
            'api-key'      => $api_key,
        );

        $response = wp_remote_post( $url, array(
            'method'    => 'POST',
            'headers'   => $headers,
            'body'      => wp_json_encode( $body ),
            'timeout'   => 30,
            'sslverify' => true,
        ) );

        if ( is_wp_error( $response ) ) {
            return $response;
        }

        $code = wp_remote_retrieve_response_code( $response );
        $data = json_decode( wp_remote_retrieve_body( $response ), true );

        return array(
            'http_code' => $code,
            'data'      => $data,
        );
    }
}
```

---

### 4.4 REST API Controller Class (`includes/class-rest-controller.php`)

Registers the WordPress routes, validates input parameters, handles variants, and formats responses.

```php
<?php
if ( ! defined( 'ABSPATH' ) ) exit;

class RobostTech_REST_Controller {

    private static $instance = null;
    private $namespace = 'robosttech/v1';

    public static function get_instance() {
        if ( null === self::$instance ) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    private function __construct() {
        add_action( 'rest_api_init', array( $this, 'register_routes' ) );
    }

    public function register_routes() {
        // Service 1: Validation Flow
        register_rest_route( $this->namespace, '/validation/submit', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_validation_submit' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        register_rest_route( $this->namespace, '/validation/status', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_validation_status' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        // Service 2: Personalization Flow
        register_rest_route( $this->namespace, '/personalization/submit', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_personalization_submit' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        register_rest_route( $this->namespace, '/personalization/status', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_personalization_status' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        // Service 3: NIN Identity Search (Dynamic Variant Endpoint)
        register_rest_route( $this->namespace, '/nin/search/(?P<variant>[a-zA-Z0-9_-]+)', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_nin_search_variant' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        // Service 4: IPE Clearance Flow
        register_rest_route( $this->namespace, '/clearance/submit', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_clearance_submit' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );

        register_rest_route( $this->namespace, '/clearance/status', array(
            'methods'             => WP_REST_Server::CREATABLE,
            'callback'            => array( $this, 'handle_clearance_status' ),
            'permission_callback' => array( $this, 'check_permissions' ),
        ) );
    }

    public function check_permissions( $request ) {
        // Enforce nonce or cap checks for logged-in users or public forms
        return current_user_can( 'read' ) || wp_verify_nonce( $request->get_header( 'X-WP-Nonce' ), 'wp_rest' );
    }

    // ─── 1. VALIDATION HANDLERS ─────────────────────────────────────────

    public function handle_validation_submit( WP_REST_Request $request ) {
        $nin = sanitize_text_field( $request->get_param( 'nin' ) );
        if ( empty( $nin ) ) {
            return new WP_Error( 'missing_param', 'NIN is required.', array( 'status' => 400 ) );
        }

        $api_key  = get_option( 'robosttech_validation_api_key' );
        $response = RobostTech_API_Client::request( '/api/validation', array( 'nin' => $nin ), $api_key );

        return $this->format_rest_response( $response );
    }

    public function handle_validation_status( WP_REST_Request $request ) {
        $nin = sanitize_text_field( $request->get_param( 'nin' ) );
        $api_key  = get_option( 'robosttech_validation_api_key' );
        $response = RobostTech_API_Client::request( '/api/validation_status', array( 'nin' => $nin ), $api_key );

        return $this->format_rest_response( $response );
    }

    // ─── 2. PERSONALIZATION HANDLERS ───────────────────────────────────

    public function handle_personalization_submit( WP_REST_Request $request ) {
        $tracking_id = sanitize_text_field( $request->get_param( 'tracking_id' ) );
        $api_key     = get_option( 'robosttech_validation_api_key' );
        $response    = RobostTech_API_Client::request( '/api/personalization', array( 'tracking_id' => $tracking_id ), $api_key );

        return $this->format_rest_response( $response );
    }

    public function handle_personalization_status( WP_REST_Request $request ) {
        $tracking_id = sanitize_text_field( $request->get_param( 'tracking_id' ) );
        $api_key     = get_option( 'robosttech_validation_api_key' );
        $response    = RobostTech_API_Client::request( '/api/personalization_status', array( 'tracking_id' => $tracking_id ), $api_key );

        return $this->format_rest_response( $response );
    }

    // ─── 3. NIN SEARCH VARIANTS HANDLER ─────────────────────────────────

    public function handle_nin_search_variant( WP_REST_Request $request ) {
        $variant = $request->get_param( 'variant' );
        $api_key = get_option( 'robosttech_nin_api_key' );

        switch ( $variant ) {
            case 'number':
                $payload  = array( 'nin' => sanitize_text_field( $request->get_param( 'nin' ) ) );
                $endpoint = '/api/nin_verify';
                break;

            case 'phone':
                $payload  = array( 'phone' => sanitize_text_field( $request->get_param( 'phone' ) ) );
                $endpoint = '/api/nin_phone';
                break;

            case 'demo':
                $payload  = array(
                    'firstname'   => sanitize_text_field( $request->get_param( 'firstname' ) ),
                    'lastname'    => sanitize_text_field( $request->get_param( 'lastname' ) ),
                    'middlename'  => sanitize_text_field( $request->get_param( 'middlename' ) ),
                    'gender'      => sanitize_text_field( $request->get_param( 'gender' ) ),
                    'dateOfBirth' => sanitize_text_field( $request->get_param( 'dateOfBirth' ) ),
                );
                $endpoint = '/api/nin_demo';
                break;

            default:
                return new WP_Error( 'invalid_variant', 'Unsupported search variant.', array( 'status' => 400 ) );
        }

        $response = RobostTech_API_Client::request( $endpoint, $payload, $api_key );
        return $this->format_rest_response( $response );
    }

    // ─── 4. IPE CLEARANCE HANDLERS ──────────────────────────────────────

    public function handle_clearance_submit( WP_REST_Request $request ) {
        $tracking_id = sanitize_text_field( $request->get_param( 'tracking_id' ) );
        $api_key     = get_option( 'robosttech_clearance_api_key' );
        $response    = RobostTech_API_Client::request( '/api/clearance', array( 'tracking_id' => $tracking_id ), $api_key );

        return $this->format_rest_response( $response );
    }

    public function handle_clearance_status( WP_REST_Request $request ) {
        $tracking_id = sanitize_text_field( $request->get_param( 'tracking_id' ) );
        $api_key     = get_option( 'robosttech_clearance_api_key' );
        $response    = RobostTech_API_Client::request( '/api/clearance_status', array( 'tracking_id' => $tracking_id ), $api_key );

        return $this->format_rest_response( $response );
    }

    // ─── HELPER: RESPONSE FORMATTER ────────────────────────────────────

    private function format_rest_response( $result ) {
        if ( is_wp_error( $result ) ) {
            return $result;
        }

        $http_code = isset( $result['http_code'] ) ? $result['http_code'] : 200;
        $data      = isset( $result['data'] ) ? $result['data'] : array();

        return new WP_REST_Response( $data, $http_code );
    }
}
```

---

## 5. Handling Variants & Response Parsing

When communicating with the upstream clearance and verification services, your custom WordPress endpoints must handle a variety of success and error payload variants:

### Variant Parsing Logic Matrix

| Service Endpoint | Response Variant Case | Response Key Indicators | Business Logic Handling |
| :--- | :--- | :--- | :--- |
| **`/clearance/submit`** | *Success Submission* | `HTTP 200` | Return confirmation; trigger background status polling. |
| **`/clearance/submit`** | *Duplicate Record* | `HTTP 400`, `exist: true` | Inform user that tracking ID was previously submitted. |
| **`/clearance/submit`** | *Previous Failure* | `HTTP 400`, `message: "Previous Clearance Failed"` | Reject resubmission; direct user to support/refund channel. |
| **`/clearance/status`**| *Cleared Success* | `cleared: true`, `status: "completed"` | Retrieve clearance approval reply code (`reply`). |
| **`/clearance/status`**| *Failed / Refunded*| `not_cleared: true`, `status: "failed"` | Display refund message stored in `reply`. |
| **`/personalization/status`** | *Completed* | `personalized: true`, `data.photo` | Render profile details and decode Base64 image payload. |

---

## 6. Frontend & Client-Side Integration (JS Fetch Example)

Below is an example JavaScript snippet to submit a clearance request and poll for status updates from a WordPress frontend page or admin panel:

```javascript
/**
 * Submits an IPE Clearance Request and Polls Status
 */
async function processIpeClearance(trackingId) {
    const nonce = wpApiSettings.nonce; // Localized WP nonce

    try {
        // Step 1: Submit Clearance Request
        const submitResponse = await fetch('/wp-json/robosttech/v1/clearance/submit', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-WP-Nonce': nonce
            },
            body: JSON.stringify({ tracking_id: trackingId })
        });

        const submitResult = await submitResponse.json();

        if (!submitResponse.ok) {
            if (submitResult.exist) {
                console.warn('Duplicate entry detected:', submitResult.message);
            } else {
                console.error('Clearance request failed:', submitResult.message);
            }
            return submitResult;
        }

        console.log('Submission received. Starting status polling...');

        // Step 2: Poll Status until complete or failed
        return await pollClearanceStatus(trackingId, nonce);

    } catch (error) {
        console.error('Network or system error:', error);
    }
}

async function pollClearanceStatus(trackingId, nonce, retries = 5, delayMs = 3000) {
    for (let i = 0; i < retries; i++) {
        await new Promise(res => setTimeout(res, delayMs));

        const response = await fetch('/wp-json/robosttech/v1/clearance/status', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-WP-Nonce': nonce
            },
            body: JSON.stringify({ tracking_id: trackingId })
        });

        const result = await response.json();

        if (result.status === 'completed' && result.cleared) {
            console.log('Clearance Approved! Approval Code:', result.reply);
            return result;
        }

        if (result.status === 'failed' || result.not_cleared) {
            console.error('Clearance Failed/Refunded:', result.reply);
            return result;
        }

        console.log(`Polling... Attempt ${i + 1} of ${retries}`);
    }

    throw new Error('Clearance status check timed out.');
}
```

---

## 7. Security, Performance & Best Practices

1. **Security & Nonce Checks:**
   - Always validate `X-WP-Nonce` header or use `current_user_can()` capabilities inside the `permission_callback`.
   - Never expose hardcoded API keys in JavaScript files.

2. **Input Sanitization:**
   - Sanitize all parameters using `sanitize_text_field()` or specialized WordPress sanitization helpers.

3. **Caching & Transients API:**
   - For static identity checks (e.g. NIN search results), cache the result using `set_transient()` to minimize API unit costs:
   ```php
   $cache_key = 'nin_search_' . md5( $nin );
   $cached    = get_transient( $cache_key );

   if ( false !== $cached ) {
       return new WP_REST_Response( $cached, 200 );
   }

   // Perform API request...
   set_transient( $cache_key, $response['data'], HOUR_IN_SECONDS * 12 );
   ```

4. **Error Logging:**
   - Enable debugging and log API client exceptions using `error_log()` for auditing and troubleshooting.
