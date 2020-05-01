---
title: Provisão simulado dispositivo X.509 para O Hub Azure IoT usando Java
description: Azure Quickstart - Crie e forre um dispositivo S.509 simulado utilizando o dispositivo Java SDK para o Serviço de Provisionamento de Dispositivos IoT Hub (DPS). Este quickstart usa matrículas individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 5122726cbda2145d190e3dbeb10a3c5b4ae65947
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82588457"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Criar e fornecer um dispositivo Sisimulado X.509 utilizando o dispositivo Java SDK para o Serviço de Provisionamento de Dispositivos IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Neste arranque rápido, cria-se um dispositivo X.509 simulado num computador Windows. Utiliza o código Java da amostra do dispositivo para ligar este dispositivo simulado ao seu hub IoT utilizando uma inscrição individual com o Serviço de Provisionamento de Dispositivos (DPS).

## <a name="prerequisites"></a>Pré-requisitos

- Revisão dos [conceitos de fornecimento automático.](concepts-auto-provisioning.md)
- Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Kit](https://aka.ms/azure-jdks)de Desenvolvimento Java SE 8 .
- [Maven.](https://maven.apache.org/install.html)
- [Git.](https://git-scm.com/download/)

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

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições individuais](concepts-service.md#individual-enrollment): Utilizado para inscrever um único dispositivo.

Este artigo demonstra inscrições individuais.

1. Utilizando o pedido de comando de `target` passos anteriores, navegue para a pasta e execute o ficheiro .jar criado no passo anterior.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Introduza **N** para _Do you want to input common name_. Copie para a área de transferência a saída de `Client Cert`, a partir de *-----BEGIN CERTIFICATE-----* e terminando em *-----END CERTIFICATE-----*.

   ![Gerador de certificados individuais](./media/java-quick-create-simulated-device-x509/individual.png)

3. Crie um ficheiro com o nome **_X509individual.pem_** no seu computador Windows, abra-o num editor à sua escolha e copie os conteúdos da área de transferência para este ficheiro. Guarde o ficheiro e feche o editor.

4. Na solicitação de comando, introduza **N** para _Do se deseja inserir Código_ de Verificação e manter a saída do programa aberta para referência posterior mente no arranque rápido. Depois, copie os valores `Client Cert` e `Client Cert Private Key`, para utilização na secção seguinte.

5. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione o botão **Todos os recursos** no menu à esquerda e abra a sua instância de Serviço de Provisionamento de Dispositivos.

6. A partir do menu do Serviço de Fornecimento de Dispositivos, selecione **Gerir as matrículas.** Selecione o separador **Individual Registrations** e selecione o botão **de inscrição individual Adicionar** na parte superior. 

7. No painel **Adicionar Inscrições,** introduza as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - Sob o *certificado primário .pem ou .cer file,* escolha *Selecione um ficheiro* para selecionar o ficheiro de certificado **X509individual.pem** criado nos passos anteriores.  
   - Opcionalmente, pode fornecer as seguintes informações:
     - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
     - Introduza um ID de dispositivo exclusivo. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
     - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
     - Uma vez concluído, prima o botão **Guardar.** 

     [![Adicione inscrições individuais para a atesta X.509 no portal](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Quando a inscrição for bem-sucedida, o seu dispositivo X.509 aparece como **microsoftriotcore** na coluna *ID de Registo* no separador *Inscrições Individuais*. 



## <a name="simulate-the-device"></a>Simular o dispositivo

1. No menu do Serviço de Fornecimento de Dispositivos, selecione **visão geral** e note o seu _id scope_ e _provisioning service Global Endpoint_.

    ![Informações de serviço](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Abra uma linha de comandos. Navegue para a pasta de projeto de exemplo no repositório SDK do Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Introduza as informações do serviço de aprovisionamento e as informações de identidade X.509 no seu código. Isto é utilizado durante o aprovisionamento automático, para atestado do dispositivo simulado, antes do registo do dispositivo:

   - Editar o `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`ficheiro, para incluir o seu _id scope_ e _provisioning service Global Endpoint_ como notado anteriormente. Inclua também o _Certificado de Cliente_ e a _Chave Privada do Certificado de Cliente_ conforme indicado na secção anterior.

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

4. Compile o exemplo. Navegue `target` para a pasta e execute o ficheiro .jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. No portal do Azure, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO***ativado**.  Poderá ter de premir o botão **Refresh** na parte superior se já abriu a lâmina antes de executar a aplicação do dispositivo de amostra. 

    ![O dispositivo é registado no hub IoT](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para mais informações, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Abra a lâmina **'Gerir as Matrículas'** para o seu serviço e, *REGISTRATION ID* em seguida, selecione o separador Individual **Delete** **Registration.** 
3. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra a lâmina dos **dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *DISPOSITIVO ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Apagar** na parte superior do painel.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo X.509 simulado na sua máquina Windows. Configurou a sua inscrição no Serviço Aprovisionamento de Dispositivos no Hub IoT e, depois, aprovisionou automaticamente o dispositivo para o seu hub IoT. Para aprender a inscrever o seu dispositivo X.509 programáticamente, continue a acelerar para a inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivos X.509 para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-x509-java.md)
