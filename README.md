# Tel_SMS_Network
Collection of network checks, telephone and SMS usage.

# Telephone

```java
    //calls for the built in number dialer
    public void dial(View view) {
        if (hasNumber()) {
            telBroj = TEL_PREFIX + etBroj.getText().toString();
            Intent intent = new Intent(Intent.ACTION_DIAL, Uri.parse(telBroj));
            startActivity(intent);
        }
    }

    private boolean hasNumber() {
        if (etBroj.getText().length() == 0) {
            Toast.makeText(this, R.string.unesite_broj, Toast.LENGTH_SHORT).show();
            return false;
        }
        return true;
    }
    //make calls from the application, need permissions 
    public void call(View view) {
        if (hasNumber()) {

            if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
                ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.CALL_PHONE}, REQUEST_PERMISSION);

            } else {
                telBroj = TEL_PREFIX + etBroj.getText().toString();
                Intent intent = new Intent(Intent.ACTION_CALL);
                intent.setData(Uri.parse(telBroj));
                if (ActivityCompat.checkSelfPermission(MainActivity.this, Manifest.permission.CALL_PHONE) == PackageManager.PERMISSION_GRANTED) {
                    startActivity(intent);
                }
            }
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {

        switch (requestCode) {
            case 10:

                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    Intent callIntent = new Intent(Intent.ACTION_CALL);
                    callIntent.setData(Uri.parse(telBroj));
                    if (ActivityCompat.checkSelfPermission(MainActivity.this, Manifest.permission.CALL_PHONE) == PackageManager.PERMISSION_GRANTED) {
                        startActivity(callIntent);
                    }
                } else {
                    Toast.makeText(this, "You Deny permission", Toast.LENGTH_SHORT).show();
                    return;
                }
        }
    }
```
    
# SMS
    
```java
    //calls the SMS app on the phone
    public void prepareSMS(View view) {
        if (hasNumber() && hasText()) {
            Intent intent3 = new Intent(Intent.ACTION_VIEW);
            intent3.putExtra("address", etBroj.getText().toString());
            intent3.putExtra("sms_body", etPoruka.getText().toString());
            intent3.setType("vnd.android-dir/mms-sms");
            startActivity(intent3);
        }
    }

    private boolean hasText() {
        if (etPoruka.getText().length() == 0) {
            Toast.makeText(this, R.string.unesite_tekst_poruke, Toast.LENGTH_SHORT).show();
            return false;
        }
        return true;
    }
    //Sends SMS from the app, needs permission
    public void sendSMS(View view) {
        if (checkSelfPermission(Manifest.permission.SEND_SMS) == PackageManager.PERMISSION_DENIED) {
            requestPermissions(new String[]{Manifest.permission.SEND_SMS}, 1);
        }
        if (checkSelfPermission(Manifest.permission.SEND_SMS) == PackageManager.PERMISSION_GRANTED) {
            Toast.makeText(this, "Permission passed", Toast.LENGTH_SHORT).show();
            SmsManager sms = SmsManager.getDefault();
            sms.sendTextMessage(etBroj.getText().toString(), null, etPoruka.getText().toString(), null, null);
        }
    }
```
   
# Network
   
```java
   public void Internet(View view) {
        //network manager
        ConnectivityManager mobilnaMreza = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
        //get active network
        NetworkInfo aktivnaMreza = mobilnaMreza.getActiveNetworkInfo();
        if (aktivnaMreza != null && aktivnaMreza.getState() == NetworkInfo.State.CONNECTED) {
            //checks the active network type
            int vrstaMreze = aktivnaMreza.getType();
            switch (vrstaMreze) {
                //mobile network
                case (ConnectivityManager.TYPE_MOBILE):
                    Toast.makeText(getApplicationContext(), "Aktivna je mobilna mreža", Toast.LENGTH_SHORT).show();
                    //checks if roaming is on
                    if (aktivnaMreza.isRoaming()) {
                    }
                    break;
                //wireless connection
                case (ConnectivityManager.TYPE_WIFI):
                    Toast.makeText(getApplicationContext(), "Aktivna je bežična mreža", Toast.LENGTH_SHORT).show();
                    break;
            }
        } else {

            Toast.makeText(getApplicationContext(), "Internet veza nedostupna", Toast.LENGTH_SHORT).show();
        }
    }

    //tracks connection
    public void pracenjeStanjaMreze(View view) {
        registerReceiver(new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                Bundle paket = intent.getExtras();
                if (paket.getBoolean(ConnectivityManager.EXTRA_IS_FAILOVER)) {
                    //network issues
                    Toast.makeText(getApplicationContext(),"Problemi s mrežom",Toast.LENGTH_SHORT).show();
                }
                if (paket.getBoolean(ConnectivityManager.EXTRA_NO_CONNECTIVITY)) {
                    //no network connection
                    Toast.makeText(getApplicationContext(),"Uređaj nije spojen niti na jednu mrezu",Toast.LENGTH_SHORT).show();
                }
                if (paket.getStringArrayList(ConnectivityManager.EXTRA_REASON) != null) {
                    //network issues
                    Toast.makeText(getApplicationContext(),"Problem s mrežom",Toast.LENGTH_SHORT).show();
                }
            }

        }, new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION));
    }
```
    
