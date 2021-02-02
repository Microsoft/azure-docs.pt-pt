---
title: Como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos Azure
description: Este documento descreve como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259524"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Como transferir cargas entre dispositivos e DPS
Por vezes, o DPS precisa de mais dados dos dispositivos para os fornecer adequadamente ao IoT Hub certo, e esses dados precisam de ser fornecidos pelo dispositivo. Vice-versa, o DPS pode devolver dados ao dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando Utilizar
Esta funcionalidade pode ser utilizada como um melhoramento para [a atribuição personalizada.](./how-to-use-custom-allocation-policies.md) Por exemplo, pretende alocar os seus dispositivos com base no modelo do dispositivo sem intervenção humana. Neste caso, utilizará [a atribuição personalizada.](./how-to-use-custom-allocation-policies.md) Pode configurar o dispositivo para comunicar as informações do modelo como parte da chamada do [dispositivo de registo](/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará a carga útil do dispositivo para o webhook de atribuição personalizado. E a sua função pode decidir a que IoT Hub este dispositivo irá quando receber informações sobre o modelo do dispositivo. Da mesma forma, se o webhook quiser devolver alguns dados ao dispositivo, passará os dados de volta como uma cadeia na resposta webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Dispositivo envia carga útil de dados para DPS
Quando o seu dispositivo está a enviar uma chamada de [dispositivo de registo](/rest/api/iot-dps/runtimeregistration/registerdevice) para DPS, a chamada de registo pode ser melhorada para tomar outros campos no corpo. O corpo parece o seguinte: 
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
Esta funcionalidade está disponível em C, C#, JAVA e Node.js [cliente SDKs](./index.yml).  

## <a name="next-steps"></a>Passos seguintes
* Desenvolver usando o [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) para Azure IoT Hub e Azure IoT Hub Device Provisioning Service