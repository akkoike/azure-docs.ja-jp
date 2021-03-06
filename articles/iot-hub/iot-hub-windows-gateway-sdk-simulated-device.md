---
title: "IoT Gateway SDK を使用したデバイスのシミュレート | Microsoft Docs"
description: "Windows で Azure IoT Gateway SDK を使用してシミュレートされたデバイスからテレメトリを送信する方法を示す、Azure IoT Gateway SDK のチュートリアル。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f5eeea933dbd5b63c6a8f2bd5b065a13286a4bae


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-app-using-windows"></a>Azure IoT Gateway SDK - Windows を使用してシミュレートされたデバイス アプリでデバイスからクラウドへのメッセージを送信する
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行
作業を開始する前に、以下を行う必要があります。

* Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]します。
* Azure サブスクリプションで [IoT ハブを作成][lnk-create-hub]します。このチュートリアルを実行するには、ハブの名前が必要です。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます。
* 2 つのデバイスを IoT Hub に追加し、デバイスの ID とデバイス キーをメモしておきます。 [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、前の手順で作成した IoT ハブにデバイスを追加し、キーを取得します。

サンプルをビルドするには、次の手順に従います。

1. **開発者コマンド プロンプト for VS2015** コマンド プロンプトを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドして、テストを実行します。 Visual Studio ソリューションは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **build** フォルダーにあります。

サンプルを実行するには:

テキスト エディターで、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** ファイルを開きます。 このファイルでは、サンプル ゲートウェイの各モジュールを構成します。

* **IoTHub** モジュールは、IoT Hub に接続します。 データを IoT Hub に送信するようにモジュールを構成する必要があります。 具体的には、**IoTHubName** 値を実際の IoT Hub の名前に設定し、**IoTHubSuffix** 値を **azure-devices.net** に設定します。 **Transport** の値を "HTTP"、"AMQP"、または "MQTT" のいずれかに設定します。 現在、すべてのデバイス メッセージで 1 つの TCP 接続を共有するのは "HTTP" のみです。 値を "AMQP" または "MQTT" に設定すると、ゲートウェイは各デバイスで IoT Hub に対する TCP 接続を別個に維持します。
* **mapping** モジュールは、シミュレートされたデバイスの MAC アドレスを IoT Hub のデバイス ID にマップします。 **deviceId** 値が IoT Hub に追加した 2 つのデバイスの ID と一致し、**deviceKey** 値に 2 つのデバイスのキーが含まれていることを確認します。
* **BLE1** モジュールと **BLE2** モジュールは、シミュレートされたデバイスです。 これらのモジュールの MAC アドレスが **mapping** モジュールの MAC アドレスとどのように一致しているかに注意してください。
* **Logger** モジュールは、ゲートウェイのアクティビティをファイルに記録します。
* 次に示す **module path** の値は、IoT Gateway SDK リポジトリを **C:** ドライブのルートに複製していることを前提としています。 リポジトリを別の場所にダウンロードした場合は、 **module path** の値を適宜調整する必要があります。
* JSON ファイルの下部にある **links** 配列は、**BLE1** モジュールと **BLE2** モジュールを **mapping** モジュールに、**mapping** モジュールを **IoTHub** モジュールに接続します。 また、すべてのメッセージが **Logger** モジュールによってログに記録されます。

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

構成ファイルに加えた変更を保存します。

サンプルを実行するには:

1. コマンド プロンプトで、 **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
2. 次のコマンドを実行します。
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. [デバイス エクスプローラーまたは iothub-explorer][lnk-explorer-tools] ツールを使用して、IoT ハブがゲートウェイから受信するメッセージを監視できます。

## <a name="next-steps"></a>次のステップ
IoT Gateway SDK に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

* [IoT Gateway SDK を使用した物理デバイスからの D2C メッセージの送信][lnk-physical-device]
* [Azure IoT Gateway SDK][lnk-gateway-sdk]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [開発者ガイド][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


