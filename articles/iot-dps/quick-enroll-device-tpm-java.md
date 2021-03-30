---
title: Quickstart - Inscreva o dispositivo TPM no Serviço de Provisionamento de Dispositivos Azure utilizando a Java
description: Quickstart - Inscreva o dispositivo TPM para o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) utilizando o serviço Java SDK. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96463052"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Quickstart: Inscreva o dispositivo TPM no Serviço de Provisionamento de Dispositivos IoT Hub utilizando o Serviço Java SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Neste arranque rápido, cria programaticamente uma inscrição individual para um dispositivo TPM simulado no Serviço de Provisionamento de Dispositivos Azure IoT Hub utilizando o Serviço Java SDK com a ajuda de uma aplicação Java de amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão da [Configuração do Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Conclusão das [teclas criptográficas De leitura do dispositivo TPM](quick-create-simulated-device.md#simulatetpm).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Kit de Desenvolvimento Java SE 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). Este quickstart instala o [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) abaixo. Funciona tanto no Windows como no Linux. Este arranque rápido utiliza o Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git.](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Certifique-se de que tem o [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) instalado no seu computador. 

2. Configure variáveis de ambiente para a sua instalação de Java. A variável `PATH` deve incluir o caminho completo para o diretório *jdk1.8.x\bin*. Se esta for a primeira instalação de Java do seu computador, crie uma nova variável de ambiente com o nome `JAVA_HOME` e aponte-a para o caminho completo para o diretório *jdk1.8.x*. Num computador Windows, este diretório encontra-se na pasta *C:\\Programas\\Java\\* e pode criar ou editar variáveis de ambiente, procurando **Editar as variáveis de ambiente do sistema** no **Painel de controlo** do seu computador Windows. 

   Para verificar se Java está configurado com êxito no seu computador, execute o seguinte comando na janela de comando:

    ```cmd\sh
    java -version
    ```

3. Transfira e extraia [Maven 3](https://maven.apache.org/download.cgi) no seu computador. 

4. Edite a variável de ambiente `PATH` para apontar para a pasta *apache-maven-3.x.x\\bin* dentro da pasta onde o Maven é extraído. Pode confirmar se o Maven é instalado com êxito ao executar este comando na janela de comando:

    ```cmd\sh
    mvn --version
    ```

5. Certifique-se de que o [git](https://git-scm.com/download/) está instalado no computador e é adicionado à variável de ambiente `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Transferir e modificar o código de exemplo de Java

Esta secção mostra como adicionar os detalhes de aprovisionamento do seu dispositivo TPM ao código de exemplo. 

1. Abra uma linha de comandos. Clone o repo GitHub para a amostra de código de inscrição do dispositivo utilizando o [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. No código fonte transferido, navegue até à pasta de exemplo **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Abra o ficheiro **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** num editor à sua escolha e adicione os seguintes detalhes:

   1. Adicione `[Provisioning Connection String]` para o seu serviço de aprovisionamento, a partir do portal da seguinte forma:
       1. Navegue para o seu serviço de prestação no [portal Azure](https://portal.azure.com). 
       2. Abra as **Políticas de acesso partilhado** e selecione uma política que tenha a permissão *EnrollmentWrite*.
       3. Copie a **Cadeia de ligação da chave primária**. 

           ![Obter a cadeia de ligação de aprovisionamento a partir do portal](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. No ficheiro de código de exemplo **_ServiceEnrollmentSample.java_**, substitua a cadeia `[Provisioning Connection String]` pela **Cadeia de ligação da chave primária**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Adicione os detalhes do dispositivo TPM:
       1. Obtenha o *ID de Registo* e a *chave de endossamento de TPM* para uma simulação de dispositivo TPM, seguindo os passos que levam à secção [Simular dispositivo TPM](quick-create-simulated-device.md#simulatetpm).
       2. Utilize o **_ID de Registo_** e a **_Chave de endossamento_** da saída do passo anterior, para substituir o `[RegistrationId]` e `[TPM Endorsement Key]` no ficheiro de código de exemplo **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Opcionalmente, pode configurar o seu serviço de aprovisionamento através do código de exemplo:
      - Para adicionar esta configuração ao exemplo, siga estes passos:
        1. Navegue até ao hub IoT ligado ao seu serviço de aprovisionamento no [portal do Azure](https://portal.azure.com). Abra o separador **Descrição geral** para o hub e copie o **Hostname**. Atribua este **Hostname** ao parâmetro *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Atribua um nome amigável ao parâmetro *DEVICE_ID* e mantenha o *PROVISIONING_STATUS* como o valor *ATIVADO* predefinido. 
    
      - OU, se optar por não configurar o serviço de aprovisionamento, certifique-se de que comenta ou elimina as seguintes instruções no ficheiro _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Estude o código de exemplo. Esta cria, atualiza, consulta e elimina uma inscrição de dispositivo TPM individual. Para verificar a inscrição com êxito no portal, comente temporariamente as seguintes linhas de código no final do ficheiro _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Guarde o ficheiro _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Compilar e executar o código de exemplo de Java

1. Abra uma janela de comando e navegue até à pasta **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Compile o código de exemplo, utilizando este comando:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Este comando descarrega o pacote Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) para a sua máquina. Este pacote inclui os binários para o [Java Service SDK,](https://azure.github.io/azure-iot-sdk-java/master/service/)que o código de amostra precisa de ser construído. 

3. Execute o exemplo, utilizando estes comandos na janela de comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Observe a janela de saída para ver se a inscrição foi executada com êxito. 

5. Navegue até ao serviço de aprovisionamento no portal do Azure. **Selecione Gerir as inscrições** e selecione o **separador Inscrições Individuais.** Note que o *ID* de registo do seu dispositivo TPM simulado está listado. 

    ![Verificar a inscrição do TPM com êxito no portal](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Limpar os recursos
Se planeia explorar a amostra de serviço java, não limpe os recursos criados neste quickstart. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de Java no seu computador.
1. Feche a janela do simulador TPM que pode ter criado para simular o seu dispositivo TPM.
1. Navegue para o seu serviço de Provisionamento de Dispositivos no portal Azure, **selecione Gerir as inscrições** e, em seguida, selecione o separador **Inscrições Individuais.** Selecione a caixa de verificação ao lado do *ID de registo* para a entrada de inscrição que criou usando este arranque rápido e prima o botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, inscreveu um dispositivo TPM simulado no seu serviço de Provisionamento de Dispositivos. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)