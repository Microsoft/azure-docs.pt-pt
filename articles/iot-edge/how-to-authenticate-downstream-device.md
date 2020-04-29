---
title: Autenticar dispositivos a jusante - Azure IoT Edge [ Borda De Autenticação ] Microsoft Docs
description: Como autenticar dispositivos a jusante ou dispositivos de folhas para o IoT Hub e encaminhar a sua ligação através de dispositivos de gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411499"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo a jusante no Hub IoT do Azure

Num cenário transparente de gateway, dispositivos a jusante (por vezes chamados de dispositivos de folha ou dispositivos infantis) precisam de identidades no IoT Hub como qualquer outro dispositivo. Este artigo percorre as opções para autenticar um dispositivo a jusante ao IoT Hub e, em seguida, demonstra como declarar a ligação gateway.

Existem três passos gerais para estabelecer uma ligação transparente e bem sucedida. Este artigo cobre o segundo passo:

1. O dispositivo gateway precisa de ser capaz de se ligar de forma segura a dispositivos a jusante, receber comunicações de dispositivos a jusante e direcionar mensagens para o destino adequado. Para mais informações, consulte [Configure um dispositivo IoT Edge para funcionar como uma porta de entrada transparente](how-to-create-transparent-gateway.md).
2. **O dispositivo a jusante precisa de ter uma identidade de dispositivo para poder autenticar com o IoT Hub, e saber comunicar através do seu dispositivo gateway.**
3. O dispositivo a jusante tem de se ligar ao seu dispositivo de gateway de forma segura. Para mais informações, consulte [Ligue um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Os dispositivos a jusante podem autenticar com o Hub IoT utilizando um dos três métodos: chaves simétricas (por vezes referidas como chaves de acesso partilhadas), certificados x.509 auto-assinados ou certificados assinados pela X.509 (CA). Os passos de autenticação são semelhantes aos passos utilizados para configurar qualquer dispositivo não IoT-Edge com IoT Hub, com pequenas diferenças para declarar a relação gateway.

As etapas deste artigo mostram o fornecimento manual de dispositivos, não o fornecimento automático com o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS). O fornecimento de dispositivos a jusante com DPS não é suportado.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Configurar um dispositivo IoT Edge para funcionar como um portal transparente](how-to-create-transparent-gateway.md). Se estiver a utilizar a autenticação X.509 para o seu dispositivo a jusante, tem de utilizar o mesmo script gerador de certificado que configura no artigo de gateway transparente.

Este artigo refere-se ao nome de *anfitrião* do portal em vários pontos. O nome de gateway é declarado no parâmetro de nome de **anfitrião** do ficheiro config.yaml no dispositivo ioT Edge gateway. É referido na cadeia de ligação do dispositivo a jusante. O nome de anfitrião do gateway tem de ser resolúvel para um Endereço IP, utilizando DNS ou uma entrada de ficheiro de anfitrião.

## <a name="register-device-symmetric-key"></a>Dispositivo de registo (chave simétrica)

A autenticação simétrica da chave, ou autenticação chave de acesso partilhado, é a forma mais simples de autenticar com o IoT Hub. Com a autenticação simétrica da chave, uma chave base64 está associada com o seu ID do dispositivo IoT no IoT Hub. Inclui essa chave nas suas aplicações IoT para que o seu dispositivo possa apresentá-la quando se conecta ao IoT Hub.

### <a name="create-the-device-identity"></a>Criar a identidade do dispositivo

Adicione um novo dispositivo IoT no seu hub IoT, utilizando o portal Azure, o Azure CLI ou a extensão IoT para Visual Studio Code. Lembre-se que os dispositivos a jusante precisam de ser identificados no IoT Hub como dispositivos IoT regulares, e não dispositivos IoT Edge.

Quando criar a identidade do novo dispositivo, forneça as seguintes informações:

* Crie uma identificação para o seu dispositivo.

* Selecione **a tecla Simétrica** como o tipo de autenticação.

