---
title: Provisionar o dispositivo X. 509 simulado no Hub IoT do Azure usando o Node. js
description: Criar e provisionar um dispositivo X. 509 simulado usando o SDK do dispositivo node. js para o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS). Este guia de início rápido usa registros individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 5a713018cb616fd1b82c253554932f6589185e0b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976473"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo simulado X. 509 usando o SDK do dispositivo node. js para o serviço de provisionamento de dispositivos no Hub IoT
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estes passos mostram como criar uma entrada de inscrição no Serviço de Aprovisionamento de Dispositivos, simular um dispositivo x.509 no seu computador de desenvolvimento, ligar o dispositivo simulado com o Serviço de Aprovisionamento de Dispositivos e registar o dispositivo no seu hub IoT com o [SDK de Dispositivo Node.js do Hub IoT](https://github.com/Azure/azure-iot-sdk-node).

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Conclua os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar.

2. Certifique-se de que tem o [Node.js v4.0 ou superior](https://nodejs.org) instalado no seu computador.

3. Certifique-se de que o [Git](https://git-scm.com/download/) está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. 

4. Verifique se o [OpenSSL](https://www.openssl.org/) está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Esta biblioteca pode ser criada e instalada a partir da origem ou transferida e instalada a partir de [terceiros](https://wiki.openssl.org/index.php/Binaries) como [este](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Se já tiver criado os certificados x.509 de _raiz_, _intermédios_, e/ou de _folha_, pode ignorar este passo e todos os passos seguintes relativos à geração de certificados.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de inscrição individual

Nesta secção, vai utilizar um certificado X.509 autoassinado e é importante ter em consideração o seguinte:

* Os certificados autoassinados são apenas para teste e não devem ser utilizados na produção.
* A data de expiração predefinida para um certificado autoassinado é de um ano.

Vai utilizar o código de exemplo do SDK do [Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node.git) para criar o certificado a ser utilizado na entrada de inscrição individual do dispositivo simulado.


1. Abra uma linha de comandos. Clone o repositório do GitHub para os exemplos de código:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Navegue até ao script de gerador de certificados e compile o projeto. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Crie um certificado X.509 de _folha_ ao executar o script com o seu _certificate-name_. O nome comum do certificado de folha torna-se o [ID de registo](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), por conseguinte, não se esqueça de utilizar apenas carateres alfanuméricos em minúsculas e hífenes.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Entre no [portal do Azure](https://portal.azure.com), selecione o botão **todos os recursos** no menu esquerdo e abra a instância do serviço de provisionamento de dispositivos.

5. No menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

6. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - No *arquivo. PEM ou. cer do certificado primário*, escolha *selecionar um arquivo* para selecionar o arquivo de certificado **{nome-do-certificado} _cert. pem** criado nas etapas anteriores.  
   - Opcionalmente, pode fornecer as seguintes informações:
     - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
     - Introduza um ID de dispositivo exclusivo. Certifique-se de que evita dados confidenciais quando der o nome ao seu dispositivo. 
     - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
     - Depois de concluído, pressione o botão **salvar** . 

     [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Após o registro bem-sucedido, o dispositivo X. 509 aparece como **{CertificateName}** na coluna *ID de registro* na guia registros *individuais* . Observe esse valor para mais tarde.

## <a name="simulate-the-device"></a>Simular o dispositivo

O [SDK de Dispositivo Node.js do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-node) fornece uma forma fácil de simular um dispositivo. Para leitura adicional, veja [Conceitos do dispositivo](https://docs.microsoft.com/azure/iot-dps/concepts-device).

1. Na portal do Azure, selecione a folha **visão geral** para o serviço de provisionamento de dispositivos e anote os valores do **_ponto de extremidade do dispositivo GLobal_** e do **_escopo da ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Copie o _certificado_ e a _chave_ para a pasta de exemplo.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Navegue para o script de teste do dispositivo e compile o projeto. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Edite o ficheiro **register\_x509.js**. Guarde o ficheiro depois de efetuar as seguintes alterações.
    - Substitua `provisioning host` pelo **_Ponto Final do Dispositivo Global_** indicado no **Passo 1** acima.
    - Substitua `id scope` pelo **_escopo da ID_** anotado na **etapa 1** acima. 
    - Substitua `registration id` pela **_ID de registro_** anotada na seção anterior.
    - Substitua `cert filename` e `key filename` pelos ficheiros que copiou no **Passo 2** acima. 

5. Execute o script e verifique se o dispositivo foi aprovisionado com êxito.

    ```cmd/sh
    node register_x509.js
    ```   

6. No portal, navegue até o Hub IoT vinculado ao serviço de provisionamento e abra a folha **dispositivos IOT** . No provisionamento bem-sucedido do dispositivo X. 509 simulado para o Hub, sua ID de dispositivo aparecerá na folha **dispositivos IOT** , com *status* como **habilitado**. Talvez seja necessário pressionar o botão **Atualizar** na parte superior se você já abriu a folha antes de executar o aplicativo de dispositivo de exemplo. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
2. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Abra a folha **gerenciar registros** para seu serviço e, em seguida, selecione a guia registros **individuais** . Marque a caixa de seleção ao lado da *ID de registro* do dispositivo registrado neste guia de início rápido e pressione o botão **excluir** na parte superior do painel. 
3. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Abra a folha **dispositivos IOT** para o Hub, marque a caixa de seleção ao lado da *ID do dispositivo* que você registrou neste guia de início rápido e pressione o botão **excluir** na parte superior do painel.


## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo X. 509 simulado e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos no Hub IoT do Azure no Portal. Para saber como registrar seu dispositivo X. 509 de forma programática, continue no início rápido para o registro programático de dispositivos X. 509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure-registrar dispositivos X. 509 no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-x509-node.md)
