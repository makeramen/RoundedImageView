RoundedImageView
================

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.makeramen/roundedimageview/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.makeramen/roundedimageview)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-RoundedImageView-brightgreen.svg?style=flat)](https://android-arsenal.com/details/1/680)

A fast ImageView (and Drawable) that supports rounded corners (and ovals or circles) based on the original [example from Romain Guy](http://www.curious-creature.org/2012/12/11/android-recipe-1-image-with-rounded-corners/). It supports many additional features including ovals, rounded rectangles, ScaleTypes and TileModes.

![RoundedImageView screenshot](https://raw.github.com/makeramen/RoundedImageView/master/screenshot.png)
![RoundedImageView screenshot with ovals](https://raw.github.com/makeramen/RoundedImageView/master/screenshot-oval.png)

There are many ways to create rounded corners in android, but this is the fastest and best one that I know of because it:
* does **not** create a copy of the original bitmap
* does **not** use a clipPath which is not hardware accelerated and not anti-aliased.
* does **not** use setXfermode to clip the bitmap and draw twice to the canvas.

If you know of a better method, let me know (or even better open a pull request)!

Also has proper support for:
* Borders (with Colors and ColorStateLists)
* Ovals and Circles
* All `ScaleType`s
  * Borders are drawn at view edge, not bitmap edge
  * Except on edges where the bitmap is smaller than the view
  * Borders are **not** scaled up/down with the image (correct width and radius are maintained)
* Anti-aliasing
* Transparent backgrounds
* Hardware acceleration
* Support for LayerDrawables (including TransitionDrawables)
* TileModes for repeating drawables

Known Issues
----
- VectorDrawables are **not** supported. This library is designed for BitmapDrawables only. Other drawables will likely fail or cause high memory usage. 
- ColorDrawables are poorly supported, use your own rounded VectorDrawables instead if you want less memory pressure.
- Glide transforms are **not** supported, please use [wasabeef/glide-transformations](https://github.com/wasabeef/glide-transformations) if you want to round images loaded from Glide.

Gradle
----
RoundedImageView is available in [Maven Central](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.makeramen%22%20AND%20a%3A%22roundedimageview%22).

Add the following to your `build.gradle` to use:
```
repositories {
    mavenCentral()
}

dependencies {
    compile 'com.makeramen:roundedimageview:2.3.0'
}
```


Usage
----
Define in xml:

```xml
<com.makeramen.roundedimageview.RoundedImageView
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:id="@+id/imageView1"
        android:src="@drawable/photo1"
        android:scaleType="fitCenter"
        app:riv_corner_radius="30dip"
        app:riv_border_width="2dip"
        app:riv_border_color="#333333"
        app:riv_mutate_background="true"
        app:riv_tile_mode="repeat"
        app:riv_oval="true" />
```

Or in code:

```java
RoundedImageView riv = new RoundedImageView(context);
riv.setScaleType(ScaleType.CENTER_CROP);
riv.setCornerRadius((float) 10);
riv.setBorderWidth((float) 2);
riv.setBorderColor(Color.DKGRAY);
riv.mutateBackground(true);
riv.setImageDrawable(drawable);
riv.setBackground(backgroundDrawable);
riv.setOval(true);
riv.setTileModeX(Shader.TileMode.REPEAT);
riv.setTileModeY(Shader.TileMode.REPEAT);
```

With Android Databinding (https://developer.android.com/tools/data-binding/guide.html):

Two way databinding example (resources omited):

Fragment:
```java
package de.kashban.android.examples.rivdatabinding;

import android.databinding.BindingConversion;
import android.databinding.BindingMethod;
import android.databinding.BindingMethods;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

import de.kashban.android.examples.rivdatabinding.R;
import de.kashban.android.examples.rivdatabinding.databinding.MyLayoutBinding;
import de.kashban.android.examples.rivdatabinding.entities.ViewModel;


// Rename binding methods because xml attributes are not named equally after setters
// Add more methods to change other attributes through databinding, for example setBackground(Drawable drawable).
@BindingMethods({
        @BindingMethod(type = com.makeramen.roundedimageview.RoundedImageView.class,
                attribute = "app:riv_oval",
                method = "setOval"),
        @BindingMethod(type = com.makeramen.roundedimageview.RoundedImageView.class,
            attribute = "app:riv_corner_radius",
            method = "setCornerRadius"),
        @BindingMethod(type = com.makeramen.roundedimageview.RoundedImageView.class,
            attribute = "app:riv_border_width",
            method = "setBorderWidth")
})


public class FragmentLayout extends Fragment {

    public FragmentCalendarLayout() {}

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View inflatedView = inflater.inflate(R.layout.my_layout, container, false);
        // Create a new binding and bind it to the view (*Binding has been created by annotation processor beforehand)
        MyLayoutBinding binding = MyLayoutBinding.bind(inflatedView);
        
        // The viewmodel class 
        ViewModel model = new ViewModel();
        // Do whatever it takes to initialize the viewmodel. Here some stuff is load from SharedPreferences.
        model.initFromPrefs(getActivity());
        // And set the 
        binding.setModel(model);
        return inflatedView;
    }

    // Some on the fly databinding conversions for convenience
    @BindingConversion
    public static String convertIntToString(int value) {
        String text = ""+value;
        return text;
    }

    @BindingConversion
    public static Float convertIntToFloat(int value) {
        return (float)value;
    }
}
```

Layout
```xml
<?xml version="1.0" encoding="UTF-8"?>

<layout xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable name="model" type="de.kashban.android.examples.rivdatabinding.entities.ViewModel"/>
    </data>

                <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="wrap_content"
                    android:id="@+id/lyt_PrefsFrameContent"
                    android:paddingStart="@dimen/material_spacing"
                    android:paddingEnd="@dimen/material_spacing"
                    android:paddingTop="@dimen/material_spacing"
                    android:paddingRight="@dimen/material_spacing"
                    android:paddingLeft="@dimen/material_spacing">

                    <com.makeramen.roundedimageview.RoundedImageView
                        android:id="@+id/ivRoundedImagePreview"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:adjustViewBounds="true"
                        android:contentDescription="@string/confImageDesc"
                        android:scaleType="fitCenter"
                        android:src="@drawable/blue_window"
                        android:visibility="visible"
                        app:riv_corner_radius="@{model.roundedCorners}"
                        app:riv_mutate_background="true"
                        app:riv_oval="@{model.enableOval}"
                        app:riv_border_width="@{(float)model.frameWidth}"
                        app:riv_border_color="@color/grey_800">
                    </com.makeramen.roundedimageview.RoundedImageView>
                    <android.support.v7.widget.SwitchCompat
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/prefsPicFrameOval"
                        android:id="@+id/swFrameOval"
                        android:layout_marginBottom="@dimen/material_spacing"
                        android:checked="@{model.enableOval}"
                        app:onCheckedChangeListener="@{model.ovalStateListener}"/>

                    <LinearLayout
                        android:orientation="horizontal"
                        android:layout_width="fill_parent"
                        android:layout_height="fill_parent">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="match_parent"
                            android:text="@string/prefsPicFrameRoundedCorner"
                            android:id="@+id/tvRounding"
                            android:gravity="center_vertical"
                            android:minWidth="130dp"/>

                        <SeekBar
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:id="@+id/sbRoundedCorner"
                            android:progress="@{model.roundedCorners}"
                            android:max="50"
                            android:layout_weight="1"
                            app:onSeekBarChangeListener="@{model.onRoundedCornerChangeListener}"
                            android:enabled="@{!model.enableOval}"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="match_parent"
                            android:textAppearance="?android:attr/textAppearanceSmall"
                            android:text="@{``+model.roundedCorners}"
                            android:id="@+id/tvRoundedCornerValue"
                            android:layout_gravity="end"
                            android:gravity="center_vertical"
                            android:minWidth="20dp"/>

                    </LinearLayout>
                    <LinearLayout
                        android:orientation="horizontal"
                        android:layout_width="fill_parent"
                        android:layout_height="fill_parent"
                        android:layout_marginBottom="@dimen/default_margin">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="match_parent"
                            android:text="@string/prefsPicFrameWidth"
                            android:id="@+id/tvFrameWidth"
                            android:gravity="center_vertical"
                            android:minWidth="130dp"/>

                        <SeekBar
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:id="@+id/sbFrameWidth"
                            android:progress="@{model.frameWidth}"
                            android:max="20"
                            android:layout_weight="1"
                            app:onSeekBarChangeListener="@{model.onFrameWidthChangeListener}"/>

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="match_parent"
                            android:textAppearance="?android:attr/textAppearanceSmall"
                            android:text="@{``+model.frameWidth}"
                            android:id="@+id/tvFrameWidthValue"
                            android:layout_gravity="end"
                            android:gravity="center_vertical"
                            android:minWidth="20dp"/>

                    </LinearLayout>
                </LinearLayout>

</layout>
```

ViewModel class:

```java
package de.kashban.android.examples.rivdatabinding.entities;

import android.app.Activity;
import android.content.Context;
import android.content.SharedPreferences;
import android.databinding.ObservableBoolean;
import android.databinding.ObservableDouble;
import android.databinding.ObservableField;
import android.databinding.ObservableInt;
import android.graphics.drawable.Drawable;
import android.preference.PreferenceManager;
import android.support.v7.widget.SwitchCompat;
import android.view.View;
import android.widget.AdapterView;
import android.widget.CompoundButton;
import android.widget.SeekBar;
import android.widget.Spinner;

import org.json.JSONException;
import org.json.JSONObject;

import de.kashban.android.examples.rivdatabinding.R;


/**
 * Created by jwahlmann on 11.06.2015.
 *
 * Pojo to hold RIV parameters
 */
public class ViewModel {

// Bound properties
    public final ObservableBoolean enableOval = new ObservableBoolean();
    public final ObservableInt roundedCorners = new ObservableInt();
    public final ObservableInt frameWidth = new ObservableInt();
    private Context mPrefsContext;
    private Activity mActivity;

    public void initFromPrefs(Activity activity)
    {
        mActivity = activity;
        mPrefsContext = activity.getApplicationContext();
		      SharedPreferences mDefaultPrefs = PreferenceManager.getDefaultSharedPreferences(mPrefsContext);
        String rivJSON = mDefaultPrefs.getString(Constants.PREFS_RIV, "");
        // Parse
        try {
            JSONObject riv = new JSONObject(rivJSON);
            enableOval.set(riv.getBoolean(Constants.JSON_KEY_OVAL));
            roundedCorners.set(riv.getInt(Constants.JSON_KEY_ROUNDEDCORNER));
            frameWidth.set(riv.getInt(Constants.JSON_KEY_FRAMEWIDTH));
        } catch (JSONException e) {
            e.printStackTrace();
        }

    }

    /**
     * Save current design to prefs
     */
    public void saveToPrefs()
    {
        SharedPreferences mDefaultPrefs = PreferenceManager.getDefaultSharedPreferences(mPrefsContext);
        SharedPreferences.Editor edit = mDefaultPrefs.edit();

        String rivJSON = mDefaultPrefs.getString(Constants.PREFS_RIV, "");
        // Construct
        try {
            JSONObject riv = new JSONObject();
            riv.put(Constants.JSON_KEY_OVAL, enableOval.get());
            riv.put(Constants.JSON_KEY_ROUNDEDCORNER, roundedCorners.get());
            riv.put(Constants.JSON_KEY_FRAMEWIDTH, frameWidth.get());
            edit.putString(Constants.PREFS_RIV, riv.toString(2));
        } catch (JSONException e) {
            e.printStackTrace();
        }
        edit.apply();
    }


    // This has to be done manually, Android Databinding is basically a one way binding right now.
    public SwitchCompat.OnCheckedChangeListener getOvalStateListener() {

        return new SwitchCompat.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean state) {
                enableOval.set(state);
            }
        };
    }

    public SeekBar.OnSeekBarChangeListener getOnRoundedCornerChangeListener(){
        return new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                roundedCorners.set(progress);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                //mRivPicture.setCornerRadius(seekBar.getProgress());
            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                //mRivPicture.setCornerRadius(seekBar.getProgress());
            }
        };
    }

    public SeekBar.OnSeekBarChangeListener getOnFrameWidthChangeListener(){
        return new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                frameWidth.set(progress);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                //mRivPicture.setCornerRadius(seekBar.getProgress());
            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                //mRivPicture.setCornerRadius(seekBar.getProgress());
            }
        };
    }

}
```

Or make a Transformation for Picasso:

```java
Transformation transformation = new RoundedTransformationBuilder()
          .borderColor(Color.BLACK)
          .borderWidthDp(3)
          .cornerRadiusDp(30)
          .oval(false)
          .build();

Picasso.with(context)
    .load(url)
    .fit()
    .transform(transformation)
    .into(imageView);
```

Changelog
----------
see [Releases](https://github.com/vinc3m1/RoundedImageView/releases)




License
-------

    Copyright 2017 Vincent Mi

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
