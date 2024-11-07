# Set minimum iOS platform version to 17.0 to satisfy all dependencies
platform :ios, '17.0'

# Use modular headers instead of frameworks for better React Native compatibility
use_modular_headers!

# Resolve react_native_pods.rb with node to allow for hoisting
require Pod::Executable.execute_command('node', ['-p',
  'require.resolve(
    "react-native/scripts/react_native_pods.rb",
    {paths: [process.argv[1]]},
  )', __dir__]).strip

prepare_react_native_project!

target 'NeocomEnhancedApp' do
  # Load the native modules configuration
  config = use_native_modules!

  # Use React Native's automated setup
  use_react_native!(
    :path => config[:reactNativePath],
    :hermes_enabled => true,  # Enable Hermes explicitly
    # Absolute path to the application root
    :app_path => "#{Pod::Config.instance.installation_root}/.."
  )

  # Only add dependencies if they are not automatically added
  # Avoid adding 'React-Core' or related dependencies to prevent conflicts

  # Testing target configuration
  target 'NeocomEnhancedAppTests' do
    inherit! :complete
    # Pods for testing can be added here if needed
  end

  # Post-install script configuration
  post_install do |installer|
    # Run React Native's post-install script with Catalyst disabled
    react_native_post_install(
      installer,
      config[:reactNativePath],
      :mac_catalyst_enabled => false  # Disable Catalyst support for iOS-only projects
    )

    # Workaround for Xcode 14+ by excluding arm64 for simulators on Apple Silicon Macs
    installer.pods_project.build_configurations.each do |config|
      config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'arm64'
    end
  end
end
