# FreePBX-VM-Transcription
Script to transcribe voicemail and convert voicemail recordings to MP3

## Installation

Pull the repo to your server

    git clone https://github.com/westparkcom/FreePBX-VM-Transcription.git

Install the scripts:

    cd FreePBX-VM-Transcription
    cp emailproc /usr/local/bin
    cp sttparse /usr/local/bin
    chmod +x /usr/local/bin/emailproc
    chmod +x /usr/local/bin/sttparse

Create temporary directory for wav processing:

    mkdir /tmp/conv
    chmod 0777 /tmp/conv

Modify the "globalconfig" function parameters in the file /usr/local/bin/sttparse to suit your needs. The defaults are:

    'vm_transcription' : True, # Transcribe voicemail to text (requires Microsoft Azure Cognitive services subscription)
    'transcription_string' : '{{{{TRANSCRIPTION}}}}', # String to search for in the email which will be replaced by the transcribed text
    'ms_cognitive_api_key' : 'FILLMEIN!!!', # Microsoft Cognitive Services API string
    'speech_language' : 'en-US', # Language for speech recognition
    'vm_to_mp3' : True, # Convert voicemail audio to MP3
    'temp_dir': '/tmp/conv', # Temp location for MP3 conversion. MUST BE WRITABLE!
    'ffmpeg_location' : '/usr/bin/ffmpeg', # If vm_to_mp3 is True, location of ffmpeg executable
    'check_html' : True # Check to see if message is HTML and set mimetype accordingly

A Microsoft Azure cognitive services API key is required for transcription and can be acquired from Azure. Please refer to [this post](http://stackoverflow.com/a/40772586) for instructions on generating an API key.

## FreePBX Setup

In FreePBX browse to **Settings -> Voicemail Admin -> Settings -> Email Config** then add the transcription tag **{{{{TRANSCRIPTION}}}}** to the **Email Body**. The script will search for this tag to replace with the transcription text. You can also use HTML and the script will automatically change the content type to text/html. Here is a sample html email:

    <html>
    <body>
    <p>${VM_NAME},
    <br><br>
    There is a new voicemail in mailbox ${VM_MAILBOX}:</p>
    <p>
    <table>
      <tr>
        <th align="left">From (Name):</th>
        <td>${VM_CIDNAME}</th>
      </tr>
      <tr>
        <th align="left">From (Number):</td>
        <td><a href="tel://${VM_CIDNUM}">${VM_CIDNUM}</a></td>
      </tr>
      <tr>
        <th align="left">Length:</td>
        <td>${VM_DUR} seconds</td>
      </tr>
      <tr>
        <th align="left">Date:</td>
        <td>${VM_DATE}</td>
      </tr>
      <tr>
        <th align="left">Transcription:</td>
        <td>{{{{TRANSCRIPTION}}}}</td>
      </tr>
    </table></p>
    <p>Dial *98 to access your voicemail by phone.<br>
    Visit <a href="https://your.pbxaddress.tld">https://your.pbxaddress.tld</a> to check your voicemail with a web browser.</p>
    </body>
    </html>

Finally, set the **Mail Command** value to **/usr/local/bin/emailproc**