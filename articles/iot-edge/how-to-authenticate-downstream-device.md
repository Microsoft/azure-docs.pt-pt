---
title: Autenticar dispositivos downstream - Azure IoT Edge | Documentos da Microsoft
description: Como autenticar dispositivos downstream ou dispositivos de folha para o IoT Hub e encaminhar a sua ligação de rede através de dispositivos de gateway do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082394"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticar um downstream dispositivo hub IoT do Azure

Num cenário de gateway transparente, dispositivos downstream (às vezes chamados dispositivos de folha ou dispositivos de subordinado) tem de identidades do IoT Hub, como qualquer outro dispositivo. Este artigo explica as opções para a autenticação de um dispositivo de downstream no IoT Hub e, em seguida, demonstra como declarar a ligação de gateway.

Existem três passos gerais para configurar uma ligação de gateway transparente com êxito. Este artigo abrange a segunda etapa:

1. O dispositivo de gateway tem de ser capaz de ligar a dispositivos downstream, receber comunicações a partir de dispositivos de downstream, e encaminhar mensagens para o destino correto. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md).
2. **O dispositivo de downstream tem de ter uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub e saber para se comunicar por meio do seu dispositivo de gateway.**
3. O dispositivo de downstream tem de conseguir ligar de forma segura para o seu dispositivo de gateway. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Dispositivos Downstream podem autenticar com o IoT Hub com um dos três métodos: as chaves simétricas (por vezes referidas como chaves de acesso partilhado), X.509 os certificados autoassinados ou certificados X.509 assinado de autoridade de certificados. Os passos de autenticação são semelhantes aos passos utilizados para configurar a qualquer dispositivo de não-IoT-Edge com o IoT Hub, com pequenas diferenças para declarar a relação de gateway.

Os passos neste artigo mostram aprovisionamento de dispositivos manual, não com o serviço de aprovisionamento de dispositivos do Azure IoT Hub o aprovisionamento automático. 

## <a name="symmetric-key-authentication"></a>Autenticação de chave simétrica

Autenticação de chave simétrica, ou autenticação de chave de acesso partilhado, é a forma mais simples para autenticar com o IoT Hub. Com a autenticação de chave simétrica, uma chave de base64 é associada ao seu ID de dispositivo do IoT no IoT Hub. Incluir essa chave nas suas aplicações de IoT para que o seu dispositivo pode apresentá-los quando se liga ao IoT Hub. 

### <a name="create-the-device-identity"></a>Criar a identidade de dispositivo 

Adicione um novo dispositivo de IoT no seu hub IoT, usando o portal do Azure, CLI do Azure ou a extensão de IoT para o Visual Studio Code. Lembre-se de que os dispositivos jusante precisam ser identificadas no IoT Hub como dispositivo de IoT regular, não os dispositivos do IoT Edge. 

Ao criar a nova identidade de dispositivo, forneça as seguintes informações: 

* Crie um ID para o seu dispositivo.

* Selecione **chave simétrica** como o tipo de autenticação. 

