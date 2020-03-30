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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941403"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Guia de Início Rápido: aprovisionar um dispositivo simulado com chaves simétricas

Neste guia de início rápido, vai aprender a criar e executar um simulador de dispositivos numa máquina de desenvolvimento do Windows. Configurará este dispositivo simulado para utilizar uma chave simétrica para autenticar com uma instância do Serviço de Fornecimento de Dispositivos (DPS) e será atribuído a um hub IoT. O código de amostra dos [SDKs Microsoft Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java) será utilizado para simular uma sequência de boot para o dispositivo que inicia o fornecimento. O dispositivo será reconhecido com base numa inscrição individual com uma instância de serviço DPS e atribuído a um hub IoT.

Embora este artigo demonstre o provisionamento com uma inscrição individual, você pode usar grupos de inscrição. Existem algumas diferenças na utilização de grupos de matrícula. Por exemplo, deve utilizar uma chave de dispositivo derivada com um ID de registo único para o dispositivo. Embora os grupos de inscrições de chave simétrica não estejam limitados a dispositivos legados, o artigo [Como aprovisionar dispositivos legados com o atestado de chave simétrica](how-to-legacy-device-symm-key.md) fornece um exemplo de grupo de inscrições. Para obter mais informações, veja [Inscrições em grupo para o Atestado de Chave Simétrica](concepts-symmetric-key-attestation.md#group-enrollments).

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
3. Navegue para `azure-iot-sdk-java` o diretório de raiz e construa o projeto para descarregar todos os pacotes necessários.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu à esquerda e abra a instância do serviço de fornecimento de dispositivos (DPS).

2. Selecione o separador **'Gerir as matrículas'** e, em seguida, selecione o botão **de inscrição individual Adicionar** na parte superior. 

3. No painel **Adicionar Inscrições,** introduza as seguintes informações e prima o botão **Guardar.**

   - **Mecanismo:** selecione **Chave Simétrica** como o *Mecanismo* de atestado de identidades.

   - **Chaves de geração automática**: Verifique esta caixa.

   - **ID de Registo**: introduza um ID de registo para identificar a inscrição. Utilize apenas carateres alfanuméricos em minúsculas e travessões ("-"). Por exemplo, **symm-key-java-device-007**.

   - **ID do Dispositivo do Hub IoT:** introduza o identificador de um dispositivo. Por exemplo, **java-dispositivo-007**.

     ![Adicionar inscrição individual para o atestado de chave simétrica no portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Uma vez que tenha guardado a sua inscrição, a **Chave Primária** e a **Chave Secundária** serão geradas e adicionadas à inscrição. A sua inscrição simétrica do dispositivo chave aparece como **symm-key-java-device-007** sob a coluna *DE Identificação* de Registo no separador *Individual Registration.* 

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

4. Construa a amostra `target` e navegue até à pasta para executar o ficheiro .jar criado.

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
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para mais informações, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Abra **as Inscrições** de Gestão para o seu serviço e, em *REGISTRATION ID* seguida, selecione o separador **Individual Registration.** **Delete** 
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *DISPOSITIVO ID* do dispositivo que registou neste arranque rápido e, em seguida, prima o botão **Apagar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo simulado na sua máquina Windows e aprovisionou-o no seu hub IoT utilizando a chave Simétrica com o Serviço de Provisionamento de Dispositivos Hub Azure IoT no portal. Para aprender a inscrever o seu dispositivo programáticamente, continue a acelerar a inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivos X.509 para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-x509-java.md)
