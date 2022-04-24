# Challenge Admin Panel

> En attente de l'intitulé du challenge

## 1) Download the APK and decompile it

The file to download was AdminPanel.apk which is the file format of Android applications.

Once I've downloaded the file I decompiled it using [jadx](https://github.com/skylot/jadx).

![jadx](images/jadx.jpg)

## 2) Find the interesting code 

To access the interesting code, I went to com.example.login_page.p005ui.adminpanel.data.model

There are 3 files :

![login_files](images/login_files.jpg)

In LoginDataSource there are 2 methods which are used to check if the password of the Admin is correct :

```java
package com.example.login_page.p005ui.adminpanel.data;

import com.example.login_page.p005ui.adminpanel.data.Result;
import com.example.login_page.p005ui.adminpanel.data.model.LoggedInUser;
import java.io.IOException;
import java.util.UUID;

/* renamed from: com.example.login_page.ui.adminpanel.data.LoginDataSource */
/* loaded from: classes.dex */
public class LoginDataSource {
    public void logout() {
    }

    public Result<LoggedInUser> login(String str) {
        try {
            return m1016a(str).equals("3456614A653572655665525233496D3372506E304D") ? new Result.Success(new LoggedInUser(UUID.randomUUID().toString(), "Admin")) : new Result.Error(new Exception("blep"));
        } catch (Exception e) {
            return new Result.Error(new IOException("Error logging in", e));
        }
    }

    /* renamed from: a */
    public String m1016a(String str) {
        String str2 = "";
        for (int length = str.length(); length > 0; length--) {
            str2 = str2 + String.format("%H", Character.valueOf(str.charAt(length - 1)));
        }
        return str2;
    }
}
```
## 3) Get the flag

To understand how it works I decided to modify login() and m1016a() in Intellij :

```java


import java.io.IOException;
import java.util.UUID;


public class LoginDataSource {
    public static void main(String[] args) {
        System.out.println(new LoginDataSource().login("1234"));
    }

    public boolean login(String password) {
                                     
        return m1016a(password).equals("3456614A653572655665525233496D3372506E304D");

    }
    public String m1016a(String str) {
        String str2 = "";
        for (int length = str.length(); length > 0; length--) {

            str2 = str2 + String.format("%h", Character.valueOf(str.charAt(length - 1)));
            System.out.println(str2);
        }
        return str2;
    }
}

```
Then I tried to find the password with m1016a() : it hashes character per character of a reversed string.

For example the hash of "abc" using the method :

str2 = ""
str2 = "63"
str2 = "6362"
str2 = "636261"

So I used an ASCII table to decode the password.

![ascii_table](https://naveenr.net/content/images/2017/03/ascii-codes.gif)

3456614A653572655665525233496D3372506E304D becomes : 4vaJe5reVeRR3Im3rPn0M 

I reversed it and here is the flag : MCTF{M0nPr3mI3RReVer5eJav4}

