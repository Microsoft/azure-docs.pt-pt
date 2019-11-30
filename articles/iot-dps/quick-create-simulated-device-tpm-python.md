---
title: Início rápido-provisionar dispositivo TPM simulado para o Hub IoT do Azure usando Python
description: Início rápido-criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo Java para o serviço de provisionamento de dispositivos no Hub IoT. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: e878245d5329edfe31d402d03fc7e1aef0b6637e
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555118"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo Python para o serviço de provisionamento de dispositivos no Hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estes passos explicam como criar um dispositivo simulado na máquina de desenvolvimento com o sistema operativo Windows, executar o simulador de TPM do Windows como [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo e utilizar o exemplo de código do Python para ligar este dispositivo simulado com o Serviço de Aprovisionamento de Dispositivos e o hub IoT. 

> [!IMPORTANT]
> Este artigo se aplica somente ao SDK do Python v1 preterido. Os clientes de dispositivo e serviço para o serviço de provisionamento de dispositivos no Hub IOT ainda não estão disponíveis na v2. No momento, a equipe está trabalhando atualmente para trazer a paridade de v2 para recursos.

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de ter instalado o [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior, com a carga de trabalho C++' desenvolvimento de desktop com ' habilitada para a instalação do Visual Studio.

1. Transfira e instale o [sistema de compilação CMake](https://cmake.org/download/).

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Crie uma pasta na sua cópia local deste repositório do GitHub para o processo de compilação CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. O exemplo de código utiliza um simulador Windows TPM. Execute o seguinte comando para ativar a autenticação de token SAS. Também gera uma solução do Visual Studio para o dispositivo simulado.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. Numa linha de comandos separada, navegue para a pasta do simulador TPM e execute o simulador [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Clique em **Permitir o acesso**. O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; Você precisará manter este simulador em execução até o final deste guia de início rápido. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![Simulador TPM](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. Abra a solução gerada na pasta *cmake* com o nome `azure_iot_sdks.sln` e compile-a no Visual Studio.

1. Clique com o botão direito do rato no projeto **tpm_device_provision** e selecione **Configurar como Projeto de Arranque**. Execute a solução. A janela saída exibe a **_chave de endosso_** e a **_ID de registro_** necessária para o registro do dispositivo. Anote estes valores. 

    ![Configuração do TPM](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Entre no portal do Azure, selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos.

1. No menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

1. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Insira a *ID de registro* e a *chave de endosso* do seu dispositivo TPM a partir dos valores que você anotou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Insira uma *ID de dispositivo*exclusiva. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se você optar por não fornecer um, a ID de registro será usada para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Depois de concluído, pressione o botão **salvar** . 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 


## <a name="simulate-the-device"></a>Simular o dispositivo

1. Transfira e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python às variáveis de ambiente específicas da sua plataforma.
    - Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads), para permitir a utilização de DLLs nativas de Python.

1. Siga [estas instruções](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) para criar os pacotes Python.

   > [!NOTE]
   > Se executar `build_client.cmd` certifique-se de que utiliza o sinalizador `--use-tpm-simulator`.
   > 
   > [!NOTE]
   > Se estiver a utilizar `pip`, certifique-se de que instala também o pacote `azure-iot-provisioning-device-client`. Tenha em atenção que os pacotes PIP lançados estão a utilizar o TPM real, não o simulador. Para utilizar o simulador tem de compilar a partir da origem, utilizando o sinalizador `--use-tpm-simulator`.

1. Navegue para a pasta de exemplos.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Através do IDE Python, edite o script python com o nome **provisioning\_device\_client\_sample.py**. Modifique as variáveis *GLOBAL\_PROV\_URI* e *ID\_SCOPE* para os valores anotados previamente. Verifique também se *SECURITY\_DEVICE\_TYPE* está definido como `ProvisioningSecurityDeviceType.TPM`

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Informações de serviço](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Execute o exemplo. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

    ![Registo com êxito](./media/python-quick-create-simulated-device/registration-success.png)

1. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao seu serviço de provisionamento, a ID do dispositivo aparece na folha **dispositivos IOT** do Hub.

    ![O dispositivo é registado no hub IoT](./media/python-quick-create-simulated-device/hubregistration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra a folha **gerenciar registros** para seu serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra a folha **dispositivos IOT** para o Hub, marque a caixa de seleção ao lado da *ID do dispositivo* que você registrou neste guia de início rápido e pressione o botão **excluir** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado do TPM em seu computador e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue no início rápido para o registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de início rápido do Azure-registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-python.md)
