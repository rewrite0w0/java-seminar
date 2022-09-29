# 예제

- https://github.com/threema-ch/threema-android/blob/557b69f33dd1db96f58e41f6602e32522470f53e/app/src/main/java/ch/threema/app/activities/MainActivity.java

```java
// MainActivity.java

// https://github.com/threema-ch/threema-android/tree/557b69f33dd1db96f58e41f6602e32522470f53e/app/src/main/java/ch/threema/app/activities

// 해당 폴더가 패키지이름
// threema-android/app/src/main/java/ch/threema/app/activities/
// 에서
// /ch/threema/app/activities/ 부분이 패키지 이름이 됨(현재 mainAcitiviy.java 위치이기도 함)

package ch.threema.app.activities;



// nodejs의 import 처럼 필요한 기능을 불러옴
// 안드로이드 api

import android.content.Intent;
// https://developer.android.com/reference/android/content/Intent

import android.os.Bundle;
// https://developer.android.com/reference/android/os/Bundle

import ch.threema.app.R;
// https://developer.android.com/reference/android/R
// 이 또한 안드로이드 api
// 왜 해당 저장소에 있는지는 사실 의문
// node_modules 처럼 ignore된 상태인 것으로 추정




public class MainActivity extends ThreemaAppCompatActivity {
// MainActivity은 추상 클래스 ThreemaAppCompatActivity를 계승(자식클래스가 됨)

// ThreemaAppCompatActivity은 AppCompatActivity 계승
// https://github.com/threema-ch/threema-android/blob/fe9bf2d286451fc824a061a6e2240c8d468fa033/app/src/main/java/ch/threema/app/activities/ThreemaAppCompatActivity.java

// AppCompatActivity은 안드로이드 API
// https://developer.android.com/reference/androidx/appcompat/app/AppCompatActivity



	public MainActivity() {}
  // 일부 디바이스에서 클래스에 인수가 없는 컨스트럭터 회피하기 위함


	@Override
  // 오버라이드 선언
  // onCreate를 새로히 선언할 수 있도록

  protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
    // 기본구조
    // 1. 화면 변경시
    // 2. 메모리 부족에 의해 프로세스 kill 되었을 시
      // 등의 상황에서 savedInstanceState가 문제 상태 당시 인스턴스 상황에 따라 상황에서 복구화면을 만듬
    // 그리고 문제 상황의 상태를 저장하는 것이 onSaveInstanceState


		Intent intent = new Intent(this, HomeActivity.class);
    // https://github.com/threema-ch/threema-android/blob/e360a693e3355ac93aafc8cb388543ab08d157b1/app/src/main/java/ch/threema/app/activities/HomeActivity.java
    // 화면 전환(액티비티 전환)에 사용
		startActivity(intent);
		overridePendingTransition(R.anim.abc_fade_in, R.anim.abc_fade_out);
		finish();
	}
}
```

- https://github.com/threema-ch/threema-android/blob/fe9bf2d286451fc824a061a6e2240c8d468fa033/app/src/main/java/ch/threema/app/activities/ThreemaAppCompatActivity.java

```java
// ThreemaAppCompatActivity.java
package ch.threema.app.activities;

import android.content.res.Configuration;
import android.widget.Toast;

import org.slf4j.Logger;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import ch.threema.app.R;
import ch.threema.app.ThreemaApplication;
import ch.threema.app.backuprestore.csv.BackupService;
import ch.threema.app.backuprestore.csv.RestoreService;
import ch.threema.app.exceptions.FileSystemNotPresentException;
import ch.threema.app.managers.ServiceManager;
import ch.threema.app.utils.ConfigUtils;
import ch.threema.base.utils.LoggingUtil;

// https://developer.android.com/reference/androidx/appcompat/app/AppCompatActivity

public abstract class ThreemaAppCompatActivity extends AppCompatActivity {
  // 로깅 안드로이드 api
  // https://developer.android.com/reference/java/util/logging/Logger
	private static final Logger logger = LoggingUtil.getThreemaLogger("ThreemaAppCompatActivity");

	@Override
	protected void onResume() {
		if (BackupService.isRunning() || RestoreService.isRunning()) {
			Toast.makeText(this,  R.string.backup_restore_in_progress, Toast.LENGTH_LONG).show();
			finish();
		} else {
			if (ConfigUtils.refreshDeviceTheme(this)) {
				ConfigUtils.recreateActivity(this);

				// Reset avatar cache on theme change so that the default avatars are loaded with the correct (themed) color
				ServiceManager sm = ThreemaApplication.getServiceManager();
				if (sm != null) {
					try {
						sm.getAvatarCacheService().clear();
					} catch (FileSystemNotPresentException e) {
						logger.error("Couldn't get avatar cache service to reset cached avatars", e);
					}
				}
			}
		}
		try {
			super.onResume();
		} catch (IllegalArgumentException ignored) {}
	}


	@Override
	public void onConfigurationChanged(@NonNull Configuration newConfig) {
		super.onConfigurationChanged(newConfig);
	}
}
```

- https://github.com/threema-ch/threema-android/blob/e360a693e3355ac93aafc8cb388543ab08d157b1/app/src/main/java/ch/threema/app/activities/HomeActivity.java

