# FirebaseAuth-Yahoo-SignIn
Firebase Authentication via Yahoo Sign In 

# Getting Started
Follow these steps to continue...

1: Create Firebase Project or Connect your app to firebase
2: Go to Firebase Authentication Method
3: Setup Sign In Method 
4: Click on Yahoo (Enable it by Switch On)
5: Go to Yahoo.com or http:yahoo.com log in or create your yahoo mail id
6: Go to Developer Yahoo or http:developer.yahoo.com 
7: Click on Apps or directly go to http:developer.yahoo.com/apps
8: Click on Create App 
9: Fill the required info about your app as below
10:  

```java
package com.fireauth.auth;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import com.google.android.gms.tasks.OnFailureListener;
import com.google.android.gms.tasks.OnSuccessListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.auth.AuthResult;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.auth.FirebaseUser;
import com.google.firebase.auth.OAuthProvider;

import java.util.ArrayList;

public class AuthYahooActivity extends AppCompatActivity {

    private FirebaseAuth firebaseAuth;
    private Button buttonYahoo;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_auth_yahoo);

        firebaseAuth = FirebaseAuth.getInstance();
        buttonYahoo = findViewById(R.id.button_oauth);
        buttonYahoo.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                signInWithYahooAuthProvider(OAuthProvider.newBuilder("yahoo.com")
                        .addCustomParameter("prompt","login")
                        .addCustomParameter("language","en")
                        .setScopes(
                                new ArrayList<String>(){
                                    {
                                    //permission for email
                                    add("email");
                                    //permission for profile
                                    add("profile");

                                }}).build()

                );
            }
        });
    }

    private void signInWithYahooAuthProvider(OAuthProvider provider) {
        Task<AuthResult> taskAuthResult = firebaseAuth.getPendingAuthResult();
        if (taskAuthResult != null){
            taskAuthResult.addOnSuccessListener(new OnSuccessListener<AuthResult>() {
                @Override
                public void onSuccess(AuthResult authResult) {
                    Toast.makeText(getApplicationContext(), "Success", Toast.LENGTH_SHORT).show();
                }
            }).addOnFailureListener(new OnFailureListener() {
                @Override
                public void onFailure(@NonNull Exception e) {
                    Toast.makeText(getApplicationContext(), "Error:\n"+ e.getLocalizedMessage(), Toast.LENGTH_SHORT).show();
                }
            });
        } else {
            firebaseAuth.startActivityForSignInWithProvider(this, provider).addOnSuccessListener(new OnSuccessListener<AuthResult>() {
                @Override
                public void onSuccess(AuthResult authResult) {
                    FirebaseUser fu = firebaseAuth.getCurrentUser();
                    if (fu != null) {
                        Toast.makeText(getApplicationContext(), "Success Alert:\n" + fu.getEmail(), Toast.LENGTH_SHORT).show();
                    } else {
                        Toast.makeText(getApplicationContext(), "Success Alert, email not found", Toast.LENGTH_SHORT).show();
                    }
                }
            }).addOnFailureListener(new OnFailureListener() {
                @Override
                public void onFailure(@NonNull Exception e) {
                    Toast.makeText(getApplicationContext(), "Error Alert:\n"+e.getLocalizedMessage(), Toast.LENGTH_SHORT).show();
                }
            });
        }
    }
}
