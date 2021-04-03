---
title: 'Quickstart: Fornecimento de um dispositivo TPM simulado ao Azure IoT Hub utilizando C'
description: Este início rápido utiliza inscrições individuais. Neste arranque rápido, você cria e disponibiliza um dispositivo TPM simulado utilizando o dispositivo C SDK para o Serviço de Provisionamento de Dispositivos hub IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: e2930a3ca2ecb9d8217fdfea1cbcb0e669f61775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960041"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Início Rápido: Aprovisionar um dispositivo TPM simulado com o SDK C do Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Neste início rápido, vai aprender a criar e executar um simulador de dispositivos Trusted Platform Module (TPM) numa máquina de desenvolvimento do Windows. Vai ligar este dispositivo simulado a um hub IoT através de uma instância do Serviço de Aprovisionamento de Dispositivos. Será utilizado código de exemplo do [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) para ajudar a inscrever o dispositivo numa instância do Serviço de Aprovisionamento de Dispositivos e simular uma sequência de arranque para o mesmo.

Se não estiver familiarizado com o processo de autoprovisionamento, reveja a visão geral [do provisionamento.](about-iot-dps.md#provisioning-process) Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo vai demonstrar as inscrições individuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a secção apropriada no preparar o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o ['desenvolvimento do ambiente de trabalho com C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitado. Visual Studio 2015 e Visual Studio 2017 também são suportados.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Preparar um ambiente de desenvolvimento para o SDK C do Azure IoT

Nesta secção, vai preparar um ambiente de desenvolvimento utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) e o exemplo do simulador de dispositivos [TPM](/windows/device-security/tpm/trusted-platform-module-overview).

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes comandos a partir do `azure-iot-sdk-c` diretório:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Compilar o SDK e executar o simulador de dispositivos TPM

Nesta secção, vai compilar o SDK C do Azure IoT, que inclui o código de exemplo do simulador de dispositivos TPM. Este exemplo fornece um [mecanismo de atestado](concepts-service.md#attestation-mechanism) TPM através da autenticação de Token de Assinatura de Acesso Partilhado (SAS).

1. A partir do subdiretório `cmake` criado no repositório git azure-iot-sdk-c, execute o comando seguinte para compilar o exemplo. Será também gerada uma solução do Visual Studio para o dispositivo simulado por este comando de compilação.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Se `cmake` não encontrar o compilador de C++, poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Navegue para a pasta raiz do repositório git que clonou e execute o simulador [TPM](/windows/device-security/tpm/trusted-platform-module-overview) através do caminho mostrado abaixo. Este simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; terá de manter este simulador em execução até ao fim deste início rápido. 

   Se está na pasta *cmake*, execute os seguintes comandos:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Não verá qualquer saída do simulador. Deixe-o continuar a execução da simulação de um dispositivo TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Ler as chaves criptográficas do dispositivo TPM

Nesta secção, vai compilar e executar um exemplo que irá ler a chave de endossamento e o ID de registo do simulador TPM em execução e a escutar nas portas 2321 e 2322. Estes valores serão utilizados na inscrição de dispositivos na instância do Serviço Aprovisionamento de Dispositivos.

1. Inicie o Visual Studio e abra o novo ficheiro de solução com o nome `azure_iot_sdks.sln`. Este ficheiro de solução está localizado na pasta `cmake` que criou anteriormente na raiz do repositório git azure-iot-sdk-c.

2. No menu Estúdio Visual, selecione **Build**  >  **Build Solution** para construir todos os projetos na solução.

3. Na janela *Solution Explorer* (Explorador de Soluções) do Visual Studio, navegue para a pasta **Provision\_Tools**. Clique com o botão direito do rato no projeto **tpm_device_provision** e selecione **Configurar como Projeto de Arranque**. 

4. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. A aplicação lê e exibe um **_ID de registo_** e uma **_chave de endosso._** Anote ou copie estes valores. Serão utilizados na secção seguinte na inscrição de dispositivos. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de inscrição de dispositivos no portal

1. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

1. Selecione o **separador Descodusagens** de Gestão e, em seguida, selecione o botão **de inscrição individual Adicionar** no topo. 

1. No painel **de inscrição adicionar,** insira as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Introduza a chave *de ID* de Registo e *Endosso* para o seu dispositivo TPM a partir dos valores que registou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Introduza um *ID de dispositivo* único (pode utilizar o **dispositivo de teste-docs** sugerido ou fornecer o seu próprio). Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se optar por não fornecer um, o ID de registo será usado para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Uma vez concluído, prima o botão **Guardar.** 

      ![Introduza as informações de inscrição de dispositivos no portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a sequência de primeiro arranque para o dispositivo

Nesta secção, vai configurar o código de exemplo para utilizar o [Advanced Message Queuing Protocol (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) para enviar a sequência de arranque do dispositivo para a instância do Serviço de Aprovisionamento de Dispositivos. Esta sequência de arranque fará com que o dispositivo seja reconhecido e atribuído a um hub IoT ligado à instância do Serviço de Aprovisionamento de Dispositivos.

1. No portal do Azure, selecione o separador **Descrição Geral** do seu Serviço Aprovisionamento de Dispositivos e copie o valor **_Âmbito do ID_**.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Na janela *Solution Explorer* (Explorador de Soluções) do Visual Studio, navegue para a pasta **Provision\_Samples**. Expanda o projeto de exemplo com o nome **prov\_dev\_client\_sample**. Expanda **Source Files** (Ficheiros de Origem) e abra **prov\_dev\_client\_sample.c**.

3. Junto à parte superior do ficheiro, localize as declarações `#define` para cada protocolo de dispositivo, conforme mostrado abaixo. Certifique-se de que apenas `SAMPLE_AMQP` tem os comentários anulados.

    Atualmente, o [protocolo MQTT não é suportado para Inscrição Individual de TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_TPM` em vez de `SECURE_DEVICE_TYPE_X509`, conforme mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**. 

7. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Na pronta para reconstruir o projeto, selecione **Sim,** para reconstruir o projeto antes de correr.

    A saída seguinte é um exemplo do cliente do dispositivo de aprovisionamento a arrancar e a ligar com êxito a uma instância do Serviço de Aprovisionamento de Dispositivos para obter informações e registar o hub IoT:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Uma vez que o dispositivo simulado é alotado ao hub IoT pelo seu serviço de fornecimento, o ID do dispositivo aparece com os **dispositivos IoT** do hub . 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar e explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. Feche a janela do simulador TPM no seu computador.
3. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de Provisionamento de Dispositivos. Abrir **As Inscrições** para o seu serviço e, em seguida, selecionar o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* do dispositivo que inscreveu neste arranque rápido e prima o botão **Eliminar** na parte superior do painel. 
4. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo simulado TPM na sua máquina e forte-o para o seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Para aprender a inscrever o seu dispositivo TPM programáticamente, continue a iniciar rapidamente a inscrição programática de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva o dispositivo TPM no Serviço de Provisionamento de Dispositivos Azure IoT Hub](quick-enroll-device-tpm-java.md)