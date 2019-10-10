---
title: Conectar dispositivos downstream-Azure IoT Edge | Microsoft Docs
description: Como configurar dispositivos downstream ou de folha para se conectar a dispositivos Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c37c3ed2031746d7c476850749bb3dc613252654
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176795"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Conectar um dispositivo downstream a um gateway de Azure IoT Edge

Este artigo fornece instruções para estabelecer uma conexão confiável entre dispositivos downstream e IoT Edge gateways transparentes. Em um cenário de gateway transparente, um ou mais dispositivos podem passar suas mensagens por meio de um único dispositivo de gateway que mantém a conexão com o Hub IoT. Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) . Em muitos casos, esses aplicativos usam o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. 

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a terceira etapa:

1. O dispositivo de gateway precisa conectar-se com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino apropriado. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. O dispositivo downstream precisa de uma identidade de dispositivo para ser capaz de se autenticar no Hub IoT e conhecer a comunicação por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. **O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway.**

Este artigo identifica problemas comuns com conexões de dispositivo downstream e orienta você na configuração de seus dispositivos downstream: 

* Explicando o protocolo TLS e os conceitos básicos do certificado. 
* Explicar como as bibliotecas de TLS funcionam em diferentes sistemas operacionais e como cada sistema operacional lida com certificados.
* Percorrendo exemplos de IoT do Azure em várias linguagens para ajudá-lo a começar. 

Neste artigo, os termos *Gateway* e *IOT Edge gateway* referem-se a um dispositivo IOT Edge configurado como um gateway transparente. 

## <a name="prerequisites"></a>Pré-requisitos 

Tenha o arquivo de certificado **Azure-IOT-Test-only. root. ca. cert. pem** que foi gerado em [configurar um dispositivo IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md) disponível em seu dispositivo downstream. O dispositivo downstream usa esse certificado para validar a identidade do dispositivo de gateway. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo downstream

Um dispositivo downstream pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) . Em muitos casos, esses aplicativos usam o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, outro dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge. 

