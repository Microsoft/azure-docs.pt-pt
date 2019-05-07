---
title: Dispositivos do Windows de aprovisionamento automático com o DPS - Azure IoT Edge | Documentos da Microsoft
description: Utilizar um dispositivo simulado no seu computador Windows para testar o aprovisionamento automático de dispositivos do Azure IoT Edge com o serviço de aprovisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 01247dfc0046ef722d70fe48f7ab8ee63f685962
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153563"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Criar e aprovisionar um dispositivo de periferia de TPM simulado no Windows

Dispositivos do IoT Edge do Azure podem ser aprovisionado automaticamente com o [serviço aprovisionamento de dispositivos](../iot-dps/index.yml) tal como os dispositivos que não são habilitados no edge. Se não estiver familiarizado com o processo de aprovisionamento automático, reveja os [conceitos de aprovisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra-lhe como testar aprovisionamento automático num dispositivo Edge simulado com os seguintes passos:

* Crie uma instância do IoT Hub dispositivo aprovisionamento DPS (serviço).
* Crie um dispositivo simulado no seu computador Windows com um simulado Trusted Platform Module (TPM) para a segurança de hardware.
* Crie uma inscrição individual para o dispositivo.
* Instalar o runtime do IoT Edge e ligar o dispositivo ao IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento do Windows. Este artigo utiliza o Windows 10.
* Um IoT Hub Active Directory.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de aprovisionamento de dispositivos do IoT Hub

Criar uma nova instância do serviço de aprovisionamento de dispositivos do IoT Hub no Azure e ligá-lo ao seu hub IoT. Pode seguir as instruções em [configurar o Hub IoT DPS](../iot-dps/quick-setup-auto-provision.md).

Depois de ter a execução do serviço aprovisionamento de dispositivos, copie o valor da **âmbito do ID** da página de descrição geral. Utilize este valor quando configurar o runtime do IoT Edge.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado no seu computador de desenvolvimento do Windows. Obter o **ID de registo** e **chave de endossamento** para o seu dispositivo e usá-los para criar uma entrada de inscrição individual no DPS.

Quando criar uma inscrição em pontos de distribuição, terá a oportunidade para declarar uma **inicial estado do dispositivo duplo**. No dispositivo duplo pode definir etiquetas para agrupar dispositivos com qualquer métrica que terá na sua solução, como o tipo de dispositivo, localização, ambiente ou região. Essas marcas são usadas para criar [implementações automáticas](how-to-deploy-monitor.md).

Escolha o idioma SDK que pretende utilizar para criar o dispositivo simulado e siga os passos até criar a inscrição individual.

Ao criar a inscrição individual, selecione **habilitar** declarar que o dispositivo TPM simulado no seu computador de desenvolvimento do Windows é um **dispositivo IoT Edge**.

Dispositivo simulado e guias de inscrição individual:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar a inscrição individual, guardar o valor do **ID de registo**. Utilize este valor quando configurar o runtime do IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

Depois de concluir a seção anterior, deverá ver seu novo dispositivo listado como um dispositivo IoT Edge no seu IoT Hub. Agora, tem de instalar o runtime do IoT Edge no dispositivo.

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Seus componentes executadas em contentores e permitem-lhe implementar contentores adicionais no dispositivo para que pode executar o código na periferia.  

Siga as instruções para instalar o runtime do IoT Edge no dispositivo que está a executar o TPM simulado da secção anterior. Certifique-se configurar o runtime do IoT Edge para aprovisionamento automático e não manual.

Saber o DPS **âmbito do ID** e o dispositivo **ID de registo** antes de instalar o IoT Edge no dispositivo.

[Instalar e aprovisionar automaticamente do IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se o tempo de execução foi iniciado com êxito, pode entrar no seu IoT Hub e iniciar a implementação de módulos do IoT Edge para o seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de execução instalado e iniciado com êxito.  

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos do serviço dos últimos 5 minutos.


```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos em execução.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos Seguintes

O processo de inscrição do serviço aprovisionamento de dispositivos permite-lhe definir o ID de dispositivo e etiquetas do dispositivo duplo ao mesmo tempo, como aprovisionar o novo dispositivo. Pode utilizar esses valores para dispositivos individuais ou grupos de dispositivos com a gestão de dispositivos automático de destino. Saiba como [implementar e monitorizar módulos em dimensionar no portal do Azure do IoT Edge](how-to-deploy-monitor.md) ou [com a CLI do Azure](how-to-deploy-monitor-cli.md)
