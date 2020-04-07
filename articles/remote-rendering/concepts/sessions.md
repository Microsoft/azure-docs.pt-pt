---
title: Sessões de renderização remota
description: Descreve o que é uma sessão de renderização remota
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681691"
---
# <a name="remote-rendering-sessions"></a>Sessões de renderização remota

Em Azure Remote Rendering (ARR), uma *sessão* é um conceito chave. Este artigo explica o que é exatamente uma sessão.

## <a name="overview"></a>Descrição geral

A Renderização Remota Azure funciona descarregando tarefas complexas de renderização na nuvem. Estas tarefas de renderização não podem ser cumpridas por qualquer servidor, uma vez que a maioria dos servidores de nuvem não têm GPUs. Devido à quantidade de dados envolvidos e à difícil exigência de produzir resultados a taxas de fotogramas interativas, a responsabilidade que o servidor manuseia que o pedido do utilizador também não pode ser entregue a outra máquina em voo, como pode ser possível para um tráfego web mais comum.

Isto significa que, quando utilizar a Renderização Remota Azure, um servidor de nuvem com as capacidades de hardware necessárias deve ser reservado exclusivamente para lidar com os seus pedidos de renderização. Uma *sessão* refere-se a tudo o que está envolvido em interagir com este servidor. Começa com o pedido inicial de reserva (*locação),* uma máquina para a sua utilização, continua com todos os comandos para carregar e manipular modelos, e termina com a liberação do aluguer no servidor de nuvem.

## <a name="managing-sessions"></a>Gestão de sessões

Existem várias formas de gerir e interagir com as sessões. A forma independente de criar, atualizar e encerrar sessões é através [da gestão da sessão REST API.](../how-tos/session-rest-api.md) Em C# e C++, estas operações são expostas através das classes `AzureFrontend` e `AzureSession`. Para aplicações de Unidade, existem outras funções de utilidade fornecidas pelo `ARRServiceUnity` componente.

Uma vez *ligado* a uma sessão ativa, operações como [carregar modelos](models.md) e interagir com a cena são expostas através da `AzureSession` classe.

### <a name="managing-multiple-sessions-simultaneously"></a>Gerir várias sessões simultaneamente

Não é possível *ligar-se* totalmente a várias sessões a partir de um dispositivo. No entanto, pode criar, observar e encerrar quantas sessões quiser a partir de uma única aplicação. Desde que a aplicação não esteja destinada a alguma vez se ligar a uma sessão, também não precisa de ser executada num dispositivo como o HoloLens 2. Um caso de utilização para tal implementação pode ser se quiser controlar as sessões através de um mecanismo central. Por exemplo, pode-se construir uma aplicação web, onde vários tablets e HoloLenses podem entrar. Em seguida, a aplicação pode apresentar opções nos tablets, como qual modelo CAD a exibir. Se um utilizador fizer uma seleção, esta informação é comunicada a todos os HoloLenses para criar uma experiência partilhada.

## <a name="session-phases"></a>Fases de sessão

Cada sessão passa por várias fases.

### <a name="session-startup"></a>Arranque de sessão

