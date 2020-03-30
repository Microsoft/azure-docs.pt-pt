---
title: Quickstart - Fornecer um dispositivo TPM simulado para o Hub Azure IoT usando Java
description: Quickstart - Crie e forre um dispositivo TPM simulado utilizando o dispositivo Java SDK para o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ce67b5e254a62def5f8b024e960cea7f8780e8b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605486"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Quickstart: Criar e fornecer um dispositivo TPM simulado utilizando o dispositivo Java SDK para o Serviço de Provisionamento de Dispositivos Hub Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Neste arranque rápido, cria-se um dispositivo IoT simulado num computador Windows. O dispositivo simulado inclui um simulador TPM como módulo de segurança de hardware (HSM). Utiliza o código Java da amostra do dispositivo para ligar este dispositivo simulado ao seu hub IoT utilizando uma inscrição individual com o Serviço de Provisionamento de Dispositivos (DPS).

## <a name="prerequisites"></a>Pré-requisitos

- Revisão dos [conceitos de fornecimento automático.](concepts-auto-provisioning.md)
- Conclusão do Serviço de Provisionamento de [Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md).
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Kit](https://aka.ms/azure-jdks)de Desenvolvimento Java SE 8 .
- [Maven.](https://maven.apache.org/install.html)
- [Git.](https://git-scm.com/download/)

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de que tem o [Java SE Development Kit 8](https://aka.ms/azure-jdks) instalado no seu computador.

1. Transfira e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos. Clone o repositório do GitHub para exemplo de código de simulação de dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Executar o simulador [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) para ser o [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) para o dispositivo simulado. Clique em **Permitir acesso** para permitir que as alterações às definições da _Firewall do Windows_. O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela; é necessário manter este simulador a funcionar até ao fim deste guia de arranque rápido. 

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

1. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu à esquerda e abra o serviço de fornecimento de dispositivos. Note o seu _id scope_ e _provisioning service Global Endpoint_.

    ![Informação do Serviço Aprovisionamento de Dispositivos](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. Edite `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` para incluir o seu _id scope_ e _provisioning service Global Endpoint_ como notado anteriormente.  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```
    Guarde o ficheiro.

1. Utilize os seguintes comandos para construir o projeto, navegar para a pasta-alvo e executar o ficheiro .jar criado. Substitua `version` o espaço reservado pela sua versão de Java.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. A execução do programa é iniciada. Note a _chave de aprovação_ e _o ID_ de registo para a próxima secção e deixe o programa em funcionamento.

    ![Programa de dispositivo do TPM do Java](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de inscrição de dispositivos

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições individuais](concepts-service.md#individual-enrollment): Utilizado para inscrever um único dispositivo.

Este artigo demonstra inscrições individuais.

1. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu à esquerda e abra o serviço de fornecimento de dispositivos.

1. A partir do menu do Serviço de Fornecimento de Dispositivos, selecione **Gerir as matrículas.** Selecione o separador **Individual Registrations** e selecione o botão **de inscrição individual Adicionar** na parte superior. 

1. No painel **Adicionar Inscrições,** introduza as seguintes informações:
   - Selecione **TPM** como o *Mecanismo* de atestado de identidades.
   - Introduza a chave *de identificação* e *averbamento* de registo para o seu dispositivo TPM a partir dos valores que observou anteriormente.
   - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
   - Opcionalmente, pode fornecer as seguintes informações:
       - Introduza um ID único *do dispositivo*. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. Se optar por não fornecer um, o ID de registo será utilizado para identificar o dispositivo.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Uma vez concluído, prima o botão **Guardar.** 

     ![Introduza as informações de inscrição de dispositivos no painel do portal](./media/java-quick-create-simulated-device/enterdevice-enrollment.png)  

   Após a instalação bem-sucedida, o *ID de Registo* do seu dispositivo aparece na lista, no separador *Inscrições Individuais*. 


## <a name="simulate-the-device"></a>Simular o dispositivo

1. Na janela de comando que executa o código da amostra Java na sua máquina, prima *Enter* para continuar a executar a aplicação. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT.  

    ![Programa de dispositivo de Java TPM final](./media/java-quick-create-simulated-device/program-final.png)

1. Ao fornecer com sucesso o seu dispositivo simulado ao hub IoT ligado ao seu serviço de provisionamento, o ID do dispositivo aparece na lâmina de **dispositivos IoT** do hub.

    ![O dispositivo é registado no hub IoT](./media/java-quick-create-simulated-device/hubregistration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para mais informações, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Abra a lâmina **'Gerir as Matrículas'** para o seu serviço e, *REGISTRATION ID* em seguida, selecione o separador Individual **Delete** **Registration.** 
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra a lâmina dos **dispositivos IoT** para o seu hub, selecione a caixa de verificação ao lado do *DISPOSITIVO ID* do dispositivo que registou neste arranque rápido e, em seguida, pressione o botão **Apagar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo simulado TPM na sua máquina e foru-o ao seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos IoT Hub. Para aprender a inscrever o seu dispositivo TPM programáticamente, continue a acelerar a inscrição programática de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivo TPM para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-tpm-java.md)
