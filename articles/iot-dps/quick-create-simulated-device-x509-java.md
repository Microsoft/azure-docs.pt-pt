---
title: Aprovisionar um dispositivo X.509 simulado no Hub IoT do Azure com Java | Microsoft Docs
description: Início Rápido do Azure - criar e aprovisionar um dispositivo X.509 simulado com o SDK de dispositivo Java com o Serviço Aprovisionamento de Dispositivos no Hub IoT. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 52fbef24e8a1b9fd7e0ade404c23a587c81d6b0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106003"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e aprovisionar um dispositivo X.509 simulado com o SDK de dispositivo Java com o Serviço de Aprovisionamento de Dispositivos no Hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estes passos mostram como simular um dispositivo X.509 no seu computador de desenvolvimento que executa o SO Windows e utilizar um código de exemplo para ligar esse dispositivo ao Serviço Aprovisionamento de Dispositivos e ao seu hub IoT. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): Utilizado para inscrever vários dispositivos relacionados.
- [Inscrições individuais](concepts-service.md#individual-enrollment): Utilizado para inscrever um dispositivo individual.

Este artigo vai demonstrar as inscrições individuais.

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador.

2. Transfira e instale o [Maven](https://maven.apache.org/install.html).

3. Certifique-se de que o Git está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

4. Abra uma linha de comandos. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Navegue para o diretório do `azure-iot-sdk-` de raiz e crie o projeto para transferir todos os pacotes necessários.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navegue até ao projeto de gerador de certificados e compile o projeto. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de inscrição individual

Nesta secção, vai utilizar um certificado X.509 autoassinado e é importante ter em consideração o seguinte:

* Os certificados autoassinados são apenas para teste e não devem ser utilizados na produção.
* A data de expiração predefinida para um certificado autoassinado é de um ano.

Vai utilizar o código de exemplo do [SDK do Azure IoT para Java](https://github.com/Azure/azure-iot-sdk-java.git) para criar o certificado a ser utilizado na entrada de inscrição individual do dispositivo simulado.


1. Utilize a linha de comandos dos passos anteriores, navegue para a pasta `target` e, em seguida, execute o ficheiro jar criado no passo anterior.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Introduza **N** para _Do you want to input common name_. Copie para a área de transferência a saída de `Client Cert`, a partir de *-----BEGIN CERTIFICATE-----* e terminando em *-----END CERTIFICATE-----*.

   ![Gerador de certificados individuais](./media/java-quick-create-simulated-device-x509/individual.png)

3. Crie um ficheiro com o nome **_X509individual.pem_** no seu computador Windows, abra-o num editor à sua escolha e copie os conteúdos da área de transferência para este ficheiro. Guarde o ficheiro e feche o editor.

4. Na linha de comandos, introduza **N** para _Do you want to input Verification Code_ e mantenha a saída do programa aberta para referência mais à frente no Manual de Início Rápido. Depois, copie os valores `Client Cert` e `Client Cert Private Key`, para utilização na secção seguinte.

5. Inicie sessão no [portal do Azure](https://portal.azure.com), clique no botão **Todos os recursos** no menu do lado esquerdo e abra a instância do Serviço Aprovisionamento de Dispositivos.

6. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique no botão **Adicionar inscrição individual** na parte superior. 

7. No painel **Adicionar Inscrição**, introduza as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - Em *Ficheiro de certificado primário .pem ou .cer*, clique em *Selecionar um ficheiro* para selecionar o ficheiro de certificado **X509individual.pem** criado nos passos anteriores.  
   - Opcionalmente, pode fornecer as seguintes informações:
     - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
     - Introduza um ID de dispositivo exclusivo. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
     - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
     - Quando tiver terminado, clique no botão **Guardar**. 

     [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Quando a inscrição for bem-sucedida, o seu dispositivo X.509 aparece como **microsoftriotcore** na coluna *ID de Registo* no separador *Inscrições Individuais*. 



## <a name="simulate-the-device"></a>Simular o dispositivo

1. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Descrição geral** e tome nota do _Âmbito do ID_ e do _Ponto Final Global do Serviço de Aprovisionamento_.

    ![Informações de serviço](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Abra uma linha de comandos. Navegue para a pasta de projeto de exemplo no repositório SDK do Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Introduza as informações do serviço de aprovisionamento e as informações de identidade X.509 no seu código. Isto é utilizado durante o aprovisionamento automático, para atestado do dispositivo simulado, antes do registo do dispositivo:

   - Edite o ficheiro `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, de forma a incluir o seu _Âmbito de ID_ e o _Ponto Final do Serviço de Aprovisionamento Global_, conforme indicado anteriormente. Inclua também o _Certificado de Cliente_ e a _Chave Privada do Certificado de Cliente_ conforme indicado na secção anterior.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Utilize o seguinte formato ao copiar/colar o seu certificado e chave privada:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Compile o exemplo. Navegue para a pasta `target` e execute o ficheiro jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. No portal do Azure, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO* **ativado**.  Poderá ter de clicar no botão **Atualizar**, na parte superior, se já tiver aberto o painel antes de executar a aplicação de dispositivo de exemplo. 

    ![O dispositivo é registado no hub IoT](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).
>


## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar e a explorar o exemplo de cliente do dispositivo, não limpe os recursos criados neste Guia Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Abra o painel **Gerir Inscrições** para o seu serviço e, em seguida, clique no separador **Inscrições Individuais**. Selecione o *ID DE REGISTO* do dispositivo que inscreveu neste Início Rápido e clique no botão **Eliminar** na parte superior. 
3. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Abra o painel **Dispositivos IoT** do seu hub, selecione o *ID DE DISPOSITIVO* do dispositivo que registou neste Início Rápido e, em seguida, clique no botão **Eliminar** Na parte superior.


## <a name="next-steps"></a>Passos Seguintes

Neste Início Rápido, criou um dispositivo X.509 simulado no seu computador Windows. Configurou a sua inscrição no Serviço Aprovisionamento de Dispositivos no Hub IoT e, depois, aprovisionou automaticamente o dispositivo para o seu hub IoT. Para saber como inscrever o seu dispositivo X.509 programaticamente, continue para o Manual de Início Rápido para inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Manual de Início Rápido do Azure - Inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-x509-java.md)
