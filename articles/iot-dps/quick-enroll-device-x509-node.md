---
title: Este início rápido mostra como inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos do Azure com Node.js | Microsoft Docs
description: Este início rápido utiliza inscrições em grupo. Neste início rápido, vai inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure com o SDK do serviço Node.js
author: wesmc7777
ms.author: v-yiso
origin.date: 04/10/2019
ms.date: 05/06/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: a861bc52734f5b2bc6e455965428328cd1415b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736101"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Início rápido: Inscrever dispositivos X.509 no serviço de aprovisionamento de dispositivos com node. js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Este início rápido mostra como utilizar o Node.js para criar programaticamente um [Grupo de inscrição](concepts-service.md#enrollment-group) que utiliza certificados X.509 de AC de raiz ou intermediários. O grupo de inscrição é criado com o [SDK de IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node) e uma aplicação Node.js de exemplo. Um grupo de inscrição controla o acesso ao serviço de aprovisionamento de dispositivos que partilham um certificado de assinatura comum na respetiva cadeia de certificados. Para saber mais, veja [Controlar o acesso a dispositivos para o serviço de aprovisionamento com certificados X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Para obter mais informações sobre como utilizar a Infraestrutura de Chaves Públicas (PKI) baseada em certificados X.509 com o Hub IoT do Azure e o Serviço de Aprovisionamento de Dispositivos, veja [Descrição geral da segurança do certificado de AC X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Este início rápido espera que já tenha criado um hub IoT e a instância do Serviço de Aprovisionamento de Dispositivos. Se ainda não tiver criado estes recursos, conclua o início rápido [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de avançar neste artigo.

Embora os passos deste artigo funcionem em computadores Windows e Linux, este artigo foi concebido para um computador de desenvolvimento Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

- Instale o [Node.js v4.0 ou superior](https://nodejs.org).
- Instale o [Git](https://git-scm.com/download/).


## <a name="prepare-test-certificates"></a>Preparar os certificados de teste

Neste início rápido, precisa de ter um ficheiro .pem ou .cer com a parte pública de um certificado X.509 de AC de raiz ou intermediário. Este certificado tem de ser carregado para o serviço de aprovisionamento e verificado pelo serviço. 

O [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) contém as ferramentas de teste que podem ajudar a criar uma cadeia de certificados X.509, carregar um certificado de raiz ou intermediário dessa cadeia e realizar uma prova de posse com o serviço para verificar o certificado. Os certificados criados com as ferramentas do SDK foram concebidos para utilização **apenas para testes de desenvolvimento**. Estes certificados **não podem ser utilizados em produção**. Contêm palavras-passe hard-coded ("1234") que expiram após 30 dias. Para saber como obter certificados adequados para utilização de produção, veja [Como obter um certificado X.509 de AC](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) na documentação do Hub IoT do Azure.

Para utilizar estas ferramentas de teste para gerar certificados, execute os seguintes passos: 
 
1. Abra uma linha de comandos ou shell do Git Bash e mude para uma pasta de trabalho no seu computador. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Esta operação deve demorar vários minutos a ser concluída.

   As ferramentas de teste estão localizada em *azure-iot-sdk-c/tools/CACertificates* do repositório que clonou.    

2. Siga os passos em [Gerir certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 



## <a name="create-the-enrollment-group-sample"></a>Criar o exemplo do grupo de inscrição 

 
1. A partir de uma janela de comando na sua pasta de trabalho, execute:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Com um editor de texto, crie um ficheiro **create_enrollment_group.js** na sua pasta de trabalho. Adicione o seguinte código ao ficheiro e guarde:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ```

## <a name="run-the-enrollment-group-sample"></a>Executar o exemplo do grupo de inscrição
 
1. Para executar o exemplo, precisa da cadeia de ligação para o seu serviço de aprovisionamento. 
    1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o Serviço Aprovisionamento de Dispositivos. 
    2. Clique em **Políticas de acesso partilhado** e, em seguida, clique na política de acesso que pretende utilizar para abrir as respetivas propriedades. Na janela **Política de Acesso**, copie e tome nota da cadeia de ligação da chave primária. 

       ![Obter a cadeia de ligação do serviço de aprovisionamento a partir do portal](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Conforme indicado em [Preparar os certificados de teste](quick-enroll-device-x509-node.md#prepare-test-certificates), também precisa de um ficheiro .pem com um certificado X.509 de AC de raiz ou intermediário que foi anteriormente carregado e verificado com o serviço de aprovisionamento. Para verificar que o certificado foi carregado e confirmado, na página de resumo do Serviço de Aprovisionamento de Dispositivos no portal do Azure, clique em **Certificados**. Encontre o certificado que pretende utilizar para a inscrição do grupo e certifique-se de que o valor do estado é *verificado*.

    ![Certificado verificado no portal](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Para criar um grupo de inscrição individual para o seu certificado, execute o seguinte comando (inclua as aspas à volta dos argumentos do comando):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Após a criação com êxito, a janela de comando apresenta as propriedades do novo grupo de inscrição.

    ![Propriedades de inscrição na saída do comando](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Certifique-se de que o grupo de inscrição foi criado. No portal do Azure, no painel de resumo do Serviço de Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Grupos de Inscrição** e certifique-se de que a nova entrada de inscrição (*primeiro*) está presente.

    ![Propriedades de inscrição no portal](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Limpar recursos
Se quiser explorar os exemplos do serviço Node.js, não limpe os recursos criados neste Início Rápido. Se não planear continuar, utilize os passos seguintes para eliminar todos os recursos do Azure criados no Guia Rápido.
 
1. Feche a janela da saída do exemplo de Node.js no seu computador.
2. Navegue até ao seu serviço de Aprovisionamento de Dispositivos no portal do Azure, clique em **Gerir inscrições** e, em seguida, selecione o separador **Grupos de Inscrição**. Selecione o *ID de Registo* relativo à entrada de inscrição que criou com este Guia Rápido e clique no botão **Eliminar** na parte superior do painel.  
3. No serviço de Aprovisionamento de Dispositivos no portal do Azure, clique em **Certificados**, clique no certificado que carregou para este Guia Rápido e clique no botão **Eliminar** na parte superior da janela  **Detalhes do Certificado**.  
 
## <a name="next-steps"></a>Passos Seguintes
Este Guia Rápido, criou uma inscrição de grupo para um certificado de AC de raiz ou intermediário X.509 com o Serviço de Aprovisionamento de Dispositivos no Hub IoT do Azure. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)