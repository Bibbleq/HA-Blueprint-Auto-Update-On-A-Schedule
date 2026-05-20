# Home Assistant Auto-Update Blueprint

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2024.8.0+-blue.svg)](https://www.home-assistant.io/)
[![Blueprint](https://img.shields.io/badge/Blueprint-YAML-orange.svg)](https://www.home-assistant.io/docs/automation/using_blueprints/)

A powerful and safe Home Assistant blueprint that automatically updates Home Assistant Core, OS, add-ons, and integrations on a scheduled basis with intelligent safety features.

## 🎉 Latest Update - v26.05.0

**Strict CalVer and Metadata Cleanup:**
- **Versioning:** Standardized the current release to strict CalVer format `YY.MM.PATCH`
- **Current references:** Aligned the latest version shown in the README, blueprint, and changelog
- **Repository metadata:** Corrected current repository URLs used for imports, issues, and blueprint source metadata

**Note:** Older published version labels below are preserved as historical references.

## Previous Update - v2025.10.11

**Improved Backup Deduplication:**
- **Smart Timestamp Checking:** Enhanced backup creation logic to check actual backup timestamps
- **1-Hour Deduplication:** Prevents duplicate backups within 1 hour, regardless of source
- **Respects All Backups:** Works with backups created by any process, not just this automation
- **Simplified Architecture:** Removed resume-after-restart logic for cleaner, more reliable operation
- **Modern HA Support:** Uses `sensor.backup_last_successful_automatic_backup` for timestamp checking

**Migration Note:** If you previously configured `update_process_started_entity` helper, you can safely remove it from your automation configuration and delete the helper entity. The automation now operates in a stateless manner, re-evaluating all updates with full safeguards after any restart.

**Impact:** More reliable backup management with better deduplication and reduced storage usage.

## Previous Update - v2025.10.10

**Fixed Backup Age Check for Modern HA:**
- **Fixed Error:** Resolved "No backup entity with last_backup attribute found" error
- **Modern HA Support:** Now uses `sensor.backup_last_successful_automatic_backup` state as primary check
- **Backwards Compatible:** Falls back to legacy sensors with `last_backup` attribute
- **Improved Error Messages:** Clearer messaging when no backup entity is found

**Impact:** The backup age validation now works correctly with modern Home Assistant backup integration.

## Previous Update - v2025.10.9

**New Feature: AI-Powered Breaking Changes Analysis:**
- **Smart Analysis:** Use Home Assistant's conversation AI integration to analyze release notes
- **Environment-Aware:** Configure your environment context for personalized breaking change detection
- **Multi-Agent Support:** Works with any AI conversation agent (OpenAI, Google Generative AI, Anthropic, local LLMs, etc.)
- **Flexible Control:** Choose to skip updates on AI concern or just log the insights
- **Comprehensive Protection:** Works alongside keyword-based detection for layered safety

**Impact:** Get intelligent, context-aware analysis of release notes to identify breaking changes that may specifically affect your Home Assistant setup.

## Previous Update - v2025.10.8

**Enhanced Backup Detection:**
- **Event-Driven Monitoring:** Switched from polling to event-driven backup detection for faster and more reliable completion detection
- **New Backup Service:** Using `backup.create_automatic` instead of `hassio.backup_full` for improved compatibility with HA's native backup system
- **Dual Trigger System:**
  - Primary: Monitors `sensor.backup_backup_manager_state` transition (create_backup → idle)
  - Fallback: Monitors `sensor.backup_last_successful_automatic_backup` updates
- **Better Diagnostics:** Logs backup duration and detection method used
- **No More Polling Delays:** Instant detection when backup completes, no unnecessary waiting
- **Removed:** `backup_location` input (now uses HA's configured backup location)

**Impact:** Backups are detected as soon as they complete, reducing automation run time and eliminating false timeout warnings. See changelog for full details.

## ⚠️ Important Notice

- **Home Assistant may restart automatically** as part of the update process
- **Use at your own risk!** Please review Home Assistant community discussions about the risks involved in auto-updating production systems
- **Ensure you have reliable backups** before enabling automatic updates
- Auto-updates in production environments may lead to temporary system instability
- Updates may affect connected devices and integrations

## 🌟 Features

### Safety & Control Features

- **🧪 What-If Mode (Dry Run)**
  - Test the automation safely without making any changes
  - Shows what updates would be installed and what would be skipped
  - All notifications and logging work normally
  - No backups are created, no updates installed, no restarts performed
  - Perfect for testing automation configuration before using it in production

- **🛡️ Breaking Changes Protection** (Default: Enabled)
  - Automatically detects and skips updates containing breaking changes
  - Searches both release summary and release notes for breaking change indicators
  - Case-insensitive detection of multiple variations: "breaking change", "breaking-change", "breaking changes"
  - Can be disabled for users who want all updates applied

- **🤖 AI-Powered Breaking Changes Analysis** (Optional)
  - Uses Home Assistant's conversation AI integration to analyze release notes
  - Provides intelligent detection of breaking changes that may affect your environment
  - Supports any AI conversation agent (OpenAI, Google Generative AI, Anthropic, local LLMs, etc.)
  - Customizable environment context for more accurate analysis
  - Option to skip updates when AI flags concerns or just log AI insights
  - **AI query and response logged to System Log for review**
  - Works alongside keyword-based detection for comprehensive protection

- **💾 Automatic Backups**
  - Creates backups before applying updates (enabled by default)
  - Event-driven monitoring for instant completion detection
  - Uses Home Assistant's native automatic backup service
  - Dual trigger system (state transition + fallback)
  - Logs backup duration and detection method
  - Timestamp-based deduplication (no helper entity needed)

- **👤 Person Presence Check**
  - Only run updates when specific person is home
  - Prevents updates during absence
  - Sends notifications when updates are skipped due to absence

- **⏸️ Flexible Pause Control**
  - Pause updates using multiple methods:
    - Individual pause entities
    - Helper entity state
    - Person presence requirement

### Update Management

- **📦 Comprehensive Update Coverage**
  - Home Assistant Core updates
  - Home Assistant OS updates
  - Add-on updates (HACS and built-in)
  - Integration updates
  - Firmware updates

- **🎯 Smart Update Control**
  - Update exclusions (skip specific entities)
  - Type-based filtering (skip specific update types)
  - Schedule-based execution
  - Priority ordering (generic → firmware → core → OS)
  - All safeguards applied consistently (version mode, AI analysis, breaking change detection)

### Backup Management

- **🔄 Smart Backup Deduplication**
  - Automatically prevents duplicate backups within 1 hour
  - Respects backups created by ANY process (not just this automation)
  - Uses `sensor.backup_last_successful_automatic_backup` for modern HA
  - Timestamp-based deduplication ensures reliability

### Notification & Logging

- **📱 Mobile App Notifications**
  - Person not home notifications
  - Pre-reboot warnings (15 seconds before reboot)
  - Update completion summary with:
    - List of applied updates
    - List of skipped updates (breaking changes)
    - Reboot status

- **📨 Telegram Integration**
  - Full Telegram notification support
  - Customizable messages
  - Same notification points as mobile app

- **📝 Logbook Integration**
  - Detailed logging of all update activities
  - Track applied and skipped updates
  - Visibility in Home Assistant's logbook

## 📥 Installation

### Prerequisites

- Home Assistant 2024.8.0 or newer
- (Optional) Helper entity for update process tracking
- (Optional) Mobile app or Telegram for notifications

### Import Blueprint

1. **Via Home Assistant UI:**
   - Navigate to Settings → Automations & Scenes → Blueprints
   - Click the "Import Blueprint" button
   - Enter the blueprint URL: `https://github.com/Bibbleq/HA-Blueprint-Auto-Update-On-A-Schedule/blob/main/auto_update_scheduled.yaml`

2. **Via URL:**
   ```
   https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/Bibbleq/HA-Blueprint-Auto-Update-On-A-Schedule/blob/main/auto_update_scheduled.yaml
   ```

## ⚙️ Configuration

### Basic Configuration

Minimal setup for safe automatic updates:

```yaml
# The blueprint will use these defaults:
# - skip_breaking_changes: true (SAFE)
# - backup_bool: true (SAFE)
# - No person presence check
# - No update exclusions
```

### Advanced Configuration Example

```yaml
# Schedule
schedule_entity: schedule.updates_schedule

# Safety Features
whatif_mode: false                    # Dry-run mode for testing (default: false)
skip_breaking_changes: true           # Skip updates with breaking changes (default)
backup_bool: true                     # Create backup before updates (default)
person_home_entity: person.john_doe   # Only update when John is home

# AI Analysis (optional)
ai_analysis_enabled: true             # Enable AI-powered breaking changes analysis
ai_conversation_entity: conversation.openai  # Your AI conversation agent
ai_environment_context: "I use Z-Wave JS, Zigbee2MQTT, and HACS. Critical automations depend on climate integration and Google Home."
ai_skip_on_concern: true              # Skip updates when AI flags concerns

# Update Control
update_exclusions:
  - update.hacs_excluded_integration
  - update.sensitive_addon
  
update_types_exclusions:
  - device_update                     # Skip firmware updates

# Pause Control (optional)
pause_entities:
  - input_boolean.pause_updates
  - binary_sensor.maintenance_mode

# Mobile Notifications
notification_mobile_enable: true
notification_mobile_device: mobile_app_my_phone

# Telegram Notifications (optional)
notification_telegram: true
notification_telegram_bot_token: !secret telegram_bot_token
notification_telegram_chat_id: !secret telegram_chat_id
```

## 🚀 Usage Examples

### Example 1: Maximum Safety (Recommended for Production)

```yaml
skip_breaking_changes: true
backup_bool: true
person_home_entity: person.admin
notification_mobile_enable: true
notification_mobile_device: mobile_app_admin_phone
```

**Benefits:**
- ✅ Updates only when admin is home
- ✅ Breaking changes automatically skipped
- ✅ Automatic backups before updates
- ✅ Mobile notifications for awareness

### Example 2: Aggressive Updates (Development/Testing)

```yaml
skip_breaking_changes: false
backup_bool: true
auto_reboot: true
```

**Use Case:**
- Development/testing environments
- Want all updates immediately
- Still maintains backup safety net

### Example 3: Selective Updates with Exclusions

```yaml
skip_breaking_changes: true
backup_bool: true
update_exclusions:
  - update.custom_integration_a
  - update.experimental_addon
update_types_exclusions:
  - device_update
```

**Use Case:**
- Skip specific problematic integrations
- Exclude firmware updates (manual control preferred)
- Still get core and addon updates automatically

### Example 4: What-If Mode (Testing)

```yaml
whatif_mode: true
skip_breaking_changes: true
backup_bool: true
notification_mobile_enable: true
notification_mobile_device: mobile_app_my_phone
```

**Benefits:**
- ✅ Test automation without making any changes
- ✅ See what updates would be applied
- ✅ See what would be skipped due to breaking changes
- ✅ Receive notifications showing planned actions
- ✅ No backups created, no updates installed, no restarts
- ✅ Perfect for validating configuration before production use

**Use Case:**
- Testing new automation configuration
- Validating exclusion rules
- Checking which updates are pending
- Safe exploration of update behavior

### Example 5: AI-Powered Smart Updates

```yaml
skip_breaking_changes: true
backup_bool: true
ai_analysis_enabled: true
ai_conversation_entity: conversation.google_generative_ai
ai_environment_context: |
  My Home Assistant setup includes:
  - Z-Wave JS for smart locks and sensors
  - Zigbee2MQTT for Philips Hue and Ikea devices
  - HACS with custom components: browser_mod, mushroom cards
  - Critical automations: security system, thermostat control
  - Integrations: Google Home, Alexa, Spotify
ai_skip_on_concern: true
notification_mobile_enable: true
notification_mobile_device: mobile_app_my_phone
```

**Benefits:**
- ✅ AI analyzes release notes for breaking changes relevant to your setup
- ✅ Context-aware decisions based on your specific integrations
- ✅ Smarter than keyword-only detection
- ✅ Works with any HA conversation AI agent
- ✅ Combined with keyword detection for comprehensive protection

**Use Case:**
- Production environments with complex setups
- Users who want intelligent update filtering
- Environments where certain integrations are critical

## 📋 Version History

For a complete version history, see [CHANGELOG.md](CHANGELOG.md) in the repository root.

### v26.05.0 (Current)

**✅ Strict CalVer and Metadata Cleanup:**
- Standardized the current release to strict Calendar Versioning format (`YY.MM.PATCH`)
- Aligned current version references across the README, blueprint, and changelog
- Corrected active repository URLs for blueprint imports and support links

**Note:** Older published version labels below are preserved as historical references.

### v2025.10.11

**🔧 Improved Backup Deduplication:**
- Enhanced backup creation logic to check actual backup timestamps
- Prevents duplicate backups within 1 hour, regardless of source
- Respects backups created by any process, not just this automation
- Helper entity now truly optional (only used for resume-after-restart detection)
- Uses `sensor.backup_last_successful_automatic_backup` for timestamp checking

**Impact:**
- ✅ More reliable backup management with better deduplication
- ✅ Reduced storage usage

### v2025.10.10

**🔧 Fixed Backup Age Check for Modern HA:**
- Fixed "No backup entity with last_backup attribute found" error
- Now uses `sensor.backup_last_successful_automatic_backup` state as primary check
- Falls back to legacy sensors with `last_backup` attribute for backwards compatibility
- Improved error message clarity when no backup entity is found

**Impact:**
- ✅ Backup age validation works with modern Home Assistant backup integration
- ✅ Backwards compatible with older backup sensor formats
- ✅ Clearer error messages for troubleshooting

### v2025.10.9

**🤖 New Feature: AI-Powered Breaking Changes Analysis:**
- Use Home Assistant's conversation AI integration to analyze release notes
- Intelligent detection of breaking changes that may affect your specific environment
- Supports any AI conversation agent (OpenAI, Google Generative AI, Anthropic, local LLMs, etc.)
- Customizable environment context for more accurate analysis
- Option to skip updates when AI flags concerns or just log AI insights
- Works alongside keyword-based detection for comprehensive protection

**Configuration Options:**
- `ai_analysis_enabled`: Enable/disable AI analysis (default: false)
- `ai_conversation_entity`: Select your AI conversation agent
- `ai_environment_context`: Describe your HA environment for context-aware analysis
- `ai_skip_on_concern`: Skip updates when AI identifies concerns (default: true)

**Impact:**
- ✅ Smarter breaking change detection than keyword matching alone
- ✅ Context-aware decisions based on your specific integrations
- ✅ Reduces false positives from generic breaking change keywords
- ✅ Flexible control over AI-driven skipping behavior

### v2025.10.8

**✨ Enhanced Backup Detection:**
- Switched from `hassio.backup_full` to `backup.create_automatic` for improved reliability
- Replaced polling-based monitoring with event-driven triggers
- Primary trigger: `sensor.backup_backup_manager_state` (create_backup → idle)
- Fallback trigger: `sensor.backup_last_successful_automatic_backup` updates
- Added backup duration tracking and detection method logging
- Removed polling delays for instant completion detection
- Removed `backup_location` input (uses HA's configured backup location)

**Impact:**
- ✅ Faster backup completion detection (no polling delays)
- ✅ More reliable monitoring with dual trigger system
- ✅ Better diagnostics with duration and method logging
- ✅ Reduced false timeout warnings
- ✅ Improved compatibility with HA's native backup system

### v2025.10.7

**🐛 Critical Bug Fixes:**
- Fixed What-If mode still creating actual backups (now properly skips backup creation)
- Enhanced backup completion monitoring with fallback detection methods (idle state check)
- Added `[WHAT-IF MODE]` prefix to all notifications for better clarity
- Improved backup timeout messages with more detail

**Impact:**
- ✅ What-If mode now works as a true dry run without creating backups
- ✅ Backup monitoring more reliable with reduced false timeouts
- ✅ All notifications clearly indicate What-If mode status
- ✅ Better logging when backup times out

See [CHANGELOG_v2025.10.7.md](changelogs/CHANGELOG_v2025.10.7.md) for detailed technical information.

### v2025.10.5

**🐛 Critical Bug Fixes:**
- Fixed infinite loop in What-If mode that caused automation to repeat indefinitely
- Fixed updates still being installed despite What-If mode being enabled
- Added `processed_updates` tracking to normal mode success and timeout paths
- Wrapped "Update - Remaining" section in What-If mode conditional

**Impact:**
- ✅ What-If mode now works correctly - no infinite loops
- ✅ No updates are installed in What-If mode (true dry run)
- ✅ All update modes now consistently track processed entities

See [CHANGELOG_v2025.10.5.md](changelogs/CHANGELOG_v2025.10.5.md) for detailed technical information.

### v2025.10.4

**✨ New Feature:**
- Added What-If Mode (dry run) for safe automation testing
- Preview updates without making any changes
- See what would be installed and what would be skipped
- All notifications work normally with "[WHAT-IF]" prefix
- No backups, updates, or restarts performed in What-If mode

**📚 Documentation:**
- Added comprehensive What-If Mode documentation
- New usage example for What-If Mode testing
- Added test scenario for What-If Mode
- Updated tips and best practices

### v2025.10.3

**🔴 Critical Fixes:**
- Fixed breaking changes detection not working correctly
- Fixed backup creation when helper entity not configured

**🔴 Breaking Changes:**
- `skip_breaking_changes` now defaults to `true` (enabled) for safety
- **Migration Required:** Set `skip_breaking_changes: false` if you want all updates

**🐛 Bug Fixes:**
- Corrected entity checking logic (treated as string instead of list)
- Fixed backup condition to work without helper entity
- Improved OR logic for backup creation

**📝 Technical Details:**
- Restructured breaking changes check to avoid nested sequences
- Removed problematic `stop` command
- Added conditional wrappers around update steps
- Fixed 5 locations where entity checking was incorrect

### v2025.10.2

**Features:**
- Added person presence checking
- Added breaking changes filter (initial implementation)
- Added mobile app notifications
- Improved backup process compatibility

## 🔄 Migration Guides

### Upgrading to v2025.10.3

#### If You Want the New Safe Defaults ✅

**No action required!** The new defaults are:
- ✅ Backups are created before updates
- ✅ Breaking changes are skipped automatically

#### If You Want All Updates Applied (Including Breaking Changes)

Add this to your automation configuration:
```yaml
skip_breaking_changes: false
```

### From Earlier Versions

All changes maintain backward compatibility except for the `skip_breaking_changes` default value change. Existing automations will continue to work but may start skipping updates with breaking changes.

## 🧪 Testing Scenarios

### Scenario 1: Breaking Changes Detection
**Setup:**
- `backup_bool`: true
- `skip_breaking_changes`: true

**Expected:**
- ✅ Backup is created (if no recent backup)
- ✅ Update with breaking changes is skipped
- ✅ Notification sent about skipped update

### Scenario 2: Normal Updates
**Setup:**
- `backup_bool`: true
- Update available without breaking changes

**Expected:**
- ✅ Backup is created (if no recent backup)
- ✅ Update is applied normally

### Scenario 3: Person Not Home
**Setup:**
- `person_home_entity`: Configured
- Person is away

**Expected:**
- ✅ Updates are skipped
- ✅ Mobile notification sent (if enabled)
- ✅ Automation exits gracefully

### Scenario 4: What-If Mode Testing
**Setup:**
- `whatif_mode`: true
- `backup_bool`: true
- `skip_breaking_changes`: true
- Multiple updates available (some with breaking changes)
- Mobile notifications enabled

**Expected:**
- ✅ No backup is created (but log shows it would be)
- ✅ No updates are installed (but log shows what would be)
- ✅ Updates with breaking changes shown as "would be skipped"
- ✅ Regular updates shown as "would be updated"
- ✅ No system restart occurs (but log shows it would if needed)
- ✅ Notifications show "[WHAT-IF]" prefix
- ✅ Summary shows what would happen without making changes

### Scenario 5: AI Analysis Testing
**Setup:**
- `ai_analysis_enabled`: true
- `ai_conversation_entity`: Configured conversation agent
- `ai_environment_context`: "I use Z-Wave JS and climate integration"
- `ai_skip_on_concern`: true
- Update available with release notes mentioning Z-Wave changes

**Expected:**
- ✅ AI analyzes release notes for each pending update
- ✅ Log shows "Analyzing release notes with AI for..."
- ✅ If AI responds with "CONCERN", update is skipped
- ✅ Log shows reason provided by AI
- ✅ If AI responds with "SAFE", update proceeds normally
- ✅ Keyword-based detection still works alongside AI

For more detailed test scenarios and debugging guides, see [TESTING.md](docs/TESTING.md).

## 🤝 Contributing

This is a fork/modification of the original blueprint by [edwardtfn](https://github.com/edwardtfn). 

### Original Repository
- Original Author: Edward Firmo
- Original Repository: https://github.com/edwardtfn/ha_auto_update_scheduled
- Community Discussion: https://community.home-assistant.io/t/459281

### This Fork
- Repository: https://github.com/Bibbleq/HA-Blueprint-Auto-Update-On-A-Schedule
- Issues & Feature Requests: https://github.com/Bibbleq/HA-Blueprint-Auto-Update-On-A-Schedule/issues

## 📚 Additional Documentation

- [docs/](docs/) - Additional documentation files
- [changelogs/](changelogs/) - Version changelogs and release notes

## 💡 Tips & Best Practices

1. **Start Conservative:** Use the default safe settings initially
2. **Test First:** Use What-If mode (`whatif_mode: true`) to safely test your configuration before production
3. **Validate Configuration:** Run the automation in What-If mode to see what would be updated and skipped
4. **Enable Notifications:** Stay informed about update activities
5. **Review Logs:** Check Home Assistant logbook after automation runs
6. **Have Backups:** Always maintain external backups beyond the automation's built-in backup
7. **Monitor Breaking Changes:** Review what updates were skipped and apply manually when ready
8. **Schedule Wisely:** Choose low-usage time windows for updates

## ⚠️ Known Limitations

- Breaking changes detection relies on keywords in release notes
- Some updates may not include proper release notes
- Network interruptions during updates can cause issues
- Restart timing may vary depending on system performance
- Mobile notifications sent 15 seconds before reboot may not always deliver in time on slower networks
- AI Analysis requires a configured conversation agent in Home Assistant
- AI Analysis quality depends on the AI model and the quality of release notes
- AI responses may vary; conservative settings are recommended for production

## 📞 Support & Discussion

- **Issues:** [GitHub Issues](https://github.com/Bibbleq/HA-Blueprint-Auto-Update-On-A-Schedule/issues)
- **Original Discussion:** [Home Assistant Community Forum](https://community.home-assistant.io/t/459281)
- **Original Repository Issues:** [edwardtfn/ha_auto_update_scheduled](https://github.com/edwardtfn/ha_auto_update_scheduled/issues)

## 📄 License

This blueprint maintains compatibility with the original project's licensing terms. Please refer to the original repository for specific license information.

## ☕ Support the Original Author

If you find this blueprint useful, consider supporting the original author:
- [Buy Edward a Coffee](https://buymeacoffee.com/edwardfirmo)

---

**Last Updated:** May 2026 (v26.05.0)