```java
// HomeActivity.java
package ch.threema.app.activities;

import android.annotation.SuppressLint;
import android.app.Activity;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.graphics.Bitmap;
import android.graphics.PorterDuff;
import android.graphics.drawable.BitmapDrawable;
import android.graphics.drawable.Drawable;
import android.net.ConnectivityManager;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Bundle;
import android.os.Handler;
import android.text.format.DateUtils;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Chronometer;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.Toast;

import com.google.android.material.badge.BadgeDrawable;
import com.google.android.material.bottomnavigation.BottomNavigationView;

import org.slf4j.Logger;

import java.io.File;
import java.lang.ref.WeakReference;
import java.net.InetSocketAddress;
import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.List;
import java.util.Objects;
import java.util.concurrent.RejectedExecutionException;

import androidx.annotation.AnyThread;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.annotation.UiThread;
import androidx.appcompat.app.ActionBar;
import androidx.appcompat.widget.AppCompatImageView;
import androidx.appcompat.widget.Toolbar;
import androidx.fragment.app.Fragment;
import androidx.fragment.app.FragmentTransaction;
import androidx.lifecycle.LifecycleOwner;
import androidx.localbroadcastmanager.content.LocalBroadcastManager;
import ch.threema.app.BuildConfig;
import ch.threema.app.BuildFlavor;
import ch.threema.app.R;
import ch.threema.app.ThreemaApplication;
import ch.threema.app.activities.wizard.WizardBaseActivity;
import ch.threema.app.activities.wizard.WizardStartActivity;
import ch.threema.app.archive.ArchiveActivity;
import ch.threema.app.dialogs.GenericAlertDialog;
import ch.threema.app.dialogs.GenericProgressDialog;
import ch.threema.app.dialogs.SMSVerificationDialog;
import ch.threema.app.dialogs.ShowOnceDialog;
import ch.threema.app.dialogs.SimpleStringAlertDialog;
import ch.threema.app.exceptions.EntryAlreadyExistsException;
import ch.threema.app.exceptions.FileSystemNotPresentException;
import ch.threema.app.fragments.ContactsSectionFragment;
import ch.threema.app.fragments.MessageSectionFragment;
import ch.threema.app.fragments.MyIDFragment;
import ch.threema.app.globalsearch.GlobalSearchActivity;
import ch.threema.app.grouplinks.OutgoingGroupRequestActivity;
import ch.threema.app.listeners.AppIconListener;
import ch.threema.app.listeners.ContactCountListener;
import ch.threema.app.listeners.ConversationListener;
import ch.threema.app.listeners.MessageListener;
import ch.threema.app.listeners.ProfileListener;
import ch.threema.app.listeners.SMSVerificationListener;
import ch.threema.app.listeners.VoipCallListener;
import ch.threema.app.managers.ListenerManager;
import ch.threema.app.managers.ServiceManager;
import ch.threema.app.messagereceiver.MessageReceiver;
import ch.threema.app.preference.SettingsActivity;
import ch.threema.app.push.PushService;
import ch.threema.app.qrscanner.activity.BaseQrScannerActivity;
import ch.threema.app.routines.CheckLicenseRoutine;
import ch.threema.app.services.ContactService;
import ch.threema.app.services.ConversationService;
import ch.threema.app.services.ConversationTagService;
import ch.threema.app.services.DeviceService;
import ch.threema.app.services.FileService;
import ch.threema.app.services.LockAppService;
import ch.threema.app.services.MessageService;
import ch.threema.app.services.NotificationService;
import ch.threema.app.services.PassphraseService;
import ch.threema.app.services.PreferenceService;
import ch.threema.app.services.ThreemaPushService;
import ch.threema.app.services.UpdateSystemService;
import ch.threema.app.services.UserService;
import ch.threema.app.services.license.LicenseService;
import ch.threema.app.threemasafe.ThreemaSafeMDMConfig;
import ch.threema.app.threemasafe.ThreemaSafeService;
import ch.threema.app.ui.IdentityPopup;
import ch.threema.app.utils.AnimationUtil;
import ch.threema.app.utils.AppRestrictionUtil;
import ch.threema.app.utils.BitmapUtil;
import ch.threema.app.utils.ConfigUtils;
import ch.threema.app.utils.ConnectionIndicatorUtil;
import ch.threema.app.utils.DialogUtil;
import ch.threema.app.utils.IntentDataUtil;
import ch.threema.app.utils.RuntimeUtil;
import ch.threema.app.utils.StateBitmapUtil;
import ch.threema.app.utils.TestUtil;
import ch.threema.app.voip.activities.CallActivity;
import ch.threema.app.voip.services.VoipCallService;
import ch.threema.app.webclient.activities.SessionsActivity;
import ch.threema.base.utils.LoggingUtil;
import ch.threema.domain.protocol.api.LinkMobileNoException;
import ch.threema.domain.protocol.csp.connection.ConnectionState;
import ch.threema.domain.protocol.csp.connection.ConnectionStateListener;
import ch.threema.domain.protocol.csp.connection.ThreemaConnection;
import ch.threema.localcrypto.MasterKey;
import ch.threema.storage.models.AbstractMessageModel;
import ch.threema.storage.models.ContactModel;
import ch.threema.storage.models.ConversationModel;

import static ch.threema.app.services.ConversationTagServiceImpl.FIXED_TAG_UNREAD;
import static ch.threema.app.voip.services.VoipCallService.ACTION_HANGUP;
import static ch.threema.app.voip.services.VoipCallService.EXTRA_ACTIVITY_MODE;
import static ch.threema.app.voip.services.VoipCallService.EXTRA_CONTACT_IDENTITY;
import static ch.threema.app.voip.services.VoipCallService.EXTRA_START_TIME;

// implements: 계승과 유사한 키워드
// `,`를 통해 복수 지정 가능
// 추상클래스와 같이 인터페이스는 구현한 클래스에 추상 메서드 모두 오버라이드 필수

public class HomeActivity extends ThreemaAppCompatActivity implements
	SMSVerificationDialog.SMSVerificationDialogCallback,
	GenericAlertDialog.DialogClickListener,
	LifecycleOwner {

	private static final Logger logger = LoggingUtil.getThreemaLogger("HomeActivity");

	private static final String THREEMA_CHANNEL_IDENTITY = "*THREEMA";
	private static final String THREEMA_CHANNEL_INFO_COMMAND = "Info";
	private static final String THREEMA_CHANNEL_START_NEWS_COMMAND = "Start News";
	private static final String THREEMA_CHANNEL_START_ANDROID_COMMAND = "Start Android";
	private static final String THREEMA_CHANNEL_WORK_COMMAND = "Start Threema Work";

	private static final long PHONE_REQUEST_DELAY = 10 * DateUtils.MINUTE_IN_MILLIS;

	private static final String DIALOG_TAG_VERIFY_CODE = "vc";
	private static final String DIALOG_TAG_VERIFY_CODE_CONFIRM = "vcc";
	private static final String DIALOG_TAG_CANCEL_VERIFY = "cv";
	private static final String DIALOG_TAG_MASTERKEY_LOCKED = "mkl";
	private static final String DIALOG_TAG_SERIAL_LOCKED = "sll";
	private static final String DIALOG_TAG_FINISH_UP = "fup";
	private static final String DIALOG_TAG_THREEMA_CHANNEL_VERIFY = "cvf";
	private static final String DIALOG_TAG_UPDATING = "updating";

	private static final String FRAGMENT_TAG_MESSAGES = "0";
	private static final String FRAGMENT_TAG_CONTACTS = "1";
	private static final String FRAGMENT_TAG_PROFILE = "2";

	private static final String BUNDLE_CURRENT_FRAGMENT_TAG = "currentFragmentTag";
	private static final int REQUEST_CODE_WHATSNEW = 41912;

	public static final String EXTRA_SHOW_CONTACTS = "show_contacts";

	private ActionBar actionBar;
	private boolean isLicenseCheckStarted = false, isInitialized = false, isWhatsNewShown = false, isUpdating = false;
	private Toolbar toolbar;
	private View connectionIndicator;
	private LinearLayout noticeLayout, ongoingCallNoticeLayout;

	private ServiceManager serviceManager;
	private NotificationService notificationService;
	private UserService userService;
	private ContactService contactService;
	private LockAppService lockAppService;
	private PreferenceService preferenceService;
	private ConversationService conversationService;

	private final ArrayList<AbstractMessageModel> unsentMessages = new ArrayList<>();

	private BroadcastReceiver checkLicenseBroadcastReceiver = null;
	private final BroadcastReceiver currentCheckAppReceiver = new BroadcastReceiver() {
		@Override
		public void onReceive(Context context, final Intent intent) {
			RuntimeUtil.runOnUiThread(new Runnable() {
				@Override
				public void run() {
					if (intent.getAction().equals(IntentDataUtil.ACTION_LICENSE_NOT_ALLOWED)) {
						if (BuildFlavor.getLicenseType() == BuildFlavor.LicenseType.SERIAL ||
							BuildFlavor.getLicenseType() == BuildFlavor.LicenseType.GOOGLE_WORK ||
							BuildFlavor.getLicenseType() == BuildFlavor.LicenseType.HMS_WORK ||
							BuildFlavor.getLicenseType() == BuildFlavor.LicenseType.ONPREM) {
							//show enter serial stuff
							startActivityForResult(new Intent(HomeActivity.this, EnterSerialActivity.class), ThreemaActivity.ACTIVITY_ID_ENTER_SERIAL);
						} else {
							showErrorTextAndExit(IntentDataUtil.getMessage(intent));
						}
					} else if (intent.getAction().equals(IntentDataUtil.ACTION_UPDATE_AVAILABLE) && !ConfigUtils.isWorkBuild() && userService != null && userService.hasIdentity()) {
						new Handler().postDelayed(new Runnable() {
							@Override
							public void run() {
								Intent dialogIntent = new Intent(intent);
								dialogIntent.setClass(HomeActivity.this, DownloadApkActivity.class);
								startActivity(dialogIntent);
							}
						}, DateUtils.SECOND_IN_MILLIS * 5);
					}
				}
			});
		}
	};

	private BottomNavigationView bottomNavigationView;
	private View mainContent;

	private String currentFragmentTag;

	private static class UpdateBottomNavigationBadgeTask extends AsyncTask<Void, Void, Integer> {
		private ConversationTagService conversationTagService = null;
		private final WeakReference<Activity> activityWeakReference;

		UpdateBottomNavigationBadgeTask(Activity activity) {
			activityWeakReference = new WeakReference<>(activity);
			try {
				conversationTagService = Objects.requireNonNull(ThreemaApplication.getServiceManager()).getConversationTagService();
			} catch (Exception e) {
				logger.error("UpdateBottomNav", e);
			}
		}

		@Override
		protected Integer doInBackground(Void... voids) {
			ConversationService conversationService;
			try {
				conversationService = ThreemaApplication.getServiceManager().getConversationService();
			} catch (Exception e) {
				return 0;
			}

			if (conversationService == null) {
				return 0;
			}

			List <ConversationModel> conversationModels = conversationService.getAll(false, new ConversationService.Filter() {
				@Override
				public boolean onlyUnread() {
					return true;
				}

				@Override
				public boolean noDistributionLists() {
					return false;
				}

				@Override
				public boolean noHiddenChats() {
					return false;
				}

				@Override
				public boolean noInvalid() {
					return false;
				}
			});

			int unread = 0;

			for(ConversationModel conversationModel : conversationModels) {
				unread += conversationModel.getUnreadCount();
			}

			if (conversationTagService != null) {
				unread += conversationTagService.getCount(conversationTagService.getTagModel(FIXED_TAG_UNREAD));
			}

			return unread;
		}

		@Override
		protected void onPostExecute(Integer count) {
			if (activityWeakReference.get() != null) {
				BottomNavigationView bottomNavigationView = activityWeakReference.get().findViewById(R.id.bottom_navigation);
				if (bottomNavigationView != null) {
					BadgeDrawable badgeDrawable = bottomNavigationView.getOrCreateBadge(R.id.messages);
					if (badgeDrawable.getVerticalOffset() == 0) {
						badgeDrawable.setVerticalOffset(activityWeakReference.get().getResources().getDimensionPixelSize(R.dimen.bottom_nav_badge_offset_vertical));
					}
					badgeDrawable.setNumber(count);
					badgeDrawable.setVisible(count > 0);
				}
			}
		}
	}

	private final ConnectionStateListener connectionStateListener = new ConnectionStateListener() {
		@Override
		public void updateConnectionState(final ConnectionState connectionState, InetSocketAddress address) {
			updateConnectionIndicator(connectionState);
		}
	};

	private void updateUnsentMessagesList(AbstractMessageModel modifiedMessageModel, boolean add) {
		int numCurrentUnsent = unsentMessages.size();

		synchronized (unsentMessages) {
			String uid = modifiedMessageModel.getUid();

			Iterator<AbstractMessageModel> iterator = unsentMessages.iterator();
			while (iterator.hasNext()) {
				AbstractMessageModel unsentMessage = iterator.next();
				if (TestUtil.compare(unsentMessage.getUid(), uid)) {
					iterator.remove();
				}
			}

			if (add) {
				unsentMessages.add(modifiedMessageModel);
			}

			int numNewUnsent = unsentMessages.size();

			if (notificationService != null && !(numCurrentUnsent == 0 && numNewUnsent == 0)) {
				notificationService.showUnsentMessageNotification(unsentMessages);
			}
		}
	}

	private final SMSVerificationListener smsVerificationListener = new SMSVerificationListener() {
		@Override
		public void onVerified() {
		 	RuntimeUtil.runOnUiThread(new Runnable() {
				@Override
				public void run() {
					if (noticeLayout != null) {
						AnimationUtil.collapse(noticeLayout);
					}
				}
			});
		}

		@Override
		public void onVerificationStarted() {
		 	RuntimeUtil.runOnUiThread(new Runnable() {
				@Override
				public void run() {
					if (noticeLayout != null) {
						AnimationUtil.expand(noticeLayout);
					}
				}
			});
		}
	};

	private void updateBottomNavigation() {
		if (preferenceService.getShowUnreadBadge()) {
			RuntimeUtil.runOnUiThread(() -> {
				try {
					new UpdateBottomNavigationBadgeTask(HomeActivity.this).executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR);
				} catch (RejectedExecutionException e) {
					try {
						new UpdateBottomNavigationBadgeTask(HomeActivity.this).executeOnExecutor(AsyncTask.SERIAL_EXECUTOR);
					} catch (RejectedExecutionException ignored) {
					}
				}
			});
		}
	}

	private final ConversationListener conversationListener = new ConversationListener() {
		@Override
		public void onNew(ConversationModel conversationModel) {
			updateBottomNavigation();
		}

		@Override
		public void onModified(ConversationModel modifiedConversationModel, Integer oldPosition) {
			updateBottomNavigation();
		}

		@Override
		public void onRemoved(ConversationModel conversationModel) {
			updateBottomNavigation();
		}

		@Override
		public void onModifiedAll() {

		}
	};

	private final MessageListener messageListener = new MessageListener() {
		@Override
		public void onNew(AbstractMessageModel newMessage) {
			//do nothing
		}

		@Override
		public void onModified(List<AbstractMessageModel> modifiedMessageModels) {
			for (AbstractMessageModel modifiedMessageModel : modifiedMessageModels) {

				if (!modifiedMessageModel.isStatusMessage()
						&& modifiedMessageModel.isOutbox()) {

					switch (modifiedMessageModel.getState()) {
						case SENDFAILED:
							updateUnsentMessagesList(modifiedMessageModel, true);
							break;
						default:
							updateUnsentMessagesList(modifiedMessageModel, false);
							break;
					}
				}
			}
		}

		@Override
		public void onRemoved(AbstractMessageModel removedMessageModel) {
			updateUnsentMessagesList(removedMessageModel, false);
		}

		@Override
		public void onRemoved(List<AbstractMessageModel> removedMessageModels) {
			for (AbstractMessageModel removedMessageModel: removedMessageModels) {
				updateUnsentMessagesList(removedMessageModel, false);
			}
		}

		@Override
		public void onProgressChanged(AbstractMessageModel messageModel, int newProgress) {
			//do nothing
		}
	};

	private final AppIconListener appIconListener = new AppIconListener() {
		@Override
		public void onChanged() {
		 	RuntimeUtil.runOnUiThread(new Runnable() {
				@Override
				public void run() {
					updateAppLogo();
				}
			});
		}
	};

	private final ProfileListener profileListener = new ProfileListener() {
		@Override
		public void onAvatarChanged() {
			RuntimeUtil.runOnUiThread(() -> updateDrawerImage());
		}

		@Override
		public void onAvatarRemoved() {
			this.onAvatarChanged();
		}

		@Override
		public void onNicknameChanged(String newNickname) { }
	};

	private final VoipCallListener voipCallListener = new VoipCallListener() {
		@Override
		public void onStart(String contact, long elpasedTimeMs) {
			RuntimeUtil.runOnUiThread(() -> {
				initOngoingCallNotice();
			});
		}

		@Override
		public void onEnd() {
			RuntimeUtil.runOnUiThread(() -> {
				if (ongoingCallNoticeLayout != null) {
					Chronometer chronometer = ongoingCallNoticeLayout.findViewById(R.id.call_duration);
					chronometer.stop();
					ongoingCallNoticeLayout.setVisibility(View.GONE);
				}
			});
		}
	};

	private final ContactCountListener contactCountListener = new ContactCountListener() {
		@Override
		public void onNewContactsCountUpdated(int last24hoursCount) {
			if (preferenceService != null && preferenceService.getShowUnreadBadge()) {
				RuntimeUtil.runOnUiThread(() -> {
					if (!isFinishing() && !isDestroyed() && !isChangingConfigurations()) {
						BottomNavigationView bottomNavigationView = findViewById(R.id.bottom_navigation);
						if (bottomNavigationView != null) {
							BadgeDrawable badgeDrawable = bottomNavigationView.getOrCreateBadge(R.id.contacts);
							badgeDrawable.setBackgroundColor(ConfigUtils.getAccentColor(HomeActivity.this));
							if (badgeDrawable.getVerticalOffset() == 0) {
								badgeDrawable.setVerticalOffset(getResources().getDimensionPixelSize(R.dimen.bottom_nav_badge_offset_vertical));
							}
							badgeDrawable.setVisible(last24hoursCount > 0);
						}
					}
				});
			}
		}
	};

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		logger.debug("onCreate");

		final boolean isAppStart = savedInstanceState == null;

		AnimationUtil.setupTransitions(this.getApplicationContext(), getWindow());

		ConfigUtils.configureActivityTheme(this);

		super.onCreate(savedInstanceState);

		//check master key
		MasterKey masterKey = ThreemaApplication.getMasterKey();

		if (masterKey != null && masterKey.isLocked()) {
			startActivityForResult(new Intent(this, UnlockMasterKeyActivity.class), ThreemaActivity.ACTIVITY_ID_UNLOCK_MASTER_KEY);
		} else {
			if (ConfigUtils.isSerialLicensed() && !ConfigUtils.isSerialLicenseValid()) {
				startActivityForResult(new Intent(this, EnterSerialActivity.class), ThreemaActivity.ACTIVITY_ID_ENTER_SERIAL);
				finish();
			} else {
				this.startMainActivity(savedInstanceState);

				// only execute this on first startup
				if (isAppStart) {
					if (preferenceService != null && userService != null && userService.hasIdentity()) {
						if (ConfigUtils.isWorkRestricted()) {
							// update configuration
							final ThreemaSafeMDMConfig newConfig = ThreemaSafeMDMConfig.getInstance();
							ThreemaSafeService threemaSafeService = null;
							try {
								threemaSafeService = serviceManager.getThreemaSafeService();
							} catch (Exception e) {
								//
							}

							if (threemaSafeService != null) {
								if (newConfig.hasChanged(preferenceService)) {
									// dispose of old backup, if any
									try {
										threemaSafeService.deleteBackup();
										threemaSafeService.setEnabled(false);
									} catch (Exception e) {
										// ignore
									}

									preferenceService.setThreemaSafeServerInfo(newConfig.getServerInfo());

									if (newConfig.isBackupForced()) {
										if (newConfig.isSkipBackupPasswordEntry()) {
											// enable with given password
											enableSafe(threemaSafeService, newConfig, null);
										} else if (threemaSafeService.getThreemaSafeMasterKey() != null && threemaSafeService.getThreemaSafeMasterKey().length > 0) {
											// no password has been given by admin but a master key from a previous backup exists
											// -> create a new backup with existing password
											enableSafe(threemaSafeService, newConfig, threemaSafeService.getThreemaSafeMasterKey());
										} else {
											threemaSafeService.launchForcedPasswordDialog(this);
											finish();
											return;
										}
									}
								} else {
									if (newConfig.isBackupForced() && !preferenceService.getThreemaSafeEnabled()) {
										// config has not changed but safe is still not enabled. fix it.
										if (newConfig.isSkipBackupPasswordEntry()) {
											// enable with given password
											enableSafe(threemaSafeService, newConfig, null);
										} else {
											// ask user for a new password
											threemaSafeService.launchForcedPasswordDialog(this);
											finish();
											return;
										}
									}
								}
								// save current config as new reference
								newConfig.saveConfig(preferenceService);
							}
						}
					}
				}
			}
		}
	}

	@UiThread
	private void showMainContent() {
		if (mainContent != null) {
			if (mainContent.getVisibility() != View.VISIBLE) {
				mainContent.setVisibility(View.VISIBLE);
			}
		}
	}

	private void showWhatsNew() {
		final boolean skipWhatsNew = true; // set this to false if you want to show a What's New screen

		if (preferenceService != null) {
			if (!preferenceService.isLatestVersion(this)) {
				// so the app has just been updated

				if (preferenceService.getPrivacyPolicyAccepted() == null) {
					preferenceService.setPrivacyPolicyAccepted(new Date(), PreferenceService.PRIVACY_POLICY_ACCEPT_UPDATE);
				}

				if (!ConfigUtils.isWorkBuild() && !RuntimeUtil.isInTest() && !isFinishing()) {
					if (skipWhatsNew) {
						isWhatsNewShown = false;
					} else {
						isWhatsNewShown = true; // make sure this is set to false if whatsnew is skipped - otherwise pin unlock will not be shown once

						// Do not show whatsnew for users of the previous 4.5x version
						int previous = preferenceService.getLatestVersion() % 1000;

						if (previous < BuildConfig.VERSION_CODE) {
							Intent intent = new Intent(this, WhatsNewActivity.class);
							startActivityForResult(intent, REQUEST_CODE_WHATSNEW);
							overridePendingTransition(R.anim.abc_fade_in, R.anim.abc_fade_out);
						}
					}
					preferenceService.setLatestVersion(this);
				}
			}
		}
	}

	@SuppressLint("StaticFieldLeak")
	private void enableSafe(ThreemaSafeService threemaSafeService, ThreemaSafeMDMConfig mdmConfig, final byte[] masterkeyPreset) {
		new AsyncTask<Void, Void, byte[]>() {
			@Override
			protected byte[] doInBackground(Void... voids) {
				if (masterkeyPreset == null) {
					return threemaSafeService.deriveMasterKey(mdmConfig.getPassword(), userService.getIdentity());
				}
				return masterkeyPreset;
			}

			@Override
			protected void onPostExecute(byte[] masterkey) {
				if (masterkey != null) {
					threemaSafeService.storeMasterKey(masterkey);
					preferenceService.setThreemaSafeServerInfo(mdmConfig.getServerInfo());
					threemaSafeService.setEnabled(true);
					threemaSafeService.uploadNow(HomeActivity.this, true);
				} else {
					Toast.makeText(HomeActivity.this, R.string.safe_error_preparing, Toast.LENGTH_LONG).show();
				}
			}
		}.execute();
	}

	private void showQRPopup() {
		int[] location = getMiniAvatarLocation();

		IdentityPopup identityPopup = new IdentityPopup(this);
		identityPopup.show(this, toolbar, location, () -> {
			// show profile fragment
			bottomNavigationView.post(() -> {
				bottomNavigationView.findViewById(R.id.my_profile).performClick();
			});
		});
	}

	private int[] getMiniAvatarLocation() {
		int[] location = new int[2];
		toolbar.getLocationInWindow(location);

		location[0] += toolbar.getContentInsetLeft() +
				((getResources().getDimensionPixelSize(R.dimen.navigation_icon_padding) +
						getResources().getDimensionPixelSize(R.dimen.navigation_icon_size)) / 2);
		location[1] += toolbar.getHeight() / 2;

		return location;
	}

	private void checkApp() {
		try {
			if (this.currentCheckAppReceiver != null) {
				LocalBroadcastManager.getInstance(this).unregisterReceiver(this.currentCheckAppReceiver);
			}

			if (this.checkLicenseBroadcastReceiver != null) {
				this.unregisterReceiver(this.checkLicenseBroadcastReceiver);
			}
		} catch (IllegalArgumentException r) {
			//not registered... ignore exceptions
		}

		//Register not licensed and update available broadcast
		IntentFilter filter = new IntentFilter();
		filter.addAction(IntentDataUtil.ACTION_LICENSE_NOT_ALLOWED);
		filter.addAction(IntentDataUtil.ACTION_UPDATE_AVAILABLE);
		LocalBroadcastManager.getInstance(this).registerReceiver(currentCheckAppReceiver, filter);

		this.checkLicense();
	}

	private boolean checkLicense() {
		if (this.isLicenseCheckStarted) {
			return true;
		}

		if (serviceManager != null) {
			DeviceService deviceService = serviceManager.getDeviceService();

			if (deviceService != null && deviceService.isOnline()) {
				//start check directly
				CheckLicenseRoutine check = null;
				try {
					check = new CheckLicenseRoutine(
							this,
							serviceManager.getAPIConnector(),
							serviceManager.getUserService(),
							deviceService,
							serviceManager.getLicenseService(),
							serviceManager.getIdentityStore()
					);
				} catch (FileSystemNotPresentException e) {
					logger.error("Exception", e);
					return false;
				}

				new Thread(check).start();
				this.isLicenseCheckStarted = true;

				if (this.checkLicenseBroadcastReceiver != null) {
					try {
						this.unregisterReceiver(this.checkLicenseBroadcastReceiver);
					} catch (IllegalArgumentException e) {
						logger.error("Exception", e);
					}
				}
				return true;
			} else {
				if (this.checkLicenseBroadcastReceiver == null) {
					this.checkLicenseBroadcastReceiver = new BroadcastReceiver() {
						@Override
						public void onReceive(Context context, Intent intent) {
							logger.debug("receive connectivity change in main activity to check license");
							checkLicense();
						}
					};
					this.registerReceiver(
							this.checkLicenseBroadcastReceiver,
							new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION)
					);
				}

			}
		}

		return false;
	}

	@Override
	protected void onDestroy() {
		logger.debug("onDestroy");

		ThreemaApplication.activityDestroyed(this);

		try {
			if (this.currentCheckAppReceiver != null) {
				LocalBroadcastManager.getInstance(this).unregisterReceiver(this.currentCheckAppReceiver);
			}

			if (this.checkLicenseBroadcastReceiver != null) {
				this.unregisterReceiver(this.checkLicenseBroadcastReceiver);
			}
		} catch (IllegalArgumentException r) {
			//not registered... ignore exceptions
		}

		// remove listeners to avoid memory leaks
		ListenerManager.messageListeners.remove(this.messageListener);
		ListenerManager.smsVerificationListeners.remove(this.smsVerificationListener);
		ListenerManager.appIconListeners.remove(this.appIconListener);
		ListenerManager.profileListeners.remove(this.profileListener);
		ListenerManager.voipCallListeners.remove(this.voipCallListener);
		ListenerManager.conversationListeners.remove(this.conversationListener);
		ListenerManager.contactCountListener.remove(this.contactCountListener);

		if (serviceManager != null) {
			ThreemaConnection threemaConnection = serviceManager.getConnection();
			if (threemaConnection != null) {
				threemaConnection.removeConnectionStateListener(connectionStateListener);
			}
		}

		super.onDestroy();
	}

	private void showErrorTextAndExit(String text) {
		GenericAlertDialog.newInstance(R.string.error, text, R.string.finish, 0)
			.show(getSupportFragmentManager(), DIALOG_TAG_FINISH_UP);
	}

	@SuppressLint("StaticFieldLeak")
	private void runUpdates(final UpdateSystemService updateSystemService) {
		if (isUpdating) {
			return;
		}

		new AsyncTask<Void, Void, Void>() {
			@Override
			protected void onPreExecute() {
				isUpdating = true;

				GenericProgressDialog.newInstance(R.string.updating_system, R.string.please_wait).show(getSupportFragmentManager(), DIALOG_TAG_UPDATING);
			}

			@Override
			protected Void doInBackground(Void... voids) {
				try {
					updateSystemService.update(new UpdateSystemService.OnSystemUpdateRun() {
						@Override
						public void onStart(final UpdateSystemService.SystemUpdate systemUpdate) {
							logger.info("Running update to " + systemUpdate.getText());
						}

						@Override
						public void onFinished(UpdateSystemService.SystemUpdate systemUpdate, boolean success) {
							if (success) {
								logger.info("System updated to " + systemUpdate.getText());
							} else {
								logger.error("System update to " + systemUpdate.getText() + " failed!");
							}
						}
					});
				} catch (final Exception e) {
					logger.error("Exception", e);
				}
				return null;
			}

			@Override
			protected void onPostExecute(Void aVoid) {
				DialogUtil.dismissDialog(getSupportFragmentManager(), DIALOG_TAG_UPDATING, true);
				initMainActivity(null);

				isUpdating = false;
			}
		}.execute();
	}

	private void startMainActivity(Bundle savedInstanceState) {
		// at this point the app should be unlocked, licensed and updated
		// therefore Services are now available
		this.serviceManager = ThreemaApplication.getServiceManager();

		if (this.isInitialized) {
			return;
		}

		if (serviceManager != null) {
			this.userService = this.serviceManager.getUserService();
			this.preferenceService = this.serviceManager.getPreferenceService();
			this.notificationService = serviceManager.getNotificationService();
			this.lockAppService = serviceManager.getLockAppService();
			try {
				this.conversationService = serviceManager.getConversationService();
				this.contactService = serviceManager.getContactService();
			} catch (Exception e) {
				//
			}

			if (preferenceService == null || notificationService == null || userService == null) {
				finish();
				return;
			}

			// reset connectivity status
			preferenceService.setLastOnlineStatus(serviceManager.getDeviceService().isOnline());

			// remove restart notification
			notificationService.cancelRestartNotification();

			ListenerManager.smsVerificationListeners.add(this.smsVerificationListener);
			ListenerManager.messageListeners.add(this.messageListener);
			ListenerManager.appIconListeners.add(this.appIconListener);
			ListenerManager.profileListeners.add(this.profileListener);
			ListenerManager.voipCallListeners.add(this.voipCallListener);
			ListenerManager.conversationListeners.add(this.conversationListener);
			ListenerManager.contactCountListener.add(this.contactCountListener);

			UpdateSystemService updateSystemService = serviceManager.getUpdateSystemService();
			if (updateSystemService.hasUpdates()) {
					//runASync updates FIRST!!
					this.runUpdates(updateSystemService);
				} else {
					this.initMainActivity(savedInstanceState);
				}
		} else {
		 	RuntimeUtil.runOnUiThread(() -> showErrorTextAndExit(getString(R.string.service_manager_not_available)));
		}
	}

	@UiThread
	private void initMainActivity(@Nullable Bundle savedInstanceState) {
		final boolean isAppStart = savedInstanceState == null;

		//refresh StateBitmapUtil
		StateBitmapUtil.getInstance().refresh();

		// licensing
		checkApp();

		// start wizard if necessary
		if (preferenceService.getWizardRunning() || !userService.hasIdentity()) {
			logger.debug("Missing identity. Wizard running? " + preferenceService.getWizardRunning());

			if (userService.hasIdentity()) {
				startActivity(new Intent(this, WizardBaseActivity.class));
			} else {
				startActivity(new Intent(this, WizardStartActivity.class));
			}
			finish();
			return;
		}

		// set custom locale
		ConfigUtils.setLocaleOverride(this, preferenceService);

		// set up content
		setContentView(R.layout.activity_home);

		// Wizard complete
		// Write master key now if no passphrase has been set
		if (!ThreemaApplication.getMasterKey().isProtected()) {
			try {
				ThreemaApplication.getMasterKey().setPassphrase(null);
			} catch (Exception e) {
				// better die if something went wrong as the master key may not have been saved
				throw new RuntimeException(e);
			}
		}

		// Set up the action bar.
		initActionBar();

		//init custom icon
		updateAppLogo();

		// reset accent color
		ConfigUtils.resetAccentColor(this);

		actionBar.setDisplayShowTitleEnabled(false);
		actionBar.setDisplayUseLogoEnabled(false);

		ConfigUtils.setScreenshotsAllowed(this, preferenceService, lockAppService);

		// add connection state listener for displaying colored connection status line above toolbar
		new Thread(() -> {
			if (serviceManager != null) {
				ThreemaConnection threemaConnection = serviceManager.getConnection();
				if (threemaConnection != null) {
					threemaConnection.addConnectionStateListener(connectionStateListener);
					updateConnectionIndicator(threemaConnection.getConnectionState());
				}
			}
		}).start();

		// call onPrepareOptionsMenu
		this.invalidateOptionsMenu();

		// Checks on app start
		if (isAppStart) {
			// If a non-libre build of Threema cannot find push services, fall back to Threema Push
			if (!BuildFlavor.forceThreemaPush() && !PushService.servicesInstalled(this)) {
				this.enableThreemaPush();
				if (!ConfigUtils.isBlackBerry() && !ConfigUtils.isAmazonDevice() && !ConfigUtils.isWorkBuild()) {
					RuntimeUtil.runOnUiThread(() -> {
						// Show "push not available" dialog
						int title = R.string.push_not_available_title;
						final String message = getString(R.string.push_not_available_text1) + "\n\n" + getString(R.string.push_not_available_text2, getString(R.string.app_name));
						ShowOnceDialog.newInstance(title, message).show(getSupportFragmentManager(), "nopush");
					});
				}
			}

			// For libre builds of Threema, always ask user to disable battery permissions
			if (BuildFlavor.forceThreemaPush()) {
				preferenceService.setUseThreemaPush(true);

				if (!DisableBatteryOptimizationsActivity.isIgnoringBatteryOptimizations(this)) {
					final Intent intent = new Intent(this, DisableBatteryOptimizationsActivity.class);
					intent.putExtra(DisableBatteryOptimizationsActivity.EXTRA_NAME, getString(R.string.threema_push));
					intent.putExtra(DisableBatteryOptimizationsActivity.EXTRA_CONFIRM, true);
					startActivityForResult(intent, 12345);
				}
			}
		}

		this.mainContent = findViewById(R.id.main_content);
		this.noticeLayout = findViewById(R.id.notice_layout);
		findViewById(R.id.notice_button_enter_code).setOnClickListener(v -> SMSVerificationDialog.newInstance(userService.getLinkedMobile(true)).show(getSupportFragmentManager(), DIALOG_TAG_VERIFY_CODE));
		findViewById(R.id.notice_button_cancel).setOnClickListener(v -> GenericAlertDialog.newInstance(R.string.verify_title, R.string.really_cancel_verify, R.string.yes, R.string.no)
				.show(getSupportFragmentManager(), DIALOG_TAG_CANCEL_VERIFY));
		this.noticeLayout.setVisibility(
				userService.getMobileLinkingState() == UserService.LinkingState_PENDING ?
						View.VISIBLE : View.GONE);

		this.ongoingCallNoticeLayout = findViewById(R.id.ongoing_call_layout);
		findViewById(R.id.call_container).setOnClickListener(v -> {
			if (VoipCallService.isRunning()) {
				final Intent openIntent = new Intent(HomeActivity.this, CallActivity.class);
				openIntent.putExtra(EXTRA_ACTIVITY_MODE, CallActivity.MODE_ACTIVE_CALL);
				openIntent.putExtra(EXTRA_CONTACT_IDENTITY, VoipCallService.getOtherPartysIdentity());
				openIntent.putExtra(EXTRA_START_TIME, VoipCallService.getStartTime());
				startActivity(openIntent);
			}
		});
		findViewById(R.id.call_hangup).setOnClickListener(v -> {
			final Intent hangupIntent = new Intent(HomeActivity.this, VoipCallService.class);
			hangupIntent.setAction(ACTION_HANGUP);
			startService(hangupIntent);
		});
		initOngoingCallNotice();

		/*
		 * setup fragments
		 */
		String initialFragmentTag = FRAGMENT_TAG_MESSAGES;
		final int initialItemId;
		final Fragment contactsFragment, messagesFragment, profileFragment;

		Intent intent = getIntent();
		if (intent != null && intent.getBooleanExtra(EXTRA_SHOW_CONTACTS, false)) {
			initialFragmentTag = FRAGMENT_TAG_CONTACTS;
			intent.removeExtra(EXTRA_SHOW_CONTACTS);
		}

		if (!isAppStart && savedInstanceState.containsKey(BUNDLE_CURRENT_FRAGMENT_TAG)) {
			// restored session
			initialFragmentTag = savedInstanceState.getString(BUNDLE_CURRENT_FRAGMENT_TAG, initialFragmentTag);

			contactsFragment = getSupportFragmentManager().findFragmentByTag(FRAGMENT_TAG_CONTACTS);
			messagesFragment = getSupportFragmentManager().findFragmentByTag(FRAGMENT_TAG_MESSAGES);
			profileFragment = getSupportFragmentManager().findFragmentByTag(FRAGMENT_TAG_PROFILE);

			currentFragmentTag = initialFragmentTag;

			switch (initialFragmentTag) {
				case FRAGMENT_TAG_CONTACTS:
					getSupportFragmentManager().beginTransaction().hide(messagesFragment).hide(profileFragment).show(contactsFragment).commit();
					initialItemId = R.id.contacts;
					break;
				case FRAGMENT_TAG_MESSAGES:
					getSupportFragmentManager().beginTransaction().hide(contactsFragment).hide(profileFragment).show(messagesFragment).commit();
					initialItemId = R.id.messages;
					break;
				case FRAGMENT_TAG_PROFILE:
					getSupportFragmentManager().beginTransaction().hide(messagesFragment).hide(contactsFragment).show(profileFragment).commit();
					initialItemId = R.id.my_profile;
					break;
				default:
					initialItemId = R.id.messages;
			}
		} else {
			// new session
			if (conversationService == null || !conversationService.hasConversations()) {
				initialFragmentTag = FRAGMENT_TAG_CONTACTS;
			}

			contactsFragment = new ContactsSectionFragment();
			messagesFragment = new MessageSectionFragment();
			profileFragment = new MyIDFragment();

			FragmentTransaction messagesTransaction = getSupportFragmentManager().beginTransaction().add(R.id.home_container, messagesFragment, FRAGMENT_TAG_MESSAGES);
			FragmentTransaction contactsTransaction = getSupportFragmentManager().beginTransaction().add(R.id.home_container, contactsFragment, FRAGMENT_TAG_CONTACTS);
			FragmentTransaction profileTransaction = getSupportFragmentManager().beginTransaction().add(R.id.home_container, profileFragment, FRAGMENT_TAG_PROFILE);

			currentFragmentTag = initialFragmentTag;

			switch (initialFragmentTag) {
				case FRAGMENT_TAG_CONTACTS:
					initialItemId = R.id.contacts;
					messagesTransaction.hide(messagesFragment);
					messagesTransaction.hide(profileFragment);
					break;
				case FRAGMENT_TAG_MESSAGES:
					initialItemId = R.id.messages;
					messagesTransaction.hide(contactsFragment);
					messagesTransaction.hide(profileFragment);
					break;
				case FRAGMENT_TAG_PROFILE:
					initialItemId = R.id.my_profile;
					messagesTransaction.hide(messagesFragment);
					messagesTransaction.hide(contactsFragment);
					break;
				default:
					// should never happen
					initialItemId = R.id.messages;
			}

			try {
				messagesTransaction.commitAllowingStateLoss();
				contactsTransaction.commitAllowingStateLoss();
				profileTransaction.commitAllowingStateLoss();
			} catch (IllegalStateException e) {
				logger.error("Exception", e);
			}
		}

		this.bottomNavigationView = findViewById(R.id.bottom_navigation);
		this.bottomNavigationView.setOnNavigationItemSelectedListener(item -> {
			showMainContent();

			Fragment currentFragment = getSupportFragmentManager().findFragmentByTag(currentFragmentTag);
			if (currentFragment != null) {
				switch (item.getItemId()) {
					case R.id.contacts:
						if (!FRAGMENT_TAG_CONTACTS.equals(currentFragmentTag)) {
							getSupportFragmentManager().beginTransaction().setCustomAnimations(R.anim.fast_fade_in, R.anim.fast_fade_out, R.anim.fast_fade_in, R.anim.fast_fade_out).hide(currentFragment).show(contactsFragment).commit();
							currentFragmentTag = FRAGMENT_TAG_CONTACTS;
						}
						return true;
					case R.id.messages:
						if (!FRAGMENT_TAG_MESSAGES.equals(currentFragmentTag)) {
							getSupportFragmentManager().beginTransaction().setCustomAnimations(R.anim.fast_fade_in, R.anim.fast_fade_out, R.anim.fast_fade_in, R.anim.fast_fade_out).hide(currentFragment).show(messagesFragment).commit();
							currentFragmentTag = FRAGMENT_TAG_MESSAGES;
						}
						return true;
					case R.id.my_profile:
						if (!FRAGMENT_TAG_PROFILE.equals(currentFragmentTag)) {
							getSupportFragmentManager().beginTransaction().setCustomAnimations(R.anim.fast_fade_in, R.anim.fast_fade_out, R.anim.fast_fade_in, R.anim.fast_fade_out).hide(currentFragment).show(profileFragment).commit();
							currentFragmentTag = FRAGMENT_TAG_PROFILE;
						}
						return true;
				}
			}
			return false;
		});
		this.bottomNavigationView.post(() -> {
			bottomNavigationView.setSelectedItemId(initialItemId);
		});

		updateBottomNavigation();

		// restore sync adapter account if necessary
		if (preferenceService.isSyncContacts()) {
			if (!userService.checkAccount()) {
				//create account
				userService.getAccount(true);
			}
			userService.enableAccountAutoSync(true);
		}

		isInitialized = true;

		showWhatsNew();

		notificationService.cancelRestoreNotification();
	}

	private void initOngoingCallNotice() {
		if (ongoingCallNoticeLayout != null) {
			if (VoipCallService.isRunning()) {
				Chronometer chronometer = ongoingCallNoticeLayout.findViewById(R.id.call_duration);
				chronometer.setBase(VoipCallService.getStartTime());
				chronometer.start();
				ongoingCallNoticeLayout.setVisibility(View.VISIBLE);
			} else {
				ongoingCallNoticeLayout.setVisibility(View.GONE);
			}
		}
	}

	/**
	 * Ensure that Threema Push is enabled in the preferences.
	 */
	private void enableThreemaPush() {
		if (!preferenceService.useThreemaPush()) {
			preferenceService.setUseThreemaPush(true);
			ThreemaPushService.tryStart(logger, ThreemaApplication.getAppContext());
		}
	}

	@SuppressLint("StaticFieldLeak")
	private void updateDrawerImage() {
		if (toolbar != null) {
			new AsyncTask<Void, Void, Drawable>() {
				@Override
				protected Drawable doInBackground(Void... params) {
					Bitmap bitmap = contactService.getAvatar(new ContactModel(userService.getIdentity(), null), false);
					if (bitmap != null) {
						int size = getResources().getDimensionPixelSize(R.dimen.navigation_icon_size);
						return new BitmapDrawable(getResources(), Bitmap.createScaledBitmap(bitmap, size, size, true));
					}
					return null;
				}

				@Override
				protected void onPostExecute(Drawable drawable) {
					if (drawable != null) {
						toolbar.setNavigationIcon(drawable);
						toolbar.setNavigationContentDescription(R.string.open_myid_popup);
					}
				}
			}.execute();
		}
	}

	@SuppressLint("StaticFieldLeak")
	private void reallyCancelVerify() {
		AnimationUtil.collapse(noticeLayout);
		new AsyncTask<Void, Void, Void>() {
			@Override
			protected Void doInBackground(Void... params) {
				try {
					userService.unlinkMobileNumber();
				} catch (Exception e) {
					logger.error("Exception", e);
				}
				return null;
			}
		}.execute();
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		super.onCreateOptionsMenu(menu);

		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.activity_home, menu);

		ConfigUtils.addIconsToOverflowMenu(this, menu);

		return true;
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		Intent intent = null;

		switch (item.getItemId()) {
			case android.R.id.home:
				showQRPopup();
				return true;
			case R.id.menu_lock:
				lockAppService.lock();
				return true;
			case R.id.menu_new_group:
				intent = new Intent(this, GroupAddActivity.class);
				break;
			case R.id.menu_new_distribution_list:
				intent = new Intent(this, DistributionListAddActivity.class);
				break;
			case R.id.group_requests:
				intent = new Intent(this, OutgoingGroupRequestActivity.class);
				break;
			case R.id.my_backups:
				intent = new Intent(HomeActivity.this, BackupAdminActivity.class);
				break;
			case R.id.webclient:
				intent = new Intent(HomeActivity.this, SessionsActivity.class);
				break;
			case R.id.scanner:
				intent = new Intent(HomeActivity.this, BaseQrScannerActivity.class);
				break;
			case R.id.help:
				intent = new Intent(HomeActivity.this, SupportActivity.class);
				break;
			case R.id.settings:
				AnimationUtil.startActivityForResult(this, null, new Intent(HomeActivity.this, SettingsActivity.class), ThreemaActivity.ACTIVITY_ID_SETTINGS);
				break;
			case R.id.directory:
				intent = new Intent(HomeActivity.this, DirectoryActivity.class);
				break;
			case R.id.threema_channel:
				confirmThreemaChannel();
				break;
			case R.id.archived:
				intent = new Intent(HomeActivity.this, ArchiveActivity.class);
				break;
			case R.id.globalsearch:
				intent = new Intent(HomeActivity.this, GlobalSearchActivity.class);
			default:
				break;
		}

		if (intent != null) {
			AnimationUtil.startActivity(this, null, intent);
		}

		return super.onOptionsItemSelected(item);
	}

	private int initActionBar() {
		toolbar = findViewById(R.id.main_toolbar);
		setSupportActionBar(toolbar);
		actionBar = getSupportActionBar();

		AppCompatImageView toolbarLogoMain = toolbar.findViewById(R.id.toolbar_logo_main);
		ViewGroup.LayoutParams layoutParams = toolbarLogoMain.getLayoutParams();
		layoutParams.height = ConfigUtils.getActionBarSize(this) / 3;
		toolbarLogoMain.setLayoutParams(layoutParams);
		toolbarLogoMain.setImageResource(R.drawable.logo_main);
		toolbarLogoMain.setColorFilter(ConfigUtils.getColorFromAttribute(this, android.R.attr.textColorSecondary),
			PorterDuff.Mode.SRC_IN);
		toolbarLogoMain.setContentDescription(getString(R.string.logo));
		toolbarLogoMain.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				if (currentFragmentTag != null) {
					Fragment currentFragment = getSupportFragmentManager().findFragmentByTag(currentFragmentTag);
					if (currentFragment != null && currentFragment.isAdded() && !currentFragment.isHidden()) {
						if (currentFragment instanceof ContactsSectionFragment) {
							((ContactsSectionFragment) currentFragment).onLogoClicked();
						} else if (currentFragment instanceof MessageSectionFragment) {
							((MessageSectionFragment) currentFragment).onLogoClicked();
						} else if (currentFragment instanceof MyIDFragment) {
							((MyIDFragment) currentFragment).onLogoClicked();
						}
					}
				}
			}
		});

		updateDrawerImage();

		return toolbar.getMinimumHeight();
	}

	@Override
	public boolean onPrepareOptionsMenu(Menu menu) {
		super.onPrepareOptionsMenu(menu);

		if (serviceManager != null) {

			MenuItem lockMenuItem = menu.findItem(R.id.menu_lock);
			if (lockMenuItem != null) {
				lockMenuItem.setVisible(
						lockAppService.isLockingEnabled()
				);
			}

			MenuItem privateChatToggleMenuItem = menu.findItem(R.id.menu_toggle_private_chats);
			if (privateChatToggleMenuItem != null) {
				if (preferenceService.isPrivateChatsHidden()) {
					privateChatToggleMenuItem.setIcon(R.drawable.ic_outline_visibility);
					privateChatToggleMenuItem.setTitle(R.string.title_show_private_chats);
				} else {
					privateChatToggleMenuItem.setIcon(R.drawable.ic_outline_visibility_off);
					privateChatToggleMenuItem.setTitle(R.string.title_hide_private_chats);
				}
				ConfigUtils.themeMenuItem(privateChatToggleMenuItem, ConfigUtils.getColorFromAttribute(this, R.attr.textColorSecondary));
			}

			Boolean addDisabled;
			boolean webDisabled = false;

			if (ConfigUtils.isWorkRestricted()) {
				MenuItem backupsMenuItem = menu.findItem(R.id.my_backups);
				if (backupsMenuItem != null) {
					if (AppRestrictionUtil.isBackupsDisabled(this) || (AppRestrictionUtil.isDataBackupsDisabled(this) && ThreemaSafeMDMConfig.getInstance().isBackupDisabled())) {
						backupsMenuItem.setVisible(false);
					}
				}

				addDisabled = AppRestrictionUtil.getBooleanRestriction(getString(R.string.restriction__disable_add_contact));
				webDisabled = AppRestrictionUtil.isWebDisabled(this);
			} else {
				addDisabled = this.contactService != null &&
						this.contactService.getByIdentity(THREEMA_CHANNEL_IDENTITY) != null;
			}

			if (ConfigUtils.isWorkBuild()) {
				MenuItem menuItem = menu.findItem(R.id.directory);
				if (menuItem != null) {
					menuItem.setVisible(ConfigUtils.isWorkDirectoryEnabled());
				}
				menuItem = menu.findItem(R.id.threema_channel);
				if (menuItem != null) {
					menuItem.setVisible(false);
				}
			} else if (addDisabled != null && addDisabled) {
				MenuItem menuItem = menu.findItem(R.id.threema_channel);
				if (menuItem != null) {
					menuItem.setVisible(false);
				}
			}

			if (ConfigUtils.supportsGroupLinks()) {
				MenuItem menuItem = menu.findItem(R.id.scanner);
				if (menuItem != null) {
					menuItem.setVisible(true);
				}

				menuItem = menu.findItem(R.id.group_requests);
				if (menuItem != null) {
					menuItem.setVisible(true);
					menu.setGroupVisible(menuItem.getGroupId(), true);
				}
			}

			MenuItem webclientMenuItem = menu.findItem(R.id.webclient);
			if (webclientMenuItem != null) {
				webclientMenuItem.setVisible(!(webDisabled || ConfigUtils.isBlackBerry()));
			}

			return true;
		}
		return false;
	}

	private void verifyPhoneCode(final String code) {
		new AsyncTask<Void, Void, String>() {
			@Override
			protected String doInBackground(Void... params) {
				try {
					userService.verifyMobileNumber(code);
				} catch (LinkMobileNoException e) {
					logger.error("Exception", e);
					return getString(R.string.code_invalid);
				} catch (Exception e) {
					logger.error("Exception", e);
					return getString(R.string.verify_failed_summary);
				}
				return null;
			}

			@Override
			protected void onPostExecute(String result) {
				if (result != null) {
					getSupportFragmentManager().beginTransaction().add(SimpleStringAlertDialog.newInstance(R.string.error, result), "ss").commitAllowingStateLoss();
				} else {
					Toast.makeText(HomeActivity.this, getString(R.string.verify_success_text), Toast.LENGTH_LONG).show();
					DialogUtil.dismissDialog(getSupportFragmentManager(), DIALOG_TAG_VERIFY_CODE, true);
				}
			}
		}.execute();
	}

	@Override
	public void onCallRequested(String tag) {
		if (System.currentTimeMillis() < userService.getMobileLinkingTime() + PHONE_REQUEST_DELAY) {
			SimpleStringAlertDialog.newInstance(R.string.verify_phonecall_text, getString(R.string.wait_one_minute)).show(getSupportFragmentManager(), "mi");
		} else {
			GenericAlertDialog.newInstance(R.string.verify_phonecall_text, R.string.prepare_call_message, R.string.ok, R.string.cancel).show(getSupportFragmentManager(), DIALOG_TAG_VERIFY_CODE_CONFIRM);
		}
	}

	private void reallyRequestCall() {
		new AsyncTask<Void, Void, String>() {
			@Override
			protected String doInBackground(Void... params) {
				try {
					userService.makeMobileLinkCall();
				} catch (LinkMobileNoException e) {
					logger.error("Exception", e);
					return e.getMessage();
				} catch (Exception e) {
					logger.error("Exception", e);
					return getString(R.string.verify_failed_summary);
				}
				return null;
			}

			@Override
			protected void onPostExecute(String result) {
				if (!TestUtil.empty(result)) {
					SimpleStringAlertDialog.newInstance(R.string.an_error_occurred, result).show(getSupportFragmentManager(), "le");
				}
			}
		}.execute();
	}

	@Override
	public void onYes(String tag, String code) {
		switch (tag) {
			case DIALOG_TAG_VERIFY_CODE:
				verifyPhoneCode(code);
				break;
			default:
				break;
		}
	}

	@Override
	public void onYes(String tag, Object data) {
		switch (tag) {
			case DIALOG_TAG_VERIFY_CODE_CONFIRM:
				reallyRequestCall();
				break;
			case DIALOG_TAG_CANCEL_VERIFY:
				reallyCancelVerify();
				break;
			case DIALOG_TAG_MASTERKEY_LOCKED:
				startActivityForResult(new Intent(HomeActivity.this, UnlockMasterKeyActivity.class), ThreemaActivity.ACTIVITY_ID_UNLOCK_MASTER_KEY);
				break;
			case DIALOG_TAG_SERIAL_LOCKED:
				startActivityForResult(new Intent(HomeActivity.this, EnterSerialActivity.class), ThreemaActivity.ACTIVITY_ID_ENTER_SERIAL);
				finish();
				break;
			case DIALOG_TAG_FINISH_UP:
				System.exit(0);
				break;
			case DIALOG_TAG_THREEMA_CHANNEL_VERIFY:
				addThreemaChannel();
				break;
			default:
				break;
		}
	}

	@Override
	public void onNo(String tag) {
	}

	@Override
	public void onNo(String tag, Object data) {
		switch (tag) {
			case DIALOG_TAG_MASTERKEY_LOCKED:
				finish();
				break;
			case DIALOG_TAG_SERIAL_LOCKED:
				finish();
				break;
			default:
				break;
		}
	}

	@Override
	public void onResume() {
		logger.debug("onResume");

		if (!isWhatsNewShown) {
			ThreemaApplication.activityResumed(this);
		} else {
			isWhatsNewShown = false;
		}

		MasterKey masterKey = ThreemaApplication.getMasterKey();
		if (masterKey != null && masterKey.isProtected()) {
			if (!PassphraseService.isRunning()) {
				PassphraseService.start(this);
			}
		}

		if (serviceManager != null) {
			new Thread(new Runnable() {
				@Override
				public void run() {
					try {
						FileService fileService = serviceManager.getFileService();
						if (fileService != null) {
							fileService.cleanTempDirs();
						}
					} catch (FileSystemNotPresentException e) {
						logger.error("Exception", e);
					}
				}
			}).start();

		}
		super.onResume();

		showMainContent();
	}

	@Override
	protected void onPause() {
		logger.debug("onPause");

		super.onPause();

		ThreemaApplication.activityPaused(this);
	}

	@Override
	public void onUserInteraction() {
		ThreemaApplication.activityUserInteract(this);
		super.onUserInteraction();
	}

	protected void onActivityResult(int requestCode, int resultCode,
									Intent data) {

		// http://www.androiddesignpatterns.com/2013/08/fragment-transaction-commit-state-loss.html
		super.onActivityResult(requestCode, resultCode, data);

		switch (requestCode) {
			case ThreemaActivity.ACTIVITY_ID_WIZARDFIRST:
				UserService userService = serviceManager.getUserService();
				if (userService != null && userService.hasIdentity()) {
					showMainContent();
					startMainActivity(null);
				} else {
					finish();
				}
				break;

			case ThreemaActivity.ACTIVITY_ID_UNLOCK_MASTER_KEY:
				MasterKey masterKey = ThreemaApplication.getMasterKey();

				if (masterKey != null && masterKey.isLocked()) {
					GenericAlertDialog.newInstance(R.string.master_key_locked,
							R.string.master_key_locked_want_exit,
							R.string.try_again, R.string.cancel)
							.show(getSupportFragmentManager(), DIALOG_TAG_MASTERKEY_LOCKED);
				} else {
					//hide after unlock
					if (IntentDataUtil.hideAfterUnlock(this.getIntent())) {
						this.finish();
						return;
					}
					this.startMainActivity(null);
					showWhatsNew();
				}
				break;
			case ThreemaActivity.ACTIVITY_ID_ENTER_SERIAL:
				if (serviceManager != null) {
					LicenseService licenseService = null;
					try {
						licenseService = serviceManager.getLicenseService();
					} catch (FileSystemNotPresentException e) {
						logger.error("Exception", e);
					}
					if (licenseService != null && !licenseService.isLicensed()) {
						GenericAlertDialog.newInstance(R.string.enter_serial_title,
								R.string.serial_required_want_exit,
								R.string.try_again, R.string.cancel)
								.show(getSupportFragmentManager(), DIALOG_TAG_SERIAL_LOCKED);
					} else {
						this.startMainActivity(null);
					}
				}
				break;
			case ThreemaActivity.ACTIVITY_ID_SETTINGS:
				this.invalidateOptionsMenu();
				break;
			case ThreemaActivity.ACTIVITY_ID_ID_SECTION:
				if (resultCode == ThreemaActivity.RESULT_RESTART) {
					Intent i = getIntent();
					finish();
					startActivity(i);
				}
				break;
			case ThreemaActivity.ACTIVITY_ID_CONTACT_DETAIL:
			case ThreemaActivity.ACTIVITY_ID_GROUP_DETAIL:
			case ThreemaActivity.ACTIVITY_ID_COMPOSE_MESSAGE:
				break;
			case REQUEST_CODE_WHATSNEW:
				showMainContent();
				break;
			default:
				break;
		}
	}

	private void updateAppLogo() {
		//update app logo disabled on "not-work" builds
		if(!ConfigUtils.isWorkBuild()) {
			return;
		}
		File customAppIcon = null;
		try {
			customAppIcon = serviceManager.getFileService()
					.getAppLogo(ConfigUtils.getAppTheme(this));
		} catch (FileSystemNotPresentException e) {
			logger.error("Exception", e);
		}

		if(customAppIcon != null && customAppIcon.exists() && this.toolbar != null) {
			//set the icon as app icon
			ImageView headerImageView = toolbar.findViewById(R.id.toolbar_logo_main);

			if(headerImageView != null) {
				headerImageView.clearColorFilter();
				headerImageView.setImageBitmap(BitmapUtil.safeGetBitmapFromUri(this, Uri.fromFile(customAppIcon), ConfigUtils.getUsableWidth(getWindowManager()), false));
			}
		}
	}

	@SuppressLint("StaticFieldLeak")
	public void addThreemaChannel() {
		final MessageService messageService;

		try {
			messageService = serviceManager.getMessageService();
		} catch (Exception e) {
			return;
		}

		new AsyncTask<Void, Void, Exception>() {
			ContactModel newContactModel;

			@Override
			protected void onPreExecute() {
				GenericProgressDialog.newInstance(R.string.threema_channel, R.string.please_wait).show(getSupportFragmentManager(), THREEMA_CHANNEL_IDENTITY);
			}

			@Override
			protected Exception doInBackground(Void... params) {
				try {
					newContactModel = contactService.createContactByIdentity(THREEMA_CHANNEL_IDENTITY, true);
				} catch (Exception e) {
					return e;
				}
				return null;
			}

			@Override
			protected void onPostExecute(Exception exception) {
				DialogUtil.dismissDialog(getSupportFragmentManager(), THREEMA_CHANNEL_IDENTITY, true);

				if (exception == null || exception instanceof EntryAlreadyExistsException) {
					launchThreemaChannelChat();

					if (exception == null) {
						new Thread(new Runnable() {
							@Override
							public void run() {
								try {
									MessageReceiver receiver = contactService.createReceiver(newContactModel);
									if (!getResources().getConfiguration().locale.getLanguage().startsWith("de")) {
										Thread.sleep(1000);
										messageService.sendText("en", receiver);
										Thread.sleep(500);
									}
									Thread.sleep(1000);
									messageService.sendText(THREEMA_CHANNEL_START_NEWS_COMMAND, receiver);
									Thread.sleep(1500);
									messageService.sendText(ConfigUtils.isWorkBuild() ? THREEMA_CHANNEL_WORK_COMMAND : THREEMA_CHANNEL_START_ANDROID_COMMAND, receiver);
									Thread.sleep(1500);
									messageService.sendText(THREEMA_CHANNEL_INFO_COMMAND, receiver);
								} catch (Exception e) {
									//
								}
							}
						}).start();
					}
				} else {
					Toast.makeText(HomeActivity.this, R.string.internet_connection_required, Toast.LENGTH_LONG).show();
				}
			}
		}.execute();
	}

	private void launchThreemaChannelChat() {
		Intent intent = new Intent(getApplicationContext(), ComposeMessageActivity.class);
		IntentDataUtil.append(THREEMA_CHANNEL_IDENTITY, intent);
		startActivity(intent);
	}

	@AnyThread
	private void updateConnectionIndicator(final ConnectionState connectionState) {
		logger.debug("connectionState = " + connectionState);
		RuntimeUtil.runOnUiThread(() -> {
			connectionIndicator = findViewById(R.id.connection_indicator);
			if (connectionIndicator != null) {
				ConnectionIndicatorUtil.getInstance().updateConnectionIndicator(connectionIndicator, connectionState);
			}
		});
	}

	private void confirmThreemaChannel() {
		if (contactService.getByIdentity(THREEMA_CHANNEL_IDENTITY) == null) {
			GenericAlertDialog.newInstance(R.string.threema_channel, R.string.threema_channel_intro, R.string.ok, R.string.cancel).show(getSupportFragmentManager(), DIALOG_TAG_THREEMA_CHANNEL_VERIFY);
		} else {
			launchThreemaChannelChat();
		}
	}

	@Override
	protected void onSaveInstanceState(@NonNull Bundle outState) {
		super.onSaveInstanceState(outState);

		if (currentFragmentTag != null) {
			outState.putString(BUNDLE_CURRENT_FRAGMENT_TAG, currentFragmentTag);
		}
	}
}
```

