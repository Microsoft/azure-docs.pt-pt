---
title: Aprovisionar um dispositivo TPM simulado no Hub IoT do Azure com C# | Microsoft Docs
description: Início Rápido do Azure - criar e aprovisionar um dispositivo TPM simulado com o SDK de dispositivo C# com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 56d41d92b77ea6ef536f1e371a825b775a780bef
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993892"
---
# <a name="create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e aprovisionar um dispositivo TPM simulado com o SDK de dispositivo C# com o Serviço de Aprovisionamento de Dispositivos no Hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estas etapas mostram como usar os exemplos de [IOT do Azure para C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) para simular um dispositivo TPM em um computador de desenvolvimento que executa o sistema operacional Windows. O exemplo também conecta o dispositivo simulado a um hub IoT usando o serviço de provisionamento de dispositivos. 

O código de exemplo utiliza o simulador de TPM do Windows como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): Usado para registrar vários dispositivos relacionados.
- [Registros individuais](concepts-service.md#individual-enrollment): Usado para registrar um único dispositivo.

Este artigo vai demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Verifique se você tem o [SDK do .NET Core 2,1 ou posterior](https://www.microsoft.com/net/download/windows) instalado em seu computador. 

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone os exemplos de IoT do C# Azure para o repositório github:

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

    Esse comando iniciará o simulador de chip do TPM em um prompt de comando separado. No Windows, você pode encontrar um alerta de segurança do Windows que pergunta se você deseja permitir que o Simulator. exe se comunique em redes públicas. Para os fins deste exemplo, você pode cancelar a solicitação.

1. A janela de comandos apresenta a **_Chave de Endossamento_** , o **_ID de Registo_** e um **_ID de Dispositivo_** necessários para a inscrição do dispositivo. Anote esses valores. Você usará esse valor para criar um registro individual em sua instância do serviço de provisionamento de dispositivos. 
   > [!NOTE]
   > Não confunda a janela que contém a saída do comando com a janela que contém a saída do simulador do TPM. Poderá ter de clicar na janela de comando para colocá-lo em primeiro plano.

    ![Resultado da janela de Comando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

4. No portal do Azure, no painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique no botão **Adicionar inscrição individual** na parte superior. 

5. Em **Adicionar Inscrição**, introduza as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Insira a *ID de registro* e a *chave de endosso* do seu dispositivo TPM que você anotou anteriormente.
   - Opcionalmente, selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Introduza um ID de dispositivo exclusivo. Pode introduzir o ID de dispositivo sugerido na saída de exemplo ou introduza o seu próprio. Se utilizar o seu próprio, certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
   - Opcionalmente, atualize o **estado inicial do dispositivo** de atualização com a configuração inicial desejada para o dispositivo.
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
