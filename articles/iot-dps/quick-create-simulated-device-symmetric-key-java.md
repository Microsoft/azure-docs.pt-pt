---
title: Quickstart - Utilize a chave simétrica para fornecer dispositivo simulado ao Hub Azure IoT usando Java
description: Neste arranque rápido, utilizará o dispositivo Java SDK para criar um dispositivo simulado que utiliza a chave simétrica com o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76941403"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas

Neste guia de início rápido, vai aprender a criar e executar um simulador de dispositivos numa máquina de desenvolvimento do Windows. Configurará este dispositivo simulado para utilizar uma chave simétrica para autenticar com uma instância do Serviço de Fornecimento de Dispositivos (DPS) e será atribuído a um hub IoT. O código de amostra dos [SDKs Microsoft Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java) será utilizado para simular uma sequência de boot para o dispositivo que inicia o fornecimento. O dispositivo será reconhecido com base numa inscrição individual com uma instância de serviço DPS e atribuído a um hub IoT.

Embora este artigo demonstre o provisionamento com um registro individual, você pode usar grupos de registro. Há algumas diferenças ao usar grupos de registro. Por exemplo, você deve usar uma chave de dispositivo derivada com uma ID de registro exclusiva para o dispositivo. Embora os grupos de inscrições de chave simétrica não estejam limitados a dispositivos legados, o artigo [Como aprovisionar dispositivos legados com o atestado de chave simétrica](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de inscrições. Para obter mais informações, veja [Inscrições em grupo para o Atestado de Chave Simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). 

Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar este início rápido. Este guia de início rápido exige que já tenha criado a instância do Serviço de Aprovisionamento de Dispositivos.

Este artigo é orientado para uma estação de trabalho baseada no Windows. No entanto, pode efetuar os procedimentos no Linux. Para obter um exemplo para Linux, veja [Como aprovisionar para multi-inquilinos](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que tem o Kit de [Desenvolvimento Java SE 8](https://aka.ms/azure-jdks) ou posteriormente instalado na sua máquina.

* Transfira e instale o [Maven](https://maven.apache.org/install.html).

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Prepare o ambiente Java SDK 

1. Certifique-se de que o Git está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

2. Abra uma linha de comandos. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navegue até à raiz `azure-iot-sdk-java` diretório e construa o projeto para descarregar todos os pacotes necessários.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu à esquerda e abra a instância do serviço de fornecimento de dispositivos (DPS).

2. Selecione a guia **gerenciar** registros e, em seguida, selecione o botão **adicionar registro individual** na parte superior. 

3. No painel **adicionar registro** , insira as informações a seguir e pressione o botão **salvar** .

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Gerar chaves automaticamente**: Marque esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **symm-key-java-device-007**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **java-dispositivo-007**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Depois de salvar o registro, a chave **primária** e a **chave secundária** serão geradas e adicionadas à entrada de registro. A sua inscrição simétrica do dispositivo chave aparece como **symm-key-java-device-007** sob a coluna *DE Identificação* de Registo no separador *Individual Registration.* 

    Abra a inscrição e copie o valor da **Chave Primária** gerada. Utilizará este valor-chave e o **ID de registo** mais tarde quando atualizar o código Java para o dispositivo.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simular a sequência de arranque do dispositivo

Nesta secção, irá atualizar o código da amostra do dispositivo para enviar a sequência de boot do dispositivo para a sua instância DPS. Esta sequência de arranque fará com que o dispositivo seja reconhecido, autenticado e atribuído a um hub IoT ligado à instância DPS.

1. No menu do Serviço de Fornecimento de Dispositivos, selecione **visão geral** e note o seu _id scope_ e _provisioning service Global Endpoint_.

    ![Informações de serviço](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Abra o código de amostra do dispositivo Java para edição. O caminho completo para o código de amostra do dispositivo é:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Adicione o _id scope_ e o serviço de _provisionamento Global Endpoint_ da sua instância DPS. Inclua também a chave simétrica primária e o ID de inscrição que escolheu para a sua inscrição individual. Guarde as alterações. 

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

4. Construa a amostra e navegue até à pasta `target` para executar o ficheiro .jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. A saída esperada deve ser semelhante à seguinte:

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

6. No portal do Azure, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Depois de ter conseguido fornecer o dispositivo de chave simétrica simulado ao hub, o seu ID do dispositivo aparece na lâmina do **Device Explorer,** com o *STATUS* tal como **está ativado**.  Poderá ter de premir o botão **Refresh** na parte superior se já abriu a lâmina antes de executar a aplicação do dispositivo de amostra. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).
>


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra **gerenciar registros** para o serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra **dispositivos IOT** para seu hub, marque a caixa de seleção ao lado da *ID do dispositivo* do dispositivo registrado neste guia de início rápido e, em seguida, pressione o botão **excluir** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado em seu computador Windows e o provisionou para o Hub IoT usando a chave simétrica com o serviço de provisionamento de dispositivos no Hub IoT do Azure no Portal. Para saber como registrar seu dispositivo programaticamente, vá para o guia de início rápido para o registro programático de dispositivos X. 509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure-registrar dispositivos X. 509 no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-x509-java.md)
