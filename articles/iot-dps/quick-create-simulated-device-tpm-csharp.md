---
title: 'Quickstart - Fornecer um dispositivo TPM simulado para o Hub Azure IoT usando C #'
description: Quickstart - Crie e forre um dispositivo TPM simulado utilizando o dispositivo C# SDK para o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74977000"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Criar e fornecer um dispositivo TPM simulado utilizando o dispositivo C# SDK para o Serviço de Provisionamento de Dispositivos IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estes passos mostram-lhe como usar as [amostras Azure IoT para C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) para simular um dispositivo TPM numa máquina de desenvolvimento que executa o Sistema operativo Windows. A amostra também liga o dispositivo simulado a um Hub IoT utilizando o Serviço de Provisionamento de Dispositivos. 

O código de exemplo utiliza o simulador de TPM do Windows como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo. 

Se não está familiarizado com o processo de fornecimento automático, certifique-se de que também revê [conceitos de fornecimento automático.](concepts-auto-provisioning.md) Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo vai demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Certifique-se de que tem o [.NET Core 2.1 SDK ou posteriormente](https://www.microsoft.com/net/download/windows) instalado na sua máquina. 

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone das amostras Azure IoT para C# GitHub repo:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Aprovisionar o dispositivo simulado

1. Inicie sessão no Portal do Azure. Selecione o botão **Todos os recursos** no menu à esquerda e abra o serviço de fornecimento de dispositivos. A partir da lâmina **de visão geral,** note o valor do âmbito de **_identificação._**

    ![Copiar o ID de Âmbito do serviço de aprovisionamento a partir do painel do portal](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Numa linha de comandos, altere os diretórios para o diretório de projeto para o exemplo de aprovisionamento de dispositivos TPM.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Escreva o seguinte comando para criar e executar o exemplo de aprovisionamento de dispositivos TPM. Substitua o valor `<IDScope>` pelo Âmbito de ID para o serviço de aprovisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

    Este comando lançará o simulador de chips TPM num pedido de comando separado. No Windows, poderá encontrar um Alerta de Segurança do Windows que pergunta se pretende permitir que o Simulator.exe se comunique nas redes públicas. Para efeitos desta amostra, pode cancelar o pedido.

1. A janela de comando original apresenta a **_chave de averbamento,_** o **_ID de registo,_** e um ID de **_dispositivo_** sugerido necessário para a inscrição do dispositivo. Tome nota destes valores. Utilizará este valor para criar uma inscrição individual na sua instância de Serviço de Provisionamento de Dispositivos. 
   > [!NOTE]
   > Não confunda a janela que contém a saída do comando com a janela que contém a saída do simulador do TPM. Pode ter de selecionar a janela de comando original para a trazer para o primeiro plano.

    ![Resultado da janela de Comando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. No portal Azure, a partir do menu serviço de fornecimento de dispositivos, selecione **Gerir as matrículas.** Selecione o separador **Individual Registrations** e selecione o botão **de inscrição individual Adicionar** na parte superior. 

1. No painel **Adicionar Inscrições,** introduza as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Introduza a chave *de identificação* e *averbamento* de registo para o seu dispositivo TPM a partir dos valores que observou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Introduza um ID único do *dispositivo* (pode utilizar o sugerido ou fornecer o seu próprio). Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se optar por não fornecer um, o ID de registo será utilizado para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Uma vez concluído, prima o botão **Guardar.** 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 

1. Pressione *Introduza* na janela de comando (a que exibiu a **_chave de averbamento,_** **_identificação de registo,_** e sugeriu **_identificação_** do dispositivo ) para inscrever o dispositivo simulado. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

1. Certifique-se de que o dispositivo foi aprovisionado. Ao fornecer com sucesso o dispositivo simulado ao hub IoT ligado ao seu serviço de provisionamento, o ID do dispositivo aparece na lâmina de **dispositivos IoT** do hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para mais informações, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Na parte superior da lâmina **de visão geral,** prima **Delete** na parte superior do painel.  
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Na parte superior da lâmina **de visão geral,** prima **Delete** na parte superior do painel.  

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo simulado TPM na sua máquina e foru-o ao seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Para aprender a inscrever o seu dispositivo TPM programáticamente, continue a acelerar a inscrição programática de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivo TPM para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-tpm-csharp.md)
