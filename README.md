<img src="http://i.hizliresim.com/zrM9Mj.png" align="left" />
# RecordPhoneCalls
> It's a simple application to record and save your incoming or outgoing phone calls.It also contains Read Contacts of user, basic ListView example, using Shared Preferences and Folder works in sd card.
<br><br><br>

###Screenshots
<p align="center">
<img src="http://i.hizliresim.com/7MWgJm.png"/>
</p>

##How it works

This app has some steps to make. I'll write this below like I made.

## Step 1 : Shared Preferences

I used shared preferences to save user's choice. This choices may be Listen / Not Listen so Phone Listener service will work with respect to this choice.The method getState(), checks users choice via shared preferences below.

    private void getState()
    {
        if (sharedpreferences.contains(State))
        {
            state.setText(sharedpreferences.getString(State, ""));
            durum = state.getText().toString();
            if (durum.equals("Listening..."))
            {
                imgbtn.setImageResource(R.drawable.stop);
            }
            else
            {
                imgbtn.setImageResource(R.drawable.microphone);
            }
        }
        else
        {
            durum = "false";
            state.setText("Listening...");
            imgbtn.setImageResource(R.drawable.microphone);
        }
    }
User clicks listen/not listen button (First Screenshot), and via this choice CallListener service will work or stop. Don't forget! You must save this choice with shared preferences to show to user next time.

        Intent intent = new Intent(getActivity(), CallDetectService.class);
        SharedPreferences.Editor editor = sharedpreferences.edit();
        if (!durum.equals("Listening..."))
        {
          editor.putString(State, "Listening...");
          state.setText("Listening...");
          imgbtn.setImageResource(R.drawable.stop);
          ****getActivity().startService(intent)****;
        }
        else
        {
         editor.putString(State, "Not Listening...");
         state.setText("Not Listening...");
         imgbtn.setImageResource(R.drawable.microphone);
         ****getActivity().stopService(intent)*****;
        }

## Step 2 : Creating Call Detect Service

In my application, after service started , service call some class CallHelper which has PhoneStateListener class to listen  phone state. I considered three statement which is given below.

    private class CallStateListener extends PhoneStateListener {
        @Override
        public void onCallStateChanged(int state, String incomingNumber) {
            switch (state) {
                case TelephonyManager.CALL_STATE_RINGING:
                    /*HERE! PHONE IS RINGING!*/
                    break;
                case TelephonyManager.CALL_STATE_IDLE:
                    /*PHONE IDLE*/
                    break;
                case TelephonyManager.CALL_STATE_OFFHOOK:
                    /*PHONE CALL ANSWERED*/
                    break;
            }
        }
    }

## Step 3: Prepare yourself to recording!

The record will start after CALL_STATE_OFFHOOK but you must prepare something like FileName. It's completely optional. I wanted to save file with good name.So I created a method which works get incoming/outgoing phone number and finds name of this number at phone contacts and get CurrentDatetime and combine this two staffs.

    private void prepareFileName(String incoming)
    {
        if (incoming.length() >= 11)/*Check the number is special or not (like 911)*/
        {
            getContactList(incoming);
        }
        else
        {
            NameofNum = incoming;
        }
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd_HH-mm-ss-SS");
        String date = sdf.format(new Date());
        FileName ="/"+ NameofNum + "|" + date + ".3gpp";
    }

getContactList(String number) method is pretty long method. So I didn't write here. You can find it easily here  [CallHelper.java](https://github.com/TayfunCesur/RecordPhoneCalls/blob/master/src/com/xionces/StoreCallRecords/CallHelper.java)
After this we have good,formatted filename. And let's start recording.

## Step 4: Recording Phone Call

First, you need MediaRecorder object. After created some, you must set some parameters into him. This parameters,especially *_AudioSource!._* This point is the main point of whole project. You have to set MediaRecorder.AudioSource.VOICE_CALL into setAudioSource() method.