* Opcionalmente, escolha **definir um dispositivo principal** e selecione o dispositivo de gateway IoT Edge que este dispositivo a jusante irá ligar. Este passo é opcional para a autenticação simétrica da chave, mas é recomendado porque a definição de um dispositivo-mãe permite [capacidades offline](offline-capabilities.md) para o seu dispositivo a jusante. Pode sempre atualizar os detalhes do dispositivo para adicionar ou alterar o progenitor mais tarde.

   ![Criar id do dispositivo com auth chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para completar a mesma operação. O exemplo seguinte cria um novo dispositivo IoT com autenticação simétrica da chave e atribui um dispositivo-mãe:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Para obter mais informações sobre os comandos Azure CLI para a criação de dispositivos e gestão de pais/filhos, consulte o conteúdo de referência para comandos de [identidade de dispositivo sot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Em seguida, [recupere e modifique a cadeia](#retrieve-and-modify-connection-string) de ligação de modo a que o seu dispositivo saiba ligar-se através do seu portal.

## <a name="register-device-x509-self-signed"></a>Dispositivo de registo (X.509 auto-assinado)

Para a autenticação auto-assinada X.509, por vezes referida como autenticação de impressão digital, é necessário criar novos certificados para colocar no seu dispositivo IoT. Estes certificados têm uma impressão digital que partilha com o IoT Hub para autenticação.

Se não tiver autoridade para criar certificados X.509, pode criar certificados de [demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md). Ao gerar certificados de teste para o seu dispositivo a jusante, utilize o mesmo certificado CA raiz que gerou os certificados para o seu dispositivo gateway.

1. Utilizando o seu certificado CA, crie dois certificados de dispositivo (primário e secundário) para o dispositivo a jusante.

   O certificado do dispositivo deve ter o nome de assunto definido no ID do dispositivo que utilizará ao registar o dispositivo IoT no Hub Azure IoT. Esta definição é necessária para autenticação.

2. Recupere a impressão digital SHA1 (chamada de impressão digital na interface IoT Hub) de cada certificado, que é uma cadeia de caracteres hexadecimal de 40. Utilize o seguinte comando aberto para visualizar o certificado e encontrar a impressão digital:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Execute este comando duas vezes, uma para o certificado primário e outra para o certificado secundário. Fornece impressões digitais para ambos os certificados quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

3. Navegue para o seu hub IoT no portal Azure e crie uma nova identidade de dispositivo IoT com os seguintes valores:

   * Forneça o ID do **Dispositivo** que corresponda ao nome do assunto dos certificados do seu dispositivo.
   * Selecione **X.509 Auto-Assinado** como o tipo de autenticação.
   * Colhe as cordas hexadecimais que copiou dos certificados primários e secundários do seu dispositivo.
   * Selecione **Definir um dispositivo principal** e escolha o dispositivo de gateway IoT Edge que este dispositivo a jusante irá ligar. É necessário um dispositivo-mãe para a autenticação X.509 de um dispositivo a jusante.

   ![Crie id de dispositivo com x.509 auto-assinado auth no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie o certificado do dispositivo e as chaves de qualquer local no dispositivo a jusante. Mova também uma cópia do certificado CA de raiz partilhada que gerou tanto o certificado do dispositivo gateway como os certificados do dispositivo a jusante.

   Irá referenciar estes ficheiros nas aplicações do dispositivo de folha que se ligam ao IoT Hub. Pode utilizar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo de cópia [Segura](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

5. Dependendo da sua língua preferida, reveja as amostras de como os certificados X.509 podem ser referenciados em aplicações IoT:

   * C#: [Instale a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para completar o mesmo funcionamento de criação do dispositivo. O exemplo seguinte cria um novo dispositivo IoT com autenticação auto-assinada X.509 e atribui um dispositivo-mãe:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obter mais informações sobre os comandos Azure CLI para criação de dispositivos, geração de certificados e gestão de pais e filhos, consulte o conteúdo de referência para comandos [de identidade de dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) sitia hub.

Em seguida, [recupere e modifique a cadeia](#retrieve-and-modify-connection-string) de ligação de modo a que o seu dispositivo saiba ligar-se através do seu portal.

## <a name="register-device-x509-ca-signed"></a>Dispositivo de registo (X.509 CA assinado)

Para a autenticação assinada pela X.509 (CA), é necessário um certificado CA raiz registado no IoT Hub que utiliza para assinar certificados para o seu dispositivo IoT. Qualquer dispositivo que utilize um certificado que tenha sido emitido pelo certificado de raiz ca ou qualquer um dos seus certificados intermédios será autorizado a autenticar.

Esta secção baseia-se nas instruções detalhadas no artigo IoT Hub [Configurar a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md). Siga os passos nesta secção para saber quais os valores a utilizar para configurar um dispositivo a jusante que se liga através de um portal.

Se não tiver autoridade para criar certificados X.509, pode criar certificados de [demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md). Ao gerar certificados de teste para o seu dispositivo a jusante, utilize o mesmo certificado CA raiz que gerou os certificados para o seu dispositivo gateway.

1. Siga as instruções nos certificados De SEm Registo [X.509 na sua](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) secção de hub IoT de *configurar a segurança X.509 no seu hub Azure IoT*. Nessa secção, executa os seguintes passos:

   1. Faça upload de um certificado ca raiz. Se estiver a utilizar os certificados de demonstração, a raiz CA é ** \<caminho>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verifique se é dono do certificado de raiz da AC.

2. Siga as instruções no [dispositivo Create a X.509 para](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) a sua secção de hub IoT de configurar a segurança *X.509 no seu hub Azure IoT*. Nessa secção, executa os seguintes passos:

   1. Adicione um novo dispositivo. Forneça um nome minúsculo para id do **dispositivo,** e escolha o tipo de autenticação **X.509 CA Assinado**.
   2. Detete um dispositivo de pais. Para dispositivos a jusante, selecione **Definir um dispositivo-mãe** e escolha o dispositivo de gateway IoT Edge que fornecerá a ligação ao IoT Hub.

3. Crie uma cadeia de certificados para o seu dispositivo a jusante. Use o mesmo certificado de CA raiz que carregou para o IoT Hub para fazer esta cadeia. Utilize o mesmo ID do dispositivo minúsculo que deu à identidade do seu dispositivo no portal.

4. Copie o certificado do dispositivo e as chaves de qualquer local no dispositivo a jusante. Mova também uma cópia do certificado CA de raiz partilhada que gerou tanto o certificado do dispositivo gateway como os certificados do dispositivo a jusante.

   Irá referenciar estes ficheiros nas aplicações do dispositivo de folha que se ligam ao IoT Hub. Pode utilizar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo de cópia [Segura](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

5. Dependendo da sua língua preferida, reveja as amostras de como os certificados X.509 podem ser referenciados em aplicações IoT:

   * C#: [Instale a segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Pode utilizar a [extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para completar o mesmo funcionamento de criação do dispositivo. O exemplo seguinte cria um novo dispositivo IoT com autenticação assinada por X.509 CA e atribui um dispositivo-mãe:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para mais informações, consulte o conteúdo de referência Azure CLI para comandos [de identidade de dispositivo sot hub.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Em seguida, [recupere e modifique a cadeia](#retrieve-and-modify-connection-string) de ligação de modo a que o seu dispositivo saiba ligar-se através do seu portal.

## <a name="retrieve-and-modify-connection-string"></a>Recuperar e modificar a cadeia de ligação

Depois de criar uma identidade de dispositivo IoT no portal, pode recuperar as suas chaves primárias ou secundárias. Uma destas teclas precisa de ser incluída na cadeia de ligação que as aplicações usam para comunicar com o IoT Hub. Para autenticação simétrica da chave, o IoT Hub fornece a cadeia de ligação totalmente formada nos detalhes do dispositivo para sua conveniência. É necessário adicionar informações adicionais sobre o dispositivo de gateway à cadeia de ligação.

As cordas de ligação para dispositivos a jusante necessitam dos seguintes componentes:

* O hub IoT a que o dispositivo se liga:`Hostname={iothub name}.azure-devices.net`
* O ID do dispositivo registado no centro:`DeviceID={device ID}`
* Ou a chave primária ou secundária:`SharedAccessKey={key}`
* O dispositivo de gateway que o dispositivo liga. Forneça o valor do nome de **anfitrião** a partir do ficheiro config.yaml do dispositivo ioT Edge:`GatewayHostName={gateway hostname}`

Tudo junto, uma cadeia de ligação completa parece:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se estabelecer uma relação pai/filho para este dispositivo a jusante, então pode simplificar a cadeia de ligação chamando a porta de entrada diretamente como hospedeiro de ligação. As relações pai/filho são necessárias para a autenticação X.509, mas opcionais para a autenticação simétrica da chave. Por exemplo:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Neste ponto, deve ter um dispositivo IoT Edge registado e configurado como um portal. Também tem um dispositivo IoT a jusante registado e apontado para o seu dispositivo gateway. O passo final é colocar certificados no seu dispositivo a jusante para que possa ligar-se de forma segura ao portal.

Continue para o próximo artigo da série gateway, [Ligue um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Passos seguintes

Ao completar este artigo, deve ter um dispositivo IoT Edge a funcionar como uma porta de entrada transparente e um dispositivo a jusante registado com um hub IoT. Em seguida, é necessário configurar os seus dispositivos a jusante para confiar no dispositivo gateway e ligar-se a ele de forma segura. Para mais informações, consulte [Ligue um dispositivo a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-device.md).