Quando pede à ARR para [criar uma nova sessão,](../how-tos/session-rest-api.md#create-a-session)a primeira coisa que faz é devolver uma sessão [uUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Este UUID permite-lhe consultar informações sobre a sessão. O UUID e algumas informações básicas sobre a sessão são perduradas por 30 dias, para que possa consultar essa informação mesmo depois de a sessão ter sido interrompida. Neste momento, o estado da **sessão** será reportado como **Inicial .**

Em seguida, a Renderização Remota Azure tenta encontrar um servidor que possa acolher a sua sessão. Há dois parâmetros para esta busca. Primeiro, só reservaservidores na sua [região.](../reference/regions.md) Isso porque a latência da rede em todas as regiões pode ser demasiado alta para garantir uma experiência decente. O segundo fator é o *tamanho* desejado que especificou. Em cada região, há um número limitado de servidores que podem cumprir o pedido de tamanho *Standard* ou *Premium.* Consequentemente, se todos os servidores do tamanho solicitado estiverem atualmente em uso na sua região, a criação da sessão falhará. A razão do fracasso [pode ser consultada.](../how-tos/session-rest-api.md#get-sessions-properties)

> [!IMPORTANT]
> Se solicitar um tamanho *VM Standard* e o pedido falhar devido à elevada procura, isso não implica que solicitar um servidor *Premium* também falhe. Portanto, se for uma opção para si, pode tentar voltar a um VM *Premium.*

Quando o serviço encontra um servidor adequado, tem de copiar a máquina virtual adequada (VM) para o transformar num hospedeiro de renderização remota Azure. Este processo demora vários minutos. Em seguida, o VM é iniciado e a **sessão estatal** transições para **Ready**.

Neste momento, o servidor está exclusivamente à espera da sua entrada. Este é também o ponto a partir do qual você é cobrado para o serviço.

### <a name="connecting-to-a-session"></a>Ligação a uma sessão

Uma vez que a sessão esteja *pronta,* pode *ligar-se* a ela. Enquanto ligado, o dispositivo pode enviar comandos para carregar e modificar os modelos. Cada anfitrião de ARR só serve um dispositivo cliente de cada vez, por isso, quando um cliente se conecta a uma sessão, tem controlo exclusivo sobre o conteúdo renderizado. Isto também significa que o desempenho da renderização nunca variará por razões fora do seu controlo.

> [!IMPORTANT]
> Embora apenas um cliente possa *ligar-se* a uma sessão, informações básicas sobre sessões, como o seu estado atual, podem ser consultadas sem ligação.

Enquanto um dispositivo está ligado a uma sessão, as tentativas de ligação por outros dispositivos falharão. No entanto, uma vez que o dispositivo conectado se desliga, voluntariamente ou devido a algum tipo de falha, a sessão aceitará outro pedido de ligação. Todo o estado anterior (modelos carregados e tal) é descartado de modo a que o próximo dispositivo de ligação obtenha uma ficha limpa. Assim, as sessões podem ser reutilizadas muitas vezes, por diferentes dispositivos e pode ser possível esconder a despesa de arranque da sessão do utilizador final na maioria dos casos.

> [!IMPORTANT]
> O servidor remoto nunca altera o estado dos dados do lado do cliente. Todas as mutações de dados (tais como atualizações de transformação e pedidos de carga) devem ser realizadas pela aplicação do cliente. Todas as ações atualizam imediatamente o estado do cliente.

### <a name="session-end"></a>Fim da sessão

Quando solicita uma nova sessão, especifice um *tempo máximo*de arrendamento , normalmente entre uma a oito horas. Esta é a duração durante a qual o anfitrião aceitará a sua entrada.

Há duas razões regulares para uma sessão terminar. Ou solicita manualmente que a sessão seja interrompida ou o tempo máximo de arrendamento expira. Em ambos os casos, qualquer ligação ativa ao hospedeiro está fechada imediatamente, e o serviço é desligado nesse servidor. O servidor é então devolvido à piscina Azure e pode ser requisitado para outros fins. Parar uma sessão não pode ser desfeito ou cancelado. A consulta do estado da **sessão** numa sessão interrompida irá devolver **Parado** ou **Expirado,** dependendo se foi desligado manualmente ou porque o tempo máximo de arrendamento foi atingido.

Uma sessão também pode ser interrompida devido a algum fracasso.

Em todos os casos, não será cobrado mais quando uma sessão for interrompida.

> [!WARNING]
> Se te ligas a uma sessão, e por quanto tempo, não afeta a faturação. O que paga pelo serviço depende da duração da *sessão,* o que significa o tempo que um servidor é reservado exclusivamente para si, e as capacidades de hardware solicitadas (o tamanho VM). Se iniciar uma sessão, conecte-se durante cinco minutos e, em seguida, não pare a sessão, de modo a que continue a funcionar até que o seu contrato de arrendamento expire, será cobrado pelo tempo de arrendamento completo da sessão. Inversamente, o *tempo máximo* de arrendamento é principalmente uma rede de segurança. Não importa se solicita uma sessão com um tempo de aluguer de oito horas, então use-a apenas durante cinco minutos, se parar manualmente a sessão depois.

#### <a name="extend-a-sessions-lease-time"></a>Prolongue o tempo de arrendamento de uma sessão

Pode [prolongar o tempo](../how-tos/session-rest-api.md#update-a-session) de arrendamento de uma sessão ativa, se se verificar que precisa por mais tempo.

## <a name="example-code"></a>Código de exemplo

O código abaixo mostra uma simples implementação de iniciar uma sessão, à espera do estado *pronto,* conectando-se e, em seguida, desligar e desligar novamente.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

`AzureFrontend` Várias `AzureSession` e instâncias podem ser mantidas, manipuladas e consultadas a partir do código. Mas apenas um único `AzureSession` dispositivo pode ligar-se a um de cada vez.

A vida de uma máquina virtual `AzureFrontend` não está `AzureSession` ligada à instância ou à instância. `AzureSession.StopAsync`deve ser chamado para parar uma sessão.

O ID de sessão persistente `AzureSession.SessionUUID()` pode ser consultado via e cached localmente. Com este ID, um `AzureFrontend.OpenSession` pedido pode ligar-se a essa sessão.

Quando `AzureSession.IsConnected` é `AzureSession.Actions` verdade, devolve `RemoteManager`uma instância de , que contém as funções de [carregar modelos](models.md), manipular [entidades](entities.md), e [consultar informações](../overview/features/spatial-queries.md) sobre a cena renderizada.

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Modelos](models.md)
