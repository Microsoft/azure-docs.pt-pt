---
title: Como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos Azure
description: Este documento descreve como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209414"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Como transferir uma carga útil entre o dispositivo e o DPS
Por vezes, o DPS necessita de mais dados dos dispositivos para os fornecer corretamente ao IoT Hub certo, e que os dados precisam de ser fornecidos pelo dispositivo. Vice-versa, o DPS pode devolver dados ao dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando utilizar
Esta funcionalidade pode ser usada como um melhoramento para [a atribuição personalizada.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Por exemplo, pretende alocar os seus dispositivos com base no modelo do dispositivo sem intervenção humana. Neste caso, utilizará a [atribuição personalizada.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Pode configurar o dispositivo para reportar as informações do modelo como parte da chamada do [dispositivo de registo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará a carga útil do dispositivo para o webhook de alocação personalizada. E a sua função pode decidir a que Hub IoT irá para quando receber informações do modelo do dispositivo. Da mesma forma, se o webhook desejar devolver alguns dados ao dispositivo, passará os dados de volta como uma cadeia na resposta webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Dispositivo envia carga útil de dados para DPS
Quando o seu dispositivo está a enviar uma chamada de dispositivo de [registo](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) para DPS, a chamada de registo pode ser melhorada para tomar outros campos do corpo. O corpo parece o seguinte: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS devolve dados ao dispositivo
Se a política de alocação personalizada pretender devolver alguns dados ao dispositivo, passará os dados de volta como uma cadeia na resposta do webhook. A alteração está na secção de carga útil abaixo. 
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

## <a name="sdk-support"></a>Suporte SDK
Esta funcionalidade está disponível em C, C#JAVA e Node.js cliente [SDKs](https://docs.microsoft.com/azure/iot-dps/).  

## <a name="next-steps"></a>Passos seguintes
* Desenvolver a utilização do [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) para o Azure IoT Hub e o Serviço de Provisionamento de Dispositivos Azure IoT Hub
