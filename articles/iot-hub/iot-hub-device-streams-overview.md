---
title: Fluxos de dispositivo IoT Hub do Azure (pré-visualização) | Documentos da Microsoft
description: Descrição geral dos fluxos de dispositivo do IoT Hub.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 7b436acb937216ad02289282f4a7ac5f88df2eea
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245154"
---
# <a name="iot-hub-device-streams-preview"></a>Fluxos de dispositivo do Hub IoT (pré-visualização)

## <a name="overview"></a>Descrição geral
O IoT Hub do Azure *fluxos de dispositivo* facilitar a criação de túneis seguros de TCP bidirecionais para uma variedade de cenários de comunicação de cloud-para-dispositivo. Um fluxo de dispositivo é mediado por um IoT Hub *ponto final de transmissão em fluxo* que atua como um proxy entre os pontos finais de serviço e dispositivo. Esta configuração, descrita no diagrama abaixo, é especialmente útil quando os dispositivos estão protegidos por uma firewall de rede ou de residir na mesma rede privada. Como tal, fluxos de dispositivo do IoT Hub ajudam a necessidade dos clientes de endereço para chegarem aos dispositivos de IoT, de forma compatível com firewall e sem a necessidade de amplamente abrir as portas de firewall de rede de entrada ou de saída.

![Texto alternativo](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png "descrição geral de fluxos de dispositivos no IoT Hub")


Utilizar fluxos de dispositivo do IoT Hub, os dispositivos permanecem seguros e só precisará de abrir ligações de saída de TCP para o ponto final de transmissão em fluxo do hub IoT através da porta 443. Assim que um fluxo é estabelecido, os aplicativos do lado do serviço e do lado do dispositivo cada um terá acesso programático a um objeto de cliente de WebSocket para enviar e receber bytes não processados para um do outro. A fiabilidade e a ordenação garantias fornecidas por este túnel é igual de TCP.

## <a name="benefits"></a>Benefícios
Fluxos de dispositivo do IoT Hub fornecem as seguintes vantagens:
- **Compatível com firewall de conectividade segura:** Dispositivos IoT podem ser contactados a partir de pontos finais de serviço sem abertura da porta de firewall de entrada em perímetros de rede ou de dispositivo (apenas conectividade de saída para o IoT Hub é necessária através da porta 443).

- **Autenticação:** Serviço e dispositivo lados do túnel tem de autenticar com o IoT Hub com as respetivas credenciais correspondentes.

- **Encriptação:** Por predefinição, os fluxos de dispositivo do IoT Hub utilizam ligações habilitados para TLS. Isto garante que o tráfego é sempre encriptado independentemente se o aplicativo usa a criptografia ou não.

- **Simplicidade de conectividade:** Em muitos casos, a utilização de fluxos de dispositivo elimina a necessidade de configuração complexa de redes virtuais privadas para permitir a conectividade a dispositivos de IoT.

- **Compatibilidade com a pilha TCP/IP:** Fluxos de dispositivo do IoT Hub podem acomodar o tráfego de aplicativo de TCP/IP. Isso significa que uma grande variedade de protocolos proprietários, bem como com base em padrões pode tirar partido desta funcionalidade.

- **Facilidade de uso em configurações de rede privada:** Ao referenciar o ID de dispositivo, em vez de endereço IP do dispositivo, o serviço pode comunicar com um dispositivo. Isto é útil em situações em que um dispositivo está localizado numa rede privada e tem um endereço IP privado, ou o endereço IP for atribuído dinamicamente e é desconhecido no lado do serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho do dispositivo Stream
Um fluxo de dispositivo é iniciado quando o serviço de pedidos para ligar a um dispositivo ao fornecer o seu ID de dispositivo. Este fluxo de trabalho particularmente se encaixa num modelo de comunicação de cliente/servidor, incluindo o protocolo RDP, em que um utilizador pretende ligar remotamente ao servidor SSH ou RDP em execução no dispositivo com um programa de cliente SSH ou RDP e SSH.

O processo de criação de fluxo de dispositivo envolve uma negociação entre o dispositivo, serviço, main do hub IoT e os pontos finais de transmissão em fluxo. Embora o ponto final de principal do hub IoT orquestra a criação de um fluxo de dispositivo, o ponto final de transmissão em fluxo processa o tráfego que flui entre o serviço e dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação de fluxo de dispositivo
Criação programática de um fluxo de dispositivo com o SDK envolve os seguintes passos, que também são descritos na figura abaixo:

