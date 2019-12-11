---
title: Início rápido-provisionar um dispositivo TPM simulado para o Hub IoT do Azure usandoC#
description: Início rápido – criar e provisionar um dispositivo TPM C# simulado usando o SDK do dispositivo para o serviço de provisionamento de dispositivos do Hub IOT do Azure (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977000"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo TPM C# simulado usando o SDK do dispositivo para o serviço de provisionamento de dispositivos no Hub IOT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estas etapas mostram como usar os exemplos de [IOT do Azure para C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) para simular um dispositivo TPM em um computador de desenvolvimento que executa o sistema operacional Windows. O exemplo também conecta o dispositivo simulado a um hub IoT usando o serviço de provisionamento de dispositivos. 

O código de exemplo utiliza o simulador de TPM do Windows como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

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

1. Inicie sessão no Portal do Azure. Selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos. Na folha **visão geral** , observe o valor de **_escopo da ID_** .

    ![Copiar o ID de Âmbito do serviço de aprovisionamento a partir do painel do portal](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Numa linha de comandos, altere os diretórios para o diretório de projeto para o exemplo de aprovisionamento de dispositivos TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Escreva o seguinte comando para criar e executar o exemplo de aprovisionamento de dispositivos TPM. Substitua o valor `<IDScope>` pelo Âmbito de ID para o serviço de aprovisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

    Esse comando iniciará o simulador de chip do TPM em um prompt de comando separado. No Windows, você pode encontrar um alerta de segurança do Windows que pergunta se você deseja permitir que o Simulator. exe se comunique em redes públicas. Para os fins deste exemplo, você pode cancelar a solicitação.

1. A janela de comando original exibe a **_chave de endosso_** , a ID de **_registro_** e uma ID de **_dispositivo_** sugerida necessária para o registro do dispositivo. Anote esses valores. Você usará esse valor para criar um registro individual em sua instância do serviço de provisionamento de dispositivos. 
   > [!NOTE]
   > Não confunda a janela que contém a saída do comando com a janela que contém a saída do simulador do TPM. Talvez seja necessário selecionar a janela de comando original para trazê-la para o primeiro plano.

    ![Resultado da janela de Comando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. No portal do Azure, no menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

1. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Insira a *ID de registro* e a *chave de endosso* do seu dispositivo TPM a partir dos valores que você anotou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Insira uma *ID de dispositivo* exclusiva (você pode usar a sugestão de uma ou fornecer a sua própria). Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se você optar por não fornecer um, a ID de registro será usada para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Depois de concluído, pressione o botão **salvar** . 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 

1. Pressione *Enter* na janela de comando (aquela que exibiu a **_chave de endosso_** , a **_ID de registro_** e a **_ID de dispositivo_** sugerida) para registrar o dispositivo simulado. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

1. Certifique-se de que o dispositivo foi aprovisionado. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao seu serviço de provisionamento, a ID do dispositivo aparece na folha **dispositivos IOT** do Hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Na parte superior da folha de **visão geral** , pressione **delete** na parte superior do painel.  
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Na parte superior da folha de **visão geral** , pressione **delete** na parte superior do painel.  

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado do TPM em seu computador e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue no início rápido para o registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de início rápido do Azure-registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-csharp.md)
