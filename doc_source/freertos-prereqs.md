# Prerequisites<a name="freertos-prereqs"></a>

To follow along with this tutorial, you need an AWS account, an IAM user with permission to access AWS IoT and Amazon FreeRTOS, and one of the supported hardware platforms\.

## AWS Account and Permissions<a name="freertos-account-and-permissions"></a>

To create an AWS account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/)\.

To add an IAM user to your AWS account, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. To grant your IAM user account access to AWS IoT and Amazon FreeRTOS, attach the following IAM policies to your IAM user account:
+ `AmazonFreeRTOSFullAccess`
+ `AWSIoTFullAccess`

**To attach the AmazonFreeRTOSFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the AWSIoTFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM and user accounts, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

For more information about policies, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Amazon FreeRTOS Supported Hardware Platforms<a name="freertos-hardware"></a>

You need one of the supported MCU boards:
+ [STMicroelectronicsSTM32L4 Discovery Kit IoT Node](http://www.st.com/en/evaluation-tools/b-l475e-iot01a.html)
+ [Texas Instruments CC3220SF\-LAUNCHXL](http://www.ti.com/tool/CC3220SF-LAUNCHXL)
+ [NXP LPC54018 IoT Module](https://www.nxp.com/products/processors-and-microcontrollers/arm-based-processors-and-mcus/lpc-cortex-m-mcus/lpc54000-series-cortex-m4-mcus/lpc54018-iot-module-for-the-lpc540xx-family-of-mcus:OM40007)
+ [Microchip Curiosity PIC32MZEF Bundle](http://www.microchip.com/developmenttools/productdetails.aspx?partno=dm320104)
+ [Espressif ESP32\-DevKitC](https://www.espressif.com/en/products/hardware/esp32-devkitc/overview)
+ [Espressif ESP\-WROVER\-KIT](https://www.espressif.com/en/products/hardware/esp-wrover-kit/overview)
+ [Infineon XMC4800 IoT Connectivity Kit](https://www.infineon.com/cms/en/product/evaluation-boards/kit_xmc48_iot_aws_wifi)
+ [Xilinx Avnet MicroZed Industrial IoT Kit](https://www.xilinx.com/products/boards-and-kits/1-8dyf-85.html)
+ Microsoft Windows 7 or later, with at least a dual core and a hard\-wired Ethernet connection
+ [Nordic nRF52840\-DK \[BETA\]](https://www.nordicsemi.com/eng/Products/nRF52840-DK)

## Registering Your MCU Board with AWS IoT<a name="get-started-freertos-thing"></a>

 You must register your MCU board so it can communicate with AWS IoT\. To register your device, you must create:
+ An IoT thing\.

  An IoT thing allows you to manage your devices in AWS IoT\. 
+ A private key and X\.509 certificate\.

  The private key and certificate allow your device to authenticate with AWS IoT\.
+ An AWS IoT policy\. 

  The AWS IoT policy grants your device permissions to access AWS IoT resources\.

**To create an AWS IoT policy**

1. To create an IAM policy, you need to know your AWS Region and AWS account number\. 

   To find your AWS account number, in the upper\-right corner of the AWS Management Console, choose **My Account**\. Your account ID is displayed under **Account Settings**\.

   To find the AWS Region for your AWS account, open a command prompt window and enter the following command:

   ```
   AWS IoT describe-endpoint
   ```

   The output should look like this:

   ```
   {
       "endpointAddress": "xxxxxxxxxxxxxx.iot.us-west-2.amazonaws.com"
   }
   ```

   In this example, the region is `us-west-2`\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\.

1. Enter a name to identify your policy\.

1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace *aws\-region* and *aws\-account* with your region and account ID \.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
       {
           "Effect": "Allow",
           "Action": "iot:Connect",
           "Resource":"arn:aws:iot:<aws-region>:<aws-account-id>:*"
   	}, 
       {
           "Effect": "Allow",
           "Action": "iot:Publish",
           "Resource": "arn:aws:iot:<aws-region>:<aws-account-id>:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Subscribe",
            "Resource": "arn:aws:iot:<aws-region>>:<aws-account-id>:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Receive",
            "Resource": "arn:aws:iot:<aws-region>:<aws-account-id>:*"
       }
       ]
   }
   ```

   This policy grants the following permissions:  
`iot:Connect`  
Grants your device the permission to connect to the AWS IoT message broker\.  
`iot:Publish`  
Grants your device the permission to publish an MQTT message on the `freertos/demos/echo` MQTT topic\.  
`iot:Subscribe`  
Grants your device the permission to subscribe to the `freertos/demos/echo` MQTT topic filter\.  
`iot:Receive`  
Grants your device the permission to receive messages from the AWS IoT message broker\.

1. Choose **Create**\.

**To create an IoT thing, private key, and certificate for your device**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

1. If you do not have any IoT things registered in your account, the **You don't have any things yet** page is displayed\. If you see this page, choose **Register a thing**\. Otherwise, choose **Create**\.

1. On the **Creating AWS IoT things** page, choose **Create a single thing**\.

1. On the **Add your device to the thing registry** page, enter a name for your thing, and then choose **Next**\.

1. On the **Add a certificate for your thing** page, under **One\-click certificate creation**, choose **Create certificate**\.

1. Download your private key and certificate by choosing the **Download** links for each\. Make a note of the certificate ID\. You need it later to attach a policy to your certificate\.

1. Choose **Activate** to activate your certificate\. Certificates must be activated prior to use\.

1. Choose **Attach a policy** to attach a policy to your certificate that grants your device access to AWS IoT operations\.

1. Choose the policy you just created and choose **Register thing**\.

## Install a Terminal Emulator<a name="uart-term"></a>

A terminal emulator can help you diagnose problems or verify your device code is running properly\. There are a variety of terminal emulators available for Windows, macOS, and Linux\. You must connect your device to your computer before you attempt to connect a terminal emulator to your device\.

 Use these settings in your terminal emulator:


| Terminal Setting | Value | 
| --- | --- | 
|  Port  |  Depends on platform and other devices you have connected to your computer\.  | 
|  BAUD rate  |  115200  | 
|  Data  |  8 bit  | 
|  Parity  |  none  | 
|  Stop  |  1 bit  | 
|  Flow control  |  none  | 