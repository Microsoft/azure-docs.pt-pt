---
title: Quickstart - Use a chave simétrica para providenciar dispositivo simulado para O Azure IoT Hub usando Java
description: Neste arranque rápido utilizará o dispositivo Java SDK para criar um dispositivo simulado que utiliza a chave simétrica com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc, devx-track-java
ms.openlocfilehash: a32811b439a569a8f8f82fcc046e0f4b89e47a38
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966705"
---
# <a name="quickstart-provision-a-simulated-device-to-iot-hub-with-symmetric-keys"></a>Quickstart: Fornecimento de um dispositivo simulado ao IoT Hub com teclas simétricas

Neste guia de início rápido, vai aprender a criar e executar um simulador de dispositivos numa máquina de desenvolvimento do Windows. Configurará este dispositivo simulado para utilizar uma chave simétrica para autenticar com uma instância do Serviço de Provisionamento de Dispositivos (DPS) e será atribuído a um hub IoT. O código de amostra dos [SDKs IoT do Microsoft Azure para Java](https://github.com/Azure/azure-iot-sdk-java) será utilizado para simular uma sequência de arranque para o dispositivo que inicia o fornecimento. O dispositivo será reconhecido com base numa inscrição individual com uma instância de serviço DPS e atribuído a um hub IoT.

Embora este artigo demonstre o provisionamento com uma inscrição individual, você pode usar grupos de inscrição. Há algumas diferenças na utilização de grupos de inscrição. Por exemplo, deve utilizar uma chave de dispositivo derivada com um ID de registo único para o dispositivo. Embora os grupos de inscrições de chave simétrica não estejam limitados a dispositivos legados, o artigo [Como aprovisionar dispositivos legados com o atestado de chave simétrica](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de inscrições. Para obter mais informações, veja [Inscrições em grupo para o Atestado de Chave Simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento.](about-iot-dps.md#provisioning-process) 

Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. Este guia de início rápido exige que já tenha criado a instância do Serviço de Aprovisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que tem [o Kit de Desenvolvimento Java SE 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) ou mais tarde instalado na sua máquina.

* Transfira e instale o [Maven](https://maven.apache.org/install.html).

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Prepare o ambiente Java SDK 

1. Certifique-se de que o Git está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

2. Abra uma linha de comandos. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navegue até ao diretório de raiz `azure-iot-sdk-java` e construa o projeto para descarregar todos os pacotes necessários.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu esquerdo e abra a sua instância de Serviço de Provisionamento de Dispositivos (DPS).

2. Selecione o **separador Descodusagens** de Gestão e, em seguida, selecione o botão **de inscrição individual Adicionar** no topo. 

3. No painel **'Adicionar Inscrição',** introduza as seguintes informações e prima o botão **Guardar.**

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Teclas de geração automática**: Verifique esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **symm-key-java-device-007**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **java-device-007**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Uma vez guardada a sua inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à inscrição. A inscrição do seu dispositivo chave simétrico aparece como **symm-key-java-device-007** sob a coluna *de Identificação* de Registo no separador *Inscrições Individuais.* 

    Abra a inscrição e copie o valor da **Chave Primária** gerada. Utilizará este valor chave e o **ID de registo** mais tarde quando atualizar o código Java para o dispositivo.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simular sequência de arranque do dispositivo

Nesta secção, atualizará o código de amostra do dispositivo para enviar a sequência de arranque do dispositivo para a sua instância DPS. Esta sequência de arranque fará com que o dispositivo seja reconhecido, autenticado e atribuído a um hub IoT ligado à instância DPS.

1. A partir do menu serviço de fornecimento de dispositivos, selecione **Overview** e note o seu _ID Scope_ e _Serviço de Provisionamento Global Endpoint_.

    ![Informações de serviço](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Abra o código de amostra do dispositivo Java para edição. O caminho completo para o código de amostra do dispositivo é:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Adicione o _ID Scope_ and _Provisioning Service Global Endpoint_ da sua instância DPS. Inclua também a chave simétrica primária e o ID de inscrição que escolheu para a sua inscrição individual. Guarde as alterações. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Abra um pedido de comando para a construção. Navegue para a pasta do projeto de amostra de provisionamento do repositório Java SDK.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Construa a amostra e navegue na `target` pasta para executar o ficheiro .jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. A produção esperada deve ser semelhante à seguinte:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. No portal do Azure, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o fornecimento bem sucedido do dispositivo de chave simétrica simulado para o hub, o seu ID do dispositivo aparece na lâmina do Explorador do **Dispositivo,** com *o STATUS* **como ativado**.  Pode ser necessário premir o botão **Refresh** na parte superior se já tiver aberto a lâmina antes de executar a aplicação do dispositivo de amostra. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub.](../iot-hub/iot-hub-devguide-device-twins.md)
>


## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar e explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de Provisionamento de Dispositivos. Abrir **As Inscrições** para o seu serviço e, em seguida, selecionar o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* do dispositivo que inscreveu neste arranque rápido e prima o botão **Eliminar** na parte superior do painel. 
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo simulado na sua máquina Windows e forcou-o no seu hub IoT utilizando a chave simétrica com o Serviço de Provisionamento de Dispositivos Azure IoT Hub no portal. Para aprender a inscrever o seu dispositivo programáticamente, continue a iniciar o quickstart para a inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivos X.509 para O Serviço de Provisionamento de Dispositivos Azure IoT Hub](quick-enroll-device-x509-java.md)