# 📱 Lab 5 – ConverterTabsJava

## 🎯 Objectif général

Réaliser une application Android avec deux onglets :

* Conversion de température (°C ↔ °F)
* Conversion de distance (Km ↔ Miles)

Fonctionnalités supplémentaires :

* Menu quitter
* Confirmation avant fermeture de l’application

---

## 🧩 Étape 1 — Création du projet

Créer un projet Android Studio :

* Nom : **ConverterTabsJava**
* Type : Empty Views Activity
* Langage : Java
* Minimum SDK : API 24

---

## 🧩 Étape 2 — Dépendances

```gradle
dependencies {
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.viewpager2:viewpager2:1.0.0'
}
```

---

## 🧩 Étape 3 — Interface principale

### 📄 activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabIndicatorFullWidth="false"
        app:tabMode="fixed"/>

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```

---

## 🧩 Étape 4 — MainActivity

```java
package com.example.converttabsjava;

import androidx.appcompat.app.AppCompatActivity;
import androidx.viewpager2.widget.ViewPager2;
import android.os.Bundle;
import androidx.appcompat.app.AlertDialog;
import com.google.android.material.tabs.TabLayout;
import com.google.android.material.tabs.TabLayoutMediator;

public class MainActivity extends AppCompatActivity {

    TabLayout tabLayout;
    ViewPager2 viewPager;
    ViewPagerAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tabLayout = findViewById(R.id.tabLayout);
        viewPager = findViewById(R.id.viewPager);

        adapter = new ViewPagerAdapter(this);
        viewPager.setAdapter(adapter);

        new TabLayoutMediator(tabLayout, viewPager,
                (tab, position) -> tab.setText(position == 0 ? "Température" : "Distance")
        ).attach();
    }

    @Override
    public void onBackPressed() {
        new AlertDialog.Builder(this)
                .setTitle("Quitter")
                .setMessage("Voulez-vous vraiment quitter l'application ?")
                .setPositiveButton("Oui", (dialog, which) -> finish())
                .setNegativeButton("Non", null)
                .show();
    }
}
```

---

## 🧩 Étape 5 — Adapter

```java
package com.example.converttabsjava;

import androidx.annotation.NonNull;
import androidx.fragment.app.Fragment;
import androidx.fragment.app.FragmentActivity;
import androidx.viewpager2.adapter.FragmentStateAdapter;

public class ViewPagerAdapter extends FragmentStateAdapter {

    public ViewPagerAdapter(@NonNull FragmentActivity fa) {
        super(fa);
    }

    @NonNull
    @Override
    public Fragment createFragment(int position) {
        if (position == 0)
            return new TempFragment();
        else
            return new DistanceFragment();
    }

    @Override
    public int getItemCount() {
        return 2;
    }
}
```

---

## 🌡️ Étape 6 — Fragment Température

### 📄 fragment_temp.xml

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:padding="16dp"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <TextView
        android:text="Conversion Celsius ↔ Fahrenheit"
        android:textStyle="bold" android:textSize="18sp"
        android:layout_width="wrap_content" android:layout_height="wrap_content"/>

    <RadioGroup android:id="@+id/rgTemp"
        android:orientation="horizontal"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <RadioButton android:id="@+id/rbCtoF" android:text="C → F" android:checked="true"/>
        <RadioButton android:id="@+id/rbFtoC" android:text="F → C" android:layout_marginStart="16dp"/>
    </RadioGroup>

    <EditText android:id="@+id/etTempInput"
        android:hint="Entrer la valeur"
        android:inputType="numberDecimal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

    <Button android:id="@+id/btnConvertTemp"
        android:text="Calculer"
        android:layout_width="match_parent" android:layout_height="wrap_content"/>

    <TextView android:id="@+id/tvTempResult"
        android:text="Résultat : -" android:textSize="16sp"
        android:layout_marginTop="8dp"
        android:layout_width="wrap_content" android:layout_height="wrap_content"/>
</LinearLayout>
```

### 📄 TempFragment.java

