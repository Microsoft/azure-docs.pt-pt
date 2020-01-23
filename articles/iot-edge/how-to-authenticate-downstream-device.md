---
title: Autenticar dispositivos downstream-Azure IoT Edge | Microsoft Docs
description: Como autenticar dispositivos downstream ou dispositivos de folha no Hub IoT e rotear sua conexão por meio de dispositivos Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548769"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um dispositivo a jusante no Hub IoT do Azure

Em um cenário de gateway transparente, dispositivos downstream (às vezes chamados de dispositivos folha ou dispositivos filho) precisam de identidades no Hub IoT como qualquer outro dispositivo. Este artigo percorre as opções de autenticação de um dispositivo downstream no Hub IoT e demonstra como declarar a conexão de gateway.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a segunda etapa:

1. O dispositivo de gateway precisa ser capaz de se conectar com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino adequado. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. **O dispositivo downstream precisa ter uma identidade de dispositivo para ser capaz de autenticar com o Hub IoT e saber se comunicar por meio de seu dispositivo de gateway.**
3. O dispositivo downstream precisa se conectar ao seu dispositivo de gateway com segurança. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Os dispositivos downstream podem se autenticar no Hub IoT usando um dos três métodos: chaves simétricas (às vezes chamadas de chaves de acesso compartilhado), X. 509 certificados autoassinados ou certificados assinados por AC (autoridade de certificação) X. 509. As etapas de autenticação são semelhantes às etapas usadas para configurar qualquer dispositivo não IoT Edge com o Hub IoT, com pequenas diferenças para declarar a relação de gateway.

As etapas neste artigo mostram o provisionamento manual de dispositivos, não o provisionamento automático com o DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure. Não há suporte para o provisionamento de dispositivos downstream com o DPS.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). Se você estiver usando a autenticação X. 509 para seu dispositivo downstream, você precisará usar o mesmo script que gera o certificado que você configurou no artigo gateway transparente.

Este artigo refere-se ao *nome de host do gateway* em vários pontos. O nome do host do gateway é declarado no parâmetro **hostname** do arquivo config. YAML no dispositivo de gateway de IOT Edge. Ele é mencionado na cadeia de conexão do dispositivo downstream. O nome do host do gateway precisa ser resolvido para um endereço IP, usando o DNS ou uma entrada de arquivo de host.

## <a name="register-device-symmetric-key"></a>Registrar dispositivo (chave simétrica)

A autenticação de chave simétrica ou a autenticação de chave de acesso compartilhado é a maneira mais simples de autenticar com o Hub IoT. Com a autenticação de chave simétrica, uma chave Base64 é associada à ID do dispositivo IoT no Hub IoT. Você inclui essa chave em seus aplicativos de IoT para que seu dispositivo possa apresentá-lo quando se conectar ao Hub IoT.

### <a name="create-the-device-identity"></a>Criar a identidade do dispositivo

Adicione um novo dispositivo IoT em seu hub IoT, usando o portal do Azure, CLI do Azure ou a extensão de IoT para Visual Studio Code. Lembre-se de que os dispositivos downstream precisam ser identificados no Hub IoT como dispositivos IoT regulares, e não IoT Edge dispositivos.

Ao criar a nova identidade do dispositivo, forneça as seguintes informações:

* Crie uma ID para seu dispositivo.

* Selecione **chave simétrica** como o tipo de autenticação.

