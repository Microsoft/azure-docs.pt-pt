---
title: Ligar dispositivos downstream - Azure IoT Edge | Documentos da Microsoft
description: Como configurar dispositivos downstream ou de folha para se conectar a dispositivos Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f739bdbd295662006a964f890147ad67c373d7b5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698618"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ligar um dispositivo jusante a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma conexão confiável entre dispositivos downstream e IoT Edge gateways transparentes. Em um cenário de gateway transparente, um ou mais dispositivos podem passar suas mensagens por meio de um único dispositivo de gateway que mantém a conexão com o Hub IoT. Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. 

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a terceira etapa:

1. O dispositivo de gateway precisa conectar-se com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino apropriado. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).
2. O dispositivo downstream precisa de uma identidade de dispositivo para ser capaz de se autenticar no Hub IoT e conhecer a comunicação por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. **O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway.**

Este artigo identifica os problemas comuns com ligações de dispositivos de downstream e orienta-o na configuração de seus dispositivos downstream por: 

* Explicando transport layer security (TLS) e conceitos básicos de certificados. 
* Explicar como bibliotecas TLS funcionam em sistemas operativos diferentes e a forma como cada sistema operacional lida com certificados.
* Percorrendo o IoT do Azure a utilizar o exemplos em vários idiomas para ajudá-lo. 

Neste artigo, os termos *gateway* e *gateway do IoT Edge* consulte um dispositivo IoT Edge configurado como um gateway transparente. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo de downstream

Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, outro dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge. 

