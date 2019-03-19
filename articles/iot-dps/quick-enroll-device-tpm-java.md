---
title: Inscrever o dispositivo TPM no Serviço de Aprovisionamento de Dispositivos do Azure com Java | Microsoft Docs
description: Início Rápido do Azure - inscrever o dispositivo TPM no Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure com o SDK do serviço Java. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 12/20/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ae1fbd93b26838b262dc6f07081f20b63e853d5c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104748"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Inscrever o dispositivo TPM no Serviço de Aprovisionamento de Dispositivos no Hub IoT com o SDK do serviço Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Estes passos explicam como criar através de programação uma inscrição individual para um dispositivo TPM simulado no Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure com o [SDK do Serviço Java](https://azure.github.io/azure-iot-sdk-java/service/) com ajuda de uma aplicação Java de exemplo. Embora o SDK do serviço Java funcione em computadores Windows e Linux, este artigo utiliza um computador de desenvolvimento Windows para percorrer o processo de inscrição.

Antes de avançar, certifique-se de que [configura o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) e que [simula um dispositivo TPM](quick-create-simulated-device.md#simulatetpm).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador. 

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

1. Abra uma linha de comandos. Clone o repositório do GitHub para o exemplo de código de inscrição de dispositivos com o SDK do serviço Java:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. No código fonte transferido, navegue até à pasta de exemplo **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Abra o ficheiro **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** num editor à sua escolha e adicione os seguintes detalhes:

   1. Adicione `[Provisioning Connection String]` para o seu serviço de aprovisionamento, a partir do portal da seguinte forma:
       1. Navegue até ao serviço de aprovisionamento no [portal do Azure](https://portal.azure.com). 
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

   Este comando transfere o pacote Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) para o seu computador. Este pacote inclui os binários para o SDK do serviço Java, que o código de exemplo precisa de compilar. 

3. Execute o exemplo, utilizando estes comandos na janela de comando:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Observe a janela de saída para ver se a inscrição foi executada com êxito. 

5. Navegue até ao serviço de aprovisionamento no portal do Azure. Clique em **Gerir inscrições**e selecione o separador **Inscrições Individuais**. Repare que o *ID de Registo* do seu dispositivo TPM simulado está agora listado. 

    ![Verificar a inscrição do TPM com êxito no portal](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser explorar o exemplo de serviço Java, não limpe os recursos criados neste Manual de Início Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de Java no seu computador.
1. Feche a janela do simulador TPM que pode ter criado para simular o seu dispositivo TPM.
1. Navegue até ao seu serviço de Aprovisionamento de Dispositivos no portal do Azure, clique em **Gerir inscrições** e, em seguida, selecione o separador **Inscrições Individuais**. Selecione o *ID de Registo* do dispositivo que inscreveu com este Manual de Início Rápido e clique no botão **Eliminar** na parte superior do painel. 

## <a name="next-steps"></a>Passos Seguintes
Neste Manual de Início Rápido, inscreveu um dispositivo TPM simulado no seu serviço de Aprovisionamento de Dispositivos. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
