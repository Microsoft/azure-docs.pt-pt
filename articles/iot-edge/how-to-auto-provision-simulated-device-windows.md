---
title: Provisionar dispositivos Windows automaticamente com DPS-Azure IoT Edge | Microsoft Docs
description: Utilizar um dispositivo simulado no seu computador Windows para testar o aprovisionamento automático de dispositivos do Azure IoT Edge com o serviço de aprovisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457133"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Criar e provisionar um dispositivo de IoT Edge simulado com um TPM virtual no Windows

Azure IoT Edge dispositivos podem ser provisionados automaticamente usando o serviço de [provisionamento de dispositivos](../iot-dps/index.yml) , assim como os dispositivos que não estão habilitados para a borda. Se você não estiver familiarizado com o processo de provisionamento automático, examine os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

O DPS dá suporte a atestado de chave simétrica para dispositivos IoT Edge no registro individual e no registro de grupo. Para o registro de grupo, se você marcar a opção "é IoT Edge dispositivo" como verdadeira no atestado de chave simétrica, todos os dispositivos registrados nesse grupo de registro serão marcados como IoT Edge dispositivos. 

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Crie uma instância do IoT Hub dispositivo aprovisionamento DPS (serviço).
* Crie um dispositivo simulado no seu computador Windows com um simulado Trusted Platform Module (TPM) para a segurança de hardware.
* Crie uma inscrição individual para o dispositivo.
* Instalar o runtime do IoT Edge e ligar o dispositivo ao IoT Hub.

> [!NOTE]
> O TPM 2,0 é necessário ao usar o atestado de TPM com o DPS e só pode ser usado para criar registros individuais, não de grupo.

> [!TIP]
> Este artigo descreve como testar o provisionamento automático usando o atestado de TPM em dispositivos virtuais, mas grande parte dele se aplica ao usar o hardware TPM físico também.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento do Windows. Este artigo utiliza o Windows 10.
* Um IoT Hub Active Directory.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de aprovisionamento de dispositivos do IoT Hub

Criar uma nova instância do serviço de aprovisionamento de dispositivos do IoT Hub no Azure e ligá-lo ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IOT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o serviço de provisionamento de dispositivos, copie o valor do **escopo da ID** da página Visão geral. Utilize este valor quando configurar o runtime do IoT Edge.

> [!TIP]
> Se você estiver usando um dispositivo TPM físico, você precisará determinar a **chave de endosso**, que é exclusiva de cada chip TPM e é obtida do fabricante do chip TPM associado a ela. Você pode derivar uma **ID de registro** exclusiva para seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções no artigo [como gerenciar registros de dispositivo com o portal do Azure](../iot-dps/how-to-manage-enrollments.md) para criar seu registro no DPS e continue com a seção [instalar o IOT Edge Runtime](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado no seu computador de desenvolvimento do Windows. Recupere a **ID de registro** e a **chave de endosso** para seu dispositivo e use-as para criar uma entrada de registro individual no DPS.

Ao criar um registro no DPS, você tem a oportunidade de declarar um **estado inicial**de um dispositivo. No dispositivo duplo pode definir etiquetas para agrupar dispositivos com qualquer métrica que terá na sua solução, como o tipo de dispositivo, localização, ambiente ou região. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

Escolha o idioma SDK que pretende utilizar para criar o dispositivo simulado e siga os passos até criar a inscrição individual.

Ao criar o registro individual, selecione **true** para declarar que o dispositivo TPM simulado em seu computador de desenvolvimento do Windows é um **dispositivo IOT Edge**.

Dispositivo simulado e guias de inscrição individual:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar o registro individual, salve o valor da **ID de registro**. Utilize este valor quando configurar o runtime do IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Seus componentes executadas em contentores e permitem-lhe implementar contentores adicionais no dispositivo para que pode executar o código na periferia.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor de **escopo da ID** de DPS
* A **ID de registro** do dispositivo que você criou

Instale o IoT Edge tempo de execução no dispositivo que está executando o TPM simulado. Você configurará o tempo de execução de IoT Edge para o provisionamento automático, não manual.

> [!TIP]
> Mantenha a janela que está a executar o simulador de TPM aberta durante a instalação e teste.

Para obter informações mais detalhadas sobre como instalar o IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar IoT Edge, consulte [instalar o Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o tempo de execução de IOT Edge em seu computador. O comando usa como padrão o provisionamento manual com contêineres do Windows. Use o sinalizador `-Dps` para usar o serviço de provisionamento de dispositivos em vez do provisionamento manual.

   Substitua os valores de espaço reservado para `{scope_id}` e `{registration_id}` pelos dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

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

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do serviço aprovisionamento de dispositivos permite-lhe definir o ID de dispositivo e etiquetas do dispositivo duplo ao mesmo tempo, como aprovisionar o novo dispositivo. Pode utilizar esses valores para dispositivos individuais ou grupos de dispositivos com a gestão de dispositivos automático de destino. Saiba como [implantar e monitorar módulos IOT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando CLI do Azure](how-to-deploy-monitor-cli.md)
