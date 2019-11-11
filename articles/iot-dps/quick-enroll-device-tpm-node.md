---
title: 'Início rápido: registrar o dispositivo TPM no serviço de provisionamento de dispositivos do Azure usando o Node. js'
description: Início Rápido do Azure - inscrever o dispositivo TPM no Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure com o SDK do serviço Node.js. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 02d5ec3b7ddd960e15b75fd3aa6691a187b5911a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903475"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Início rápido: registrar o dispositivo TPM no serviço de provisionamento de dispositivos no Hub IoT usando o SDK do serviço node. js

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Estes passos explicam como criar através de programação uma inscrição individual para um dispositivo TPM no Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure com o [SDK do Serviço Node.js](https://github.com/Azure/azure-iot-sdk-node) e um exemplo de aplicação Node.js. Opcionalmente, pode inscrever um dispositivo TPM simulado no serviço de aprovisionamento com esta entrada de inscrição individual. Embora estes passos funcionem em computadores Windows e Linux, vamos utilizar um computador de desenvolvimento Windows para efeitos deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de avançar, certifique-se de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md). 
-  Certifique-se de que tem o [Node.js v4.0 ou superior](https://nodejs.org) instalado no seu computador.
- Se pretender inscrever um dispositivo simulado no final deste Manual de Início Rápido, siga os passos em [Criar e aprovisionar um dispositivo simulado](quick-create-simulated-device.md) até ao passo onde pode obter uma chave de endossamento para o dispositivo. Tome nota da chave de endossamento, que irá utilizar mais à frente neste Manual de Início Rápido. **Não siga os passos para criar uma inscrição individual com o portal do Azure.**
 
## <a name="create-the-individual-enrollment-sample"></a>Criar o exemplo de inscrição individual 

 
1. A partir de uma janela de comando na sua pasta de trabalho, execute:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Com um editor de texto, crie um ficheiro **create_individual_enrollment.js** na sua pasta de trabalho. Adicione o seguinte código ao ficheiro e guarde:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Executar o exemplo de inscrição individual
  
1. Para executar o exemplo, precisa da cadeia de ligação para o seu serviço de aprovisionamento. 
    1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o Serviço Aprovisionamento de Dispositivos. 
    2. Clique em **Políticas de acesso partilhado** e, em seguida, clique na política de acesso que pretende utilizar para abrir as respetivas propriedades. Na janela **Política de Acesso**, copie e tome nota da cadeia de ligação da chave primária. 

       ![Obter a cadeia de ligação do serviço de aprovisionamento a partir do portal](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Também precisa da chave de endossamento para o seu dispositivo. Se seguiu o manual de início rápido [Criar e aprovisionar um dispositivo simulado](quick-create-simulated-device.md) para criar um dispositivo TPM simulado, utilize a chave criada para esse dispositivo. Caso contrário, para criar uma inscrição individual de exemplo, pode utilizar a seguinte chave de endossamento fornecida com o SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Para criar uma inscrição individual para o seu dispositivo TPM, execute o seguinte comando (inclua as aspas à volta dos argumentos do comando):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Após a criação com êxito, a janela de comando apresenta as propriedades da nova inscrição individual.

    ![Propriedades de inscrição na saída do comando](./media/quick-enroll-device-tpm-node/output.png) 

4. Verifique se foi criada uma inscrição individual. No portal do Azure, no painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique na nova entrada de inscrição (*primeiro*) para verificar a chave de endossamento e outras propriedades dessa entrada.

    ![Propriedades de inscrição no portal](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Agora que criou uma inscrição individual para um dispositivo TPM, se pretender inscrever um dispositivo simulado, pode continuar com os restantes passos em [Criar e aprovisionar um dispositivo simulado](quick-create-simulated-device.md). Ignore os passos para criar uma inscrição individual com o portal do Azure nesse Manual de Início Rápido.

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser explorar os exemplos do serviço Node.js, não limpe os recursos criados neste Início Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos criados no Guia Rápido.

1. Feche a janela da saída do exemplo de Node.js no seu computador.
1. Se criou um dispositivo TPM simulado, feche a janela do simulador TPM.
2. Navegue até o serviço de provisionamento de dispositivos no portal do Azure, clique em **gerenciar registros**e, em seguida, selecione a guia registros **individuais** . Selecione a ID de *registro* da entrada de registro que você criou usando este início rápido e clique no botão **excluir** na parte superior da folha. 
 
## <a name="next-steps"></a>Passos seguintes
Neste Manual de Início Rápido, criou através de programação uma entrada de inscrição individual para um dispositivo TPM e, opcionalmente, criou um dispositivo simulado TPM no seu computador e aprovisionou-o no seu hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)