- https://github.com/threema-ch/threema-android/blob/main/app/src/test/java/ch/threema/app/logging/LogcatBackendTest.java

```java
// LogcatBackendTest.java
// 부록: 테스트 코드

package ch.threema.app.logging;

import android.util.Log;

import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.ArgumentCaptor;
import org.powermock.api.mockito.PowerMockito;
import org.powermock.core.classloader.annotations.PrepareForTest;
import org.powermock.modules.junit4.PowerMockRunner;

import ch.threema.app.BuildConfig;
import ch.threema.logging.LogLevel;
import ch.threema.logging.backend.LogcatBackend;

import static org.mockito.Mockito.times;

/**
 * Logcat backend test.
 */
@RunWith(PowerMockRunner.class)
@PrepareForTest({Log.class})
public class LogcatBackendTest {
	private final ArgumentCaptor<String> tagCaptor = ArgumentCaptor.forClass(String.class);
	private final ArgumentCaptor<String> msgCaptor = ArgumentCaptor.forClass(String.class);
	private final ArgumentCaptor<Integer> levelCaptor = ArgumentCaptor.forClass(Integer.class);

	private void assertLogArguments(@LogLevel int level, String tag, String msg) {
		Assert.assertEquals(Integer.valueOf(level), levelCaptor.getValue());
		Assert.assertEquals(tag, tagCaptor.getValue());
		Assert.assertEquals(msg, msgCaptor.getValue());
	}

	/**
	 * Make sure that enabling the debug log file actually creates the debug log file.
	 * Also test that the file is only created when enabled.
	 */
	@Test
	public void testTagCleaning() {
		PowerMockito.mockStatic(Log.class);
		final LogcatBackend backend = new LogcatBackend(Log.INFO);

		backend.print(Log.WARN, "ch.threema.app.Hello", null, "hello");
		PowerMockito.verifyStatic(Log.class, times(1));
		Log.println(levelCaptor.capture(), tagCaptor.capture(), msgCaptor.capture());
		this.assertLogArguments(Log.WARN, BuildConfig.LOG_TAG, "Hello: hello");

		backend.print(Log.INFO, "ch.threema.domain.Bye", null, "goodbye");
		PowerMockito.verifyStatic(Log.class, times(2));
		Log.println(levelCaptor.capture(), tagCaptor.capture(), msgCaptor.capture());
		this.assertLogArguments(Log.INFO, BuildConfig.LOG_TAG, "Bye: goodbye");

		backend.print(Log.INFO, "ch.threema.app.subpackage.Abcd", null, "msg");
		PowerMockito.verifyStatic(Log.class, times(3));
		Log.println(levelCaptor.capture(), tagCaptor.capture(), msgCaptor.capture());
		this.assertLogArguments(Log.INFO, BuildConfig.LOG_TAG, "subpackage.Abcd: msg");

		backend.print(Log.ERROR, "any.other.package", null, "hmmmm");
		PowerMockito.verifyStatic(Log.class, times(4));
		Log.println(levelCaptor.capture(), tagCaptor.capture(), msgCaptor.capture());
		this.assertLogArguments(Log.ERROR, BuildConfig.LOG_TAG, "any.other.package: hmmmm");
	}
}
```