>[!NOTE]
>Os dispositivos IoT que têm identidades registradas no Hub IoT podem usar o [módulo gêmeos](../iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções em um único dispositivo. IoT Edge gateways dão suporte a conexões de módulo downstream usando a autenticação de chave simétrica, mas não a autenticação de certificado X. 509. 

Para ligar um dispositivo jusante a um gateway do IoT Edge, precisa de duas coisas:

* Um dispositivo ou aplicação que está configurada com uma cadeia de ligação de dispositivos do IoT Hub anexada com informações para ligá-lo para o gateway. 

    Esta etapa é explicada em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicativo precisa confiar no certificado de **autoridade de certificação raiz** do gateway para validar as conexões TLS com o dispositivo de gateway. 

    Esta etapa é explicada em detalhes no restante deste artigo. Essa etapa pode ser executada de duas maneiras: Instalando o certificado de autoridade de certificação no repositório de certificados do sistema operacional ou (para determinados idiomas) referenciando o certificado em aplicativos usando os SDKs do IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Noções básicas do TLS e o certificado

O desafio de ligar com segurança a jusante dispositivos IoT Edge é como qualquer comunicação de cliente/servidor seguro que ocorre através da internet. Um cliente e um servidor comunicam de forma segura com o uso de internet [security (TLS) da camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS baseia-se com a norma [infraestrutura de chaves públicas (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) construções chamado de certificados. O TLS é uma especificação bastante envolvida e aborda uma ampla variedade de tópicos relacionados à proteção de dois pontos de extremidade. Esta seção resume os conceitos relevantes para você conectar dispositivos com segurança a um IoT Edge gateway.

Quando um cliente se liga a um servidor, o servidor apresenta uma cadeia de certificados, chamado de *cadeia de certificados de servidor*. Uma cadeia de certificados normalmente é composto por um certificado de autoridade (CA) do certificado de raiz, um ou mais certificados de AC intermediários e, finalmente, o certificado do servidor em si. Um cliente estabelece a fidedignidade com um servidor criptograficamente verificando a cadeia de certificados de servidor completo. Essa validação de cliente da cadeia de certificados de servidor é chamada *validação de cadeia de servidores*. O cliente desafia criptograficamente o serviço a provar a posse da chave privada associada ao certificado do servidor em um processo chamado *prova de posse*. A combinação de validação de cadeia de servidores e prova de posse é chamada de *autenticação de servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de AC de raiz que foi utilizado para criar (ou emitir) o certificado do servidor. Normalmente ao ligar ao Web sites, um navegador está pré-configurada com certificados de AC usados para que o cliente tenha um processo uniforme. 

Quando um dispositivo se liga ao IoT Hub do Azure, o dispositivo é o cliente e o serviço em nuvem do IoT Hub é o servidor. O serviço em nuvem do IoT Hub é apoiado por um certificado de AC de raiz chamado **Baltimore CyberTrust Root**, que é amplamente utilizada e disponível ao público. Uma vez que o certificado de AC do IoT Hub já está instalado na maioria dos dispositivos, muitas implementações do TLS (OpenSSL, Schannel, LibreSSL) utilizá-lo durante a validação de certificados de servidor. Um dispositivo que pode ligar com êxito para o IoT Hub pode ter problemas, tentando se conectar a um gateway do IoT Edge.

Quando um dispositivo se liga a um gateway do IoT Edge, o dispositivo de downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criar cadeias de certificados de gateway, no entanto, sua preferência. O operador pode optar por utilizar um certificado de AC público, como o Baltimore, ou utilize uma raiz autoassinada (ou interna) certificado da AC. Certificados de AC públicos, muitas vezes, têm um custo associado a eles, portanto, são normalmente utilizadas em cenários de produção. Certificados de AC autoassinados são preferenciais para desenvolvimento e teste. Os artigos de configuração do gateway transparente listados na introdução usam certificados de AC raiz autoassinados. 

Quando utiliza um certificado de AC de raiz autoassinado para um gateway do IoT Edge, precisará ser instalado ou fornecido para todos os dispositivos de downstream tentando se conectar ao gateway. 

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre certificados do IoT Edge e algumas implicações de produção, veja [detalhes de utilização de certificados do IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornecer o certificado de autoridade de certificação raiz

Para verificar os certificados do dispositivo de gateway, o dispositivo downstream precisa de sua própria cópia do certificado de autoridade de certificação raiz. Se você usou os scripts fornecidos no repositório git IoT Edge para criar certificados de teste, o certificado de autoridade de certificação raiz será chamado **Azure-IOT-Test-only. root. ca. cert. pem**. Se você ainda não fez parte das outras etapas de preparação do dispositivo downstream, mova esse arquivo de certificado para qualquer diretório em seu dispositivo downstream. Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover o arquivo de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no sistema operacional

A instalação do certificado de autoridade de certificação raiz no repositório de certificados do sistema operacional geralmente permite que a maioria dos aplicativos use o certificado de autoridade de certificação raiz. Há algumas exceções, como aplicativos NodeJS que não usam o repositório de certificados do sistema operacional, mas usam o repositório de certificados interno do tempo de execução do nó. Se você não puder instalar o certificado no nível do sistema operacional, pule para [usar certificados com SDKs de IOT do Azure](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Os comandos seguintes são um exemplo de como instalar um certificado de AC num host de Ubuntu. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Deverá ver uma mensagem que diz, "certificados de atualização no /etc/ssl/certs... 1 adicionou, removeu a 0; concluído."

### <a name="windows"></a>Windows

Os seguintes passos são um exemplo de como instalar um certificado de AC num host do Windows. Este exemplo pressupõe que você esteja usando o certificado **Azure-IOT-Test-only. root. ca. cert. pem** dos artigos de pré-requisitos e que você copiou o certificado em um local no dispositivo downstream.

1. No menu Iniciar, procure e selecione **gerir certificados de computador**. Um utilitário chamado **certlm** abre.
2. Navegue para **certificados - Computador Local** > **autoridades de certificação de raiz fidedigna**.
3. Com o botão direito **certificados** e selecione **todas as tarefas** > **importação**. Deve iniciar o Assistente para importar certificados. 
4. Siga os passos conforme indicado e importar o ficheiro de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluída, deverá ver uma mensagem "Importada com êxito". 

Também pode instalar certificados através de programação através de APIs do .NET, conforme mostrado no exemplo de .NET neste artigo. 

Normalmente, aplicativos usam o Windows fornecido chamado de pilha TLS [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para ligar em segurança por TLS. Schannel *requer* que todos os certificados ser instalado no arquivo de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Utilizar certificados com o Azure IoT SDKs

Esta secção descreve como o Azure IoT SDKs ligar a um dispositivo IoT Edge com aplicativos de exemplo simples. O objetivo de todos os exemplos é ligar o cliente do dispositivo e enviar mensagens de telemetria para o gateway, em seguida, feche a ligação e sair. 

Ter prontas antes de utilizar os exemplos de nível de aplicativo de duas coisas:

* A cadeia de conexão do Hub IoT do dispositivo downstream foi modificada para apontar para o dispositivo de gateway e todos os certificados necessários para autenticar seu dispositivo downstream no Hub IoT. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de AC de raiz que copiados e guardados em algum lugar no seu dispositivo de downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de exemplo para ligar um cliente de dispositivo do Azure IoT NodeJS para um gateway do IoT Edge. Para aplicativos NodeJS, você deve instalar o certificado de autoridade de certificação raiz no nível do aplicativo, conforme mostrado aqui. Os aplicativos NodeJS não usam o repositório de certificados do sistema. 

1. Obter o de exemplo **edge_downstream_device.js** partir a [repositório de exemplos do Azure IoT device SDK para node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Certifique-se de que tem todos os pré-requisitos para executar o exemplo ao rever o **readme.md** ficheiro. 
3. No ficheiro edge_downstream_device.js, atualize o **connectionString** e **edge_ca_cert_path** variáveis. 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo no seu dispositivo. 

Para compreender o que estiver a executar o exemplo, o seguinte trecho de código é como o cliente SDK lê o ficheiro de certificado e a utiliza para estabelecer uma conexão TLS segura: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta secção apresenta um aplicativo de exemplo para ligar um cliente de dispositivo do IoT do Azure .NET para um gateway do IoT Edge. No entanto, os aplicativos .NET são podem utilizar quaisquer certificados instalados no arquivo de certificados do sistema em anfitriões de Linux e Windows automaticamente.

1. Obter o de exemplo **EdgeDownstreamDevice** da [pasta de exemplos do IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Certifique-se de que tem todos os pré-requisitos para executar o exemplo ao rever o **readme.md** ficheiro. 
3. Na **propriedades / launchSettings.json** de ficheiros, atualize o **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH** variáveis. Se pretender utilizar o certificado instalado no arquivo de certificados fidedignos no sistema host, deixe esta variável em branco. 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo no seu dispositivo. 

Por meio de programação instalar um certificado fidedigno no arquivo de certificados através de um aplicativo .NET, consulte a **InstallCACert()** funcionar a **EdgeDownstreamDevice / Program.cs** ficheiro. Esta operação é idempotente, pelo que pode ser executado várias vezes com os mesmos valores com nenhum efeito adicional. 

### <a name="c"></a>C

Esta secção apresenta um aplicativo de exemplo para ligar um cliente de dispositivo C de IoT do Azure para um gateway do IoT Edge. O SDK de C pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para obter mais informações, consulte a [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

1. Obter o **iotedge_downstream_device_sample** aplicação a partir do [Azure IoT device SDK para C exemplos](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Certifique-se de que tem todos os pré-requisitos para executar o exemplo ao rever o **readme.md** ficheiro. 
3. No ficheiro iotedge_downstream_device_sample.c, atualize o **connectionString** e **edge_ca_cert_path** variáveis. 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo no seu dispositivo. 

Azure IoT device SDK para C fornece uma opção para registar um certificado de AC quando configurar o cliente. Esta operação não instala o certificado em qualquer lugar, mas em vez disso, usa um formato de cadeia de caracteres do certificado na memória. O certificado guardado é fornecido para a pilha TLS subjacente ao estabelecer uma ligação. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Em anfitriões do Windows, se não estiver a utilizar OpenSSL ou outra biblioteca TLS, o SDK padrão usando o Schannel. Para o Schannel funcionar, o certificado de AC de raiz do IoT Edge deve ser instalado no arquivo de certificados do Windows, não definido usando o `IoTHubDeviceClient_SetOption` operação. 

### <a name="java"></a>Java

Esta secção apresenta um aplicativo de exemplo para ligar um cliente de dispositivo do Azure IoT Java para um gateway do IoT Edge. 

1. Obter o de exemplo **evento de envio** da [Azure IoT device SDK para exemplos de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Certifique-se de que tem todos os pré-requisitos para executar o exemplo ao rever o **readme.md** ficheiro. 
3. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo no seu dispositivo.

### <a name="python"></a>Python

Esta secção apresenta um aplicativo de exemplo para ligar um cliente de dispositivo do Azure IoT Python para um gateway do IoT Edge. 

1. Obter o de exemplo **edge_downstream_client** partir a [Azure IoT device SDK para amostras de Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Certifique-se de que tem todos os pré-requisitos para executar o exemplo ao rever o **readme.md** ficheiro. 
3. No ficheiro edge_downstream_client.py, atualize o **CONNECTION_STRING** e **TRUSTED_ROOT_CA_CERTIFICATE_PATH** variáveis. 
4. Consulte a documentação do SDK para obter instruções sobre como executar o exemplo no seu dispositivo. 


## <a name="test-the-gateway-connection"></a>Testar a ligação de gateway

Este é um comando de exemplo que testa se tudo foi configurado corretamente. Deverá ver uma mensagem a indicar "verificado OK".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Solucionar problemas de conexão de gateway

Se o dispositivo de folha tiver uma conexão intermitente com seu dispositivo de gateway, tente as etapas a seguir para resolução. 

1. O nome do host do gateway na cadeia de conexão é igual ao valor do nome do host no arquivo IoT Edge config. YAML no dispositivo de gateway?
2. O nome do host do gateway é resolvido para um endereço IP? Você pode resolver conexões intermitentes usando DNS ou adicionando uma entrada de arquivo de host no dispositivo de folha.
3. As portas de comunicação são abertas em seu firewall? A comunicação baseada no protocolo usado (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) deve ser possível entre o dispositivo downstream e o IoT Edge transparente.

## <a name="next-steps"></a>Passos Seguintes

Saiba como pode estender o IoT Edge [recursos offline](offline-capabilities.md) a jusante dispositivos. 