```java
package com.example.converttabsjava;

import android.os.Bundle;
import android.text.TextUtils;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.*;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class TempFragment extends Fragment {

    RadioGroup rgTemp;
    RadioButton rbCtoF, rbFtoC;
    EditText etTempInput;
    Button btnConvertTemp;
    TextView tvTempResult;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater,
                             @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {

        View view = inflater.inflate(R.layout.fragment_temp, container, false);

        rgTemp = view.findViewById(R.id.rgTemp);
        rbCtoF = view.findViewById(R.id.rbCtoF);
        rbFtoC = view.findViewById(R.id.rbFtoC);
        etTempInput = view.findViewById(R.id.etTempInput);
        btnConvertTemp = view.findViewById(R.id.btnConvertTemp);
        tvTempResult = view.findViewById(R.id.tvTempResult);

        btnConvertTemp.setOnClickListener(v -> {
            String input = etTempInput.getText().toString();
            if (TextUtils.isEmpty(input)) {
                Toast.makeText(getContext(), "Veuillez entrer une valeur", Toast.LENGTH_SHORT).show();
                return;
            }

            double val = Double.parseDouble(input);
            double result;

            if (rbCtoF.isChecked())
                result = (1.8 * val) + 32;
            else
                result = (val - 32) / 1.8;

            tvTempResult.setText("Résultat : " + String.format("%.2f", result));
        });

        return view;
    }
}
```

---

## 📏 Étape 7 — Fragment Distance

### 📄 fragment_distance.xml

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:padding="16dp"
    android:layout_width="match_parent" android:layout_height="match_parent">

    <TextView
        android:text="Conversion Km ↔ Miles"
        android:textStyle="bold" android:textSize="18sp"
        android:layout_width="wrap_content" android:layout_height="wrap_content"/>

    <RadioGroup android:id="@+id/rgDist"
        android:orientation="horizontal"
        android:layout_width="match_parent" android:layout_height="wrap_content">
        <RadioButton android:id="@+id/rbKmToMiles" android:text="Km → Miles" android:checked="true"/>
        <RadioButton android:id="@+id/rbMilesToKm" android:text="Miles → Km" android:layout_marginStart="16dp"/>
    </RadioGroup>

    <EditText android:id="@+id/etDistInput"
        android:hint="Entrer la valeur"
        android:inputType="numberDecimal"
        android:layout_width="match_parent" android:layout_height="wrap_content"/>

    <Button android:id="@+id/btnConvertDist"
        android:text="Calculer"
        android:layout_width="match_parent" android:layout_height="wrap_content"/>

    <TextView android:id="@+id/tvDistResult"
        android:text="Résultat : -" android:textSize="16sp"
        android:layout_marginTop="8dp"
        android:layout_width="wrap_content" android:layout_height="wrap_content"/>
</LinearLayout>
```

### 📄 DistanceFragment.java

```java
package com.example.converttabsjava;

import android.os.Bundle;
import android.text.TextUtils;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.*;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class DistanceFragment extends Fragment {

    RadioGroup rgDist;
    RadioButton rbKmToMiles, rbMilesToKm;
    EditText etDistInput;
    Button btnConvertDist;
    TextView tvDistResult;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater,
                             @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_distance, container, false);

        rgDist = view.findViewById(R.id.rgDist);
        rbKmToMiles = view.findViewById(R.id.rbKmToMiles);
        rbMilesToKm = view.findViewById(R.id.rbMilesToKm);
        etDistInput = view.findViewById(R.id.etDistInput);
        btnConvertDist = view.findViewById(R.id.btnConvertDist);
        tvDistResult = view.findViewById(R.id.tvDistResult);

        btnConvertDist.setOnClickListener(v -> {
            String input = etDistInput.getText().toString();
            if (TextUtils.isEmpty(input)) {
                Toast.makeText(getContext(), "Veuillez entrer une valeur", Toast.LENGTH_SHORT).show();
                return;
            }

            double val = Double.parseDouble(input);
            double result;

            if (rbKmToMiles.isChecked())
                result = val * 0.6214;
            else
                result = val / 0.6214;

            tvDistResult.setText("Résultat : " + String.format("%.2f", result));
        });

        return view;
    }
}
```

---

## ✅ Résultat attendu

* Onglet Température : 25°C → 77°F
* Onglet Distance : 10 Km → 6.21 Miles
* Bouton retour : affiche une boîte de confirmation

---

## 🧠 Conclusion

Ce projet permet de comprendre :

* L’utilisation des **Fragments**
* Navigation avec **TabLayout + ViewPager2**
* Gestion des événements utilisateur
* Manipulation des entrées utilisateur et affichage des résultats