>[!NOTE]
>Os dispositivos IoT que têm identidades registradas no Hub IoT podem usar o [módulo gêmeos](../iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções em um único dispositivo. IoT Edge gateways dão suporte a conexões de módulo downstream usando a autenticação de chave simétrica, mas não a autenticação de certificado X. 509. 

Para conectar um dispositivo downstream a um gateway de IoT Edge, você precisa de duas coisas:

* Um dispositivo ou aplicativo configurado com uma cadeia de conexão de dispositivo do Hub IoT anexado com informações para conectá-lo ao gateway. 

    Esta etapa é explicada em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicativo precisa confiar no certificado de **autoridade de certificação raiz** do gateway para validar as conexões TLS com o dispositivo de gateway. 

    Esta etapa é explicada em detalhes no restante deste artigo. Essa etapa pode ser executada de duas maneiras: Instalando o certificado de autoridade de certificação no repositório de certificados do sistema operacional ou (para determinados idiomas) referenciando o certificado em aplicativos usando os SDKs do IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Conceitos básicos de TLS e certificado

O desafio de conectar dispositivos downstream com segurança a IoT Edge é assim como qualquer outra comunicação de cliente/servidor segura que ocorra pela Internet. Um cliente e um servidor se comunicam com segurança pela Internet usando o protocolo [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é criado usando construções [de PKI (infraestrutura de chave pública)](https://en.wikipedia.org/wiki/Public_key_infrastructure) padrão chamados certificados. O TLS é uma especificação bastante envolvida e aborda uma ampla variedade de tópicos relacionados à proteção de dois pontos de extremidade. Esta seção resume os conceitos relevantes para você conectar dispositivos com segurança a um IoT Edge gateway.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada de *cadeia de certificados do servidor*. Uma cadeia de certificados normalmente inclui um certificado de AC (autoridade de certificação) raiz, um ou mais certificados de autoridade de certificação intermediários e, por fim, o próprio certificado do servidor. Um cliente estabelece uma relação de confiança com um servidor, verificando criptograficamente toda a cadeia de certificados do servidor. Essa validação de cliente da cadeia de certificados de servidor é chamada *validação de cadeia de servidores*. O cliente desafia criptograficamente o serviço a provar a posse da chave privada associada ao certificado do servidor em um processo chamado *prova de posse*. A combinação de validação de cadeia de servidores e prova de posse é chamada de *autenticação de servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de autoridade de certificação raiz que foi usado para criar (ou emitir) o certificado do servidor. Normalmente, ao se conectar a sites, um navegador vem pré-configurado com certificados de AC comumente usados para que o cliente tenha um processo contínuo. 

Quando um dispositivo se conecta ao Hub IoT do Azure, o dispositivo é o cliente e o serviço de nuvem do Hub IoT é o servidor. O serviço de nuvem do Hub IoT é apoiado por um certificado de autoridade de certificação raiz chamado **Baltimore Cybertrust Root**, que é publicamente disponível e amplamente usado. Como o certificado de autoridade de certificação do Hub IoT já está instalado na maioria dos dispositivos, muitas implementações de TLS (OpenSSL, Schannel, LibreSSL) o usam automaticamente durante a validação do certificado do servidor. Um dispositivo que pode se conectar com êxito ao Hub IoT pode ter problemas ao tentar se conectar a um gateway de IoT Edge.

Quando um dispositivo se conecta a um gateway de IoT Edge, o dispositivo downstream é o cliente e o dispositivo de gateway é o servidor. Azure IoT Edge permite que operadores (ou usuários) criem cadeias de certificados de gateway, no entanto, eles veem o ajuste. O operador pode optar por usar um certificado de autoridade de certificação pública, como Baltimore, ou usar um certificado de AC raiz autoassinado (ou interno). Os certificados de autoridade de certificação pública geralmente têm um custo associado a eles, portanto, normalmente são usados em cenários de produção. Os certificados de autoridade de certificação autoassinados são preferidos para desenvolvimento e teste. Os artigos de configuração do gateway transparente listados na introdução usam certificados de AC raiz autoassinados. 

Quando você usa um certificado de AC raiz autoassinado para um gateway de IoT Edge, ele precisa ser instalado ou fornecido para todos os dispositivos downstream que tentam se conectar ao gateway. 

![Configuração do certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre IoT Edge certificados e algumas implicações de produção, consulte [IOT Edge detalhes de uso do certificado](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornecer o certificado de autoridade de certificação raiz

Para verificar os certificados do dispositivo de gateway, o dispositivo downstream precisa de sua própria cópia do certificado de autoridade de certificação raiz. Se você usou os scripts fornecidos no repositório git IoT Edge para criar certificados de teste, o certificado de autoridade de certificação raiz será chamado **Azure-IOT-Test-only. root. ca. cert. pem**. Se você ainda não fez parte das outras etapas de preparação do dispositivo downstream, mova esse arquivo de certificado para qualquer diretório em seu dispositivo downstream. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover o arquivo de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no sistema operacional

A instalação do certificado de autoridade de certificação raiz no repositório de certificados do sistema operacional geralmente permite que a maioria dos aplicativos use o certificado de autoridade de certificação raiz. Há algumas exceções, como aplicativos NodeJS que não usam o repositório de certificados do sistema operacional, mas usam o repositório de certificados interno do tempo de execução do nó. Se você não puder instalar o certificado no nível do sistema operacional, pule para [usar certificados com SDKs de IOT do Azure](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Os comandos a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host Ubuntu. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Você deverá ver uma mensagem que diz "Atualizando certificados no/etc/SSL/Certs... 1 adicionado, 0 removido; concluído ".

### <a name="windows"></a>Windows

As etapas a seguir são um exemplo de como instalar um certificado de autoridade de certificação em um host do Windows. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

Você pode instalar certificados usando o [importar certificado](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) do PowerShell como administrador:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Você também pode instalar certificados usando o utilitário **certlm** : 

1. No menu Iniciar, procure e selecione **gerenciar certificados de computador**. Um utilitário chamado **certlm** é aberto.
2. Navegue até **certificados-computador Local** > **autoridades de certificação raiz confiáveis**.
3. Clique com o botão direito do mouse em **certificados** e selecione **todas as tarefas** > **importar**. O assistente para importação de certificados deve ser iniciado. 
4. Siga as etapas conforme indicado e importe o arquivo de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluído, você deverá ver uma mensagem "importada com êxito". 

Você também pode instalar certificados programaticamente usando APIs .NET, conforme mostrado no exemplo do .NET mais adiante neste artigo. 

Normalmente, os aplicativos usam a pilha TLS fornecida pelo Windows chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectar com segurança via TLS. O Schannel *requer* que todos os certificados sejam instalados no repositório de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usar certificados com SDKs do Azure IoT

Esta seção descreve como os SDKs do IoT do Azure se conectam a um dispositivo IoT Edge usando aplicativos de exemplo simples. O objetivo de todos os exemplos é conectar o cliente do dispositivo e enviar mensagens de telemetria para o gateway e, em seguida, fechar a conexão e sair. 

Tenha duas coisas prontas antes de usar os exemplos de nível de aplicativo:

* A cadeia de conexão do Hub IoT do dispositivo downstream foi modificada para apontar para o dispositivo de gateway e todos os certificados necessários para autenticar seu dispositivo downstream no Hub IoT. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de autoridade de certificação raiz que você copiou e salvou em algum lugar em seu dispositivo downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de exemplo para conectar um cliente do dispositivo NodeJS do Azure IoT a um gateway IoT Edge. Para aplicativos NodeJS, você deve instalar o certificado de autoridade de certificação raiz no nível do aplicativo, conforme mostrado aqui. Os aplicativos NodeJS não usam o repositório de certificados do sistema. 

1. Obtenha o exemplo de **edge_downstream_device. js** do [SDK do dispositivo IOT do Azure para o repositório de exemplos do node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Verifique se você tem todos os pré-requisitos para executar o exemplo examinando o arquivo **README.MD** . 
3. No arquivo edge_downstream_device. js, atualize as variáveis **ConnectionString** e **edge_ca_cert_path** . 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo em seu dispositivo. 

Para entender o exemplo que você está executando, o seguinte trecho de código é como o SDK do cliente lê o arquivo de certificado e o utiliza para estabelecer uma conexão TLS segura: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta seção apresenta um aplicativo de exemplo para conectar um cliente do dispositivo .NET IoT do Azure a um gateway IoT Edge. No entanto, os aplicativos .NET são capazes de usar automaticamente os certificados instalados no repositório de certificados do sistema em hosts Linux e Windows.

1. Obtenha o exemplo de **EdgeDownstreamDevice** da [pasta IOT Edge .net Samples](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Verifique se você tem todos os pré-requisitos para executar o exemplo examinando o arquivo **README.MD** . 
3. No arquivo **Properties/launchSettings. JSON** , atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH** . Se você quiser usar o certificado instalado no repositório de certificados confiáveis no sistema host, deixe essa variável em branco. 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo em seu dispositivo. 

Para instalar programaticamente um certificado confiável no repositório de certificados por meio de um aplicativo .NET, consulte a função **InstallCACert ()** no arquivo **EdgeDownstreamDevice/Program.cs** . Essa operação é idempotente, portanto, pode ser executada várias vezes com os mesmos valores sem nenhum efeito adicional. 

### <a name="c"></a>C

Esta seção apresenta um aplicativo de exemplo para conectar um cliente de dispositivo do Azure IoT C a um IoT Edge gateway. O SDK do C pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para obter mais informações, consulte o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c). 

1. Obtenha o aplicativo **iotedge_downstream_device_sample** do [SDK do dispositivo IOT do Azure para C Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Verifique se você tem todos os pré-requisitos para executar o exemplo examinando o arquivo **README.MD** . 
3. No arquivo iotedge_downstream_device_sample. c, atualize as variáveis **ConnectionString** e **edge_ca_cert_path** . 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo em seu dispositivo. 

O SDK do dispositivo IoT do Azure para C fornece uma opção para registrar um certificado de autoridade de certificação ao configurar o cliente. Essa operação não instala o certificado em qualquer lugar, mas usa um formato de cadeia de caracteres do certificado na memória. O certificado salvo é fornecido para a pilha TLS subjacente ao estabelecer uma conexão. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Em hosts do Windows, se você não estiver usando o OpenSSL ou outra biblioteca TLS, o SDK usará o Schannel como padrão. Para que o Schannel funcione, o certificado de autoridade de certificação raiz IoT Edge deve ser instalado no repositório de certificados do Windows, não definido usando a operação `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Esta seção apresenta um aplicativo de exemplo para conectar um cliente de dispositivo Java IoT do Azure a um gateway IoT Edge. 

1. Obtenha o exemplo para **enviar eventos** do SDK do [dispositivo IOT do Azure para exemplos de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Verifique se você tem todos os pré-requisitos para executar o exemplo examinando o arquivo **README.MD** . 
3. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo em seu dispositivo.

### <a name="python"></a>Python

Esta seção apresenta um aplicativo de exemplo para conectar um cliente do dispositivo Python do Azure IoT a um gateway IoT Edge. 

1. Obtenha o exemplo de **send_message** do [SDK do dispositivo IOT do Azure para exemplos do Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Verifique se você está executando em um contêiner de IoT Edge ou em um cenário de depuração, tenha as variáveis de ambiente `EdgeHubConnectionString` e `EdgeModuleCACertificateFile` definidas.
3. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo em seu dispositivo. 


## <a name="test-the-gateway-connection"></a>Testar a conexão do gateway

Use este comando de exemplo para testar se o dispositivo downstream pode se conectar ao dispositivo de gateway: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Esse comando testa conexões em MQTTS (porta 8883). Se você estiver usando um protocolo diferente, ajuste o comando conforme necessário para AMQPS (5671) ou HTTPS (433).

A saída desse comando pode ser longa, incluindo informações sobre todos os certificados na cadeia. Se a conexão for bem-sucedida, você verá uma linha como `Verification: OK` ou `Verify return code: 0 (ok)`.

![Verificar conexão de gateway](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Solucionar problemas de conexão de gateway

Se o dispositivo de folha tiver uma conexão intermitente com seu dispositivo de gateway, tente as etapas a seguir para resolução. 

1. O nome do host do gateway na cadeia de conexão é igual ao valor do nome do host no arquivo IoT Edge config. YAML no dispositivo de gateway?
2. O nome do host do gateway é resolvido para um endereço IP? Você pode resolver conexões intermitentes usando DNS ou adicionando uma entrada de arquivo de host no dispositivo de folha.
3. As portas de comunicação são abertas em seu firewall? A comunicação baseada no protocolo usado (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) deve ser possível entre o dispositivo downstream e o IoT Edge transparente.

## <a name="next-steps"></a>Passos seguintes

Saiba como IoT Edge pode estender [recursos offline](offline-capabilities.md) para dispositivos downstream. 
