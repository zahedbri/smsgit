# smsgit
A sms sending in HTML
<html xmlns="http://www.w3.org/1999/xhtml" >
<head>
  <title>ActiveXperts SMS Component Javascript Demo</title>
  <link rel="Stylesheet" type="text/css" href="css/SampleStylesheet.css" />

  <script language="JavaScript" type="text/javascript">
    var objGsm = new ActiveXObject("AxSms.Gsm");

    window.attachEvent("onload", PageLoaded, false);

    function PageLoaded() {
      document.getElementById("APIinfo").innerHTML = "Build: " + objGsm.Build + ";
        Module: " + objGsm.Module;

      var objForm = document.forms["GsmSampleForm"];
      var strDevice = objGsm.FindFirstDevice();

      objForm.txtResult.value = "n/a";

      while (objGsm.LastError == 0) {
        var NewOption = document.createElement('option');
        NewOption.text = NewOption.value = strDevice;

        objForm.ddlDevices.add(NewOption);
        strDevice = objGsm.FindNextDevice();
      }

      for (var i = 1; i <= 20; i++) {
        var NewOption = document.createElement('option');
        NewOption.text = NewOption.value = "COM" + i; ;

        objForm.ddlDevices.add(NewOption);
      }
    }

    function SendMessage() {
      var objSmsMessage = new ActiveXObject("AxSms.Message");
      var objSmsConstants = new ActiveXObject("AxSms.Constants");

      var iFlowControl = objSmsConstants.GSM_FLOWCONTROL_AUTO;

      var objForm = document.forms["GsmSampleForm"];

      var strName = objForm.ddlDevices.value;
      var strPincode = objForm.txtPincode.value;
      var iDeviceSpeed = objForm.ddlDeviceSpeed.value;

      objGsm.Clear();
      objGsm.Open(strName, strPincode, iFlowControl, iDeviceSpeed);

      if (objGsm.LastError != 0) {
          UpdateResult(objForm);
      }
      else {
        //Message Settings
        objSmsMessage.Clear();
        objSmsMessage.ToAddress = objForm.txtToAddress.value;
        objSmsMessage.Body = objForm.txtBody.value;
        objSmsMessage.BodyFormat = objSmsConstants.BODYFORMAT_TEXT;

        var iMultipart = (objForm.cbxMultipart.checked == 1)
          ? objSmsConstants.MULTIPART_OK
          : objSmsConstants.MULTIPART_TRUNCATE;

        if (objForm.cbxFlash.checked == 1)
          objSmsMessage.DataCoding = objSmsConstants.GSM_DATACODING_FLASH;

        //Send the message !
        objGsm.SendSms(objSmsMessage, iMultipart, 10000);
        UpdateResult(objForm);
      }
    }

    function UpdateResult(objForm) {
      objForm.txtResult.value = objGsm.LastError + ": " +
        objGsm.GetErrorDescription(objGsm.LastError);
      objGsm.Close();
    }
  </script>
  <body>
    <div>

      <div>
        <h1>SMS Component Javascript GSM Sample</h1>
        <hr />
        <p>
        This demo requires a GSM modem or GSM phone connected to your computer.
        A SIM card is required in this GSM modem.
        The product works with almost all available GSM modems.
        </p>
        <form name="GsmSampleForm" action="gsm.html" method="post">
          <h2>SMS Component:</h2>
          <h3 id="APIinfo"></h3>

          <!-- Device -->
          <label for="Devices">Device:</label>
          <p>
            <select id="Devices" name="ddlDevices"></select>
          </p>

          <!-- Device Speed -->
          <label for="DeviceSpeed">Device Speed:</label>
          <p>
            <select id="DeviceSpeed" name="ddlDeviceSpeed">
              <option value="0">Default</option>

              <option value="110">110</option>
              <option value="300">300</option>
              <option value="600">600</option>
              <option value="1200">1200</option>
              <option value="2400">2400</option>
              <option value="4800">4800</option>

              <option value="9600">9600</option>
              <option value="14400">14400</option>
              <option value="19200">19200</option>
              <option value="38400">38400</option>
              <option value="56000">56000</option>
              <option value="57600">57600</option>

              <option value="64000">64000</option>
              <option value="115200">115200</option>
              <option value="128000">128000</option>
              <option value="230400">230400</option>
              <option value="256000">256000</option>
              <option value="460800">460800</option>

              <option value="921600">921600</option>
            </select>
            (only applies to direct ports, i.e. COM1, COM2, etc.)
          </p>

          <!-- Pincode -->
          <label for="Pincode">Pincode:</label>
          <p>
            <input type="password" id="Pincode" name="txtPincode" />
            (only required if SIM card has PIN code)
          </p>

          <!-- ToAddress -->
          <label for="ToAddress">ToAddress:</label>
          <p>
            <input type="text" id="ToAddress" name="txtToAddress" value="[ToAddress]" />
            <a href="https://www.activexperts.com/support/sms-component/?kb=Q4200015"
                target="_blank">Recipient number format</a>
          </p>

          <!-- Body, Multipart, Flash -->
          <label for="Body">Body:</label>
          <p>
            <textarea id="Body" name="txtBody" style="height:55px;">
                Hello world send from ActiveXperts SMS Component!
            </textarea><br />

            <input type="checkbox id="Multipart" name="cbxMultipart" value="1" />
            <label for="Multipart">Allow Multipart</label><br />

            <input type="checkbox id="Flash" name="cbxFlash" value="1" />
            <label for="Flash">Flash</label>
          </p>

          <!-- Send button -->
          <div></div>
          <p>
            <input type="button" onclick="SendMessage()" value="Send SMS Message!" />
          </p>

          <!-- Result -->
          <label><b>Result:</b></label>
          <p>
            <input type="text" name="txtResult"
                style="font-weight: bold;" value="n/a" />
          </p>
        </form>

      </div><!-- /container -->
    </div><!-- /maincontainer -->
  </body>
</html>  
