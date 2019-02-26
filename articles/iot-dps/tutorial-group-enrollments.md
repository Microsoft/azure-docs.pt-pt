---
title: Aprovisionar um dispositivo X.509 simulado no Hub IoT do Azure com Java e grupos de inscrição | Microsoft Docs
description: Tutorial do Azure - Criar e aprovisionar um dispositivo X.509 simulado com o SDK de dispositivos e serviços Java e grupos de inscrição no Serviço Aprovisionamento de Dispositivos no Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 8e926c3ff7c3d7abc9467291e9b1de77781f664e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805058"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Criar e aprovisionar um dispositivo X.509 simulado com o SDK de dispositivos e serviços Java e inscrições em grupos no Serviço Aprovisionamento de Dispositivos no Hub IoT

Estes passos mostram como simular um dispositivo X.509 no seu computador de desenvolvimento que executa o SO Windows e utilizar um código de exemplo para ligar esse dispositivo ao Serviço Aprovisionamento de Dispositivos e ao seu hub IoT mediante a utilização de grupos de inscrição. 

Antes de avançar, certifique-se de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador.

1. Transfira e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Utilize o seguinte procedimento [descrição geral de certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para criar os certificados de teste.

    > [!NOTE]
    > Este passo requer [OpenSSL](https://www.openssl.org/), que pode qualquer um ser criado e instalado de origem ou transferido e instalado a partir de um [3rd party](https://wiki.openssl.org/index.php/Binaries) como [isso](https://sourceforge.net/projects/openssl/). Se já tiver criado os certificados de _raiz_, _intermédios_ e de _dispositivo_, pode ignorar este passo.
    >

    1. Siga os dois primeiros passos para criar os certificados de _raiz_ e  _intermédio_.

    1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o seu serviço de aprovisionamento.

        1. No painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Certificados** e clique no botão **Adicionar** na parte superior.

        1. Em **Adicionar Certificado**, introduza as informações seguintes:
            - Introduza um nome exclusivo de certificado.
            - Selecione o **_rootca_** ficheiro que criou.
            - Quando tiver terminado, clique no botão **Guardar**.

           ![Adicionar certificado](./media/tutorial-group-enrollments/add-certificate.png)

        1. Selecione o certificado que acabou de criar:
            - Clique em **Gerar Código de Verificação**. Copie o código gerado.
            - Execute o passo de verificação. Introduza o _código de verificação_ ou clique com o botão direito do rato na janela do PowerShell que está em execução.  Prima **Enter**.
            - Selecione o ficheiro **_verifyCert4.pem_** acabado de criar no portal do Azure. Clique em **Verificar**.

              ![Validar certificado](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Execute os passos para criar os certificados do dispositivo e limpar recursos para concluir.

       > [!NOTE]
       > Ao criar certificados de dispositivo, não se esqueça de utilizar apenas carateres alfanuméricos em letra minúscula e hífenes no nome do dispositivo.
       >


## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. Abra uma linha de comandos. Clone o repositório do GitHub para os exemplos de código do SDK Java:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. No código fonte transferido, navegue até à pasta de exemplo **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Abra o ficheiro **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** num editor à sua escolha e adicione os seguintes detalhes:

    1. Adicione `[Provisioning Connection String]` para o seu serviço de aprovisionamento, a partir do portal da seguinte forma:

        1. Navegue até ao serviço de aprovisionamento no [portal do Azure](https://portal.azure.com).

        1. Abra as **Políticas de acesso partilhado** e selecione uma política que tenha a permissão *EnrollmentWrite*.

        1. Copie a **Cadeia de ligação da chave primária**.

            ![Obter a cadeia de ligação de aprovisionamento a partir do portal](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. No ficheiro de código de exemplo **_ServiceEnrollmentGroupSample.java_**, substitua a cadeia `[Provisioning Connection String]` pela **Cadeia de ligação da chave primária**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Abra o ficheiro de certificado de assinatura intermediários num editor de texto. Atualização do `PUBLIC_KEY_CERTIFICATE_STRING` valor com o valor do seu certificado de assinatura de intermediário.

        Se os certificados de dispositivo com o shell de Bash, gerado *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave de certificado intermédio. Se os certificados foram gerados com o PowerShell, *./Intermediate1.pem* será o ficheiro de certificado intermédio.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Navegue até ao hub IoT ligado ao seu serviço de aprovisionamento no [portal do Azure](https://portal.azure.com). Abra o separador **Descrição geral** para o hub e copie o **Hostname**. Atribua este **Hostname** ao parâmetro *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Estude o código de exemplo. Este cria, atualiza, consulta e elimina uma inscrição de grupo para dispositivos X.509. Para verificar a inscrição com êxito no portal, comente temporariamente as seguintes linhas de código no final do ficheiro _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Guarde o ficheiro _ServiceEnrollmentGroupSample.java_.

1. Abra uma janela de comando e navegue até à pasta **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Compile o código de exemplo, utilizando este comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Execute o exemplo, utilizando estes comandos na janela de comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Observe a janela de saída para ver se a inscrição foi executada com êxito.

    ![Inscrição com êxito](./media/tutorial-group-enrollments/enrollment.png) 

1. Navegue até ao serviço de aprovisionamento no portal do Azure. Clique em **Gerir inscrições**. Repare que o seu grupo de dispositivos X.509 é apresentado no separador **Grupos de Inscrição**, com um *NOME DE GRUPO* gerado automaticamente.

## <a name="simulate-the-device"></a>Simular o dispositivo

1. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Descrição geral** e tome nota do _Âmbito do ID_ e do _Ponto Final Global do Serviço de Aprovisionamento_.

    ![Informações de serviço](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Abra uma linha de comandos. Navegue até à pasta do projeto de exemplo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Editar `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para incluir sua _âmbito do ID_ e _aprovisionamento Global ponto final de serviço_ que anotou anteriormente.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Atualização do `leafPublicPem` e `leafPrivateKey` variáveis com os certificados de dispositivo públicas e privadas.

    Se gerar os certificados de dispositivo com o PowerShell, os ficheiros mydevice * conter a chave pública, a chave privada e o PFX para o dispositivo.

    Se gerar os certificados de dispositivo com o shell de Bash,./certs/new-device.cert.pem contém a chave pública. Chave privada do dispositivo será no ficheiro./private/new-device.key.pem.

    Abrir o seu ficheiro de chave pública e a atualização a `leafPublicPem` variável com esse valor. Copie o texto do _---BEGIN PRIVATE KEY---_ ao _---END PRIVATE KEY---_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Abrir o seu ficheiro de chave privada e a atualização a `leafPrivatePem` variável com esse valor. Copie o texto do _---BEGIN RSA PRIVATE KEY---_ ao _---END RSA PRIVATE KEY---_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Adicionar uma nova variável logo abaixo `leafPrivateKey` para o seu certificado intermédio. Nome esta nova variável `intermediateKey`. Dar a ele o valor do seu certificado de assinatura de intermediário.

    Se os certificados de dispositivo com o shell de Bash, gerado *./certs/azure-iot-test-only.intermediate.cert.pem* contém a chave de certificado intermédio. Se os certificados foram gerados com o PowerShell, *./Intermediate1.pem* será o ficheiro de certificado intermédio.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. Na `main` funcionar, adicione o `intermediateKey` para o `signerCertificates` coleção antes da inicialização do `securityProviderX509`.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Guardar as alterações e criar o exemplo. Navegue até à pasta de destino e execute o ficheiro jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Registo com êxito](./media/tutorial-group-enrollments/registration.png)

1. No portal, navegue para o IoT hub ligado ao seu serviço de aprovisionamento e abra o painel **Explorador de Dispositivos**. Após o aprovisionamento bem-sucedido do dispositivo X.509 simulado no hub, o ID de dispositivo aparece no painel **Explorador de Dispositivos**, com o *ESTADO* **ativado**. Tenha em atenção que poderá ter de clicar no botão **Atualizar**, na parte superior, se já tiver aberto o painel antes de executar a aplicação de dispositivo de exemplo. 

    ![O dispositivo é registado no hub IoT](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar e a explorar o exemplo de cliente do dispositivo, não limpe os recursos criados neste Guia Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Abra o painel **Gerir Inscrições** para o seu serviço e, em seguida, clique no separador **Inscrições Individuais**. Selecione o *ID DE REGISTO* do dispositivo que inscreveu neste Início Rápido e clique no botão **Eliminar** na parte superior. 
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Abra o painel **Dispositivos IoT** do seu hub, selecione o *ID DE DISPOSITIVO* do dispositivo que registou neste Início Rápido e, em seguida, clique no botão **Eliminar** Na parte superior.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um dispositivo X.509 simulado no seu computador Windows e aprovisionou-o no seu hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT e os grupos de inscrição. Para saber mais sobre o seu dispositivo X.509, avance para os conceitos do dispositivo. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service device concepts](concepts-device.md) (Conceitos de dispositivo do Serviço Aprovisionamento de Dispositivos no Hub IoT)
