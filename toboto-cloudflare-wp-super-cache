<?php
/**
 * Plugin Name: Cloudflare Cache Purge for Super Cache
 * Description: Automatically purges Cloudflare cache when Super Cache is cleared
 * Version: 1.0.0
 * Author: traffband
 *
 * @package traffband-plugin
 */

if ( ! defined( 'ABSPATH' ) ) {
	exit;
}

/**
 * Main plugin class that handles automatic Cloudflare cache purging
 * when WordPress Super Cache is cleared
 *
 * @since 1.0.0
 */
class CloudflareSupercachePurger {
	/**
	 * Cloudflare account email address
	 *
	 * @var string
	 * @since 1.0.0
	 */
	private mixed $cf_email;

	/**
	 * Cloudflare API key
	 *
	 * @var string
	 * @since 1.0.0
	 */
	private mixed $cf_api_key;

	/**
	 * Cloudflare Zone ID
	 *
	 * @var string
	 * @since 1.0.0
	 */
	private mixed $cf_zone_id;

	/**
	 * Plugin slug used for menu pages and option naming
	 *
	 * @var string
	 * @since 1.0.0
	 */
	private string $plugin_slug = 'traffband-supercache-purger';

	/**
	 * Constructor - initializes the plugin and sets up hooks
	 *
	 * @since 1.0.0
	 */
	public function __construct() {
		$this->cf_email   = defined( 'CLOUDFLARE_EMAIL' ) ? CLOUDFLARE_EMAIL : get_option( 'traffband_cloudflare_supercache_email', '' );
		$this->cf_api_key = defined( 'CLOUDFLARE_API_KEY' ) ? CLOUDFLARE_API_KEY : get_option( 'traffband_cloudflare_supercache_api_key', '' );
		$this->cf_zone_id = defined( 'CLOUDFLARE_ZONE_ID' ) ? CLOUDFLARE_ZONE_ID : get_option( 'traffband_cloudflare_supercache_zone_id', '' );

		add_action( 'wp_cache_cleared', array( $this, 'purge_cloudflare_cache' ) );
		add_action( 'admin_menu', array( $this, 'add_admin_menu' ) );
		add_action( 'admin_init', array( $this, 'register_settings' ) );
		add_filter( 'plugin_action_links_' . plugin_basename( __FILE__ ), array( $this, 'add_settings_link' ) );
	}

	/**
	 * Adds settings page to WordPress admin menu
	 *
	 * @since 1.0.0
	 */
	public function add_admin_menu() {
		add_options_page(
			'Cloudflare Cache Purge for Super Cache',
			'Cloudflare Cache',
			'manage_options',
			$this->plugin_slug,
			array( $this, 'display_settings_page' )
		);
	}

	/**
	 * Adds settings link to plugins page
	 *
	 * @param array $links Existing plugin links
	 * @return array Modified plugin links
	 * @since 1.0.0
	 */
	public function add_settings_link( array $links ): array {
		$settings_link = '<a href="options-general.php?page=' . $this->plugin_slug . '">' . __( 'Settings' ) . '</a>';
		array_unshift( $links, $settings_link );
		return $links;
	}

	/**
	 * Registers plugin settings and fields
	 *
	 * @since 1.0.0
	 */
	public function register_settings() {
		register_setting(
			$this->plugin_slug . '-settings-group',
			'traffband_cloudflare_supercache_email',
			array( 'sanitize_callback' => 'sanitize_text_field' )
		);

		register_setting(
			$this->plugin_slug . '-settings-group',
			'traffband_cloudflare_supercache_api_key',
			array( 'sanitize_callback' => 'sanitize_text_field' )
		);

		register_setting(
			$this->plugin_slug . '-settings-group',
			'traffband_cloudflare_supercache_zone_id',
			array( 'sanitize_callback' => 'sanitize_text_field' )
		);

		add_settings_section(
			'traffband_cloudflare_supercache_main_section',
			'Cloudflare API Settings',
			array( $this, 'settings_section_callback' ),
			$this->plugin_slug
		);

		add_settings_field(
			'traffband_cloudflare_supercache_email',
			'Cloudflare Email',
			array( $this, 'email_field_callback' ),
			$this->plugin_slug,
			'traffband_cloudflare_supercache_main_section'
		);

		add_settings_field(
			'traffband_cloudflare_supercache_api_key',
			'Cloudflare API Key',
			array( $this, 'api_key_field_callback' ),
			$this->plugin_slug,
			'traffband_cloudflare_supercache_main_section'
		);

		add_settings_field(
			'traffband_cloudflare_supercache_zone_id',
			'Cloudflare Zone ID',
			array( $this, 'zone_id_field_callback' ),
			$this->plugin_slug,
			'traffband_cloudflare_supercache_main_section'
		);
	}

	/**
	 * Renders the settings section description
	 *
	 * @since 1.0.0
	 */
	public function settings_section_callback() {
		echo '<p>Enter your Cloudflare credentials to automatically purge Cloudflare cache when Super Cache is cleared.</p>';
	}

	/**
	 * Renders the email field
	 *
	 * @since 1.0.0
	 */
	public function email_field_callback() {
		$email = get_option( 'traffband_cloudflare_supercache_email', '' );
		echo '<input type="text" id="traffband_cloudflare_supercache_email" name="traffband_cloudflare_supercache_email" value="' . esc_attr( $email ) . '" class="regular-text">';
		echo '<p class="description">Your Cloudflare account email address</p>';
	}

