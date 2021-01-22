---
title: Autenticar dispositivos a jusante - Azure IoT Edge | Microsoft Docs
description: Como autenticar dispositivos a jusante ou dispositivos de folha para o IoT Hub e encaminhar a sua ligação através de dispositivos de gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3876b44bc6bb1ddbc5398126421fb9651003838f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678828"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo a jusante no Hub IoT do Azure

Num cenário transparente de gateway, os dispositivos a jusante (por vezes chamados dispositivos de folha ou dispositivos infantis) precisam de identidades no IoT Hub como qualquer outro dispositivo. Este artigo percorre as opções de autenticação de um dispositivo a jusante para o IoT Hub e, em seguida, demonstra como declarar a ligação gateway.

Existem três passos gerais para estabelecer uma ligação transparente de gateway bem sucedida. Este artigo abrange o segundo passo:

1. Configure o dispositivo gateway como um servidor para que os dispositivos a jusante possam ligar-se a ele de forma segura. Configurar o portal para receber mensagens de dispositivos a jusante e encaminhá-las para o destino adequado. Para esses passos, consulte [configurar um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md).
2. **Crie uma identidade do dispositivo para o dispositivo a jusante para que possa autenticar com o IoT Hub. Configure o dispositivo a jusante para enviar mensagens através do dispositivo gateway.**
3. Ligue o dispositivo a jusante ao dispositivo gateway e comece a enviar mensagens. Para esses passos, consulte [Ligar um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Os dispositivos a jusante podem autenticar-se com o IoT Hub utilizando um dos três métodos: chaves simétricas (por vezes designadas como chaves de acesso partilhadas), certificados auto-assinados X.509 ou Certificados assinados pela X.509 .Ca. Os passos de autenticação são semelhantes aos passos utilizados para configurar qualquer dispositivo não-IoT-Edge com IoT Hub, com pequenas diferenças para declarar a relação gateway.

O fornecimento automático de dispositivos a jusante com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Configurar um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md).

Se estiver a utilizar a autenticação X.509, irá gerar certificados para o seu dispositivo a jusante. Tenha o mesmo certificado de CA de raiz e o script gerador de certificado que usou para o artigo transparente de gateway disponível para usar novamente.

Este artigo refere-se ao *nome de anfitrião gateway* em vários pontos. O nome de anfitrião do gateway é declarado no parâmetro **do nome anfitrião** do ficheiro config.yaml no dispositivo de gateway IoT Edge. É referido na cadeia de ligação do dispositivo a jusante. O nome de anfitrião do gateway tem de ser resolúvel para um Endereço IP, utilizando DNS ou uma entrada de ficheiro de anfitrião no dispositivo a jusante.

## <a name="register-device-with-iot-hub"></a>Dispositivo de registo com IoT Hub

Escolha como pretende que o seu dispositivo a jusante autente com o IoT Hub:

