---
title: Ligar dispositivos a jusante - Azure IoT Edge [ Borda Azure IoT ] Microsoft Docs
description: Como configurar dispositivos a jusante ou de folhas para ligar aos dispositivos de gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510809"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ligar um dispositivo a jusante a um gateway do Azure IoT Edge

Este artigo fornece instruções para estabelecer uma ligação fidedigna entre dispositivos a jusante e gateways transparentes IoT Edge. Num cenário transparente de gateway, um ou mais dispositivos podem passar as suas mensagens através de um único dispositivo de gateway que mantém a ligação ao IoT Hub. Um dispositivo a jusante pode ser qualquer aplicação ou plataforma que tenha uma identidade criada com o serviço de nuvem [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) Em muitos casos, estas aplicações utilizam o [dispositivo Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser uma aplicação em execução no próprio dispositivo de gateway IoT Edge.

Existem três passos gerais para estabelecer uma ligação transparente e bem sucedida. Este artigo cobre o terceiro passo:

1. O dispositivo gateway precisa de ligar-se de forma segura a dispositivos a jusante, receber comunicações de dispositivos a jusante e direcionar mensagens para o destino adequado. Para mais informações, consulte [Configure um dispositivo IoT Edge para funcionar como uma porta de entrada transparente](how-to-create-transparent-gateway.md).
2. O dispositivo a jusante precisa de uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub, e saber comunicar através do seu dispositivo gateway. Para mais informações, consulte [Authenticate um dispositivo a jusante para o Hub Azure IoT](how-to-authenticate-downstream-device.md).
3. **O dispositivo a jusante tem de se ligar ao seu dispositivo de gateway de forma segura.**

Este artigo identifica problemas comuns com ligações a jusante e guia-o na configuração dos seus dispositivos a jusante por:

* Explicando a segurança da camada de transporte (TLS) e os fundamentos do certificado.
* Explicando como as bibliotecas TLS funcionam em diferentes sistemas operativos e como cada sistema operativo lida com certificados.
* Andando por amostras de IoT Azure em várias línguas para ajudá-lo a começar.

Neste artigo, os termos *gateway* e *IoT Edge gateway* referem-se a um dispositivo IoT Edge configurado como um gateway transparente.

## <a name="prerequisites"></a>Pré-requisitos

* Disponha do ficheiro de certificado **azure-iot-test-only.root.ca.cert.pem** que foi gerado no [Configure um dispositivo IoT Edge para funcionar como uma porta](how-to-create-transparent-gateway.md) de entrada transparente disponível no seu dispositivo a jusante. O seu dispositivo a jusante utiliza este certificado para validar a identidade do dispositivo gateway.
* Dispor da cadeia de ligação modificada que aponta para o dispositivo gateway, como explica do [Authenticate um dispositivo a jusante ao Hub Azure IoT](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Prepare um dispositivo a jusante

Um dispositivo a jusante pode ser qualquer aplicação ou plataforma que tenha uma identidade criada com o serviço de nuvem [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) Em muitos casos, estas aplicações utilizam o [dispositivo Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Um dispositivo a jusante pode até ser uma aplicação em execução no próprio dispositivo de gateway IoT Edge. No entanto, outro dispositivo IoT Edge não pode estar a jusante de um gateway IoT Edge.

>[!NOTE]
>Os dispositivos IoT que tenham identidades registadas no IoT Hub podem usar [gémeos módulos](../iot-hub/iot-hub-devguide-module-twins.md) para isolar diferentes processos, hardware ou funções num único dispositivo. Os gateways IoT Edge suportam ligações de módulos a jusante utilizando a autenticação simétrica da chave, mas não a autenticação do certificado X.509.

Para ligar um dispositivo a jusante a um portal IoT Edge, precisa de duas coisas:

* Um dispositivo ou aplicação configurado com uma cadeia de ligação de dispositivo IoT Hub anexada com informações para ligá-lo ao gateway.

    Este passo é explicado em [Authenticate um dispositivo a jusante para o Hub Azure IoT](how-to-authenticate-downstream-device.md).

* O dispositivo ou aplicação tem de confiar no certificado **CA raiz** do gateway para validar as ligações TLS ao dispositivo gateway.

    Este passo é explicado em detalhe no resto deste artigo. Este passo pode ser realizado de duas formas: instalando o certificado CA no certificado do sistema operativo, ou (para certos idiomas) fazendo referência ao certificado dentro das aplicações utilizando os SDKs Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>TLS e fundamentos de certificado

O desafio de ligar os dispositivos a jusante de forma segura ao IoT Edge é como qualquer outra comunicação segura de cliente/servidor que ocorra através da internet. Um cliente e um servidor comunicam de forma segura através da internet utilizando a segurança da camada de [transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). O TLS é construído utilizando as construções padrão de [infraestrutura pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) chamadas certificados. TLS é uma especificação bastante envolvida e aborda uma vasta gama de tópicos relacionados com a garantia de dois pontos finais. Esta secção resume os conceitos relevantes para que possa ligar os dispositivos de forma segura a um gateway IoT Edge.

Quando um cliente se conecta a um servidor, o servidor apresenta uma cadeia de certificados, chamada cadeia de *certificados*do servidor . Uma cadeia de certificados normalmente compreende um certificado de autoridade de certificado de raiz (CA), um ou mais certificados ca intermédios, e finalmente o próprio certificado do servidor. Um cliente estabelece a confiança com um servidor verificando criptograficamente toda a cadeia de certificados do servidor. Esta validação do cliente da cadeia de certificados de servidor chama-se *validação da cadeia*de servidores. O cliente desafia criptograficamente o serviço para provar a posse da chave privada associada ao certificado de servidor num processo chamado *prova de posse*. A combinação de validação da cadeia de servidores e prova de posse chama-se *autenticação*do servidor . Para validar uma cadeia de certificados de servidor, um cliente precisa de uma cópia do certificado CA raiz que foi usado para criar (ou emitir) o certificado do servidor. Normalmente, quando se conecta a websites, um navegador vem pré-configurado com certificados CA geralmente usados para que o cliente tenha um processo sem emenda.

Quando um dispositivo se liga ao Azure IoT Hub, o dispositivo é o cliente e o serviço de nuvem IoT Hub é o servidor. O serviço de nuvem IoT Hub é apoiado por um certificado ca raiz chamado **Baltimore CyberTrust Root**, que é publicamente disponível e amplamente utilizado. Uma vez que o certificado IoT Hub CA já está instalado na maioria dos dispositivos, muitas implementações de TLS (OpenSSL, Schannel, LibreSSL) usam-no automaticamente durante a validação do certificado do servidor. Um dispositivo que pode ligar-se com sucesso ao IoT Hub pode ter problemas ao tentar ligar-se a um gateway IoT Edge.

Quando um dispositivo se liga a um portal IoT Edge, o dispositivo a jusante é o cliente e o dispositivo gateway é o servidor. O Azure IoT Edge permite que os operadores (ou utilizadores) construam cadeias de certificados de gateway como entenderem. O operador pode optar por utilizar um certificado de AC público, como Baltimore, ou utilizar um certificado de ac raiz auto-assinado (ou interno). Os certificados públicos de CA têm frequentemente um custo associado a eles, por isso são normalmente usados em cenários de produção. Os certificados de A Auto-assinados são preferidos para o desenvolvimento e teste. Os artigos de configuração transparentes de gateway listados na introdução utilizam certificados ca raiz auto-assinados.

Quando utilizar um certificado de AC raiz auto-assinado para um gateway IoT Edge, este tem de ser instalado ou fornecido a todos os dispositivos a jusante que tentem ligar-se ao portal.

![Configuração do certificado gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Para saber mais sobre os certificados IoT Edge e algumas implicações de produção, consulte detalhes de utilização do [certificado IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornecer o certificado de ca raiz

Para verificar os certificados do dispositivo gateway, o dispositivo a jusante precisa da sua própria cópia do certificado ca raiz. Se utilizou os scripts fornecidos no repositório IoT Edge git para criar certificados de teste, então o certificado ca raiz é chamado **azure-iot-test-only.root.ca.cert.pem**. Se ainda não fez parte das outras etapas de preparação do dispositivo a jusante, desloque este ficheiro de certificado para qualquer diretório no seu dispositivo a jusante. Pode utilizar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo de cópia [Segura](https://www.ssh.com/ssh/scp/) para mover o ficheiro de certificado.

## <a name="install-certificates-in-the-os"></a>Instalar certificados no SO

A instalação do certificado CA raiz na loja de certificados do sistema operativo permite geralmente que a maioria das aplicações utilizem o certificado CA raiz. Existem algumas exceções, como aplicações nodeJS que não usam a loja de certificados S, mas sim usam a loja de certificados internos do Nó. Se não conseguir instalar o certificado ao nível do sistema operativo, salte para a frente para [utilizar certificados com SDKs Azure IoT](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Os seguintes comandos são um exemplo de como instalar um certificado CA num hospedeiro Ubuntu. Este exemplo pressupõe que está a usar o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos pré-requisitos, e que copiou o certificado para um local no dispositivo a jusante.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Deve ver uma mensagem que diz: "Atualizar certificados em /etc/ssl/certs... 1 adicionado, 0 removido; feito.

### <a name="windows"></a>Windows

Os seguintes passos são um exemplo de como instalar um certificado CA num hospedeiro do Windows. Este exemplo pressupõe que está a usar o certificado **azure-iot-test-only.root.ca.cert.pem** dos artigos pré-requisitos, e que copiou o certificado para um local no dispositivo a jusante.

Pode instalar certificados utilizando o [Certificado de Importação](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) da PowerShell como administrador:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Também pode instalar certificados utilizando o utilitário **certlm:**

1. No menu Iniciar, procure e selecione **Gerir certificados de computador**. Abre-se um utilitário chamado **certlm.**
2. Navegar para **Certificados - Autoridades locais** > de**certificação de raiz fidedignas**de computador.
3. Clique em **certificados** de clique à direita e selecione **Todas as Tarefas** > **Importadas**. O assistente de importação de certificados deve ser lançado.
4. Siga os passos como ficheiro `<path>/azure-iot-test-only.root.ca.cert.pem`de certificado de importação e direcionado. Quando concluído, deve ver uma mensagem "importada com sucesso".

Também pode instalar certificados programáticamente utilizando APIs .NET, como mostra a amostra .NET mais tarde neste artigo.

Normalmente, as aplicações utilizam o Windows fornecido a pilha TLS chamada [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) para se conectarem de forma segura sobre tLS. O Schannel exige que quaisquer certificados sejam *instalados* na loja de certificados Windows antes de tentar estabelecer uma ligação TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Utilize certificados com DSKs Azure IoT

Esta secção descreve como os SDKs Azure IoT se ligam a um dispositivo IoT Edge utilizando aplicações simples de amostra. O objetivo de todas as amostras é ligar o cliente do dispositivo e enviar mensagens de telemetria para o portal, em seguida, fechar a ligação e sair.

Tenha duas coisas prontas antes de utilizar as amostras de nível de aplicação:

* A cadeia de ligação IoT Hub do seu dispositivo a jusante modificada para apontar para o dispositivo gateway, e quaisquer certificados necessários para autenticar o seu dispositivo a jusante ao IoT Hub. Para mais informações, consulte [Authenticate um dispositivo a jusante para o Hub Azure IoT](how-to-authenticate-downstream-device.md).

* O caminho completo para o certificado de ca raiz que copiou e guardou algures no seu dispositivo a jusante.

    Por exemplo, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Esta secção fornece uma aplicação de amostra para ligar um cliente do dispositivo Azure IoT NodeJS a um gateway IoT Edge. Para aplicações NodeJS, deve instalar o certificado CA raiz ao nível da aplicação, como mostrado aqui. As aplicações nodeJS não usam a loja de certificados do sistema.

1. Obtenha a amostra para **edge_downstream_device.js** do [dispositivo Azure IoT SDK para amostras node.js repo](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra revendo o ficheiro **readme.md.**
3. No ficheiro edge_downstream_device.js, atualize as variáveis **connectionString** e **edge_ca_cert_path.**
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

Para compreender a amostra que está a executar, o seguinte código é como o SDK cliente lê o ficheiro de certificado e o utiliza para estabelecer uma ligação TLS segura:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Esta secção introduz uma aplicação de amostra para ligar um cliente de dispositivo Azure IoT .NET a um gateway IoT Edge. No entanto, as aplicações .NET podem utilizar automaticamente quaisquer certificados instalados na loja de certificados do sistema, tanto nos anfitriões do Linux como do Windows.

1. Obtenha a amostra para **EdgeDownstreamDevice** na pasta de [amostras IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra revendo o ficheiro **readme.md.**
3. No ficheiro **Properties / launchSettings.json,** atualize as variáveis **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH.** Se pretender utilizar o certificado instalado no certificado de confiança no sistema de hospedagem, deixe esta variável em branco.
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

Para instalar programáticamente um certificado fidedigno na loja de certificados através de uma aplicação .NET, consulte a função **InstallCACert** no ficheiro **EdgeDownstreamDevice/Program.cs.** Esta operação é idempotente, pelo que pode ser executada várias vezes com os mesmos valores sem efeito adicional.

### <a name="c"></a>C

Esta secção introduz uma aplicação de amostra para ligar um cliente do dispositivo Azure IoT C a um gateway IoT Edge. O C SDK pode operar com muitas bibliotecas TLS, incluindo OpenSSL, WolfSSL e Schannel. Para mais informações, consulte o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Obtenha a aplicação **iotedge_downstream_device_sample** do [dispositivo Azure IoT SDK para amostras De C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra revendo o ficheiro **readme.md.**
3. No ficheiro iotedge_downstream_device_sample.c, atualize as variáveis **de ligaçãoString** e **edge_ca_cert_path.**
4. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

O dispositivo Azure IoT SDK para C oferece uma opção de registar um certificado CA ao configurar o cliente. Esta operação não instala o certificado em lado nenhum, mas utiliza um formato de cadeia do certificado na memória. O certificado guardado é fornecido à pilha tLS subjacente ao estabelecer uma ligação.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Nos anfitriões do Windows, se não estiver a utilizar o OpenSSL ou outra biblioteca TLS, o padrão SDK é utilizar o Canal schannel. Para que o Schannel funcione, o certificado CA raiz IoT Edge deve `IoTHubDeviceClient_SetOption` ser instalado na loja de certificados Windows, não definido utilizando a operação.

### <a name="java"></a>Java

Esta secção introduz uma aplicação de amostra para ligar um cliente do dispositivo Azure IoT Java a um gateway IoT Edge.

1. Obtenha a amostra para o **envio** do [dispositivo Azure IoT SDK para amostras java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Certifique-se de que tem todos os pré-requisitos para executar a amostra revendo o ficheiro **readme.md.**
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

### <a name="python"></a>Python

Esta secção introduz uma aplicação de amostra para ligar um cliente do dispositivo Azure IoT Python a um gateway IoT Edge.

1. Obtenha a amostra para **send_message** do [dispositivo Azure IoT SDK para amostras de Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios).
2. Certifique-se de que está a correr num recipiente IoT Edge, ou num cenário de depuração, tem as `EdgeHubConnectionString` variáveis e `EdgeModuleCACertificateFile` ambiente definidas.
3. Consulte a documentação do SDK para obter instruções sobre como executar a amostra no seu dispositivo.

## <a name="test-the-gateway-connection"></a>Testar a ligação de gateway

Utilize este comando de amostra para testar que o seu dispositivo a jusante pode ligar-se ao dispositivo de gateway:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Este comando testa ligações sobre MQTTS (porta 8883). Se estiver a utilizar um protocolo diferente, ajuste o comando conforme necessário para AMQPS (5671) ou HTTPS (433)

A saída deste comando pode ser longa, incluindo informações sobre todos os certificados da cadeia. Se a sua ligação for bem `Verification: OK` sucedida, verá uma linha como ou. `Verify return code: 0 (ok)`

![Verificar a ligação de gateway](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Problemas de resolução da ligação de gateway

Se o seu dispositivo de folha tiver ligação intermitente ao seu dispositivo gateway, experimente os seguintes passos para resolução.

1. O nome de gateway na cadeia de ligação é o mesmo que o valor do nome de anfitrião no ficheiro config.yaml IoT Edge no dispositivo gateway?
2. O nome de anfitrião do portal é resolúvel para um endereço IP? Pode resolver ligações intermitentes utilizando DNS ou adicionando uma entrada de ficheiro de hospedeiro no dispositivo de folha.
3. As portas de comunicação estão abertas na sua firewall? A comunicação com base no protocolo utilizado (MQTTS:8883/AMQPS:5671/HTTPS:433) deve ser possível entre o dispositivo a jusante e o IoT Edge transparente.

## <a name="next-steps"></a>Passos seguintes

Saiba como o IoT Edge pode estender [as capacidades offline](offline-capabilities.md) a dispositivos a jusante.
