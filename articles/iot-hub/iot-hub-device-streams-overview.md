---
title: Fluxos de dispositivo do Hub IoT do Azure (visualização) | Microsoft Docs
description: Visão geral dos fluxos de dispositivo do Hub IoT
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: c71ca96ee657cd4d4d0d57e05dc31c03112dc848
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900731"
---
# <a name="iot-hub-device-streams-preview"></a>Fluxos de dispositivo do Hub IoT (visualização)

Os fluxos de *dispositivo* do Hub IOT do Azure facilitam a criação de TÚNEIs TCP bidirecionais seguros para uma variedade de cenários de comunicação da nuvem para o dispositivo. Um fluxo de dispositivo é mediado por um *ponto de extremidade de streaming* do Hub IOT que atua como um proxy entre seus pontos de extremidade de dispositivo e de serviço. Essa configuração, descrita no diagrama a seguir, é especialmente útil quando os dispositivos estão atrás de um firewall de rede ou residem dentro de uma rede privada. Dessa forma, os fluxos de dispositivo do Hub IoT ajudam a atender às necessidades dos clientes de acessar dispositivos IoT de maneira amigável ao firewall e sem a necessidade de abrir amplamente as portas de firewall de rede de entrada ou saída.

!["Visão geral dos fluxos de dispositivo do Hub IoT"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Usando fluxos de dispositivo do Hub IoT, os dispositivos permanecem seguros e só precisarão abrir conexões TCP de saída para o ponto de extremidade de streaming do Hub IoT pela porta 443. Depois que um fluxo é estabelecido, os aplicativos do lado do serviço e do lado do dispositivo terão acesso programático a um objeto de cliente WebSocket para enviar e receber bytes brutos uns dos outros. As garantias de confiabilidade e ordenação fornecidas por esse túnel estão em par com o TCP.

## <a name="benefits"></a>Vantagens

Os fluxos de dispositivo do Hub IoT oferecem os seguintes benefícios:

* **Conectividade segura compatível com firewall:** Os dispositivos IoT podem ser acessados de pontos de extremidade de serviço sem abrir a porta de firewall de entrada no dispositivo ou perímetros de rede (somente a conectividade de saída para o Hub IoT é necessária na porta 443).

* **Autenticação:** Os lados de dispositivo e serviço do túnel precisam se autenticar no Hub IoT usando suas credenciais correspondentes.

* **Criptografia:** Por padrão, os fluxos de dispositivo do Hub IoT usam conexões habilitadas para TLS. Isso garante que o tráfego sempre seja criptografado, independentemente de o aplicativo usar ou não a criptografia.

* **Simplicidade de conectividade:** Em muitos casos, o uso de fluxos de dispositivo elimina a necessidade de configuração complexa de redes virtuais privadas para habilitar a conectividade com dispositivos IoT.

* **Compatibilidade com a pilha TCP/IP:** Os fluxos de dispositivo do Hub IoT podem acomodar o tráfego do aplicativo TCP/IP. Isso significa que uma ampla variedade de protocolos proprietários e baseados em padrões pode aproveitar esse recurso.

* **Facilidade de uso em configurações de rede privada:** O serviço pode se comunicar com um dispositivo referenciando sua ID do dispositivo, em vez do endereço IP do dispositivo. Isso é útil em situações em que um dispositivo está localizado dentro de uma rede privada e tem um endereço IP privado, ou seu endereço IP é atribuído dinamicamente e é desconhecido para o lado do serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho de fluxo de dispositivo

Um fluxo de dispositivo é iniciado quando o serviço solicita a conexão com um dispositivo fornecendo sua ID de dispositivo. Esse fluxo de trabalho particularmente se encaixa em um modelo de comunicação de cliente/servidor, incluindo SSH e RDP, em que um usuário pretende se conectar remotamente ao servidor SSH ou RDP em execução no dispositivo usando um programa cliente SSH ou RDP.

O processo de criação de fluxo de dispositivo envolve uma negociação entre o dispositivo, o serviço, os pontos de extremidade principal e de streaming do Hub IoT. Enquanto o ponto de extremidade principal do Hub IoT orquestra a criação de um fluxo de dispositivo, o ponto de extremidade de streaming manipula o tráfego que flui entre o serviço e o dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação de fluxo de dispositivo

A criação programática de um fluxo de dispositivo usando o SDK envolve as seguintes etapas, que também são descritas na figura abaixo:

!["Processo de handshake de fluxo do dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. O aplicativo do dispositivo registra um retorno de chamada com antecedência para ser notificado sobre quando um novo fluxo de dispositivo é iniciado para o dispositivo. Normalmente, essa etapa ocorre quando o dispositivo é inicializado e se conecta ao Hub IoT.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo a ID do dispositivo (_não_ o endereço IP).

3. O Hub IoT notifica o programa do lado do dispositivo invocando o retorno de chamada registrado na etapa 1. O dispositivo pode aceitar ou rejeitar a solicitação de início de fluxo. Essa lógica pode ser específica para seu cenário de aplicativo. Se a solicitação de fluxo for rejeitada pelo dispositivo, o Hub IoT informará o serviço adequadamente; caso contrário, as etapas a seguir são seguidas.

4. O dispositivo cria uma conexão TCP de saída segura para o ponto de extremidade de streaming pela porta 443 e atualiza a conexão com um WebSocket. A URL do ponto de extremidade de streaming, bem como as credenciais a serem usadas para autenticação, são fornecidas ao dispositivo pelo Hub IoT como parte da solicitação enviada na etapa 3.

5. O serviço é notificado do resultado do dispositivo que está aceitando o fluxo e prossegue para criar seu próprio cliente WebSocket para o ponto de extremidade de streaming. Da mesma forma, ele recebe a URL do ponto de extremidade de streaming e as informações de autenticação do Hub IoT.

No processo de handshake acima:

* O processo de handshake deve ser concluído em 60 segundos (etapa 2 a 5), caso contrário, o handshake falhará com um tempo limite e o serviço será notificado de acordo.

* Após a conclusão do fluxo de criação de fluxo acima, o ponto de extremidade de streaming atuará como um proxy e transferirá o tráfego entre o serviço e o dispositivo em seus respectivos WebSockets.

* O dispositivo e o serviço precisam de conectividade de saída para o ponto de extremidade principal do Hub IoT, bem como o ponto de extremidade de streaming pela porta 443. A URL desses pontos de extremidade está disponível na guia *visão geral* no portal do Hub IOT.

* As garantias de confiabilidade e ordenação de um fluxo estabelecido estão em par com o TCP.

* Todas as conexões com o Hub IoT e o ponto de extremidade de streaming usam TLS e são criptografadas.

### <a name="termination-flow"></a>Fluxo de terminação

Um fluxo estabelecido termina quando uma das conexões TCP com o gateway é desconectada (pelo serviço ou dispositivo). Isso pode ocorrer voluntariamente fechando o WebSocket nos programas de dispositivo ou serviço ou, involuntariamente, em caso de um tempo limite de conectividade de rede ou de um processo de falha. Após a rescisão da conexão do dispositivo ou do serviço ao ponto de extremidade de streaming, a outra conexão TCP também será (de modo forçado) encerrada e o serviço e o dispositivo serão responsáveis por recriar o fluxo, se necessário.

## <a name="connectivity-requirements"></a>Requisitos de conectividade

Os lados do dispositivo e do serviço de um fluxo de dispositivo devem ser capazes de estabelecer conexões habilitadas para TLS com o Hub IoT e seu ponto de extremidade de streaming. Isso requer conectividade de saída pela porta 443 para esses pontos de extremidade. O nome do host associado a esses pontos de extremidade pode ser encontrado na guia *visão geral* do Hub IOT, conforme mostrado na figura abaixo:

!["Pontos de extremidade de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, as informações dos pontos de extremidade podem ser recuperadas usando CLI do Azure na seção de propriedades do Hub, especificamente, `property.hostname` e `property.deviceStreams` chaves.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A saída é um objeto JSON de todos os pontos de extremidade aos quais o dispositivo e o serviço do Hub podem precisar se conectar para estabelecer um fluxo de dispositivo.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Certifique-se de ter instalado CLI do Azure versão 2.0.57 ou mais recente. Você pode baixar a versão mais recente na página [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Permitir conectividade de saída para os pontos de extremidade de streaming do dispositivo

Conforme mencionado no início deste artigo, seu dispositivo cria uma conexão de saída para o ponto de extremidade de streaming do Hub IoT durante o processo de inicialização de fluxos de dispositivo. Seus firewalls no dispositivo ou em sua rede devem permitir a conectividade de saída para o gateway de streaming pela porta 443 (Observe que a comunicação ocorre em uma conexão WebSocket criptografada usando TLS).

O nome do host do ponto de extremidade de streaming do dispositivo pode ser encontrado no portal do Hub IoT do Azure na guia Visão geral. !["pontos de extremidade de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, você pode encontrar essas informações usando CLI do Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Certifique-se de ter instalado CLI do Azure versão 2.0.57 ou mais recente. Você pode baixar a versão mais recente na página [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Solucionar problemas via logs de atividade de fluxos de dispositivo

Você pode configurar logs de Azure Monitor para coletar o log de atividades dos fluxos de dispositivo em seu hub IoT. Isso pode ser muito útil em cenários de solução de problemas.

Siga as etapas abaixo para configurar os logs de Azure Monitor para as atividades de fluxo do dispositivo do Hub IoT:

1. Navegue até a guia *configurações de diagnóstico* no Hub IOT e clique no link *Ativar diagnóstico* .

   !["Habilitando logs de diagnóstico"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Forneça um nome para as configurações de diagnóstico e escolha *Enviar para log Analytics* opção. Você será guiado para escolher um recurso existente do espaço de trabalho Log Analytics ou criar um novo. Além disso, verifique o *DeviceStreams* da lista.

    !["Habilitar logs de fluxos de dispositivo"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Agora você pode acessar os logs de fluxos de dispositivo na guia *logs* no portal do seu hub IOT. Os logs de atividade do fluxo do dispositivo aparecerão na tabela `AzureDiagnostics` e terão `Category=DeviceStreams`.

   Como mostrado abaixo, a identidade do dispositivo de destino e o resultado da operação também estão disponíveis nos logs.

   !["Acessar logs de fluxo do dispositivo"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Disponibilidade Regional

Durante a visualização pública, os fluxos de dispositivo do Hub IoT estão disponíveis nas regiões EUA Central, EUA Central EUAP, Europa Setentrional e sudeste asiático. Certifique-se de criar o Hub em uma dessas regiões.

## <a name="sdk-availability"></a>Disponibilidade do SDK

Dois lados de cada fluxo (no lado do dispositivo e do serviço) usam o SDK do Hub IoT para estabelecer o túnel. Durante a visualização pública, os clientes podem escolher entre as seguintes linguagens do SDK:

* Os fluxos de C# dispositivo de suporte do C e do SDK no lado do dispositivo.

* O NodeJS e C# SDK dão suporte a fluxos de dispositivo no lado do serviço.

## <a name="iot-hub-device-stream-samples"></a>Amostras de fluxo de dispositivo do Hub IoT

Há duas [amostras de início rápido](/azure/iot-hub) disponíveis na página do Hub IOT. Eles demonstram o uso de fluxos de dispositivo por aplicativos.

* O exemplo *Echo* demonstra o uso programático de fluxos de dispositivo (chamando diretamente a API do SDK).

* O exemplo de *proxy local* demonstra o túnel do tráfego de aplicativo de cliente/servidor (como SSH, RDP ou Web) fora do mercado por meio de fluxos de dispositivo.

Esses exemplos são abordados em mais detalhes abaixo.

### <a name="echo-sample"></a>Exemplo de eco

O exemplo Echo demonstra o uso programático de fluxos de dispositivo para enviar e receber bytes entre aplicativos de serviço e dispositivo. Observe que você pode usar programas de serviço e dispositivo em diferentes idiomas. Por exemplo, você pode usar o programa de dispositivo C com C# o programa de serviço.

Aqui estão os exemplos de eco:

* [C#programa de serviço e serviço](quickstart-device-streams-echo-csharp.md)

* [Programa de serviço node. js](quickstart-device-streams-echo-nodejs.md)

* [Programa de dispositivo C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Exemplo de proxy local (para SSH ou RDP)

O exemplo de proxy local demonstra uma maneira de habilitar o túnel de um tráfego de aplicativo existente que envolve a comunicação entre um cliente e um programa de servidor. Essa configuração funciona para protocolos de cliente/servidor como SSH e RDP, onde o lado do serviço atua como um cliente (executando programas de cliente SSH ou RDP) e o lado do dispositivo atua como o servidor (executando o daemon SSH ou programas de servidor RDP).

Esta seção descreve o uso de fluxos de dispositivo para permitir que o usuário execute o SSH para um dispositivo em fluxos de dispositivo (o caso para RDP ou outro aplicativo cliente/servidor é semelhante usando a porta correspondente do protocolo).

A instalação utiliza dois programas de *proxy local* mostrados na figura abaixo, ou seja, o *proxy* local do dispositivo e o *proxy de serviço local*. Os programas de proxy local são responsáveis por executar o [handshake de início de fluxo do dispositivo](#device-stream-creation-flow) com o Hub IOT e interagir com o cliente SSH e o daemon SSH usando soquetes de cliente/servidor comuns.

!["Configuração de proxy do fluxo do dispositivo para SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. O usuário executa o proxy de serviço local para iniciar um fluxo de dispositivo para o dispositivo.

2. O proxy de dispositivo local aceita a solicitação de início de fluxo e o túnel é estabelecido para o ponto de extremidade de streaming do Hub IoT (como discutido acima).

3. O proxy de dispositivo local conecta-se ao ponto de extremidade do daemon SSH escutando na porta 22 no dispositivo.

4. O proxy de serviço local escuta em uma porta designada aguardando novas conexões SSH do usuário (porta 2222 usada no exemplo, mas isso pode ser configurado para qualquer outra porta disponível). O usuário aponta o cliente SSH para a porta do proxy local de serviço no localhost.

### <a name="notes"></a>Notas

* As etapas acima concluem um túnel de ponta a ponta entre o cliente SSH (à direita) e o daemon SSH (à esquerda). Parte dessa conectividade de ponta a ponta envolve o envio de tráfego em um fluxo de dispositivo para o Hub IoT.

* As setas na figura indicam a direção em que as conexões são estabelecidas entre os pontos de extremidade. Especificamente, observe que não há conexões de entrada que vão para o dispositivo (isso geralmente é bloqueado por um firewall).

* A escolha de usar a porta 2222 no proxy local de serviço é uma opção arbitrária. O proxy pode ser configurado para usar qualquer outra porta disponível.

* A escolha da porta 22 é dependente de protocolo e específica ao SSH, nesse caso. Para o caso do RDP, a porta 3389 deve ser usada. Isso pode ser configurado nos programas de exemplo fornecidos.

Use os links abaixo para obter instruções sobre como executar os programas de proxy local no idioma de sua escolha. Semelhante ao [exemplo de eco](#echo-sample), você pode executar programas de proxy local de dispositivo e serviço em diferentes idiomas, pois eles são totalmente interoperáveis.

* [C#programa de serviço e serviço](quickstart-device-streams-proxy-csharp.md)

* [Programa de serviço node. js](quickstart-device-streams-proxy-nodejs.md)

* [Programa de dispositivo C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Passos seguintes

Use os links abaixo para saber mais sobre os fluxos de dispositivo.

> [!div class="nextstepaction"]
> [Fluxos de dispositivo no IoT show (canal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