* Opcionalmente, optar por **definir um dispositivo principal** e selecione o dispositivo de gateway do IoT Edge deste dispositivo downstream estabelecerão ligação através de. Este passo é opcional para autenticação de chave simétrica, mas é recomendado porque a definição de um dispositivo principal permite [recursos offline](offline-capabilities.md) para o seu dispositivo de downstream. Pode atualizar os detalhes do dispositivo para adicionar ou alterar o elemento principal mais tarde. 

   ![Criar ID do dispositivo com autenticação de chave simétrica no portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Pode utilizar o [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a mesma operação. O exemplo seguinte cria um novo dispositivo de IoT com autenticação de chave simétrica e atribui um dispositivo principal: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Para obter mais informações sobre os comandos da CLI do Azure para criação de dispositivo e a gestão de pai/filho, consulte o conteúdo de referência para [az iot hub-identidade de dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandos.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Ligar ao IoT Hub através de um gateway

O mesmo processo é utilizado para autenticar regular IoT dispositivos ao IoT Hub com chaves simétricas também se aplica a dispositivos de downstream. A única diferença é que precisa adicionar um ponteiro para o dispositivo de gateway para encaminhar a ligação ou, em cenários offline, para lidar com a autenticação em nome do IoT Hub. 

Para autenticação de chave simétrica, não existe nenhum passos adicionais que necessitar de colocar no seu dispositivo para o mesmo autenticar com o IoT Hub. Ainda precisa os certificados no local para que o seu dispositivo downstream pode ligar ao seu dispositivo de gateway, conforme descrito em [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Depois de criar uma identidade de dispositivo do IoT no portal, pode obter as chaves primárias ou secundárias. Um destas chaves precisa ser incluído na cadeia de ligação que devem ser incluídos em qualquer aplicativo que se comunica com o IoT Hub. Para autenticação de chave simétrica, o IoT Hub fornece a cadeia de ligação totalmente formada nos detalhes do dispositivo para sua comodidade. Tem de adicionar informações adicionais sobre o dispositivo de gateway para a cadeia de ligação. 

Cadeias de ligação da chave simétrica para dispositivos downstream tem os seguintes componentes: 

* O IoT hub que o dispositivo estabelece ligação: `Hostname={iothub name}.azure-devices.net`
* O ID de dispositivo registado com o hub: `DeviceID={device ID}`
* Qualquer um da chave primária ou secundária: `SharedAccessKey={key}`
* O dispositivo de gateway que o dispositivo estabelece ligação através de. Forneça o **hostname** valor a partir do ficheiro de config.yaml o dispositivo de gateway de IoT Edge: `GatewayHostName={gateway hostname}`

Tudo isso, uma cadeia de ligação completa é semelhante a:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se estabelecer uma relação de pai/filho para este dispositivo downstream, pode simplificar a cadeia de ligação ao chamar o gateway diretamente como o anfitrião de ligação. Por exemplo: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Autenticação de X.509 

Existem duas formas de autenticar um dispositivo de IoT utilizando certificados X.509. Qualquer forma, optar por autenticar, os passos para ligar o seu dispositivo ao IoT Hub são os mesmos. Escolha os certificados autoassinados ou assinado pela AC para autenticação, em seguida, continue a aprender a ligar ao IoT Hub. 

Para obter mais informações sobre como o IoT Hub utiliza autenticação X.509, consulte os artigos seguintes: 
* [Autenticação do dispositivo usando certificados X.509 de AC](../iot-hub/iot-hub-x509ca-overview.md)
* [Compreensão conceptual de certificados X.509 de AC no setor de IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Criar a identidade de dispositivo com certificados autoassinados X.509

Para a autenticação autoassinada X.509, às vezes chamada de autenticação de thumbprint, terá de criar novos certificados para colocar no seu dispositivo IoT. Estes certificados têm um thumbprint neles que partilha com o IoT Hub para a autenticação. 

A maneira mais fácil para testar este cenário é usar o mesmo computador que utilizou para criar certificados no [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md). Essa máquina deve já ser integrada com a ferramenta correta, o certificado de AC de raiz e o certificado de AC intermediária para criar os certificados de dispositivo do IoT. Pode copiar os certificados finais e as respetivas chaves privadas através de para o seu dispositivo downstream posteriormente. Seguindo estes passos no artigo de gateway, configurar openssl no seu computador, em seguida, clonar o repositório do IoT Edge para scripts de criação do certificado de acesso. Em seguida, o que fez um diretório de trabalho que chamamos  **\<WRKDIR >** para armazenar os certificados. Os certificados de predefinição destinam-se para o desenvolvimento e teste, por isso, apenas os últimos 30 dias. Deverá ter criado um certificado de AC de raiz e um certificado intermédio. 

1. Navegue para o seu diretório de trabalho num bash ou janela do PowerShell. 

2. Crie dois certificados (primários e secundários) para o dispositivo de downstream. Forneça o nome do seu dispositivo e, em seguida, a etiqueta principal ou secundária. Estas informações são utilizadas para nomear os arquivos para que pode manter o controle de certificados para vários dispositivos. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Obter a identificação digital de SHA1 (chamada de um thumbprint na interface do IoT Hub) de cada certificado, o que é uma cadeia de 40 carateres hexadecimais. Utilize o seguinte comando de openssl para ver o certificado e descobrir a identificação digital:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Navegue até ao seu hub IoT no portal do Azure e criar uma nova identidade de dispositivo de IoT com os seguintes valores: 

   * Selecione **X.509 Autoassinado** como o tipo de autenticação.
   * Cole as cadeias de caracteres hexadecimal que copiou do certificados primário e secundário do seu dispositivo.
   * Selecione **definir um dispositivo principal** e escolha um dispositivo de gateway que este dispositivo downstream estabelecerão ligação através do IoT Edge. Um dispositivo principal é necessário para a autenticação de X.509 de um dispositivo de downstream. 

   ![Criar ID do dispositivo com autenticação de autoassinado X.509 no portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Copie os seguintes ficheiros para qualquer diretório no seu dispositivo downstream:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Vai fazer referência a esses arquivos nos aplicativos de dispositivos de folha que ligar ao IoT Hub. Pode utilizar um serviço como [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [cópia seguro protoco](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

Pode utilizar o [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a operação de criação do mesmo dispositivo. O exemplo seguinte cria um novo dispositivo de IoT com a autenticação de X.509 autoassinada e atribui um dispositivo principal: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Para obter mais informações sobre os comandos da CLI do Azure para a criação do dispositivo, a geração do certificado e a gestão de principais e subordinados, consulte o conteúdo de referência para [az iot hub-identidade de dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandos.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Criar o dispositivo identidade com a AC X.509 assinado certificados

Para X.509 autoridade (CA) assinada autenticação de certificados, é necessário um certificado de AC de raiz registado no IoT Hub que utiliza para assinar certificados para o seu dispositivo IoT. Qualquer dispositivo com um certificado que foi problemas, o certificado de AC de raiz ou qualquer um dos seus certificados intermédios será permitido para autenticar. 

Esta secção baseia-se as instruções detalhadas no artigo do IoT Hub [configurar a segurança X.509 no seu hub IoT do Azure](../iot-hub/iot-hub-security-x509-get-started.md). Siga os passos nesta secção para saber quais valores a utilizar para configurar um dispositivo de downstream que se liga através de um gateway. 

A maneira mais fácil para testar este cenário é usar o mesmo computador que utilizou para criar certificados no [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md). Essa máquina deve já ser integrada com a ferramenta correta, o certificado de AC de raiz e o certificado de AC intermediária para criar os certificados de dispositivo do IoT. Pode copiar os certificados finais e as respetivas chaves privadas através de para o seu dispositivo downstream posteriormente. Seguindo estes passos no artigo de gateway, configurar openssl no seu computador, em seguida, clonar o repositório do IoT Edge para scripts de criação do certificado de acesso. Em seguida, o que fez um diretório de trabalho que chamamos  **\<WRKDIR >** para armazenar os certificados. Os certificados de predefinição destinam-se para o desenvolvimento e teste, por isso, apenas os últimos 30 dias. Deverá ter criado um certificado de AC de raiz e um certificado intermédio. 

1. Siga as instruções no [certificados de AC de X.509 registe-se ao seu hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) secção *configurar a segurança X.509 no seu hub IoT do Azure*. Nesta secção, execute os seguintes passos: 

   1. Carregar um certificado de AC de raiz. Se estiver a utilizar os certificados que criou neste artigo gateway transparente, carregue  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** como o ficheiro de certificado de raiz. 
   2. Certifique-se de que seu esse certificado de AC de raiz. Pode verificar posse com as ferramentas de certificado no \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Siga as instruções no [criar um dispositivo X.509 para o seu hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) secção *configurar a segurança X.509 no seu hub IoT do Azure*. Nesta secção, execute os seguintes passos: 

   1. Adicione um novo dispositivo. Forneça um nome em minúsculas para **ID do dispositivo**e escolha o tipo de autenticação **assinado de AC X.509**. 
   2. Defina um dispositivo principal. Para dispositivos downstream, selecione **definir um dispositivo principal** e escolha o IoT Edge dispositivo de gateway que irá fornecer a ligação ao IoT Hub. 

3. Crie uma cadeia de certificados para o seu dispositivo de downstream. Utilize o mesmo certificado de AC de raiz que carregou para o IoT Hub para fazer esta cadeia. Utilize o mesmo ID de dispositivo em minúsculas, que deu para sua identidade de dispositivo no portal.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Copie os seguintes ficheiros para qualquer diretório no seu dispositivo downstream: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Vai fazer referência a esses arquivos nos aplicativos de dispositivos de folha que ligar ao IoT Hub. Pode utilizar um serviço como [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [cópia seguro protoco](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.

Pode utilizar o [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para concluir a operação de criação do mesmo dispositivo. O exemplo seguinte cria um novo dispositivo de IoT com a autenticação de AC X.509 assinada e atribui um dispositivo principal: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Para obter mais informações sobre os comandos da CLI do Azure para criação de dispositivo e a gestão de pai/filho, consulte o conteúdo de referência para [az iot hub-identidade de dispositivo](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandos.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Ligar ao IoT Hub através de um gateway

Cada SDK do IoT do Azure processa a autenticação de X.509 de maneira um pouco diferente. No entanto, o mesmo processo serve para autenticar regular IoT dispositivos ao IoT Hub com certificados X.509 também se aplica a dispositivos de downstream. A única diferença é que precisa adicionar um ponteiro para o dispositivo de gateway para encaminhar a ligação ou, em cenários offline, para lidar com a autenticação em nome do IoT Hub. Em geral, pode seguir os mesmos passos de autenticação X.509 para todos os dispositivos do IoT Hub, em seguida, simplesmente substitua o valor de **Hostname** na cadeia de ligação para ser o nome de anfitrião do seu dispositivo de gateway. 

As secções seguintes mostram alguns exemplos para idiomas diferentes do SDK. 

>[!IMPORTANT]
>Os exemplos seguintes demonstram como os SDKs do IoT Hub utilizar certificados para autenticar os dispositivos. Numa implementação de produção, deve armazenar todos os segredos, como privada ou chaves SAS num módulo de segurança de hardware (HSM). 

#### <a name="net"></a>.NET

Para obter um exemplo de um C# programa de autenticação para o IoT Hub com certificados X.509, consulte [configurar a segurança X.509 no seu hub IoT do Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Algumas das principais linhas desse exemplo são incluídas aqui para demonstrar o processo de autenticação.

Ao declarar o nome de anfitrião para a sua instância de DeviceClient, utilize o nome de anfitrião do dispositivo de gateway do IoT Edge. O nome de anfitrião pode ser encontrada no arquivo de config.yaml do dispositivo de gateway. 

Se estiver a utilizar os certificados de teste fornecidos no repositório de git do IoT Edge, a chave para os certificados é **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Para obter um exemplo de um programa em C, a autenticação no IoT Hub com certificados X.509, consulte o SDK de IoT de C [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) exemplo. Algumas das principais linhas desse exemplo são incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de ligação para o seu dispositivo downstream, utilize o nome de anfitrião do dispositivo de gateway do IoT Edge para o **HostName** parâmetro. O nome de anfitrião pode ser encontrada no arquivo de config.yaml do dispositivo de gateway. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Para obter um exemplo de um programa de node. js, a autenticação no IoT Hub com certificados X.509, consulte o SDK de IoT de node. js [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) exemplo. Algumas das principais linhas desse exemplo são incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de ligação para o seu dispositivo downstream, utilize o nome de anfitrião do dispositivo de gateway do IoT Edge para o **HostName** parâmetro. O nome de anfitrião pode ser encontrada no arquivo de config.yaml do dispositivo de gateway. 

Se estiver a utilizar os certificados de teste fornecidos no repositório de git do IoT Edge, a chave para os certificados é **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Para obter um exemplo de um programa de Python a autenticação no IoT Hub com certificados X.509, consulte o SDK de IoT de Java [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) exemplo. Algumas das principais linhas desse exemplo são incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de ligação para o seu dispositivo downstream, utilize o nome de anfitrião do dispositivo de gateway do IoT Edge para o **HostName** parâmetro. O nome de anfitrião pode ser encontrada no arquivo de config.yaml do dispositivo de gateway. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Para obter um exemplo de um programa de Java a autenticação no IoT Hub com certificados X.509, consulte o SDK de IoT de Java [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) exemplo. Algumas das principais linhas desse exemplo são incluídas aqui para demonstrar o processo de autenticação.

Ao definir a cadeia de ligação para o seu dispositivo downstream, utilize o nome de anfitrião do dispositivo de gateway do IoT Edge para o **HostName** parâmetro. O nome de anfitrião pode ser encontrada no arquivo de config.yaml do dispositivo de gateway. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Passos Seguintes

Ao concluir neste artigo, deve ter um dispositivo IoT Edge como gateway transparente a funcionar e um dispositivo de downstream registado com um hub IoT. Em seguida, terá de configurar os seus dispositivos jusante para o dispositivo de gateway de confiança e enviar mensagens ao mesmo. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).