* Opcionalmente, escolha **definir um dispositivo pai** e selecione o IOT Edge dispositivo de gateway ao qual esse dispositivo downstream se conectará. Essa etapa é opcional para a autenticação de chave simétrica, mas é recomendável porque a configuração de um dispositivo pai habilita [recursos offline](offline-capabilities.md) para seu dispositivo downstream. Você sempre pode atualizar os detalhes do dispositivo para adicionar ou alterar o pai mais tarde.

   ![Criar ID do dispositivo com autenticação de chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação. O exemplo a seguir cria um novo dispositivo IoT com autenticação de chave simétrica e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Para obter mais informações sobre os comandos de CLI do Azure para a criação de dispositivos e o gerenciamento de pai/filho, consulte o conteúdo de referência para os comandos de [identidade de dispositivo do Hub IOT para AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .


Em seguida, [recupere e modifique a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

## <a name="register-device-x509-self-signed"></a>Registrar dispositivo (X. 509 autoassinado)

Para a autenticação autoassinada X. 509, às vezes conhecida como autenticação de impressão digital, você precisa criar novos certificados para serem colocados em seu dispositivo IoT. Esses certificados têm uma impressão digital neles que você compartilha com o Hub IoT para autenticação.

Se você não tiver uma autoridade de certificação para criar certificados X. 509, poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md). Ao gerar certificados de teste para seu dispositivo downstream, use o mesmo certificado de autoridade de certificação raiz que gerou os certificados para o dispositivo de gateway.

1. Usando seu certificado de autoridade de certificação, crie dois certificados de dispositivo (primário e secundário) para o dispositivo downstream.

   O certificado do dispositivo deve ter o nome da entidade definido como a ID do dispositivo que será usada ao registrar o dispositivo IoT no Hub IoT do Azure. Essa configuração é necessária para autenticação.

2. Recupere a impressão digital SHA1 (chamada de impressão digital na interface do Hub IoT) de cada certificado, que é uma cadeia de caracteres hexadecimal 40. Use o comando openssl a seguir para exibir o certificado e encontrar a impressão digital:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Execute este comando duas vezes, uma vez para o certificado primário e uma vez para o certificado secundário. Você fornece impressões digitais para ambos os certificados ao registrar um novo dispositivo IoT usando certificados X. 509 autoassinados.

3. Navegue até o Hub IoT no portal do Azure e crie uma nova identidade de dispositivo IoT com os seguintes valores:

   * Forneça a **ID do dispositivo** que corresponde ao nome da entidade de seus certificados de dispositivo.
   * Selecione **X. 509 autoassinado** como o tipo de autenticação.
   * Cole as cadeias de caracteres hexadecimais que você copiou dos certificados primários e secundários de seu dispositivo.
   * Selecione **definir um dispositivo pai** e escolha o IOT Edge dispositivo de gateway ao qual este dispositivo downstream se conectará. Um dispositivo pai é necessário para a autenticação X. 509 de um dispositivo downstream.

   ![Criar ID do dispositivo com a autenticação autoassinada X. 509 no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copie o certificado e as chaves do dispositivo para qualquer local no dispositivo downstream. Também mova uma cópia do certificado de autoridade de certificação raiz compartilhada que gerou o certificado do dispositivo de gateway e os certificados de dispositivo downstream.

   Você fará referência a esses arquivos nos aplicativos de dispositivo folha que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

5. Dependendo da linguagem preferida, examine exemplos de como os certificados X. 509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configurar a segurança X. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir cria um novo dispositivo IoT com a autenticação autoassinada X. 509 e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obter mais informações sobre os comandos de CLI do Azure para a criação de dispositivos, a geração de certificados e o gerenciamento pai e filho, consulte o conteúdo de referência para comandos de [identidade de dispositivo do Hub IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

Em seguida, [recupere e modifique a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

## <a name="register-device-x509-ca-signed"></a>Registrar dispositivo (AC X. 509 assinada)

Para autenticação assinada da AC (autoridade de certificação) X. 509, você precisa de um certificado de autoridade de certificação raiz registrado no Hub IoT que você usa para assinar certificados para seu dispositivo IoT. Qualquer dispositivo que use um certificado que foi emitido pelo certificado de autoridade de certificação raiz ou qualquer um de seus certificados intermediários terá permissão para autenticar.

Esta seção se baseia nas instruções detalhadas no artigo do Hub IoT [Configurar a segurança X. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md). Siga as etapas nesta seção para saber quais valores usar para configurar um dispositivo downstream que se conecta por meio de um gateway.

Se você não tiver uma autoridade de certificação para criar certificados X. 509, poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md). Ao gerar certificados de teste para seu dispositivo downstream, use o mesmo certificado de autoridade de certificação raiz que gerou os certificados para o dispositivo de gateway.

1. Siga as instruções na seção [registrar certificados de autoridade de certificação x. 509 em seu hub IOT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) de *Configurar a segurança x. 509 em seu hub IOT do Azure*. Nessa seção, você executa as seguintes etapas:

   1. Carregue um certificado de autoridade de certificação raiz. Se você estiver usando os certificados de demonstração, a autoridade de certificação raiz será **\<caminho >/certs/Azure-IOT-Test-only.root.ca.cert.pem**.

   2. Verifique se você possui esse certificado de autoridade de certificação raiz.

2. Siga as instruções na seção [criar um dispositivo X. 509 para o Hub IOT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) de *Configurar a segurança x. 509 em seu hub IOT do Azure*. Nessa seção, você executa as seguintes etapas:

   1. Adicione um novo dispositivo. Forneça um nome em minúsculas para a **ID do dispositivo**e escolha o tipo de autenticação **X. 509 AC assinado**.
   2. Definir um dispositivo pai. Para dispositivos downstream, selecione **definir um dispositivo pai** e escolha o IOT Edge dispositivo de gateway que fornecerá a conexão ao Hub IOT.

3. Crie uma cadeia de certificados para seu dispositivo downstream. Use o mesmo certificado de autoridade de certificação raiz que você carregou no Hub IoT para fazer essa cadeia. Use a mesma ID de dispositivo em minúsculas que você atribuiu à identidade do dispositivo no Portal.

4. Copie o certificado e as chaves do dispositivo para qualquer local no dispositivo downstream. Também mova uma cópia do certificado de autoridade de certificação raiz compartilhada que gerou o certificado do dispositivo de gateway e os certificados de dispositivo downstream.

   Você fará referência a esses arquivos nos aplicativos de dispositivo folha que se conectam ao Hub IoT. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.

5. Dependendo da linguagem preferida, examine exemplos de como os certificados X. 509 podem ser referenciados em aplicativos de IoT:

   * C#: [Configurar a segurança X. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Você pode usar a [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação de criação de dispositivo. O exemplo a seguir cria um novo dispositivo IoT com autenticação assinada da AC X. 509 e atribui um dispositivo pai:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para obter mais informações, consulte o CLI do Azure conteúdo de referência para comandos de [identidade de dispositivo do Hub IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

Em seguida, [recupere e modifique a cadeia de conexão](#retrieve-and-modify-connection-string) para que o dispositivo saiba se conectar por meio de seu gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperar e modificar a cadeia de conexão

Depois de criar uma identidade de dispositivo IoT no portal, você pode recuperar suas chaves primárias ou secundárias. Uma dessas chaves precisa ser incluída na cadeia de conexão que os aplicativos usam para se comunicar com o Hub IoT. Para a autenticação de chave simétrica, o Hub IoT fornece a cadeia de conexão totalmente formada nos detalhes do dispositivo para sua conveniência. Você precisa adicionar informações extras sobre o dispositivo de gateway à cadeia de conexão.

As cadeias de conexão para dispositivos downstream precisam dos seguintes componentes:

* O Hub IoT ao qual o dispositivo se conecta: `Hostname={iothub name}.azure-devices.net`
* A ID do dispositivo registrada com o Hub: `DeviceID={device ID}`
* A chave primária ou secundária: `SharedAccessKey={key}`
* O dispositivo de gateway ao qual o dispositivo se conecta. Forneça o valor do **nome do host** do arquivo config. YAML do dispositivo de gateway IoT Edge: `GatewayHostName={gateway hostname}`

Juntos, uma cadeia de conexão completa é semelhante a:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se você estabeleceu uma relação pai/filho para esse dispositivo downstream, você pode simplificar a cadeia de conexão chamando o gateway diretamente como o host de conexão. As relações pai/filho são necessárias para a autenticação X. 509, mas opcional para a autenticação de chave simétrica. Por exemplo:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Neste ponto, você deve ter um dispositivo IoT Edge registrado e configurado como um gateway. Você também tem um dispositivo IoT downstream registrado e apontando para seu dispositivo de gateway. A etapa final é inserir certificados em seu dispositivo downstream para que ele possa se conectar com segurança ao gateway.

Continue no próximo artigo da série de gateways, [Conecte um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Passos seguintes

Ao concluir este artigo, você deve ter um dispositivo IoT Edge funcionando como um gateway transparente e um dispositivo downstream registrado com um hub IoT. Em seguida, você precisa configurar seus dispositivos downstream para confiar no dispositivo de gateway e conectar-se a ele com segurança. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).
