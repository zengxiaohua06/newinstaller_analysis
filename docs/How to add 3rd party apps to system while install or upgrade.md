# Method 1 within busybox( not established )

creat a shell script /system/bin/install_apk.sh
```bash
  #!/system/bin/sh
  busybox cp /data/3rdPartyApp/AngryBirds.apk data/app
  busybox cp /data/3rdPartyApp/Skype.apk data/app
  busybox cp /data/3rdPartyApp/Twitter.apk data/app
  chmod 777 data/app/AngryBirds.apk
  chmod 777 data/app/Skype.apk
  chmod 777 data/app/Twitter.apk
```
modify init.android_x86_64.rc to add start service
```sh
  service install_apk /system/bin/install_apk.sh
  class main
  oneshot
```

# Method 2 within android

Run a service to check if there are /data/3rdPartyApp/*.apk.
If *.apk found, run pm to install it