![Texto alternativo](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png "dispositivo transmitir o processo de handshake")


1. A aplicação de dispositivo registra um retorno de chamada com antecedência para ser notificado de quando um novo fluxo de dispositivo é iniciado no dispositivo. Este passo normalmente ocorre quando o dispositivo é inicializado e liga ao IoT Hub.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo o ID de dispositivo (_não_ o endereço IP).

3. IoT hub notifica o programa do lado do dispositivo, invocando o retorno de chamada registrado no passo 1. O dispositivo pode aceitar ou rejeitar a solicitação de inicialização de fluxo. Essa lógica pode ser específica ao seu cenário de aplicação. Se o pedido de transmissão é rejeitado pelo dispositivo, o IoT Hub informa o serviço em conformidade; caso contrário, siga os passos abaixo.

4. O dispositivo cria uma ligação de TCP de saída segura, o ponto final de transmissão em fluxo através da porta 443 e atualiza a ligação para um WebSocket. O URL de ponto final da transmissão, bem como as credenciais a utilizar para autenticar são ambos fornecidos para o dispositivo ao IoT Hub como parte do pedido enviado no passo 3.

5. O serviço é notificado do resultado de dispositivo, aceita o fluxo e cria o seu próprio cliente de WebSocket para o ponto final de transmissão em fluxo. Da mesma forma, recebe as transmissão em fluxo informações de autenticação e de URL de ponto final do IoT Hub.

No processo de handshake acima:
- O processo de handshake deve ser concluído em 60 segundos (etapa 2 a 5), caso contrário, o handshake irá falhar com um tempo limite e o serviço será notificado nesse sentido.

- Depois de concluir o fluxo de criação de fluxo acima, o ponto final de transmissão em fluxo irão funcionar como um proxy e transferirá o tráfego entre o serviço e o dispositivo através de seus respectivos WebSockets.

- Dispositivo e o serviço tem conectividade de saída para o ponto final de principal do IoT Hub, bem como o ponto final de transmissão em fluxo através da porta 443. O URL desses pontos de extremidade está disponível no *descrição geral* separador no portal do IoT Hub.

- A fiabilidade e a ordenação garantias de um fluxo estabelecido é igual de TCP.

- Todas as ligações ao IoT Hub e o ponto final de transmissão em fluxo utilizam TLS e são encriptadas.

### <a name="termination-flow"></a>Fluxo de terminação
Um fluxo estabelecido termina quando qualquer uma das ligações TCP para o gateway está ligado à Internet (por dispositivo ou serviço). Isto pode ocorrer voluntariamente fechando o WebSocket em programas do dispositivo ou serviço ou involuntarily em caso de falha de tempo limite ou de processo da conectividade de rede. Após a cessação do dispositivo ou ligação do serviço para o ponto final de transmissão em fluxo, a ligação de TCP também irá ser terminada (forçadamente) e o serviço e dispositivo são responsáveis voltar a criar o fluxo, se necessário.


## <a name="connectivity-requirements"></a>Requisitos de conectividade

O dispositivo e os lados do serviço de um fluxo de dispositivo tem de ser capazes de estabelecer ligações habilitados para TLS para o IoT Hub e o respetivo ponto final de transmissão em fluxo. Isto requer conectividade de saída através da porta 443 para estes pontos finais. O nome de anfitrião associado a estes pontos finais pode ser encontrado no *descrição geral* separador do IoT Hub, conforme mostrado na figura abaixo: ![Texto alternativo](./media/iot-hub-device-streams-overview/device-stream-portal.png "pontos finais de transmissão do dispositivo")

Em alternativa, as informações de pontos finais pode utilizar ser obtido com a CLI do Azure na secção de propriedades do hub, especificamente, `property.hostname` e `property.deviceStreams` chaves.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

