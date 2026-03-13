# Admin & User Product App (Firebase Example)

A simple Android project demonstrating how an **Admin App inserts product data** and a **User App displays the products in real time** using Firebase Realtime Database.

---

# 🚀 Features

### Admin App

* Add product name
* Add product price
* Upload product to Firebase

### User App

* Fetch products from Firebase
* Display products using RecyclerView
* Real-time updates

---

# 🧰 Tech Stack

* Java (Android)
* XML Layout
* RecyclerView
* Firebase Realtime Database
* CardView (Material UI)

---

# 📂 Project Structure

```
AdminApp
 ├── MainActivity.java
 ├── activity_main.xml

UserApp
 ├── MainActivity.java
 ├── Product.java
 ├── ProductAdapter.java
 ├── activity_main.xml
 ├── product_item.xml
```

# 🧑‍💻 Admin App

## activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="20dp"
    android:orientation="vertical"
    tools:context=".MainActivity">


    <EditText
        android:id="@+id/productName"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:hint="Product Name" />

    <EditText
        android:id="@+id/productPrice"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:layout_marginTop="10dp"
        android:hint="Price" />

    <Button
        android:id="@+id/addProduct"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:layout_marginTop="10dp"
        android:text="Add Product" />


</LinearLayout>
```

---

## MainActivity.java

```java
package com.myeamin.adminapp;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;

import java.util.HashMap;

public class MainActivity extends AppCompatActivity {

    EditText name, price;
    Button addBtn;
    DatabaseReference database;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        name = findViewById(R.id.productName);
        price = findViewById(R.id.productPrice);
        addBtn = findViewById(R.id.addProduct);

        database = FirebaseDatabase.getInstance().getReference("products");

        addBtn.setOnClickListener(v -> {

            String n = name.getText().toString();
            String p = price.getText().toString();

            String id = database.push().getKey();

            HashMap<String,Object> map = new HashMap<>();
            map.put("name", n);
            map.put("price", p);

            database.child(id).setValue(map);

            Toast.makeText(this,"Product Added",Toast.LENGTH_SHORT).show();
        });
    }
}
```

---

# 👤 User App

## activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">


    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/productList"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
    
</LinearLayout>
```

---

## Product Model

```java
public class Product {

    String name;
    String price;

    public Product(){}

    public String getName() {
        return name;
    }

    public String getPrice() {
        return price;
    }
}
```

---

## product_item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    card_view:cardBackgroundColor="#263238"
    card_view:cardCornerRadius="12dp"
    card_view:cardElevation="6dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:id="@+id/name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Product Name"
            android:textColor="#FFFFFF"
            android:textSize="18sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/price"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="6dp"
            android:text="Price: 120 BDT"
            android:textColor="#CFD8DC"
            android:textSize="16sp" />


    </LinearLayout>

</androidx.cardview.widget.CardView>
```

---

## ProductAdapter.java

```java
public class ProductAdapter extends RecyclerView.Adapter<ProductAdapter.ViewHolder>{

    Context context;
    ArrayList<Product> list;

    public ProductAdapter(Context context, ArrayList<Product> list){
        this.context = context;
        this.list = list;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {

        View view = LayoutInflater.from(context)
                .inflate(R.layout.product_item,parent,false);

        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {

        Product product = list.get(position);

        holder.name.setText(product.getName());
        holder.price.setText("Price: " + product.getPrice() + " BDT");
    }

    @Override
    public int getItemCount() {
        return list.size();
    }

    public class ViewHolder extends RecyclerView.ViewHolder{

        TextView name,price;

        public ViewHolder(View itemView){
            super(itemView);

            name = itemView.findViewById(R.id.name);
            price = itemView.findViewById(R.id.price);
        }
    }
}
```

---

## User MainActivity.java

```java
package com.myeamin.userapp;

import android.os.Bundle;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    RecyclerView recyclerView;
    ArrayList<Product> list;
    ProductAdapter adapter;
    DatabaseReference database;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });
        recyclerView = findViewById(R.id.productList);

        recyclerView.setLayoutManager(new LinearLayoutManager(this));

        list = new ArrayList<>();

        adapter = new ProductAdapter(this,list);
        recyclerView.setAdapter(adapter);

        database = FirebaseDatabase.getInstance().getReference("products");

        database.addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(DataSnapshot snapshot) {

                list.clear();

                for(DataSnapshot data : snapshot.getChildren()){

                    Product product = data.getValue(Product.class);
                    list.add(product);
                }

                adapter.notifyDataSetChanged();
            }

            @Override
            public void onCancelled(DatabaseError error) {

            }
        });
    }
}
```
