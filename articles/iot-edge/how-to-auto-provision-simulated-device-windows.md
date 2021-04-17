---
title: Fornecendo automaticamente dispositivos Windows com DPS - Azure IoT Edge | Microsoft Docs
description: Utilize um dispositivo simulado na sua máquina Windows para testar o fornecimento automático de dispositivos para Azure IoT Edge com serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482012"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Criar e providenciar um dispositivo IoT Edge simulado com um TPM virtual no Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Os dispositivos Azure IoT Edge podem ser provisões automáticas utilizando o [Serviço de Provisionamento de Dispositivos,](../iot-dps/index.yml) tal como dispositivos que não estão ativados por arestas. Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

O DPS suporta a chave simétrica para dispositivos IoT Edge tanto na inscrição individual como na inscrição em grupo. Para a inscrição em grupo, se verificar "is IoT Edge device device" para ser verdadeiro no atestado de chave simétrica, todos os dispositivos registados sob esse grupo de inscrição serão marcados como dispositivos IoT Edge.

Este artigo mostra-lhe como testar o provisionamento automático num dispositivo IoT Edge simulado com os seguintes passos:

* Criar uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
* Crie um dispositivo simulado na sua máquina Windows com um Módulo de Plataforma Fidedigna simulado (TPM) para segurança de hardware.
* Crie uma inscrição individual para o dispositivo.
* Instale o tempo de funcionaamento do IoT Edge e ligue o dispositivo ao IoT Hub.

> [!TIP]
> Este artigo descreve o teste de auto-provisão utilizando atestado TPM em dispositivos virtuais, mas grande parte dele aplica-se quando se utiliza hardware TPM físico também.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento windows. Este artigo utiliza o Windows 10.
* Um hub IoT ativo.

> [!NOTE]
> TPM 2.0 é necessário ao utilizar atestado de TPM com DPS e só pode ser usado para criar inscrições individuais, não de grupo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do serviço de provisionamento de dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** na página de visão geral. Utilize este valor quando configurar o tempo de execução IoT Edge.

> [!TIP]
> Se estiver a utilizar um dispositivo TPM físico, tem de determinar a **tecla Endorsement**, que é única em cada chip TPM e é obtida do fabricante de chips TPM associado a ele. Pode obter um **ID de registo** único para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções do artigo Como gerir as inscrições do dispositivo com o [Portal Azure](../iot-dps/how-to-manage-enrollments.md) para criar a sua inscrição em DPS e, em seguida, proceder com a secção [de tempo de execução IoT Edge](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado na sua máquina de desenvolvimento Windows. Recupere a chave **de ID** e **Endosso** de Registo para o seu dispositivo e use-os para criar uma inscrição individual em DPS.

Quando cria uma inscrição em DPS, tem a oportunidade de declarar um **Estado Gémeo de Dispositivo Inicial.** No dispositivo twin pode definir tags para agrupar dispositivos por qualquer métrica que você precisa na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas.](how-to-deploy-at-scale.md)

Escolha o idioma SDK que pretende utilizar para criar o dispositivo simulado e siga os passos até criar a inscrição individual.

Quando criar a inscrição individual, selecione **True** para declarar que o dispositivo TPM simulado na sua máquina de desenvolvimento Windows é um **dispositivo IoT Edge**.

> [!TIP]
> No CLI Azure, pode criar uma [inscrição](/cli/azure/iot/dps/enrollment) ou um grupo de [inscrição](/cli/azure/iot/dps/enrollment-group) e utilizar a bandeira ativada por **arestas** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

Dispositivo simulado e guias individuais de inscrição:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Após a criação da inscrição individual, guarde o valor do ID de **inscrição.** Utilize este valor quando configurar o tempo de execução IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda. Instale o tempo de funcionamento do IoT Edge no dispositivo que está a executar o TPM simulado.

Siga os passos no [Instale o tempo de execução Azure IoT Edge](how-to-install-iot-edge.md)e, em seguida, volte a este artigo para disposir o dispositivo.

> [!TIP]
> Mantenha aberta a janela que está a executar o simulador TPM durante a sua instalação e teste.

## <a name="configure-the-device-with-provisioning-information"></a>Configure o dispositivo com informações de provisionamento

Uma vez instalado o tempo de funcionamento no seu dispositivo, configuure o dispositivo com as informações que utiliza para ligar ao Serviço de Provisionamento de Dispositivos e ao IoT Hub.

1. Conheça o seu **ID Scope DPS** e o **ID de registo do** dispositivo que foram recolhidos nas secções anteriores.

1. Abra uma janela PowerShell no modo de administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar ioT Edge, não PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a sua máquina Do Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução IoT Edge. O comando não permite a utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Os dispositivos Windows 10 ou Windows Server podem levar-lhe a reiniciar. Em caso afirmativo, reinicie já o seu dispositivo. Assim que o seu dispositivo estiver pronto, volte a executar o PowerShell como administrador.

1. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando não tem disposição manual com recipientes Windows. Utilize a `-Dps` bandeira para utilizar o Serviço de Provisionamento de Dispositivos em vez de provisionamento manual.

   Substitua os valores de espaço reservado `{scope_id}` para e `{registration_id}` pelos dados recolhidos anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Se o tempo de execução tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge no seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de funcionamento foi instalado e começou com sucesso.  

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Listar módulos de execução.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir o ID do dispositivo e as etiquetas gémeas do dispositivo ao mesmo tempo que fornece o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos individuais ou grupos de dispositivos utilizando a gestão automática do dispositivo. Saiba como [implementar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md)
