---
title: Como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos Azure
description: Este documento descreve como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80246692"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Como transferir uma carga útil entre dispositivo e DPS
Por vezes, o DPS precisa de mais dados dos dispositivos para os fornecer adequadamente ao IoT Hub certo, e esses dados precisam de ser fornecidos pelo dispositivo. Vice-versa, o DPS pode devolver dados ao dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando Utilizar
Esta funcionalidade pode ser utilizada como um melhoramento para [a atribuição personalizada.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Por exemplo, pretende alocar os seus dispositivos com base no modelo do dispositivo sem intervenção humana. Neste caso, utilizará [a atribuição personalizada.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Pode configurar o dispositivo para comunicar as informações do modelo como parte da chamada do [dispositivo de registo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará a carga útil do dispositivo para o webhook de atribuição personalizado. E a sua função pode decidir a que IoT Hub este dispositivo irá quando receber informações sobre o modelo do dispositivo. Da mesma forma, se o webhook quiser devolver alguns dados ao dispositivo, passará os dados de volta como uma cadeia na resposta webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Dispositivo envia carga útil de dados para DPS
Quando o seu dispositivo está a enviar uma chamada de [dispositivo de registo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) para DPS, a chamada de registo pode ser melhorada para tomar outros campos no corpo. O corpo parece o seguinte: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS devolve dados ao dispositivo
Se a política de atribuição personalizada webhook quiser devolver alguns dados ao dispositivo, passará os dados de volta como uma cadeia na resposta webhook. A alteração está na secção de carga útil abaixo. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Suporte de SKDs
Esta funcionalidade está disponível em C, C#, JAVA e Node.js [cliente SDKs](https://docs.microsoft.com/azure/iot-dps/).  

## <a name="next-steps"></a>Passos seguintes
* Desenvolver usando o [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) para Azure IoT Hub e Azure IoT Hub Device Provisioning Service
