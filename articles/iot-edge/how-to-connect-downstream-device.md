---
title: Ligar dispositivos a jusante - Azure IoT Edge / Microsoft Docs
description: Como configurar dispositivos a jusante ou de folhas para ligar aos dispositivos de gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 78600b7b57a7c30fc609434a700f13fa21e079ce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019264"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ligar um dispositivo a jusante a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma ligação fidedigna entre dispositivos a jusante e gateways transparentes IoT Edge. Num cenário transparente de gateway, um ou mais dispositivos podem passar as suas mensagens através de um único dispositivo de gateway que mantém a ligação ao IoT Hub.

Existem três passos gerais para estabelecer uma ligação transparente de gateway bem sucedida. Este artigo abrange o terceiro passo:

1. Configure o dispositivo gateway como um servidor para que os dispositivos a jusante possam ligar-se a ele de forma segura. Configurar o portal para receber mensagens de dispositivos a jusante e encaminhá-las para o destino adequado. Para esses passos, consulte [configurar um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md).
2. Crie uma identidade do dispositivo para o dispositivo a jusante para que possa autenticar com o IoT Hub. Configure o dispositivo a jusante para enviar mensagens através do dispositivo gateway. Para esses passos, consulte [Autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Ligue o dispositivo a jusante ao dispositivo gateway e comece a enviar mensagens.**

Este artigo discute conceitos básicos para ligações a jusante do dispositivo e guia-o na configuração dos seus dispositivos a jusante:

* Explicando a segurança da camada de transporte (TLS) e os fundamentos dos certificados.
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operativos e como cada sistema operativo lida com certificados.
* Andando por amostras de IoT do Azure em várias línguas para ajudá-lo a começar.

Neste artigo, os termos *gateway* e *gateway IoT Edge* referem-se a um dispositivo IoT Edge configurado como um gateway transparente.

## <a name="prerequisites"></a>Pré-requisitos

* Tenha o ficheiro de certificado de CA raiz que foi utilizado para gerar o certificado ca do dispositivo em [Configure um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md) disponível no seu dispositivo a jusante. O seu dispositivo a jusante utiliza este certificado para validar a identidade do dispositivo gateway. Se usou os certificados de demonstração, o certificado de CA raiz é denominado **azure-iot-test-only.root.ca.cert.pem**.
* Tenha a cadeia de ligação modificada que aponta para o dispositivo gateway, como explicado no [Authenticate um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Preparar um dispositivo a jusante

Um dispositivo a jusante pode ser qualquer aplicação ou plataforma que tenha uma identidade criada com o serviço de nuvem Azure IoT Hub. Em muitos casos, estas aplicações utilizam o [dispositivo Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser uma aplicação em execução no próprio dispositivo de gateway IoT Edge. No entanto, outro dispositivo IoT Edge não pode ser a jusante de um gateway IoT Edge.

>[!NOTE]
>Os dispositivos IoT registados com o IoT Hub podem usar [gémeos módulos](../iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções num único dispositivo. Os gateways IoT Edge suportam ligações de módulos a jusante utilizando a autenticação da chave simétrica, mas não a autenticação do certificado X.509.

Para ligar um dispositivo a jusante a um gateway IoT Edge, precisa de duas coisas:

* Um dispositivo ou aplicação configurado com uma cadeia de ligação do dispositivo IoT Hub anexada a informações para conectá-lo ao gateway.

    Este passo foi preenchido no artigo anterior, [Autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* O dispositivo ou aplicação tem de confiar no **certificado de CA raiz** do gateway para validar as ligações de segurança da camada de transporte (TLS) ao dispositivo gateway.

    Este passo é explicado em detalhe no resto deste artigo. Este passo pode ser realizado de duas formas: instalando o certificado de CA na loja de certificados do sistema operativo, ou (para determinadas línguas), referindo o certificado dentro das aplicações utilizando os SDKs Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>TLS e fundamentos de certificado

O desafio de ligar seguramente os dispositivos a jusante ao IoT Edge é como qualquer outra comunicação segura de cliente/servidor que ocorre através da internet. Um cliente e um servidor comunicam-se de forma segura através da internet utilizando a [segurança da camada de transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído utilizando as construções padrão [de infraestruturas de chaves públicas (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas certificados. O TLS é uma especificação bastante envolvida e aborda uma vasta gama de tópicos relacionados com a garantia de dois pontos finais. Esta secção resume os conceitos relevantes para ligar os dispositivos de forma segura a um gateway IoT Edge.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada cadeia de *certificados* do servidor . Uma cadeia de certificados compreende normalmente um certificado de autoridade de certificados de raiz (CA), um ou mais certificados de CA intermédios e, finalmente, o próprio certificado do servidor. Um cliente estabelece a confiança com um servidor verificando criptograficamente toda a cadeia de certificados do servidor. Esta validação do cliente da cadeia de certificados do servidor chama-se *validação da cadeia de servidores*. O cliente desafia o servidor a provar a posse da chave privada associada ao certificado do servidor num processo chamado *prova de posse*. A combinação de validação da cadeia de servidor e prova de posse chama-se *autenticação do servidor*. Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado de CA raiz que foi usado para criar (ou emitir) o certificado do servidor. Normalmente, ao ligar-se a websites, um navegador vem pré-configurado com certificados de CA comumente usados para que o cliente tenha um processo sem emenda.

Quando um dispositivo se conecta ao Azure IoT Hub, o dispositivo é o cliente e o serviço de nuvem IoT Hub é o servidor. O serviço de nuvem IoT Hub é apoiado por um certificado de CA raiz chamado **Baltimore CyberTrust Root**, que está disponível ao público e amplamente utilizado. Uma vez que o certificado IoT Hub CA já está instalado na maioria dos dispositivos, muitas implementações TLS (OpenSSL, Schannel, LibreSSL) usam-no automaticamente durante a validação do certificado do servidor. No entanto, um dispositivo que se conecta com sucesso ao IoT Hub pode ter problemas ao tentar ligar-se a um gateway IoT Edge.

Quando um dispositivo se liga a um gateway IoT Edge, o dispositivo a jusante é o cliente e o dispositivo gateway é o servidor. O Azure IoT Edge permite-lhe construir cadeias de certificados de gateway como entenderem. Você pode optar por usar um certificado de CA público, como Baltimore, ou usar um certificado de CA de raiz auto-assinado (ou interno). Os certificados de CA públicos têm frequentemente um custo associado a eles, por isso são normalmente usados em cenários de produção. Os certificados de CA auto-assinados são preferidos para desenvolvimento e teste. Se estiver a usar os certificados de demonstração, são certificados de CA de raiz auto-assinados.

Quando utilizar um certificado de CA de raiz auto-assinado para um gateway IoT Edge, este precisa de ser instalado ou fornecido a todos os dispositivos a jusante que tentam ligar-se ao gateway.

![Configuração do certificado gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre certificados IoT Edge e algumas implicações de produção, consulte os detalhes de [utilização do certificado IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornecer o certificado de CA raiz

Para verificar os certificados do dispositivo gateway, o dispositivo a jusante necessita da sua própria cópia do certificado de CA raiz. Se usou os scripts fornecidos no repositório IoT Edge git para criar certificados de teste, então o certificado de CA raiz é chamado **azure-iot-test-only.root.ca.cert.pem**. Se ainda não fez parte das outras etapas de preparação do dispositivo a jusante, mova este ficheiro de certificado para qualquer diretório do seu dispositivo a jusante. Pode utilizar um serviço como [o Azure Key Vault](../key-vault/index.yml) ou uma função como o protocolo de cópia [Secure](https://www.ssh.com/ssh/scp/) para mover o ficheiro de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no SISTEMA

Uma vez que o certificado de CA raiz esteja no dispositivo a jusante, você precisa ter certeza de que as aplicações que estão conectando ao gateway podem aceder ao certificado.

A instalação do certificado de CA raiz na loja de certificados do sistema operativo geralmente permite que a maioria das aplicações utilize o certificado de CA raiz. Existem algumas exceções, como as aplicações NodeJS que não utilizam a loja de certificados OS, mas sim a loja de certificados internos do Nó. Se não conseguir instalar o certificado ao nível do sistema operativo, avance para [utilizar certificados com SDKs Azure IoT](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado de CA num anfitrião Ubuntu. Este exemplo pressupõe que está a utilizar o certificado **azure-iot-test-only.root.ca.cert.pem** a partir dos artigos pré-requisitos, e que copiou o certificado para um local no dispositivo a jusante.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Deve ver uma mensagem que diz: "Atualizar certificados em /etc/ssl/certs... 1 adicionado, 0 removido; feito.

### <a name="windows"></a>Windows

Os seguintes passos são um exemplo de como instalar um certificado de CA num anfitrião do Windows. Este exemplo pressupõe que está a utilizar o certificado **azure-iot-test-only.root.ca.cert.pem** a partir dos artigos pré-requisitos, e que copiou o certificado para um local no dispositivo a jusante.

Pode instalar certificados utilizando o [Certificado de Importação](/powershell/module/pkiclient/import-certificate?view=win10-ps) da PowerShell como administrador:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Também pode instalar certificados utilizando o utilitário **certlm:**

1. No menu Iniciar, procure e **selecione Obter certificados de computador**. Um utilitário chamado **certlm** abre.
2. Navegar para **certificados - Autoridades locais**  >  **de certificação de raiz fidedignas de** computador.
3. Clique com o botão **direito Certificados** e selecione **Todas as**  >  **Tarefas Importadas**. O assistente de importação de certificados deve ser lançado.
4. Siga as etapas conforme o ficheiro de certificados direcionado e de `<path>/azure-iot-test-only.root.ca.cert.pem` importação. Quando concluído, deverá ver uma mensagem "importada com sucesso".

Também pode instalar certificados programáticamente utilizando .NET APIs, como mostrado na amostra .NET mais tarde neste artigo.

Normalmente, as aplicações utilizam a stack TLS fornecida pelo Windows chamada [Schannel](/windows/desktop/com/schannel) para ligar de forma segura sobre o TLS. A Schannel *exige* que quaisquer certificados sejam instalados na loja de certificados do Windows antes de tentar estabelecer uma ligação TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Utilize certificados com Azure IoT SDKs

Esta secção descreve como os SDKs Azure IoT ligam-se a um dispositivo IoT Edge utilizando aplicações simples de amostragem. O objetivo de todas as amostras é ligar o cliente do dispositivo e enviar mensagens de telemetria para o gateway, para depois fechar a ligação e saída.

Ter duas coisas prontas antes de utilizar as amostras de nível de aplicação:

* A cadeia de ligação IoT Hub do seu dispositivo a jusante modificou-se para apontar para o dispositivo gateway e quaisquer certificados necessários para autenticar o seu dispositivo a jusante para o IoT Hub. Para obter mais informações, consulte [Autenticar um dispositivo a jusante para o Azure IoT Hub](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de CA raiz que copiou e guardou em algum lugar no seu dispositivo a jusante.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Esta secção fornece uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT NodeJS a um gateway IoT Edge. Para aplicações NodeJS, deve instalar o certificado de CA raiz ao nível da aplicação, como mostrado aqui. As aplicações nodeJS não utilizam a loja de certificados do sistema.

1. Obtenha a amostra para **edge_downstream_device.js** do [dispositivo Azure IoT SDK para Node.js amostras repo](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra, revendo o ficheiro **readme.md.**
3. No ficheiro edge_downstream_device.js, atualize as variáveis **connectionString** e **edge_ca_cert_path.**
4. Consulte a documentação SDK para obter instruções sobre como executar a amostra no seu dispositivo.

Para compreender a amostra que está a executar, o seguinte código é como o cliente SDK lê o ficheiro de certificado e o utiliza para estabelecer uma ligação TLS segura:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta secção introduz uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT .NET a um gateway IoT Edge. No entanto, as aplicações .NET podem utilizar automaticamente quaisquer certificados instalados na loja de certificados do sistema tanto nos anfitriões Linux como windows.

1. Obtenha a amostra de **EdgeDownstreamDevice** na pasta de [amostras IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra, revendo o ficheiro **readme.md.**
3. Nas **Propriedades /launchSettings.jsem** arquivo, atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH.** Se pretender utilizar o certificado instalado na loja de certificados fidedigna no sistema de anfitrião, deixe esta variável em branco.
4. Consulte a documentação SDK para obter instruções sobre como executar a amostra no seu dispositivo.

Para instalar programáticamente um certificado de confiança na loja de certificados através de uma aplicação .NET, consulte a função **InstallCACert()** no ficheiro **EdgeDownstreamDevice/Program.cs.** Esta operação é idempotente, pelo que pode ser executada várias vezes com os mesmos valores sem efeito adicional.

### <a name="c"></a>C

Esta secção introduz uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT C a um gateway IoT Edge. O C SDK pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para mais informações, consulte o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Obtenha a aplicação **iotedge_downstream_device_sample** do [dispositivo Azure IoT SDK para amostras C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra, revendo o ficheiro **readme.md.**
3. No ficheiro iotedge_downstream_device_sample.c, atualize as variáveis **connectionString** e **edge_ca_cert_path.**
4. Consulte a documentação SDK para obter instruções sobre como executar a amostra no seu dispositivo.


O dispositivo Azure IoT SDK para C oferece uma opção de registar um certificado de CA ao configurar o cliente. Esta operação não instala o certificado em lado nenhum, mas utiliza um formato de cadeia do certificado na memória. O certificado guardado é fornecido à pilha TLS subjacente ao estabelecer uma ligação.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> O método para registar um certificado de CA ao configurar o cliente pode ser alterado se utilizar um pacote ou biblioteca [gerido.](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) Por exemplo, a [biblioteca baseada em Arduino IDE](https://github.com/azure/azure-iot-arduino) exigirá a adição do certificado de CA a um conjunto de certificados definido num ficheiro global [.c,](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) em vez de utilizar a `IoTHubDeviceClient_LL_SetOption` operação.  

Nos anfitriões windows, se não estiver a utilizar o OpenSSL ou outra biblioteca TLS, o SDK não pode utilizar o Schannel. Para o Schannel funcionar, o certificado CA raiz IoT Edge deve ser instalado na loja de certificados Windows, não definido utilizando a `IoTHubDeviceClient_SetOption` operação.

### <a name="java"></a>Java

Esta secção introduz uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT Java a um gateway IoT Edge.

1. Obtenha a amostra para **envio do dispositivo** [Azure IoT SDK para amostras de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra, revendo o ficheiro **readme.md.**
3. Consulte a documentação SDK para obter instruções sobre como executar a amostra no seu dispositivo.

### <a name="python"></a>Python

Esta secção introduz uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT Python a um gateway IoT Edge.

1. Obtenha a amostra para **send_message_downstream** do [dispositivo Azure IoT SDK para amostras python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Desaprova as `IOTHUB_DEVICE_CONNECTION_STRING` variáveis e `IOTEDGE_ROOT_CA_CERT_PATH` ambientais especificadas nos comentários do script Python.
3. Consulte a documentação SDK para obter instruções adicionais sobre como executar a amostra no seu dispositivo.

## <a name="test-the-gateway-connection"></a>Teste a ligação gateway

Utilize este comando de amostra no dispositivo a jusante para testar que pode ligar-se ao dispositivo gateway:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Este comando testa as ligações sobre o MQTTS (porta 8883). Se estiver a utilizar um protocolo diferente, ajuste o comando conforme necessário para AMQPS (5671) ou HTTPS (433)

A saída deste comando pode ser longa, incluindo informações sobre todos os certificados da cadeia. Se a sua ligação for bem sucedida, verá uma linha como `Verification: OK` ou `Verify return code: 0 (ok)` .

![Verifique a ligação do gateway](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Resolução de problemas da ligação gateway

Se o seu dispositivo de folha tiver ligação intermitente ao seu dispositivo gateway, experimente os seguintes passos para resolução.

1. O nome de anfitrião do gateway na cadeia de ligação é o mesmo que o valor do nome hospedeiro no ficheiro IoT Edge config.yaml no dispositivo gateway?
2. O nome de anfitrião do portal é resolúvel para um endereço IP? Pode resolver ligações intermitentes utilizando DNS ou adicionando uma entrada de ficheiro de anfitrião no dispositivo de folha.
3. As portas de comunicação estão abertas na sua firewall? A comunicação com base no protocolo utilizado (MQTTS:8883/AMQPS:5671/HTTPS:433) deve ser possível entre o dispositivo a jusante e o IoT Edge transparente.

## <a name="next-steps"></a>Passos seguintes

Saiba como o IoT Edge pode estender [as capacidades offline](offline-capabilities.md) a dispositivos a jusante.