* [Autenticação de chave simétrica](#symmetric-key-authentication): O IoT Hub cria uma chave que coloca no dispositivo a jusante. Quando o dispositivo autenticar, o IoT Hub verifica se as duas teclas coincidem. Não é necessário criar certificados adicionais para utilizar a autenticação de chaves simétricas.

  Este método é mais rápido de começar se estiver a testar gateways num cenário de desenvolvimento ou teste.

* [X.509 autenticação auto-assinada](#x509-self-signed-authentication): Por vezes chamada de autenticação de impressão digital, porque partilha a impressão digital do certificado X.509 do dispositivo com o IoT Hub.

  A autenticação de certificados é recomendada para dispositivos em cenários de produção.

* [Autenticação assinada por X.509 CA](#x509-ca-signed-authentication): Carrequim o certificado de CA raiz para ioT Hub. Quando os dispositivos apresentam o seu certificado X.509 para autenticação, o IoT Hub verifica se pertence a uma cadeia de confiança assinada pelo mesmo certificado de CA raiz.

  A autenticação de certificados é recomendada para dispositivos em cenários de produção.

### <a name="symmetric-key-authentication"></a>Autenticação de chave simétrica

A autenticação da chave simétrica, ou a autenticação da chave de acesso partilhado, é a forma mais simples de autenticar com o IoT Hub. Com a autenticação da chave simétrica, uma chave base64 está associada ao seu IoT device ID no IoT Hub. Inclui essa chave nas suas aplicações IoT para que o seu dispositivo possa apresentá-la quando se ligar ao IoT Hub.

Adicione um novo dispositivo IoT no seu hub IoT, utilizando o portal Azure, Azure CLI ou a extensão IoT para o Código do Estúdio Visual. Lembre-se que os dispositivos a jusante precisam de ser identificados no IoT Hub como dispositivos IoT regulares, e não dispositivos IoT Edge.

Quando criar a nova identidade do dispositivo, forneça as seguintes informações:

* Crie uma identificação para o seu dispositivo.

* Selecione **a tecla simétrica** como o tipo de autenticação.

* Selecione **Definir um dispositivo-mãe** e selecionar o dispositivo de gateway IoT Edge que este dispositivo a jusante irá ligar. Podes sempre mudar o pai mais tarde.

   ![Criar iD do dispositivo com chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Também pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) completar a mesma operação. O exemplo a seguir utiliza o comando [az iot hub de identidade do dispositivo](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IoT com autenticação de chave simétrica e atribuir um dispositivo-mãe:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Em seguida, [recupere e modifique a cadeia de ligação](#retrieve-and-modify-connection-string) para que o seu dispositivo saiba ligar através do seu gateway.

### <a name="x509-self-signed-authentication"></a>X.509 autenticação auto-assinada

Para a autenticação auto-assinada X.509, por vezes referida como autenticação por impressão digital, é necessário criar certificados para colocar no seu dispositivo a jusante. Estes certificados têm uma impressão digital que partilha com o IoT Hub para autenticação.

1. Utilizando o seu certificado de CA, crie dois certificados de dispositivo (primário e secundário) para o dispositivo a jusante.

   Se não tiver autoridade de certificado para criar certificados X.509, pode utilizar os scripts de certificados de demonstração IoT Edge para criar certificados de [dispositivo a jusante](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga os passos para a criação de certificados auto-assinados. Utilize o mesmo certificado de CA de raiz que gerou os certificados para o seu dispositivo gateway.

   Se criar os seus próprios certificados, certifique-se de que o nome do certificado do dispositivo está definido para o ID do dispositivo que utiliza ao registar o dispositivo IoT no Azure IoT Hub. Esta definição é necessária para a autenticação.

2. Recupere a impressão digital SHA1 (chamada impressão digital na interface IoT Hub) de cada certificado, que é uma cadeia de caracteres hexadecimal de 40. Utilize o seguinte comando de abertura para visualizar o certificado e encontrar a impressão digital:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Executar este comando duas vezes, uma para o certificado primário e uma para o certificado secundário. Fornece impressões digitais para ambos os certificados quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

3. Navegue até ao seu hub IoT no portal Azure e crie uma nova identidade do dispositivo IoT com os seguintes valores:

   * Forneça o **ID** do dispositivo que corresponda ao nome do sujeito dos certificados do seu dispositivo.
   * Selecione **X.509 Auto-Assinado** como o tipo de autenticação.
   * Cole as cordas hexadémicas que copiou dos certificados primários e secundários do seu dispositivo.
   * Selecione **Definir um dispositivo-mãe** e escolher o dispositivo de gateway IoT Edge que este dispositivo a jusante irá ligar. Podes sempre mudar o pai mais tarde.

   ![Criar ID do dispositivo com X.509 auto-assinado no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie os certificados do dispositivo primário e secundário e as suas chaves para qualquer local do dispositivo a jusante. Também mova uma cópia do certificado de CA de raiz partilhada que gerou tanto o certificado do dispositivo gateway como os certificados do dispositivo a jusante.

   Você vai referenciar estes ficheiros de certificado em quaisquer aplicações no dispositivo a jusante que se conectam ao IoT Hub. Pode utilizar um serviço como [o Azure Key Vault](../key-vault/index.yml) ou uma função como o protocolo de cópia [Secure](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

5. Dependendo do seu idioma preferido, reveja amostras de como os certificados X.509 podem ser referenciados em aplicações IoT:

   * C#: [Instale a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Também pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para completar a mesma operação de criação de dispositivos. O exemplo a seguir utiliza o comando [az iot hub de identidade do dispositivo](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IoT com a autenticação auto-assinada X.509 e atribui um dispositivo-mãe:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Em seguida, [recupere e modifique a cadeia de ligação](#retrieve-and-modify-connection-string) para que o seu dispositivo saiba ligar através do seu gateway.

### <a name="x509-ca-signed-authentication"></a>Autenticação assinada pela X.509 CA

Para a autenticação assinada pela Autoridade de Certificados X.509 (CA), necessita de um certificado de CA raiz registado no IoT Hub que utilize para assinar certificados para o seu dispositivo a jusante. Qualquer dispositivo que utilize um certificado que tenha sido emitido pelo certificado de CA raiz ou qualquer um dos seus certificados intermédios será autorizado a autenticar.

Esta secção baseia-se nas instruções detalhadas no artigo do IoT Hub [Configurar a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md).

1. Utilizando o seu certificado de CA, crie dois certificados de dispositivo (primário e secundário) para o dispositivo a jusante.

   Se não tiver autoridade de certificado para criar certificados X.509, pode utilizar os scripts de certificados de demonstração IoT Edge para criar certificados de [dispositivo a jusante](how-to-create-test-certificates.md#create-downstream-device-certificates). Siga os passos para a criação de certificados assinados pela AC. Utilize o mesmo certificado de CA de raiz que gerou os certificados para o seu dispositivo gateway.

2. Siga as instruções nos [certificados De registo X.509 CA para a sua](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) secção de hub IoT de *Configurar a segurança X.509 no seu hub Azure IoT*. Nessa secção, executa os seguintes passos:

   1. Faça upload de um certificado de CA raiz. Se estiver a utilizar os certificados de demonstração, a raiz CA é **\<path> /certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verifique se é dono do certificado de CA raiz.

3. Siga as instruções no [dispositivo Criar um dispositivo X.509 para a sua](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) secção de hub IoT de *Configurar a segurança X.509 no seu hub Azure IoT*. Nessa secção, executa os seguintes passos:

   1. Adicione um novo dispositivo. Forneça um nome minúsculo para **identificação** do dispositivo e escolha o tipo de autenticação **X.509 CA Assinado**.

   2. Desementa um dispositivo dos pais. Selecione **Definir um dispositivo-mãe** e escolha o dispositivo de gateway IoT Edge que fornecerá a ligação ao Hub IoT.

4. Crie uma cadeia de certificados para o seu dispositivo a jusante. Use o mesmo certificado de CA de raiz que fez para o IoT Hub para fazer esta cadeia. Utilize o mesmo ID do dispositivo minúsculo que deu à identidade do seu dispositivo no portal.

5. Copie o certificado e as chaves do dispositivo para qualquer local do dispositivo a jusante. Também mova uma cópia do certificado de CA de raiz partilhada que gerou tanto o certificado do dispositivo gateway como os certificados do dispositivo a jusante.

   Irá fazer referência a estes ficheiros em quaisquer aplicações do dispositivo a jusante que se ligam ao IoT Hub. Pode utilizar um serviço como [o Azure Key Vault](../key-vault/index.yml) ou uma função como o protocolo de cópia [Secure](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

6. Dependendo do seu idioma preferido, reveja amostras de como os certificados X.509 podem ser referenciados em aplicações IoT:

   * C#: [Instale a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Também pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para completar a mesma operação de criação de dispositivos. O exemplo a seguir utiliza o comando [az iot hub de identidade de dispositivo](/cli/azure/ext/azure-iot/iot/hub/device-identity) para criar um novo dispositivo IoT com a autenticação assinada X.509 CA e atribui um dispositivo-mãe:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Em seguida, [recupere e modifique a cadeia de ligação](#retrieve-and-modify-connection-string) para que o seu dispositivo saiba ligar através do seu gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperar e modificar a cadeia de ligação

Depois de criar uma identidade do dispositivo IoT no portal, pode recuperar as suas chaves primárias ou secundárias. Uma destas teclas precisa de ser incluída na cadeia de ligação que as aplicações usam para comunicar com o IoT Hub. Para a autenticação simétrica da chave, o IoT Hub fornece a cadeia de ligação totalmente formada nos detalhes do dispositivo para sua conveniência. Tem de adicionar informações adicionais sobre o dispositivo gateway à cadeia de ligação.

As cadeias de ligação para dispositivos a jusante necessitam dos seguintes componentes:

* O hub IoT a que o dispositivo se conecta: `Hostname={iothub name}.azure-devices.net`
* O ID do dispositivo registado no centro: `DeviceID={device ID}`
* O método de autenticação, seja a chave simétrica ou os certificados X.509
  * Se utilizar a autenticação simétrica da chave, forneça a chave primária ou secundária: `SharedAccessKey={key}`
  * Se utilizar a autenticação do certificado X.509, forneça uma bandeira: `x509=true`
* O dispositivo de gateway através do dispositivo. Fornecer o valor do **nome do anfitrião** a partir do ficheiro config.yaml do dispositivo de gateway IoT Edge: `GatewayHostName={gateway hostname}`

Tudo junto, parece:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Ou:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Graças à relação pai/filho, pode simplificar a cadeia de ligação chamando o gateway diretamente como anfitrião de ligação. Por exemplo:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Utilizará esta cadeia de ligação modificada no próximo artigo da série transparente gateway.

## <a name="next-steps"></a>Passos seguintes

Neste momento, tem um dispositivo IoT Edge registado com o seu hub IoT e configurado como um gateway transparente. Também tem um dispositivo a jusante registado no seu hub IoT e aponta para o seu dispositivo gateway.

Em seguida, é necessário configurar o seu dispositivo a jusante para confiar no dispositivo gateway e conectá-lo de forma segura. Continue para o próximo artigo na série de gateway transparente, [Ligue um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).