	/**
	 * Renders the API key field
	 *
	 * @since 1.0.0
	 */
	public function api_key_field_callback(): void {
		$api_key = get_option( 'traffband_cloudflare_supercache_api_key', '' );
		echo '<input type="password" id="traffband_cloudflare_supercache_api_key" name="traffband_cloudflare_supercache_api_key" value="' . esc_attr( $api_key ) . '" class="regular-text">';
		echo '<p class="description">Your Global API key from Cloudflare</p>';
	}

	/**
	 * Renders the Zone ID field
	 *
	 * @since 1.0.0
	 */
	public function zone_id_field_callback() {
		$zone_id = get_option( 'traffband_cloudflare_supercache_zone_id', '' );
		echo '<input type="text" id="traffband_cloudflare_supercache_zone_id" name="traffband_cloudflare_supercache_zone_id" value="' . esc_attr( $zone_id ) . '" class="regular-text">';
		echo '<p class="description">You can find your Zone ID in the Cloudflare dashboard under Overview > API section.</p>';
	}

	/**
	 * Renders the plugin settings page
	 *
	 * @since 1.0.0
	 */
	public function display_settings_page() {
		if ( ! current_user_can( 'manage_options' ) ) {
			return;
		}

		$using_constants = defined( 'CLOUDFLARE_EMAIL' ) && defined( 'CLOUDFLARE_API_KEY' ) && defined( 'CLOUDFLARE_ZONE_ID' );

		?>
		<div class="wrap">
			<h1><?php echo esc_html( get_admin_page_title() ); ?></h1>

			<?php if ( $using_constants ) : ?>
				<div class="notice notice-warning">
					<p><strong>Note:</strong> You have defined Cloudflare credentials in your wp-config.php file. These settings below will be ignored.</p>
				</div>
			<?php endif; ?>

			<form method="post" action="options.php">
				<?php
				settings_fields( $this->plugin_slug . '-settings-group' );
				do_settings_sections( $this->plugin_slug );
				submit_button();
				?>
			</form>

			<div class="card">
				<h2>Manual Cache Purge</h2>
				<p>Click the button below to manually purge your Cloudflare cache:</p>
				<form method="post" action="">
					<?php wp_nonce_field( 'traffband_cloudflare_manual_purge', 'traffband_cloudflare_manual_purge_nonce' ); ?>
					<input type="hidden" name="traffband_cloudflare_manual_purge" value="1">
					<button type="submit" class="button button-primary">Purge Cloudflare Cache</button>
				</form>
			</div>

			<?php
			if ( isset( $_POST['traffband_cloudflare_manual_purge'] ) && check_admin_referer( 'traffband_cloudflare_manual_purge', 'traffband_cloudflare_manual_purge_nonce' ) ) {
				$purge_result = $this->purge_cloudflare_cache();
				if ( $purge_result ) {
					echo '<div class="notice notice-success is-dismissible"><p>Cloudflare cache purged successfully!</p></div>';
				} else {
					echo '<div class="notice notice-error is-dismissible"><p>Failed to purge Cloudflare cache. Please check your credentials and try again.</p></div>';
				}
			}
			?>
		</div>
		<?php
	}

	/**
	 * Purges the Cloudflare cache via API
	 *
	 * @return bool True on successful purge, false on failure
	 * @since 1.0.0
	 */
	public function purge_cloudflare_cache() {
		if ( empty( $this->cf_email ) ) {
			$this->cf_email = get_option( 'traffband_cloudflare_supercache_email', '' );
		}

		if ( empty( $this->cf_api_key ) ) {
			$this->cf_api_key = get_option( 'traffband_cloudflare_supercache_api_key', '' );
		}

		if ( empty( $this->cf_zone_id ) ) {
			$this->cf_zone_id = get_option( 'traffband_cloudflare_supercache_zone_id', '' );
		}

		if ( empty( $this->cf_email ) || empty( $this->cf_api_key ) || empty( $this->cf_zone_id ) ) {
			error_log( 'Cloudflare credentials not set. Cannot purge cache.' );
			return false;
		}

		$response = wp_remote_post(
			'https://api.cloudflare.com/client/v4/zones/' . $this->cf_zone_id . '/purge_cache',
			array(
				'method'  => 'POST',
				'timeout' => 30,
				'headers' => array(
					'X-Auth-Email' => $this->cf_email,
					'X-Auth-Key'   => $this->cf_api_key,
					'Content-Type' => 'application/json',
				),
				'body'    => json_encode(
					array(
						'purge_everything' => true,
					)
				),
			)
		);

		if ( is_wp_error( $response ) ) {
			error_log( 'Cloudflare cache purge error: ' . $response->get_error_message() );
			return false;
		}

		$response_body = json_decode( wp_remote_retrieve_body( $response ), true );

		if ( isset( $response_body['success'] ) && $response_body['success'] === true ) {
			error_log( 'Cloudflare cache successfully purged!' );
			return true;
		} else {
			$error_message = isset( $response_body['errors'][0]['message'] ) ? $response_body['errors'][0]['message'] : 'Unknown error';
			error_log( 'Cloudflare cache purge failed: ' . $error_message );
			return false;
		}
	}
}

$cloudflare_supercache_purger = new CloudflareSupercachePurger();
