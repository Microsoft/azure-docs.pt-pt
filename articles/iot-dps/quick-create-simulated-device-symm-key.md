---
title: Início rápido – usar chave simétrica para provisionar dispositivo simulado para o Hub IoT do Azure usando C
description: Neste guia de início rápido, você usará o SDK do dispositivo C para criar um dispositivo simulado que usa a chave simétrica com o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 73d2e075dd4d3cf9e9b37aa9131ab5f4c822e140
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895913"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas

Neste guia de início rápido, vai aprender a criar e executar um simulador de dispositivos numa máquina de desenvolvimento do Windows. Irá configurar este dispositivo simulado para utilizar uma chave simétrica para autenticação com uma instância do Serviço de Aprovisionamento de Dispositivos e atribuição a um hub IoT. Será utilizado código de exemplo do [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) para simular uma sequência de arranque do dispositivo que inicia o aprovisionamento. O dispositivo será reconhecido com base numa inscrição individual com uma instância do serviço de aprovisionamento e atribuído a um hub IoT.

Embora este artigo demonstre o provisionamento com um registro individual, você pode usar grupos de registro. Há algumas diferenças ao usar grupos de registro. Por exemplo, você deve usar uma chave de dispositivo derivada com uma ID de registro exclusiva para o dispositivo. Embora os grupos de inscrições de chave simétrica não estejam limitados a dispositivos legados, o artigo [Como aprovisionar dispositivos legados com o atestado de chave simétrica](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de inscrições. Para obter mais informações, veja [Inscrições em grupo para o Atestado de Chave Simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). 

Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. Este guia de início rápido exige que já tenha criado a instância do Serviço de Aprovisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a seção apropriada em [preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação do SDK do.

* O [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com a carga de [trabalho C++' desenvolvimento de desktop com '](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) habilitada. Também há suporte para o Visual Studio 2015 e o Visual Studio 2017.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparar um ambiente de programação para o SDK C do Azure IoT

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

O SDK inclui o código de exemplo para um dispositivo simulado. Esse dispositivo simulado irá tentar fazer o aprovisionamento durante a respetiva sequência de arranque.

1. Baixe o [sistema de Build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Clique em **marcas** e localize o nome da marca para a versão mais recente na [página versão do SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Abra uma linha de comandos ou a shell do Git Bash. Execute os comandos a seguir para clonar a versão mais recente do repositório GitHub do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) . Use a marca que você encontrou na etapa anterior como o valor para o parâmetro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Execute os seguintes comandos no diretório `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Execute o seguinte comando para compilar uma versão do SDK específica da plataforma de cliente de desenvolvimento. Será gerada uma solução do Visual Studio para o dispositivo simulado no diretório `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Se `cmake` não encontrar o compilador de C++, poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de inscrição de dispositivos no portal

1. Entre no [portal do Azure](https://portal.azure.com), selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos.

2. Selecione a guia **gerenciar** registros e, em seguida, selecione o botão **adicionar registro individual** na parte superior. 

3. No painel **adicionar registro** , insira as informações a seguir e pressione o botão **salvar** .

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Gerar chaves automaticamente**: Marque esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **Symm-Key-Device-007**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **device-007**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Depois de salvar o registro, a chave **primária** e a **chave secundária** serão geradas e adicionadas à entrada de registro. A inscrição do dispositivo de chave simétrica é apresentada como **symm-key-device-007** na coluna *ID de Registo* do separador *Inscrições Individuais*. 

    Abra a inscrição e copie o valor da **Chave Primária** gerada.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a sequência de primeiro arranque para o dispositivo

Nesta secção, atualize o código de exemplo para enviar a sequência de arranque do dispositivo para a instância do Serviço de Aprovisionamento de Dispositivos. Esta sequência de arranque fará com que o dispositivo seja reconhecido e atribuído a um hub IoT ligado à instância do Serviço de Aprovisionamento de Dispositivos.



1. Na portal do Azure, selecione a guia **visão geral** para o serviço de provisionamento de dispositivos e anote o valor de **_escopo da ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Visual Studio, abra o ficheiro de solução **azure_iot_sdks.sln** gerado ao executar o CMake. O ficheiro de solução deve estar na seguinte localização:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Na janela *Solution Explorer* (Explorador de Soluções) do Visual Studio, navegue para a pasta **Provision\_Samples**. Expanda o projeto de exemplo com o nome **prov\_dev\_client\_sample**. Expanda **Source Files** (Ficheiros de Origem) e abra **prov\_dev\_client\_sample.c**.

4. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, conforme mostrado abaixo:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Localize a chamada para `prov_dev_set_symmetric_key_info()` em **prov\_dev\_cliente\_exemplo. c** , que é comentado.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Remova a marca de comentário da chamada de função e substitua os valores de espaço reservado (incluindo os colchetes angulares) por sua ID de registro e valores de chave primária.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Guarde o ficheiro.

7. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 

8. No menu do Visual Studio, selecione **Debug** (Depurar)  > **Start without debugging** (Iniciar sem depuração) para executar a solução. No prompt para recriar o projeto, selecione **Sim**para recompilar o projeto antes de executá-lo.

    O resultado seguinte é um exemplo do dispositivo simulado a arrancar com êxito e a ligar à instância do serviço de aprovisionamento para atribuição a um hub IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. No portal, navegue até o Hub IoT ao qual o dispositivo simulado foi atribuído e selecione a guia **dispositivos IOT** . No provisionamento bem-sucedido da Simulated para o Hub, sua ID de dispositivo aparece na folha **dispositivos IOT** , com *status* como **habilitado**. Talvez seja necessário pressionar o botão **Atualizar** na parte superior. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra **gerenciar registros** para o serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra **dispositivos IOT** para seu hub, marque a caixa de seleção ao lado da *ID do dispositivo* do dispositivo registrado neste guia de início rápido e, em seguida, pressione o botão **excluir** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado em seu computador Windows e o provisionou para o Hub IoT usando a chave simétrica com o serviço de provisionamento de dispositivos no Hub IoT do Azure no Portal. Para saber como registrar seu dispositivo programaticamente, vá para o guia de início rápido para o registro programático de dispositivos X. 509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure-registrar dispositivos X. 509 no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-x509-java.md)
