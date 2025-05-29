ComunicaVizinhanca/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/comunicavizinhanca/
│   │   │   │   ├── activities/
│   │   │   │   ├── adapters/
│   │   │   │   ├── fragments/
│   │   │   │   ├── models/
│   │   │   │   ├── services/
│   │   │   │   └── utils/
│   │   │   ├── res/
│   │   │   └── AndroidManifest.xml
│   ├── build.gradle
├── .gitignore
├── build.gradle
└── settings.gradle
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'com.google.gms.google-services'
}

android {
    compileSdkVersion 33
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "com.example.comunicavizinhanca"
        minSdkVersion 21
        targetSdkVersion 33
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    buildFeatures {
        viewBinding true
    }
}

dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib:1.8.0"
    implementation 'androidx.core:core-ktx:1.9.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.8.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.navigation:navigation-fragment-ktx:2.5.3'
    implementation 'androidx.navigation:navigation-ui-ktx:2.5.3'
    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    
    // Firebase
    implementation platform('com.google.firebase:firebase-bom:32.0.0')
    implementation 'com.google.firebase:firebase-analytics'
    implementation 'com.google.firebase:firebase-auth'
    implementation 'com.google.firebase:firebase-database'
    implementation 'com.google.firebase:firebase-storage'

    // Google Maps
    implementation 'com.google.android.gms:play-services-maps:18.1.0'
    implementation 'com.google.android.gms:play-services-location:21.0.1'
    
    // Utilitários
    implementation 'com.github.bumptech.glide:glide:4.12.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.12.0'
}<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.comunicavizinhanca">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.CALL_PHONE" />

    <application
        android:name=".App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.ComunicaVizinhanca">
        
        <activity
            android:name=".activities.LoginActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        
        <activity android:name=".activities.MainActivity" />
        <activity android:name=".activities.CreatePostActivity" />
        <activity android:name=".activities.CreateEventActivity" />
        
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="SUA_CHAVE_API_GOOGLE_MAPS" />
    </application>
</manifest>
package com.example.comunicavizinhanca.models

data class User(
    val id: String = "",
    val name: String = "",
    val email: String = "",
    val phone: String = "",
    val isServiceProvider: Boolean = false,
    val services: List<String> = emptyList()
)package com.example.comunicavizinhanca.models

data class CommunityPost(
    var id: String = "",
    val title: String = "",
    val content: String = "",
    val authorId: String = "",
    val timestamp: Long = System.currentTimeMillis(),
    val category: String = "Geral"
)package com.example.comunicavizinhanca.models

data class Complaint(
    var id: String = "",
    val title: String = "",
    val description: String = "",
    val category: String = "",
    val timestamp: Long = System.currentTimeMillis(),
    val anonymous: Boolean = true
)package com.example.comunicavizinhanca.fragments

import android.content.Intent
import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment
import androidx.recyclerview.widget.LinearLayoutManager
import com.example.comunicavizinhanca.activities.CreatePostActivity
import com.example.comunicavizinhanca.adapters.PostAdapter
import com.example.comunicavizinhanca.databinding.FragmentHomeBinding
import com.example.comunicavizinhanca.models.CommunityPost
import com.example.comunicavizinhanca.services.FirebaseDatabaseHelper
import com.google.firebase.database.DataSnapshot
import com.google.firebase.database.DatabaseError
import com.google.firebase.database.ValueEventListener

class HomeFragment : Fragment() {
    
    private lateinit var binding: FragmentHomeBinding
    private lateinit var postAdapter: PostAdapter
    private val postList = mutableListOf<CommunityPost>()
    
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentHomeBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        setupRecyclerView()
        loadPosts()
        
        binding.fabAddPost.setOnClickListener {
            startActivity(Intent(requireContext(), CreatePostActivity::class.java))
        }
    }
    
    private fun setupRecyclerView() {
        postAdapter = PostAdapter(postList)
        binding.recyclerViewPosts.apply {
            layoutManager = LinearLayoutManager(requireContext())
            adapter = postAdapter
        }
    }
    
    private fun loadPosts() {
        FirebaseDatabaseHelper.postsRef
            .orderByChild("timestamp")
            .addValueEventListener(object : ValueEventListener {
                override fun onDataChange(snapshot: DataSnapshot) {
                    postList.clear()
                    for (postSnapshot in snapshot.children) {
                        val post = postSnapshot.getValue(CommunityPost::class.java)
                        post?.let { postList.add(it) }
                    }
                    postList.sortByDescending { it.timestamp }
                    postAdapter.notifyDataSetChanged()
                }
                
                override fun onCancelled(error: DatabaseError) {
                    // Tratar erro
                }
            })
    }
}package com.example.comunicavizinhanca.services

import com.google.firebase.database.FirebaseDatabase

object FirebaseDatabaseHelper {
    private val database = FirebaseDatabase.getInstance()
    
    val usersRef = database.getReference("users")
    val postsRef = database.getReference("posts")
    val servicesRef = database.getReference("services")
    val eventsRef = database.getReference("events")
    val complaintsRef = database.getReference("complaints")
    
    fun addPost(post: CommunityPost, onComplete: (Boolean) -> Unit) {
        val key = postsRef.push().key
        key?.let {
            post.id = it
            postsRef.child(it).setValue(post)
                .addOnSuccessListener { onComplete(true) }
                .addOnFailureListener { onComplete(false) }
        }
    }
    
    fun addComplaint(complaint: Complaint, onComplete: (Boolean) -> Unit) {
        val key = complaintsRef.push().key
        key?.let {
            complaint.id = it
            complaintsRef.child(it).setValue(complaint)
                .addOnSuccessListener { onComplete(true) }
                .addOnFailureListener { onComplete(false) }
        }
    }
}