O resultado é um objeto JSON de todos os pontos finais que poderão necessitar de dispositivo e o serviço do seu hub para ligar a fim de estabelecer um fluxo de dispositivo.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Certifique-se de que instalou a CLI do Azure versão 2.0.57 ou mais recente. Pode transferir a versão mais recente [aqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
> 

## <a name="whitelist-device-streaming-endpoints"></a>Pontos finais de transmissão em fluxo do dispositivo da lista de permissões

Conforme mencionado [anteriores](#Overview), o dispositivo cria uma ligação de saída para o ponto final de transmissão em fluxo do IoT Hub durante transmissões de dispositivo o processo de inicialização. As firewalls na sua rede ou o dispositivo tem de permitir conectividade de saída para o gateway de transmissão em fluxo através da porta 443 (Observe que a comunicação ocorre numa conexão WebSocket, que é encriptada utilizando TLS).

O nome de anfitrião do ponto final de transmissão em fluxo do dispositivo pode ser encontrado no portal do IoT Hub do Azure, no separador descrição geral. ![Texto alternativo](./media/iot-hub-device-streams-overview/device-stream-portal.PNG "pontos finais de transmissão do dispositivo")

Em alternativa, pode encontrar estas informações com a CLI do Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Certifique-se de que instalou a CLI do Azure versão 2.0.57 ou mais recente. Pode transferir a versão mais recente [aqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
> 

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Resolver problemas, através de fluxos de dispositivo, os registos de atividades

Pode configurar os registos do Azure Monitor para recolher o registo de atividades de fluxos de dispositivos no IoT Hub. Isso pode ser muito útil para cenários de resolução de problemas.

Siga os passos abaixo para configurar os registos do Azure Monitor para atividades de fluxo de dispositivo do IoT Hub:

1. Navegue para o *das definições de diagnóstico* separador do seu IoT Hub e clique em *ativar diagnósticos* ligação.

  ![Texto alternativo](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings.PNG "ativar disgnostics registos")


2. Indique um nome para as definições de diagnóstico e escolha *enviar para o Log Analytics* opção. Vai ser orientado ao escolher um recurso de área de trabalho do Log Analytics existente ou crie um novo. Além disso, verifique os *DeviceStreams* da lista.

    ![Texto alternativo](./media/iot-hub-device-streams-overview/device-streams-diagnostics.PNG "ativar dispositivo transmite em fluxo registos")

3. Agora pode aceder aos seus registos de fluxos de dispositivo sob o *registos* separador no portal do seu IoT Hub. Registos de atividades de fluxo do dispositivo irão aparecer na `AzureDiagnostics` de tabela e ter `Category=DeviceStreams`.

    <p>
Conforme mostrado abaixo a identidade de dispositivo de destino e o resultado da operação também está disponível nos registos.
    ![Texto alternativo](./media/iot-hub-device-streams-overview/device-streams-log-analytics.PNG "aceder aos registos de fluxo de dispositivo")


## <a name="regional-availability"></a>Disponibilidade Regional

Durante a pré-visualização pública, os fluxos de dispositivo do IoT Hub estão disponíveis nas regiões E.U.A. Central e E.u.a. Central EUAP. Certifique-se de que criar o seu hub de uma dessas regiões. 


## <a name="sdk-availability"></a>Disponibilidade SDK

Dois lados de cada fluxo (no lado do serviço e dispositivo) utilizam o SDK do Hub IoT para estabelecer o túnel. Durante a pré-visualização pública, os clientes podem escolher dos idiomas SDK seguintes:
- O C e C# SDK suporta fluxos de dispositivo do lado do dispositivo.

- O NodeJS e C# SDK suporta fluxos de dispositivo no lado do serviço.


## <a name="iot-hub-device-stream-samples"></a>Exemplos de Stream de dispositivo do IoT Hub

Publicamos dois [exemplos de início rápido](/azure/iot-hub) para demonstrar o uso de fluxos de dispositivo por aplicativos.
* O *eco* exemplo demonstra o uso programático dos fluxos de dispositivo (chamando a API de SDK diretamente).
* O *local proxy* exemplo demonstra o encapsulamento do tráfego de aplicativo de cliente/servidor prontas para utilização (por exemplo, o SSH, o RDP ou web) através de fluxos de dispositivo.

Estes exemplos são abordados mais detalhadamente abaixo.

### <a name="echo-sample"></a>Exemplo de eco
O exemplo de eco demonstra o uso programático dos fluxos de dispositivo para enviar e receber bytes entre aplicativos de serviço e dispositivo. Use os links abaixo para acessar os guias de início rápido. Tenha em atenção que pode utilizar programas de serviço e dispositivo em idiomas diferentes, por exemplo, o programa de dispositivo C pode trabalhar com C# programa de serviço.

| SDK    | Programa de serviço                                          | Programa de dispositivo                                           |
|--------|----------------------------------------------------------|----------------------------------------------------------|
| C#     | [Link](quickstart-device-streams-echo-csharp.md) | [Link](quickstart-device-streams-echo-csharp.md) |
| Node.js | [Link](quickstart-device-streams-echo-nodejs.md) | -                                                        |
| C      | -                                                        | [Link](quickstart-device-streams-echo-c.md)      |

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Exemplo de local Proxy (para SSH ou RDP)
O exemplo de local proxy demonstra uma forma de ativar o encapsulamento do tráfego de uma aplicação existente que envolve a comunicação entre um cliente e um programa do servidor. Este conjunto de cópia de segurança funciona para protocolos de cliente/servidor, como SSH e do protocolo RDP, onde o lado do serviço age como um cliente (em execução de programas do cliente de SSH ou RDP) e o lado do dispositivo age como o servidor (com o daemon de SSH ou de programas do servidor de RDP). 

Esta secção descreve a utilização de fluxos de dispositivo para permitir que o utilizador SSH para um dispositivo através de fluxos de dispositivo (o caso de RDP ou outra aplicação de cliente/servidor são semelhantes utilizando de porta correspondente o protocolo).

A configuração utiliza dois *local proxy* programas mostrados na figura abaixo, ou seja *proxy local do dispositivo* e *proxy de serviço local*. Os programas de local proxy são responsáveis por executar o [handshake de inicialização de fluxo de dispositivo](#device-stream-creation-flow) com o IoT Hub e interagir com o cliente SSH e o daemon de SSH usar soquetes de cliente/servidor normal.

![Texto alternativo](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png "configuração de proxy de fluxo de dispositivo para SSH/RDP")

1. O usuário executa o proxy de serviço local para iniciar um fluxo de dispositivo no dispositivo.

2. O proxy de dispositivo local aceita a solicitação de inicialização de fluxo e estabelecido o túnel para o ponto final de transmissão em fluxo do IoT Hub (conforme referido acima).

3. O proxy local do dispositivo liga-se para o ponto final daemon SSH à escuta na porta 22 no dispositivo.

4. O proxy de serviço local fica à escuta numa porta designada aguardar novas ligações de SSH do usuário (porta 2222 usada no exemplo, mas isso pode ser configurada para qualquer outra porta disponível). O utilizador aponta o cliente SSH para a porta de proxy de serviço local no localhost.

### <a name="notes"></a>Notas
- Os passos acima concluir um túnel de ponto a ponto entre o cliente SSH (no lado direito) para o daemon de SSH (no lado esquerdo). Parte desta conectividade de ponto-a-ponto envolve o envio de tráfego ao longo de um fluxo de dispositivo ao IoT Hub.

- As setas na figura indicam a direção em que as ligações são estabelecidas entre pontos de extremidade. Especificamente, tenha em atenção que não existe nenhum ligações de entrada vai para o dispositivo (isto é, muitas vezes, bloqueado por uma firewall).

- A opção de usar a porta 2222 no proxy de serviço local é uma opção arbitrária. O proxy pode ser configurado para utilizar qualquer outra porta disponível.

- A escolha dos porta 22 é dependente do procotocol e específico para encaminhar o SSH neste caso. Para o caso de RDP, tem de ser utilizada a porta 3389. Isso pode ser configurado nos programas de exemplo fornecido.

Use os links abaixo para obter instruções sobre como executar os programas de local proxy na linguagem de sua escolha. Semelhante a [exemplo de eco](#echo-sample), pode executar programas de proxy local do dispositivo e do serviço em idiomas diferentes, pois são totalmente interoperáveis.

| SDK    | Proxy de Serviço Local                                       | Proxy Local do dispositivo                                |
|--------|-----------------------------------------------------------|---------------------------------------------------|
| C#     | [Link](quickstart-device-streams-proxy-csharp.md)  | [Link](quickstart-device-streams-proxy-csharp.md) |
| NodeJS | [Link](quickstart-device-streams-proxy-nodejs.md)         | -                                                 |
| C      | -                                                         | [Link](quickstart-device-streams-proxy-c.md)      |

## <a name="next-steps"></a>Passos Seguintes

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Fluxos de dispositivos no IoT mostram (canal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
> [tente um guia de início rápido do fluxo de dispositivo](/azure/iot-hub)
