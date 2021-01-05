---
title: Fluxos de dispositivoS Azure IoT Hub / Microsoft Docs
description: Visão geral das correntes de dispositivos Azure IoT Hub, que facilitam túneis TCP bidirecionais seguros para uma variedade de cenários de comunicação nuvem-a-dispositivo.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825170"
---
# <a name="iot-hub-device-streams-preview"></a>Streams de dispositivos do hub IoT (pré-visualização)

As correntes de *dispositivos* Azure IoT Hub facilitam a criação de túneis TCP bidirecionais seguros para uma variedade de cenários de comunicação nuvem-a-dispositivo. Um fluxo de dispositivo é mediado por um *ponto final de streaming* IoT Hub que funciona como um proxy entre o seu dispositivo e os pontos finais de serviço. Esta configuração, representada no diagrama abaixo, é especialmente útil quando os dispositivos estão por trás de uma firewall de rede ou residem dentro de uma rede privada. Como tal, os fluxos de dispositivos IoT Hub ajudam a resolver a necessidade dos clientes de chegarem aos dispositivos IoT de forma amigável e sem a necessidade de abrir amplamente portas de firewall de entrada ou saída.

!["IoT Hub device streams overview"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Utilizando fluxos de dispositivos IoT Hub, os dispositivos permanecem seguros e só precisarão de abrir as ligações TCP de saída ao ponto final de streaming do hub IoT sobre a porta 443. Uma vez estabelecido um stream, as aplicações do lado do serviço e do lado do dispositivo terão acesso programático a um objeto cliente WebSocket para enviar e receber bytes crus uns aos outros. A fiabilidade e as garantias de encomenda fornecidas por este túnel estão a par da TCP.

## <a name="benefits"></a>Benefícios

Os fluxos de dispositivos IoT Hub proporcionam os seguintes benefícios:

* **Conectividade segura amiga da firewall:** Os dispositivos IoT podem ser alcançados a partir de pontos finais de serviço sem abertura da porta de firewall de entrada no dispositivo ou perímetros de rede (só é necessária conectividade de saída para o IoT Hub durante a porta 443).

* **Autenticação:** Tanto os lados do dispositivo como os lados de serviço do túnel precisam de autenticar com o IoT Hub utilizando as suas credenciais correspondentes.

* **Encriptação:** Por predefinição, os fluxos de dispositivos IoT Hub utilizam ligações ativadas pelo TLS. Isto garante que o tráfego é sempre encriptado independentemente de a aplicação usar ou não encriptação.

* **Simplicidade da conectividade:** Em muitos casos, a utilização de streams de dispositivos elimina a necessidade de uma configuração complexa de Redes Privadas Virtuais para permitir a conectividade com dispositivos IoT.

* **Compatibilidade com pilha TCP/IP:** Os fluxos de dispositivos IoT Hub podem acomodar o tráfego de aplicações TCP/IP. Isto significa que uma ampla gama de protocolos baseados em padrões, bem como protocolos baseados em padrões, podem alavancar esta funcionalidade.

* **Facilidade de utilização em configurações de rede privada:** O serviço pode comunicar com um dispositivo referindo o seu ID do dispositivo, em vez do endereço IP do dispositivo. Isto é útil em situações em que um dispositivo está localizado dentro de uma rede privada e tem um endereço IP privado, ou o seu endereço IP é atribuído dinamicamente e é desconhecido para o lado de serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho de fluxo de dispositivo

Um fluxo de dispositivo é iniciado quando o serviço solicita a ligação a um dispositivo fornecendo o seu ID do dispositivo. Este fluxo de trabalho enquadra-se particularmente num modelo de comunicação cliente/servidor, incluindo SSH e RDP, onde um utilizador pretende ligar-se remotamente ao servidor SSH ou RDP em execução no dispositivo utilizando um programa de cliente SSH ou RDP.

O processo de criação de streams de dispositivo envolve uma negociação entre o dispositivo, o serviço, os principais pontos finais do hub IoT e streaming. Enquanto o principal ponto final do hub IoT orquestra a criação de um fluxo de dispositivo, o ponto final de streaming lida com o tráfego que flui entre o serviço e o dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação de fluxo de dispositivo

A criação programática de um fluxo de dispositivos utilizando o SDK envolve os seguintes passos, que também são retratados na figura abaixo:

!["Processo de aperto de mão de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. A aplicação do dispositivo regista uma chamada antecipadamente para ser notificada de quando um novo fluxo de dispositivo é iniciado no dispositivo. Este passo ocorre normalmente quando o dispositivo arranca e liga ao IoT Hub.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo o ID do dispositivo _(não_ o endereço IP).

3. O hub IoT notifica o programa do lado do dispositivo invocando a chamada registada no passo 1. O dispositivo pode aceitar ou rejeitar o pedido de iniciação do fluxo. Esta lógica pode ser específica para o seu cenário de aplicação. Se o pedido de transmissão for rejeitado pelo dispositivo, o IoT Hub informa o serviço em conformidade; caso contrário, seguem-se os passos abaixo.

4. O dispositivo cria uma ligação TCP de saída segura ao ponto final de streaming sobre a porta 443 e atualiza a ligação a um WebSocket. O URL do ponto final de streaming, bem como as credenciais a utilizar para autenticar são fornecidos ao dispositivo pelo IoT Hub como parte do pedido enviado no passo 3.

5. O serviço é notificado do resultado do dispositivo que aceita o fluxo e procede à criação do seu próprio cliente WebSocket para o ponto final de streaming. Da mesma forma, recebe o URL de ponta de streaming e informações de autenticação do IoT Hub.

No processo de aperto de mão acima:

* O processo de aperto de mão deve estar concluído dentro de 60 segundos (passo 2 a 5), caso contrário o aperto de mão falharia com um intervalo de tempo e o serviço será notificado em conformidade.

* Após o fluxo de criação de fluxo acima concluído, o ponto final de streaming funcionará como um proxy e transferirá o tráfego entre o serviço e o dispositivo através dos respetivos WebSockets.

* Tanto o dispositivo como o serviço necessitam de conectividade de saída para o principal ponto final do IoT Hub, bem como o ponto final de streaming sobre a porta 443. O URL destes pontos finais está disponível no *separador Visão Geral* no portal do IoT Hub.

* A fiabilidade e a garantia de encomenda de um fluxo estabelecido estão a par com a TCP.

* Todas as ligações ao IoT Hub e ao ponto final de streaming utilizam o TLS e estão encriptadas.

### <a name="termination-flow"></a>Fluxo de terminação

Um fluxo estabelecido termina quando qualquer uma das ligações TCP ao gateway são desligadas (pelo serviço ou dispositivo). Isto pode ocorrer voluntariamente fechando o WebSocket no dispositivo ou nos programas de serviço, ou involuntariamente em caso de intervalo de tempo de conectividade de rede ou falha de processo. Após o encerramento de qualquer dispositivo ou ligação do serviço ao ponto final de streaming, a outra ligação TCP também será (forçadamente) encerrada e o serviço e o dispositivo são responsáveis por recriar o fluxo, se necessário.

## <a name="connectivity-requirements"></a>Requisitos de conectividade

Tanto o dispositivo como os lados de serviço de um fluxo de dispositivo devem ser capazes de estabelecer ligações ativadas pelo TLS ao IoT Hub e ao seu ponto final de streaming. Isto requer conectividade de saída sobre a porta 443 para estes pontos finais. O nome de anfitrião associado a estes pontos finais pode ser encontrado no *separador visão geral* do IoT Hub, como mostra a figura abaixo:

!["Pontos finais de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternativamente, as informações sobre os pontos finais podem ser recuperadas usando o Azure CLI sob a secção de propriedades do hub, especificamente, `property.hostname` e `property.deviceStreams` chaves.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A saída é um objeto JSON de todos os pontos finais a que o dispositivo e o serviço do seu hub podem precisar de se ligar para estabelecer uma corrente de dispositivo.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Certifique-se de que instalou a versão 2.0.57 do Azure CLI ou mais recente. Pode descarregar a versão mais recente da página [Install Azure CLI.](/cli/azure/install-azure-cli)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Permitir a conectividade de saída para os pontos finais de streaming do dispositivo

Como mencionado no início deste artigo, o seu dispositivo cria uma ligação de saída ao ponto final de streaming IoT Hub durante o processo de iniciação do streams do dispositivo. As suas firewalls no dispositivo ou na sua rede devem permitir a conectividade de saída ao gateway de streaming sobre a porta 443 (note que a comunicação ocorre através de uma ligação WebSocket que é encriptada usando TLS).

O nome de anfitrião do ponto final de streaming do dispositivo pode ser encontrado no portal Azure IoT Hub sob o separador Visão Geral ![ . Pontos finais de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Em alternativa, pode encontrar esta informação utilizando o Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Certifique-se de que instalou a versão 2.0.57 do Azure CLI ou mais recente. Pode descarregar a versão mais recente da página [Install Azure CLI.](/cli/azure/install-azure-cli)
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Resolução de problemas através de registos de recursos de streams de dispositivos

Pode configurar o Azure Monitor para recolher os [registos de recursos para os fluxos de dispositivos emitidos](monitor-iot-hub-reference.md#device-streams-preview) pelo seu Hub IoT. Isto pode ser muito útil em cenários de resolução de problemas.

Siga os passos abaixo para criar uma definição de diagnóstico para enviar registos de streams de dispositivos para o seu Hub IoT para Registos monitores Azure:

1. No portal Azure, navegue para o seu hub IoT. No painel esquerdo, em **Monitorização,** selecione **Definições de Diagnóstico**. Em seguida, **selecione Adicionar a definição de diagnóstico**.

2. Forneça um nome para a definição de diagnóstico e selecione **DeviceStreams** da lista de registos. Em seguida, selecione **Enviar para registar analíticos**. Você será guiado para escolher um espaço de trabalho log analytics existente ou criar um novo.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Ativar os registos de fluxos de dispositivos":::

3. Depois de criar uma definição de diagnóstico para enviar os registos de streams do seu dispositivo para um espaço de trabalho do Log Analytics, pode aceder aos registos selecionando **Registos** sob **monitorização** no painel esquerdo do seu hub IoT no portal Azure. Os registos de fluxos de dispositivos aparecerão na `AzureDiagnostics` tabela e terão `Category=DeviceStreams` . Tenha em atenção que pode demorar alguns minutos após uma operação para que os registos apareçam na tabela.

   Como mostrado abaixo, a identidade do dispositivo alvo e o resultado da operação também estão disponíveis nos registos.

   !["Aceder a registos de fluxo de dispositivos"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Para saber mais sobre a utilização do Azure Monitor com o IoT Hub, consulte [o Monitor IoT Hub](monitor-iot-hub.md). Para obter informações sobre todos os registos de recursos, métricas e tabelas disponíveis para o IoT Hub, consulte [a referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Disponibilidade regional

Durante a pré-visualização pública, os fluxos de dispositivos IoT Hub estão disponíveis nas regiões central dos EUA, EUA EUA, Norte da Europa e Sudeste Asiático. Por favor, certifique-se de criar o seu centro numa destas regiões.

## <a name="sdk-availability"></a>Disponibilidade de SDK

Dois lados de cada corrente (no lado do dispositivo e do serviço) utilizam o Hub IoT SDK para estabelecer o túnel. Durante a pré-visualização pública, os clientes podem escolher entre os seguintes idiomas SDK:

* O dispositivo de suporte C e C# SDK transmite no lado do dispositivo.

* O dispositivo de suporte NodeJS e C# SDK transmite no lado de serviço.

## <a name="iot-hub-device-stream-samples"></a>Amostras de fluxo de dispositivo ioT Hub

Existem [duas amostras de arranque rápido](./index.yml) disponíveis na página IoT Hub. Estes demonstram a utilização de fluxos de dispositivos por aplicações.

* A amostra *de eco* demonstra o uso programático de fluxos de dispositivos (chamando diretamente o SDK API).

* A amostra *de procuração local* demonstra o túnel do tráfego de aplicações de cliente/servidor off-the-shelf (como SSH, RDP ou web) através de fluxos de dispositivos.

Estas amostras são cobertas com maior detalhe abaixo.

### <a name="echo-sample"></a>Amostra de eco

A amostra de eco demonstra o uso programático de fluxos de dispositivos para enviar e receber bytes entre aplicações de serviço e dispositivo. Note que pode utilizar programas de serviço e dispositivo em diferentes idiomas. Por exemplo, pode utilizar o programa de dispositivo C com o programa de serviço C#.

Aqui estão as amostras de eco:

* [C# serviço e programa de serviço](quickstart-device-streams-echo-csharp.md)

* [Node.js programa de serviço](quickstart-device-streams-echo-nodejs.md)

* [Programa de dispositivos C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Amostra de procuração local (para SSH ou RDP)

A amostra de procuração local demonstra uma forma de permitir a escavação do tráfego de uma aplicação existente que envolve a comunicação entre um cliente e um programa de servidor. Esta configuração funciona para protocolos cliente/servidor como SSH e RDP, onde o lado do serviço funciona como cliente (executando programas de clientes SSH ou RDP), e o lado do dispositivo funciona como o servidor (executando programas de servidor SSH ou servidor RDP).

Esta secção descreve a utilização de fluxos de dispositivos para permitir ao utilizador sSH a um dispositivo sobre fluxos de dispositivos (a caixa para RDP ou outra aplicação cliente/servidor são semelhantes utilizando a porta correspondente do protocolo).

A configuração aproveita dois programas *de procuração locais* mostrados na figura abaixo, nomeadamente *procuração local de dispositivo* e *procuração local* de serviço . Os programas de procuração locais são responsáveis pela realização do aperto de mão de [iniciação](#device-stream-creation-flow) do stream do dispositivo com o IoT Hub, e interagir com o cliente SSH e o daemon SSH usando tomadas regulares de cliente/servidor.

!["Configuração de procuração de fluxo de dispositivo para SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. O utilizador executa o serviço de procuração local para iniciar um fluxo de dispositivo para o dispositivo.

2. O representante local do dispositivo aceita o pedido de iniciação do fluxo e o túnel é estabelecido para o ponto final de streaming do IoT Hub (como discutido acima).

3. O dispositivo-procuração local liga-se ao ponto final do daemon SSH, ouvindo na porta 22 do dispositivo.

4. O representante local de serviço ouve numa porta designada à espera de novas ligações SSH do utilizador (porta 2222 utilizada na amostra, mas esta pode ser configurada para qualquer outra porta disponível). O utilizador aponta o cliente SSH para a porta de procuração local de serviço na localidade.

### <a name="notes"></a>Notas

* Os passos acima completam um túnel de ponta a ponta entre o cliente SSH (à direita) até ao daemon SSH (à esquerda). Parte desta conectividade de ponta a ponta envolve o envio de tráfego através de um fluxo de dispositivo para o IoT Hub.

* As setas na figura indicam a direção em que as ligações são estabelecidas entre os pontos finais. Em concreto, note que não existem ligações de entrada que vão para o dispositivo (isto é frequentemente bloqueado por uma firewall).

* A escolha de usar a porta 2222 no proxy local de serviço é uma escolha arbitrária. O proxy pode ser configurado para utilizar qualquer outra porta disponível.

* A escolha da porta 22 é dependente do protocolo e específica para a SSH neste caso. Para o caso do PDR, deve ser utilizado o porto 3389. Isto pode ser configurado nos programas de amostragem fornecidos.

Utilize os links abaixo para obter instruções sobre como executar os programas de procuração locais no seu idioma de eleição. Semelhante à amostra de [eco,](#echo-sample)pode executar programas de procuração local e dispositivo em diferentes línguas, uma vez que são totalmente interoperáveis.

* [C# serviço e programa de serviço](quickstart-device-streams-proxy-csharp.md)

* [Node.js programa de serviço](quickstart-device-streams-proxy-nodejs.md)

* [Programa de dispositivos C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Passos seguintes

Use os links abaixo para saber mais sobre os fluxos de dispositivos.

> [!div class="nextstepaction"]
> [Streams de dispositivos no programa IoT (Canal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
