---
title: Início rápido-provisionar um dispositivo TPM simulado para o Hub IoT do Azure usando o Java
description: Início rápido-criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo Java para o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 00f95fb249c80dffacdb10f6679f310dce218118
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976690"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo Java para o serviço de provisionamento de dispositivos no Hub IoT do Azure

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Estes passos mostram como criar um dispositivo simulado no seu computador de desenvolvimento que executa o SO Windows, executa o simulador Windows TPM como o [Módulo de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo e utiliza o código de exemplo para ligar esse dispositivo com o Serviço Aprovisionamento de Dispositivos e o seu hub IoT. 

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador.

1. Transfira e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Execute o simulador [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Clique em **Permitir acesso** para permitir que as alterações às definições da _Firewall do Windows_. O simulador escuta através de um socket nas portas 2321 e 2322. Não fechar esta janela; Você precisa manter este simulador em execução até o final deste guia de início rápido. 

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![Simulador TPM](./media/java-quick-create-simulated-device/simulator.png)

1. Numa linha de comandos separada, navegue para a pasta de raiz e crie as dependências de exemplo.

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

1. Navegue para a pasta de exemplo.

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

1. Entre no portal do Azure, selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos. Anote o _Âmbito do ID_ e o _Ponto Final Global do Serviço de Aprovisionamento_.

    ![Informação do Serviço Aprovisionamento de Dispositivos](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. Edite `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` para incluir o _Âmbito do ID_ e o _Ponto Final Global do Serviço de Aprovisionamento_, conforme indicado anteriormente.  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```
    Guarde o ficheiro.

1. Use os comandos a seguir para compilar o projeto, navegue até a pasta de destino e execute o arquivo. jar criado. Substitua o espaço reservado `version` pela sua versão do Java.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. A execução do programa é iniciada. Anote a _chave de endosso_ e a _ID de registro_ da próxima seção e deixe o programa em execução.

    ![Programa de dispositivo do TPM do Java](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

1. Entre no portal do Azure, selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento de dispositivos.

1. No menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

1. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Insira a *ID de registro* e a *chave de endosso* do seu dispositivo TPM a partir dos valores que você anotou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Insira uma *ID de dispositivo*exclusiva. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se você optar por não fornecer um, a ID de registro será usada para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Depois de concluído, pressione o botão **salvar** . 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/java-quick-create-simulated-device/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 


## <a name="simulate-the-device"></a>Simular o dispositivo

1. Na janela de comando que executa o código de exemplo Java em seu computador, pressione *Enter* para continuar a execução do aplicativo. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT.  

    ![Programa de dispositivo de Java TPM final](./media/java-quick-create-simulated-device/program-final.png)

1. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao seu serviço de provisionamento, a ID do dispositivo aparece na folha **dispositivos IOT** do Hub.

    ![O dispositivo é registado no hub IoT](./media/java-quick-create-simulated-device/hubregistration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra a folha **gerenciar registros** para seu serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra a folha **dispositivos IOT** para o Hub, marque a caixa de seleção ao lado da *ID do dispositivo* que você registrou neste guia de início rápido e pressione o botão **excluir** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo simulado do TPM em seu computador e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue no início rápido para o registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de início rápido do Azure-registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-tpm-java.md)
