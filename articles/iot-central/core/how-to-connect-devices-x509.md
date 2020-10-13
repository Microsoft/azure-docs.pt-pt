---
title: Conecte dispositivos com certificados X.509 numa aplicação Azure IoT Central
description: Como ligar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9a93602327b5c5294d6c17c1804c04c6603dcf37
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999881"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Como ligar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT

A IoT Central suporta tanto as assinaturas de acesso partilhado (SAS) como os certificados X.509 para garantir a comunicação entre um dispositivo e a sua aplicação. O [Create e ligue uma aplicação de cliente ao seu tutorial de aplicação Azure IoT Central](./tutorial-connect-device-nodejs.md) utiliza SAS. Neste artigo, aprende-se a modificar a amostra de código para utilizar x.509.  Os certificados X.509 são recomendados em ambientes de produção. Para mais informações, consulte [Get connected to Azure IoT Central](./concepts-get-connected.md).

Este artigo mostra duas formas de usar X.509 - matrículas de grupo tipicamente [usadas](how-to-connect-devices-x509.md#use-a-group-enrollment) em um ambiente de produção, e [inscrições individuais úteis para testes.](how-to-connect-devices-x509.md#use-an-individual-enrollment)

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão do [Create e ligue uma aplicação ao cliente ao seu tutorial de aplicação Azure IoT Central (Node.js).](./tutorial-connect-device-nodejs.md)
- [Git.](https://git-scm.com/download/)
- Faça o download e instale [o OpenSSL](https://www.openssl.org/). Se estiver a utilizar o Windows, pode utilizar os binários a partir da [página OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Use uma inscrição em grupo

Utilize certificados X.509 com inscrição em grupo em ambiente de produção. Numa inscrição em grupo, adicione um certificado X.509 raiz ou intermédio à sua aplicação IoT Central. Os dispositivos com certificados de folhas derivados da raiz ou do certificado intermédio podem ligar-se à sua aplicação.

## <a name="generate-root-and-device-cert"></a>Gerar raiz e cert de dispositivo

Nesta secção, utiliza-se um certificado X.509 para ligar um dispositivo com um certificado derivado do certificado do grupo de inscrição, que pode ligar-se à sua aplicação IoT Central.

> [!WARNING]
> Esta forma de gerar certs X.509 é apenas para testes. Para um ambiente de produção deve utilizar o seu mecanismo oficial e seguro para a geração de certificados.

1. Abra uma linha de comandos. Clone o repositório GitHub para os scripts de geração de certificados:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navegue para o script do gerador de certificados e instale as embalagens necessárias:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Crie um certificado de raiz e, em seguida, obtém um certificado de dispositivo executando o script. Certifique-se de que utiliza apenas alfanuméricos e hífenes inferiores para o nome do certificado:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

Estes comandos produzem três ficheiros cada para a raiz e o certificado do dispositivo

filename | conteúdos
-------- | --------
\<name\>_cert.pem | A parte pública do certificado X509
\<name\>_key.pem | A chave privada para o certificado X509
\<name\>_fullchain.pem | Todo o chaveiro para o certificado X509.

## <a name="create-a-group-enrollment"></a>Criar uma inscrição em grupo

1. Abra a sua aplicação IoT Central e navegue para a **Administração**  no painel esquerdo e selecione **a ligação do dispositivo**.

1. Selecione + Crie grupo de **inscrições**e crie um novo grupo de inscrições chamado _MyX509Group_ com um tipo de **certificados de atestado (X.509)**.

1. Abra o grupo de inscrições que criou e selecione **Gerir o Primário.**

1. Selecione a opção de ficheiro e carremeça o ficheiro de certificado raiz chamado _mytestrootcert_cert.pem_ que gerou anteriormente:

    ![Upload de certificados](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Para completar a verificação, gere o código de verificação, copie-o e, em seguida, use-o para criar um certificado de verificação X.509 na pronta do comando:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Faça o upload do certificado de verificação assinado _verification_cert.pem_ para completar a verificação:

    ![Certificado Verificado](./media/how-to-connect-devices-x509/verified.png)

Agora pode ligar dispositivos que tenham um certificado X.509 derivado deste certificado de raiz primário. Depois de salvar o grupo de inscrições, tome nota do ID Scope.

## <a name="run-sample-device-code"></a>Executar código de dispositivo de amostra

1. Na aplicação Azure IoT Central, selecione **Dispositivos**e crie um novo dispositivo com _a minha defesa_ como **ID** do dispositivo a partir do modelo do dispositivo **sensor ambiental.**

1. Copie os ficheiros _mytestdevice_key.pem_ e _mytestdevice_cert.pem_ para a pasta que contém a aplicação _environmentalSensor.js._ Criou esta aplicação quando completou o [tutorial de dispositivo (Node.js) do dispositivo ..](./tutorial-connect-device-nodejs.md)

1. Navegue para a pasta que contenha a aplicação environmentalSensor.js e execute o seguinte comando para instalar o pacote X.509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Edite o ficheiro **environmentalSensor.js.**
    - Substitua o `idScope` valor pelo **ID Scope** que fez anteriormente.
    - Substitua `registrationId` o valor por `mytestdevice` .

1. Editar as `require` declarações da seguinte forma:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Editar a secção que cria o cliente da seguinte forma:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. Modificar a secção que abre a ligação da seguinte forma:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Execute o script e verifique se o dispositivo foi a provisionado com sucesso:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Também pode verificar se a telemetria aparece no painel de instrumentos.

    ![Verificar telemetria do dispositivo](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Use uma inscrição individual

Utilize certificados X.509 com uma inscrição individual para testar o seu dispositivo e solução. Numa inscrição individual, não há nenhum certificado X.509 de raiz ou intermédio na sua aplicação IoT Central. Os dispositivos utilizam um certificado X.509 auto-assinado para ligar à sua aplicação.

## <a name="generate-self-signed-device-cert"></a>Gerar cert de dispositivo auto-assinado

Nesta secção, utiliza-se um certificado X.509 auto-assinado para ligar dispositivos para inscrição individual, que são utilizados para a inscrição de um único dispositivo. Os certificados auto-assinados são apenas para testes.

Crie um certificado de dispositivo X.509 auto-assinado executando o script. Certifique-se de que utiliza apenas alfanuméricos e hífenes inferiores para o nome do certificado:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Criar inscrição individual

1. Na aplicação Azure IoT Central, selecione **Dispositivos**e crie um novo dispositivo com **ID do dispositivo** como _o meu certificador_ do modelo do dispositivo sensor ambiental. Tome nota do **ID Scope,** use-o mais tarde.

1. Abra o dispositivo que criou e selecione **Connect**.

1. Selecione **as inscrições individuais** como método e certificados **de ligação** **(X.509)** como o mecanismo:

    ![Inscrição individual](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selecione a opção de ficheiro em mente primária e carrememe o ficheiro de certificado chamado _mytestselfcertprimary_cert.pem_ que gerou anteriormente.

1. Selecione a opção de ficheiro para o certificado secundário e carremeça o ficheiro de certificado chamado _mytestselfcertsecondary_cert.pem._ Em seguida, **selecione Guardar:**

    ![Upload de certificado de inscrição individual](./media/how-to-connect-devices-x509/individual-enrollment.png)

O dispositivo está agora a provisionado com certificado X.509.

## <a name="run-a-sample-individual-enrollment-device"></a>Executar uma amostra de dispositivo de inscrição individual

1. Copie os ficheiros _mytestselfcertprimary_key.pem_ e _mytestselfcertprimary_cert.pem_ para a pasta que contém a aplicação environmentalSensor.js. Criou esta aplicação quando completou o [tutorial de dispositivo (Node.js) do dispositivo ..](./tutorial-connect-device-nodejs.md)

1. Edite o ficheiro **environmentalSensor.js** da seguinte forma e guarde-o.
    - Substitua o `idScope` valor pelo **ID Scope** que fez anteriormente.
    - Substitua `registrationId` o valor por `mytestselfcertprimary` .
    - Substitua **o dispositivo varCert** como:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Execute o script e verifique se o dispositivo foi a provisionado com sucesso:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Também pode verificar se a telemetria aparece no painel de instrumentos.

    ![Inscrição individual de telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

Pode repetir os passos acima para _o certificado de certificação_ do meu desempenho também.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar dispositivos usando certificados X.509, o próximo passo sugerido é aprender a monitorizar a conectividade do [dispositivo usando o Azure CLI](howto-monitor-devices-azure-cli.md)
