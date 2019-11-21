---
title: Quickstart - Provision a simulated TPM device to Azure IoT Hub using C#
description: Quickstart - Create and provision a simulated TPM device using C# device SDK for Azure IoT Hub Device Provisioning Service. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 65e914700749fca2011d189dc1fbce701a05f16d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228697"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision a simulated TPM device using C# device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

These steps show you how to use the [Azure IoT Samples for C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) to simulate a TPM device on a development machine running the Windows OS. The sample also connects the simulated device to an IoT Hub using the Device Provisioning Service. 

O código de exemplo utiliza o simulador de TPM do Windows como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Make sure you have the [.NET Core 2.1 SDK or later](https://www.microsoft.com/net/download/windows) installed on your machine. 

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone the Azure IoT Samples for C# GitHub repo:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Aprovisionar o dispositivo simulado

1. Inicie sessão no Portal do Azure. Clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o Serviço Aprovisionamento de Dispositivos. No painel **Descrição Geral**, anote o valor do **_Âmbito do ID_** .

    ![Copiar o ID de Âmbito do serviço de aprovisionamento a partir do painel do portal](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

2. Numa linha de comandos, altere os diretórios para o diretório de projeto para o exemplo de aprovisionamento de dispositivos TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

2. Escreva o seguinte comando para criar e executar o exemplo de aprovisionamento de dispositivos TPM. Substitua o valor `<IDScope>` pelo Âmbito de ID para o serviço de aprovisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

    This command will launch the TPM chip simulator in a separate command prompt. On Windows, you may encounter a Windows Security Alert that asks whether you want to allow Simulator.exe to communicate on public networks. For the purposes of this sample, you may cancel the request.

1. A janela de comandos apresenta a **_Chave de Endossamento_** , o **_ID de Registo_** e um **_ID de Dispositivo_** necessários para a inscrição do dispositivo. Take note of these values. You will use these value to create an individual enrollment in your Device Provisioning Service instance. 
   > [!NOTE]
   > Não confunda a janela que contém a saída do comando com a janela que contém a saída do simulador do TPM. Poderá ter de clicar na janela de comando para colocá-lo em primeiro plano.

    ![Resultado da janela de Comando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

4. No portal do Azure, no painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique no botão **Adicionar inscrição individual** na parte superior. 

5. Em **Adicionar Inscrição**, introduza as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Enter the *Registration ID* and *Endorsement key* for your TPM device that you noted earlier.
   - Opcionalmente, selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Introduza um ID de dispositivo exclusivo. Pode introduzir o ID de dispositivo sugerido na saída de exemplo ou introduza o seu próprio. Se utilizar o seu próprio, certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
   - Optionally update the **Initial device twin state** with the desired initial configuration for the device.
   - Quando tiver terminado, clique no botão **Guardar**. 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 

6. Prima Enter na janela de comandos (que apresentou a **_Chave de Endossamento_** , o **_ID de Registo_** e um **_ID de Dispositivo_** ) para inscrever o dispositivo simulado. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

1. Certifique-se de que o dispositivo foi aprovisionado. Após o aprovisionamento bem-sucedido do dispositivo simulado no hub IoT que está associado ao seu serviço de aprovisionamento, o ID aparece no painel **IoT Device** do hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar e a explorar o exemplo de cliente do dispositivo, não limpe os recursos criados neste Guia Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, criou um dispositivo simulado TPM no seu computador e aprovisionou-o no seu hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT. Para saber como inscrever o seu dispositivo TPM programaticamente, continue para o Manual de Início Rápido para inscrição programática de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Manual de Início Rápido do Azure - Inscrever o dispositivo TPM no Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-csharp.md)
