---
title: Fluxos de dispositivos Azure IoT Hub / Microsoft Docs
description: Visão geral dos fluxos de dispositivos Azure IoT Hub, que facilitam a segurança dos túneis de TCP bidirecionais para uma variedade de cenários de comunicação nuvem-dispositivo.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890455"
---
# <a name="iot-hub-device-streams-preview"></a>Fluxos de dispositivos Do Hub IoT (pré-visualização)

Os fluxos de *dispositivos* Azure IoT Hub facilitam a criação de túneis TCP bidirecionais seguros para uma variedade de cenários de comunicação cloud-to-device. Um fluxo de dispositivo é mediado por um *ponto final* de streaming IoT Hub que funciona como um proxy entre o seu dispositivo e pontos finais de serviço. Esta configuração, retratada no diagrama abaixo, é especialmente útil quando os dispositivos estão por trás de uma firewall de rede ou residem dentro de uma rede privada. Como tal, os streams de dispositivos IoT Hub ajudam a resolver a necessidade dos clientes de alcançar em dispositivos IoT de forma amigável e sem a necessidade de abrir amplamente as portas de firewall de rede recebidas ou saídas.

!["IoT Hub device streams overview"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Utilizando fluxos de dispositivos IoT Hub, os dispositivos permanecem seguros e só terão de abrir ligações TCP de saída ao ponto final de streaming do hub IoT sobre a porta 443. Uma vez estabelecido um fluxo, as aplicações do lado do serviço e do lado do dispositivo terão acesso programático a um objeto de cliente WebSocket para enviar e receber bytes crus uns aos outros. As garantias de fiabilidade e ordenamento fornecidas por este túnel estão a par da TCP.

## <a name="benefits"></a>Vantagens

Os fluxos de dispositivos IoT Hub proporcionam os seguintes benefícios:

* **Conectividade segura amigável:** Os dispositivos IoT podem ser alcançados a partir de pontos finais de serviço sem abertura da porta de firewall de entrada no dispositivo ou perímetros de rede (apenas é necessária conectividade de saída para o Hub IoT sobre a porta 443).

* **Autenticação:** Tanto os lados do dispositivo como os lados de serviço do túnel precisam de autenticar com o Hub IoT utilizando as respetivas credenciais.

* **Encriptação:** Por defeito, os fluxos de dispositivos IoT Hub utilizam ligações ativadas por TLS. Isto garante que o tráfego está sempre encriptado independentemente de a aplicação utilizar encriptação ou não.

* **Simplicidade da conectividade:** Em muitos casos, o uso de fluxos de dispositivos elimina a necessidade de uma configuração complexa de Redes Privadas Virtuais para permitir a conectividade com dispositivos IoT.

* **Compatibilidade com pilha TCP/IP:** Os fluxos de dispositivos IoT Hub podem acomodar o tráfego de aplicações TCP/IP. Isto significa que uma vasta gama de protocolos proprietários e baseados em padrões podem alavancar esta funcionalidade.

* **Facilidade de utilização em configurações de rede privadas:** O serviço pode comunicar com um dispositivo referenciando o seu ID do dispositivo, em vez do endereço IP do dispositivo. Isto é útil em situações em que um dispositivo está localizado dentro de uma rede privada e tem um endereço IP privado, ou o seu endereço IP é atribuído dinamicamente e é desconhecido para o lado do serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho de fluxo de dispositivos

É iniciado um fluxo de dispositivos quando o serviço solicita a ligação a um dispositivo, fornecendo o seu ID do dispositivo. Este fluxo de trabalho encaixa-se particularmente num modelo de comunicação cliente/servidor, incluindo SSH e RDP, onde um utilizador pretende ligar-se remotamente ao servidor SSH ou RDP que funciona no dispositivo utilizando um programa de cliente SSH ou RDP.

O processo de criação de fluxo de dispositivos envolve uma negociação entre o dispositivo, o serviço, os principais pontos finais do hub IoT e os pontos finais de streaming. Enquanto o principal ponto final do ioT orquestra a criação de um fluxo de dispositivos, o ponto final de streaming lida com o tráfego que flui entre o serviço e o dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação de fluxo de dispositivo

A criação programática de um fluxo de dispositivos utilizando o SDK envolve os seguintes passos, que também são representados na figura abaixo:

!["Processo de aperto de mão de fluxo de dispositivos"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. A aplicação do dispositivo regista uma chamada antecipada para ser notificada de quando um novo fluxo de dispositivos é iniciado no dispositivo. Este passo ocorre normalmente quando o dispositivo arranca e se conecta ao IoT Hub.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo o ID do dispositivo _(não_ o endereço IP).

3. O hub IoT notifica o programa do lado do dispositivo invocando o recuo registado no passo 1. O dispositivo pode aceitar ou rejeitar o pedido de iniciação do fluxo. Esta lógica pode ser específica para o seu cenário de candidatura. Se o pedido de streaming for rejeitado pelo dispositivo, o IoT Hub informa o serviço em conformidade; caso contrário, os passos abaixo seguem.

4. O dispositivo cria uma ligação TCP de saída segura ao ponto final de streaming sobre a porta 443 e atualiza a ligação a um WebSocket. O URL do ponto final de streaming, bem como as credenciais a utilizar para autenticar, são fornecidos ao dispositivo pelo IoT Hub como parte do pedido enviado no passo 3.

5. O serviço é notificado do resultado do dispositivo que aceita o fluxo e procede à criação do seu próprio cliente WebSocket para o ponto final de streaming. Da mesma forma, recebe o URL final de streaming e informações de autenticação do IoT Hub.

No processo de aperto de mão acima:

* O processo de aperto de mão deve ser concluído dentro de 60 segundos (passo 2 a 5), caso contrário o aperto de mão falharia com um tempo de paragem e o serviço será notificado em conformidade.

* Após o fluxo de criação de fluxo acima completo, o ponto final de streaming funcionará como um proxy e transferirá o tráfego entre o serviço e o dispositivo através das respetivas WebSockets.

* O dispositivo e o serviço precisam de conectividade de saída para o principal ponto final do IoT Hub, bem como o ponto final de streaming sobre a porta 443. O URL destes pontos finais está disponível no separador *Overview* no portal do IoT Hub.

* A fiabilidade e garantiade ordenação de um fluxo estabelecido está a par da TCP.

* Todas as ligações ao IoT Hub e ao ponto final de streaming utilizam o TLS e são encriptadas.

### <a name="termination-flow"></a>Fluxo de rescisão

Um fluxo estabelecido termina quando qualquer uma das ligações TCP ao gateway são desligadas (pelo serviço ou dispositivo). Isto pode ocorrer voluntariamente fechando o WebSocket no dispositivo ou programas de serviço, ou involuntariamente em caso de tempo limite de conectividade de rede ou falha de processo. Após a interrupção da ligação de qualquer dispositivo ou serviço ao ponto final de streaming, a outra ligação TCP também será (forçadamente) terminada e o serviço e o dispositivo são responsáveis por recriar o fluxo, se necessário.

## <a name="connectivity-requirements"></a>Requisitos de conectividade

Tanto o dispositivo como os lados de serviço de um fluxo de dispositivo devem ser capazes de estabelecer ligações ativadas por TLS ao IoT Hub e ao seu ponto final de streaming. Isto requer conectividade de saída sobre a porta 443 para estes pontos finais. O nome de anfitrião associado a estes pontos finais pode ser encontrado no separador *overview* do IoT Hub, como indicado na figura abaixo:

!["Pontos finais de fluxo de dispositivos"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Em alternativa, as informações sobre pontos finais podem ser recuperadas utilizando `property.hostname` `property.deviceStreams` o Azure CLI sob a secção de propriedades do hub, especificamente, e chaves.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A saída é um objeto JSON de todos os pontos finais a que o dispositivo e o serviço do seu hub podem ter de se ligar para estabelecer um fluxo de dispositivos.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Certifique-se de que instalou a versão 2.0.57 do Azure CLI ou mais recente. Pode descarregar a versão mais recente a partir da página [Install Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Permitir a conectividade de saída aos pontos finais de streaming do dispositivo

Como mencionado no início deste artigo, o seu dispositivo cria uma ligação de saída ao ponto final de streaming IoT Hub durante o processo de iniciação de fluxos de dispositivos. As suas firewalls no dispositivo ou na sua rede devem permitir a conectividade de saída ao portal de streaming através da porta 443 (note que a comunicação ocorre através de uma ligação WebSocket que é encriptada usando TLS).

O nome de anfitrião do ponto final de streaming do dispositivo pode ser ![encontrado no portal Azure IoT Hub sob o separador Overview.](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Em alternativa, pode encontrar esta informação utilizando o Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Certifique-se de que instalou a versão 2.0.57 do Azure CLI ou mais recente. Pode descarregar a versão mais recente a partir da página [Install Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Resolução de problemas através de registos de atividade de fluxos de dispositivos

Pode configurar registos do Monitor Azure para recolher o registo de atividade dos fluxos de dispositivos no seu Hub IoT. Isto pode ser muito útil em cenários de resolução de problemas.

Siga os passos abaixo para configurar os registos do Monitor Azure para as atividades de fluxo de dispositivos do IoT Hub:

1. Navegue para o separador de *definições* de diagnóstico no seu Hub IoT e clique no link *De diagnóstico Turn on.*

   !["Habilitar os registos de diagnóstico"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Forneça um nome para as definições de diagnóstico e escolha enviar para registar a opção *Analytics.* Será orientado a escolher um recurso existente no espaço de trabalho Log Analytics ou a criar um novo. Além disso, verifique os *DeviceStreams* da lista.

    !["Ativar os registos de fluxos de dispositivos"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Agora pode aceder aos registos dos streams do seu dispositivo sob o separador *Logs* no portal do seu IoT Hub. Os registos de atividade `AzureDiagnostics` do fluxo `Category=DeviceStreams`do dispositivo aparecerão na tabela e terão .

   Como mostrado abaixo, a identidade do dispositivo-alvo e o resultado da operação também estão disponíveis nos registos.

   !["Registos de fluxo de dispositivos de acesso"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Disponibilidade Regional

Durante a pré-visualização pública, os fluxos de dispositivos IoT Hub estão disponíveis nas regiões central dos EUA, EUA Central, Norte da Europa e Sudeste Asiático. Por favor, certifique-se de criar o seu centro numa destas regiões.

## <a name="sdk-availability"></a>Disponibilidade SDK

Dois lados de cada corrente (no dispositivo e lado de serviço) utilizam o IoT Hub SDK para estabelecer o túnel. Durante a pré-visualização pública, os clientes podem escolher entre os seguintes idiomas SDK:

* O dispositivo de suporte c e C# SDK flui no lado do dispositivo.

* Os fluxos de dispositivos de suporte NodeJS e C# SDK no lado do serviço.

## <a name="iot-hub-device-stream-samples"></a>Amostras de fluxo de dispositivo sintetante do iot hub

Existem duas [amostras de arranque rápido](/azure/iot-hub) disponíveis na página Do Hub IoT. Estes demonstram a utilização de fluxos de dispositivos por aplicações.

* A amostra *de eco* demonstra a utilização programática de fluxos de dispositivos (chamando diretamente o SDK API).

* A amostra *de procuração local* demonstra o túnel do tráfego de aplicações cliente/servidor off-the-shelf (como SSH, RDP ou web) através de fluxos de dispositivos.

Estas amostras são cobertas com maior detalhe abaixo.

### <a name="echo-sample"></a>Amostra de eco

A amostra de eco demonstra a utilização programática de fluxos de dispositivos para enviar e receber bytes entre aplicações de serviço e dispositivo. Tenha em anote que pode utilizar programas de serviço e dispositivos em diferentes idiomas. Por exemplo, pode utilizar o programa de dispositivoc com o programa de serviço C#.

Aqui estão as amostras de eco:

* [C# programa de serviço e serviço](quickstart-device-streams-echo-csharp.md)

* [Programa de serviço node.js](quickstart-device-streams-echo-nodejs.md)

* [Programa de dispositivos C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Amostra local de procuração (para SSH ou RDP)

A amostra de procuração local demonstra uma forma de permitir a escavação do tráfego de uma aplicação existente que envolve a comunicação entre um cliente e um programa de servidor. Esta configuração funciona para protocolos de cliente/servidor como SSH e RDP, onde o lado do serviço funciona como um cliente (executando programas de clientes SSH ou RDP), e o lado do dispositivo funciona como o servidor (executando programas de servidores SSH ou RDP).

Esta secção descreve a utilização de fluxos de dispositivos para permitir que o utilizador do SSH seja utilizado num dispositivo sobre os fluxos do dispositivo (o caso de RDP ou outra aplicação cliente/servidor são semelhantes utilizando a porta correspondente do protocolo).

A configuração aproveita dois programas *locais* de procuração mostrados na figura abaixo, nomeadamente *procuração local de dispositivos* e *procuração local de serviço*. Os programas de procuração local são responsáveis pela realização do aperto de mão de iniciação de fluxo de [dispositivos](#device-stream-creation-flow) com o IoT Hub, e interagindo com o cliente SSH e da ememon SSH utilizando tomadas regulares de cliente/servidor.

!["Configuração de procuração de fluxo de dispositivos para SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. O utilizador executa procuração local de serviço para iniciar um fluxo de dispositivo para o dispositivo.

2. O representante local do dispositivo aceita o pedido de iniciação do fluxo e o túnel é estabelecido para o ponto final de streaming do IoT Hub (como discutido acima).

3. O proxy local do dispositivo liga-se ao ponto final do daemon SSH ouvindo na porta 22 do dispositivo.

4. O representante local de serviço ouve numa porta designada à espera de novas ligações SSH do utilizador (porta 2222 utilizada na amostra, mas esta pode ser configurada para qualquer outra porta disponível). O utilizador aponta o cliente SSH para o porto de procuração local de serviço no local de acolhimento.

### <a name="notes"></a>Notas

* Os passos acima completam um túnel de ponta a ponta entre o cliente SSH (à direita) para o daemon SSH (à esquerda). Parte desta conectividade de ponta a ponta envolve o envio de tráfego através de um fluxo de dispositivos para o IoT Hub.

* As setas do valor indicam a direção em que as ligações são estabelecidas entre pontos finais. Especificamente, note que não há ligações de entrada para o dispositivo (isto é frequentemente bloqueado por uma firewall).

* A escolha da utilização do porto 2222 no proxy local de serviço é uma escolha arbitrária. O representante pode ser configurado para utilizar qualquer outra porta disponível.

* Neste caso, a escolha da porta 22 é dependente de protocolos e específica para o SSH. Para o caso do PDR, deve utilizar-se a porta 3389. Isto pode ser configurado nos programas de amostra fornecidos.

Utilize os links abaixo para obter instruções sobre como executar os programas de procuração local na sua linguagem de eleição. Semelhante à amostra de [eco,](#echo-sample)pode executar programas de procuração local de dispositivos e serviços em diferentes idiomas, uma vez que são totalmente interoperáveis.

* [C# programa de serviço e serviço](quickstart-device-streams-proxy-csharp.md)

* [Programa de serviço node.js](quickstart-device-streams-proxy-nodejs.md)

* [Programa de dispositivos C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Passos seguintes

Utilize os links abaixo para saber mais sobre os fluxos de dispositivos.

> [!div class="nextstepaction"]
> [Fluxos de dispositivos no programa IoT (Canal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
