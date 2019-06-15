---
title: Ligar dispositivos downstream - Azure IoT Edge | Documentos da Microsoft
description: Como configurar downstream ou dispositivos de folha para ligar a dispositivos de gateway do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058508"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ligar um dispositivo jusante a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma conexão confiável entre os dispositivos de downstream e gateways transparentes do IoT Edge. Num cenário de gateway transparente, um ou mais dispositivos podem transmitir suas mensagens através de um dispositivo de gateway único que mantém a ligação ao IoT Hub. Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. 

Existem três passos gerais para configurar uma ligação de gateway transparente com êxito. Este artigo abrange o terceiro passo:

1. O dispositivo de gateway tem de ligar a dispositivos downstream, receber comunicações a partir de dispositivos de downstream, e encaminhar mensagens para o destino correto. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md).
2. O dispositivo de downstream precisa de uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub e saber para se comunicar por meio do seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).
3. **O dispositivo de downstream tem de conseguir ligar de forma segura para o seu dispositivo de gateway.**

Este artigo identifica os problemas comuns com ligações de dispositivos de downstream e orienta-o na configuração de seus dispositivos downstream por: 

* Explicando transport layer security (TLS) e conceitos básicos de certificados. 
* Explicar como bibliotecas TLS funcionam em sistemas operativos diferentes e a forma como cada sistema operacional lida com certificados.
* Percorrendo o IoT do Azure a utilizar o exemplos em vários idiomas para ajudá-lo. 

Neste artigo, os termos *gateway* e *gateway do IoT Edge* consulte um dispositivo IoT Edge configurado como um gateway transparente. 

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo de downstream

Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. 

Para ligar um dispositivo jusante a um gateway do IoT Edge, precisa de duas coisas:

