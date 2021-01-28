---
title: Conecte dispositivos com certificados X.509 numa aplicação Azure IoT Central
description: Como ligar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bffff099e8df2b944cbef50a074ef625267ed238
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944637"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Como ligar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT

A IoT Central suporta tanto as assinaturas de acesso partilhado (SAS) como os certificados X.509 para garantir a comunicação entre um dispositivo e a sua aplicação. O [Create e ligue uma aplicação de cliente ao seu tutorial de aplicação Azure IoT Central](./tutorial-connect-device.md) utiliza SAS. Neste artigo, aprende-se a modificar a amostra de código para utilizar x.509.  Os certificados X.509 são recomendados em ambientes de produção. Para mais informações, consulte [Get connected to Azure IoT Central](./concepts-get-connected.md).

Este artigo mostra duas formas de usar X.509 - matrículas de grupo tipicamente [usadas](how-to-connect-devices-x509.md#use-a-group-enrollment) em um ambiente de produção, e [inscrições individuais úteis para testes.](how-to-connect-devices-x509.md#use-an-individual-enrollment)

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão do [Create e conecte uma aplicação do cliente ao seu tutorial de aplicação Azure IoT Central (JavaScript).](./tutorial-connect-device.md)
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

1. Crie um certificado de raiz e, em seguida, obtém um certificado de dispositivo executando o script:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Um ID do dispositivo pode conter letras, números e `-` o caráter.

Estes comandos produzem três ficheiros cada para a raiz e o certificado do dispositivo

filename | conteúdos
-------- | --------
\<name\>_cert.pem | A parte pública do certificado X509
\<name\>_key.pem | A chave privada para o certificado X509
\<name\>_fullchain.pem | Todo o chaveiro para o certificado X509.

## <a name="create-a-group-enrollment"></a>Criar uma inscrição em grupo

1. Abra a sua aplicação IoT Central e navegue para a **Administração**  no painel esquerdo e selecione **a ligação do dispositivo**.

1. Selecione + Crie grupo de **inscrições** e crie um novo grupo de inscrições chamado _MyX509Group_ com um tipo de **certificados de atestado (X.509)**.

1. Abra o grupo de inscrições que criou e selecione **Gerir o Primário.**

1. Selecione a opção de ficheiro e carremeça o ficheiro de certificado raiz chamado _mytestrootcert_cert.pem_ que gerou anteriormente:

    ![Upload de certificados](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Para completar a verificação, gere o código de verificação, copie-o e, em seguida, use-o para criar um certificado de verificação X.509 na pronta do comando:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Faça o upload do certificado de verificação assinado _verification_cert.pem_ para completar a verificação:

    ![Certificado Verificado](./media/how-to-connect-devices-x509/verified.png)

Agora pode ligar dispositivos que tenham um certificado X.509 derivado deste certificado de raiz primário.

Depois de salvar o grupo de inscrições, tome nota do ID Scope.

## <a name="run-sample-device-code"></a>Executar código de dispositivo de amostra

1. Copie os ficheiros **sampleDevice01_key.pem** e **sampleDevice01_cert.pem** para a pasta _azure-iot-sdk-node/dispositivo/samples/pnp_ que contém a aplicação **simple_thermostat.js.** Utilizou esta aplicação quando completou o [tutorial de dispositivo Connect a (JavaScript).](./tutorial-connect-device.md)

1. Navegue para a pasta _azure-iot-sdk-node/dispositivo/samples/pnp_ que contém a aplicação **simple_thermostat.js** e executar o seguinte comando para instalar a embalagem X.509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Abra o ficheiro **simple_thermostat.js** num editor de texto.

1. Editar as `require` declarações para incluir as seguintes:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Adicione as seguintes quatro linhas à secção "Informações de ligação DPS" para rubricar a `deviceCert` variável:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Editar a `provisionDevice` função que cria o cliente substituindo a primeira linha com a seguinte:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Na mesma função, modifique a linha que define a `deviceConnectionString` variável da seguinte forma:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Na `main` função, adicione a seguinte linha após a linha que `Client.fromConnectionString` chama:

    ```javascript
    client.setOptions(deviceCert);
    ```

1. No seu ambiente de concha, desaprote as seguintes duas variáveis ambientais:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > Definiu as outras variáveis ambientais necessárias quando completou a [Criação e conecte uma aplicação de cliente ao seu tutorial de aplicação Azure IoT Central.](./tutorial-connect-device.md)

1. Execute o script e verifique se o dispositivo foi a provisionado com sucesso:

    ```cmd/sh
    node simple_thermostat.js
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

1. Na aplicação Azure IoT Central, selecione **Dispositivos** e crie um novo dispositivo com **iD do dispositivo** como _meu certificado auto-secundário_ do modelo do dispositivo do termóstato. Tome nota do **ID Scope,** use-o mais tarde.

1. Abra o dispositivo que criou e selecione **Connect**.

1. Selecione **as inscrições individuais** como método e certificados **de ligação** **(X.509)** como o mecanismo:

    ![Inscrição individual](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selecione a opção de ficheiro em mente primária e carrememe o ficheiro de certificado chamado _mytestselfcertprimary_cert.pem_ que gerou anteriormente.

1. Selecione a opção de ficheiro para o certificado secundário e carremeça o ficheiro de certificado chamado _mytestselfcertsecondary_cert.pem._ Em seguida, **selecione Guardar:**

    ![Upload de certificado de inscrição individual](./media/how-to-connect-devices-x509/individual-enrollment.png)

O dispositivo está agora a provisionado com certificado X.509.

## <a name="run-a-sample-individual-enrollment-device"></a>Executar uma amostra de dispositivo de inscrição individual

1. Copie os ficheiros _mytestselfcertprimary_key.pem_ e _mytestselfcertprimary_cert.pem_ para a pasta _azure-iot-sdk-node/dispositivo/samples/pnp_ que contém a aplicação **simple_thermostat.js.** Utilizou esta aplicação quando completou o [tutorial de dispositivo Connect a (JavaScript).](./tutorial-connect-device.md)

1. Modifique as variáveis ambientais utilizadas na amostra acima:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Execute o script e verifique se o dispositivo foi a provisionado com sucesso:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Também pode verificar se a telemetria aparece no painel de instrumentos.

    ![Inscrição individual de telemetria](./media/how-to-connect-devices-x509/telemetry-primary.png)

Pode repetir os passos acima para _o certificado de certificação_ do meu desempenho também.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a ligar dispositivos usando certificados X.509, o próximo passo sugerido é aprender a monitorizar a conectividade do [dispositivo usando o Azure CLI](howto-monitor-devices-azure-cli.md)
