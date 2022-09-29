# eg1

- https://github.com/threema-ch/threema-android/blob/557b69f33dd1db96f58e41f6602e32522470f53e/app/src/main/java/ch/threema/app/activities/MainActivity.java

```java
/*  _____ _
 * |_   _| |_  _ _ ___ ___ _ __  __ _
 *   | | | ' \| '_/ -_) -_) '  \/ _` |_
 *   |_| |_||_|_| \___\___|_|_|_\__,_(_)
 *
 * Threema for Android
 * Copyright (c) 2013-2022 Threema GmbH
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU Affero General Public License, version 3,
 * as published by the Free Software Foundation.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 * GNU Affero General Public License for more details.
 *
 * You should have received a copy of the GNU Affero General Public License
 * along with this program. If not, see <https://www.gnu.org/licenses/>.
 */

// https://github.com/threema-ch/threema-android/tree/557b69f33dd1db96f58e41f6602e32522470f53e/app/src/main/java/ch/threema/app/activities

// 해당 폴더가 패키지이름
// threema-android/app/src/main/java/ch/threema/app/activities/
// 에서
// /ch/threema/app/activities/ 부분이 패키지 이름이 됨(현재 mainAcitiviy.app 위치이기도 함)

package ch.threema.app.activities;



// nodejs의 import 처럼 필요한 기능을 불러옴

// 안드로이드 api

import android.content.Intent;
// https://developer.android.com/reference/android/content/Intent

// https://developer.android.com/reference/android/os/Bundle
import android.os.Bundle;

import ch.threema.app.R;


// MainActivity은 추상 클래스 ThreemaAppCompatActivity를 계승(자식클래스가 됨)

// ThreemaAppCompatActivity은 AppCompatActivity 계승
// https://github.com/threema-ch/threema-android/blob/fe9bf2d286451fc824a061a6e2240c8d468fa033/app/src/main/java/ch/threema/app/activities/ThreemaAppCompatActivity.java

// AppCompatActivity은 안드로이드 API
// https://developer.android.com/reference/androidx/appcompat/app/AppCompatActivity

public class MainActivity extends ThreemaAppCompatActivity {

	// to avoid "class has no zero argument constructor" on some devices

  // 일부 디바이스에서 클래스에 인수가 없는 컨스트럭터 회피하기 위함
	public MainActivity() {}


	@Override
  // 오버라이드 선언
  // onCreate를 새로히 선언할 수 있도록
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);

		Intent intent = new Intent(this, HomeActivity.class);
		startActivity(intent);
		overridePendingTransition(R.anim.abc_fade_in, R.anim.abc_fade_out);
		finish();
	}
}
```