* Um dispositivo ou aplicação que está configurada com uma cadeia de ligação de dispositivos do IoT Hub anexada com informações para ligá-lo para o gateway. 

    Este passo é explicado na [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicação tem de confiar do gateway **AC de raiz** certificado para validar as ligações de TLS para o dispositivo de gateway. 

    Este passo é explicado detalhadamente no resto deste artigo. Este passo pode ser efetuada de duas formas: ao instalar o certificado da AC no arquivo de certificados do sistema operacional, ou (para alguns idiomas) ao referenciar o certificado em aplicativos com os SDKs IoT do Azure.

## <a name="tls-and-certificate-fundamentals"></a>Noções básicas do TLS e o certificado

O desafio de ligar com segurança a jusante dispositivos IoT Edge é como qualquer comunicação de cliente/servidor seguro que ocorre através da internet. Um cliente e um servidor comunicam de forma segura com o uso de internet [security (TLS) da camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS baseia-se com a norma [infraestrutura de chaves públicas (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) construções chamado de certificados. TLS é uma especificação bastante envolvida e aborda uma grande variedade de tópicos relacionados com a proteção de dois pontos de extremidade. Esta secção resume os conceitos relevantes para si ligar em segurança dispositivos para um gateway do IoT Edge.

Quando um cliente se liga a um servidor, o servidor apresenta uma cadeia de certificados, chamado de *cadeia de certificados de servidor*. Uma cadeia de certificados normalmente é composto por um certificado de autoridade (CA) do certificado de raiz, um ou mais certificados de AC intermediários e, finalmente, o certificado do servidor em si. Um cliente estabelece a fidedignidade com um servidor criptograficamente verificando a cadeia de certificados de servidor completo. Esta validação de cliente de cadeia de certificados de servidor é chamada *validação da cadeia de servidor*. O cliente criptograficamente desafia o serviço para provar a posse da chave privada associada com o certificado de servidor num processo chamado *prova de posse*. A combinação de validação da cadeia de servidor e uma prova de posse são chamados *autenticação de servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de AC de raiz que foi utilizado para criar (ou emitir) o certificado do servidor. Normalmente ao ligar ao Web sites, um navegador está pré-configurada com certificados de AC usados para que o cliente tenha um processo uniforme. 

Quando um dispositivo se liga ao IoT Hub do Azure, o dispositivo é o cliente e o serviço em nuvem do IoT Hub é o servidor. O serviço em nuvem do IoT Hub é apoiado por um certificado de AC de raiz chamado **Baltimore CyberTrust Root**, que é amplamente utilizada e disponível ao público. Uma vez que o certificado de AC do IoT Hub já está instalado na maioria dos dispositivos, muitas implementações do TLS (OpenSSL, Schannel, LibreSSL) utilizá-lo durante a validação de certificados de servidor. Um dispositivo que pode ligar com êxito para o IoT Hub pode ter problemas, tentando se conectar a um gateway do IoT Edge.

Quando um dispositivo se liga a um gateway do IoT Edge, o dispositivo de downstream é o cliente e o dispositivo de gateway é o servidor. O Azure IoT Edge permite que os operadores (ou usuários) criar cadeias de certificados de gateway, no entanto, sua preferência. O operador pode optar por utilizar um certificado de AC público, como o Baltimore, ou utilize uma raiz autoassinada (ou interna) certificado da AC. Certificados de AC públicos, muitas vezes, têm um custo associado a eles, portanto, são normalmente utilizadas em cenários de produção. Certificados de AC autoassinados são preferenciais para desenvolvimento e teste. Os artigos de configuração do gateway transparente listados na introdução utilizam certificados de AC de raiz autoassinado. 

Quando utiliza um certificado de AC de raiz autoassinado para um gateway do IoT Edge, precisará ser instalado ou fornecido para todos os dispositivos de downstream tentando se conectar ao gateway. 

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre certificados do IoT Edge e algumas implicações de produção, veja [detalhes de utilização de certificados do IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Forneça o certificado de AC de raiz

Para verificar os certificados do dispositivo de gateway, o dispositivo de downstream tem sua própria cópia do certificado da AC de raiz. Se utilizou os scripts fornecidos no repositório de git do IoT Edge para criar certificados de teste, em seguida, o certificado de AC de raiz é chamado **azure-iot-teste-only.root.ca.cert.pem**. Se ainda não o fez como parte dos outros passos de preparação do dispositivo downstream, mova este ficheiro de certificado para qualquer diretório no seu dispositivo de downstream. Pode utilizar um serviço como [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [cópia seguro protocolo](https://www.ssh.com/ssh/scp/) para mover o ficheiro de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no SO

Instalar o certificado de AC de raiz no arquivo de certificados do sistema operacional em geral, permite que a maioria dos aplicativos utilizar o certificado de AC de raiz. Existem algumas exceções, como o NodeJS aplicativos que não usam o certificado de SO armazenarem, mas em vez disso, usar o arquivo de certificados internos do nó do tempo de execução. Se não conseguir instalar o certificado ao nível do sistema operativo, avançar diretamente para [utilizar certificados com o Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Os comandos seguintes são um exemplo de como instalar um certificado de AC num host de Ubuntu. Este exemplo assume que está a utilizar o **azure-iot-teste-only.root.ca.cert.pem** certificado dos artigos de pré-requisitos, e que copiou o certificado para uma localização no dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Deverá ver uma mensagem que diz, "certificados de atualização no /etc/ssl/certs... 1 adicionou, removeu a 0; concluído."

### <a name="windows"></a>Windows

Os seguintes passos são um exemplo de como instalar um certificado de AC num host do Windows. Este exemplo assume que está a utilizar o **azure-iot-teste-only.root.ca.cert.pem** certificado dos artigos de pré-requisitos, e que copiou o certificado para uma localização no dispositivo downstream.

1. No menu Iniciar, procure e selecione **gerir certificados de computador**. Um utilitário chamado **certlm** abre.
2. Navegue para **certificados - Computador Local** > **autoridades de certificação de raiz fidedigna**.
3. Com o botão direito **certificados** e selecione **todas as tarefas** > **importação**. Deve iniciar o Assistente para importar certificados. 
4. Siga os passos conforme indicado e importar o ficheiro de certificado `<path>/azure-iot-test-only.root.ca.cert.pem`. Quando concluída, deverá ver uma mensagem "Importada com êxito". 

Também pode instalar certificados através de programação através de APIs do .NET, conforme mostrado no exemplo de .NET neste artigo. 

Normalmente, aplicativos usam o Windows fornecido chamado de pilha TLS [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para ligar em segurança por TLS. Schannel *requer* que todos os certificados ser instalado no arquivo de certificados do Windows antes de tentar estabelecer uma conexão TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Utilizar certificados com o Azure IoT SDKs

Esta secção descreve como o Azure IoT SDKs ligar a um dispositivo IoT Edge com aplicativos de exemplo simples. O objetivo de todos os exemplos é ligar o cliente do dispositivo e enviar mensagens de telemetria para o gateway, em seguida, feche a ligação e sair. 

Ter prontas antes de utilizar os exemplos de nível de aplicativo de duas coisas:

* Cadeia de ligação do IoT Hub do seu dispositivo downstream modificada para apontar para o dispositivo de gateway e todos os certificados necessários para autenticar o dispositivo jusante para o IoT Hub. Para obter mais informações, consulte [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de AC de raiz que copiados e guardados em algum lugar no seu dispositivo de downstream.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Esta seção fornece um aplicativo de exemplo para ligar um cliente de dispositivo do Azure IoT NodeJS para um gateway do IoT Edge. Para aplicativos de NodeJS, tem de instalar o certificado de AC de raiz no nível do aplicativo, como mostrado aqui. As aplicações de NodeJS não utilizar o arquivo de certificados do sistema. 

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

Este é um comando de exemplo que testa o que tudo o que foi configurado corretamente. Deverá ver uma mensagem a indicar "verificado OK".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Resolver problemas relacionados com a ligação de gateway

Se o seu dispositivo de folha tem uma ligação intermitente para o seu dispositivo de gateway, tente os seguintes passos para a resolução. 

1. O nome de anfitrião de gateway na cadeia de ligação é igual ao valor de nome de anfitrião no ficheiro de config.yaml do IoT Edge no dispositivo de gateway?
2. O nome de anfitrião de gateway pode ser resolvido para um endereço IP? Pode resolver conexões intermitentes ao utilizar o DNS ou adicionando uma entrada do arquivo host do dispositivo de folha.
3. São portas de comunicação aberta na firewall? Comunicação com base no protocolo utilizado (MQTTS:8883 / AMQPS:5671 / HTTPS:433) tem de ser possível entre o dispositivo jusante e o IoT Edge transparente.

## <a name="next-steps"></a>Passos Seguintes

Saiba como pode estender o IoT Edge [recursos offline](offline-capabilities.md) a jusante dispositivos. 
