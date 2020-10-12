---
title: Sessões do Remote Rendering
description: Descreve o que é uma sessão de renderização remota
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e7f441a727bea8d1a5d401fb5e9757dee1044fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613803"
---
# <a name="remote-rendering-sessions"></a>Sessões do Remote Rendering

Na renderização remota de Azure (ARR), uma *sessão* é um conceito chave. Este artigo explica o que é exatamente uma sessão.

## <a name="overview"></a>Descrição geral

A renderização remota Azure funciona descarregando tarefas complexas de renderização na nuvem. Estas tarefas de renderização não podem ser cumpridas por qualquer servidor, uma vez que a maioria dos servidores de nuvem não tem GPUs. Devido à quantidade de dados envolvidos e à difícil exigência de produzir resultados a taxas de fotogramas interativas, a responsabilidade que o servidor trata que o pedido do utilizador também não pode ser entregue a outra máquina on-the-fly, como pode ser possível para um tráfego web mais comum.

Isto significa que quando utilizar a Renderização Remota Azure, um servidor em nuvem com as capacidades de hardware necessárias deve ser reservado exclusivamente para lidar com os seus pedidos de renderização. Uma *sessão* refere-se a tudo o que está envolvido em interagir com este servidor. Começa com o pedido inicial de reserva *(locação)* de uma máquina para o seu uso, continua com todos os comandos para carregar e manipular modelos, e termina com a libertação do arrendamento no servidor de nuvem.

## <a name="managing-sessions"></a>Sessões de gestão

Existem múltiplas formas de gerir e interagir com as sessões. A forma independente de criar, atualizar e encerrar sessões é através [da gestão da sessão REST API](../how-tos/session-rest-api.md). Em C# e C++, estas operações são expostas através das classes `AzureFrontend` e `AzureSession` . Para aplicações de unidade, existem outras funções de utilidade fornecidas pelo `ARRServiceUnity` componente.

Uma vez *ligado* a uma sessão ativa, operações como [carregar modelos](models.md) e interagir com a cena são expostas através da `AzureSession` classe.

### <a name="managing-multiple-sessions-simultaneously"></a>Gerir várias sessões simultaneamente

Não é possível *ligar-se* totalmente a várias sessões a partir de um dispositivo. No entanto, pode criar, observar e encerrar quantas sessões quiser de uma única aplicação. Desde que a aplicação não seja feita para se ligar a uma sessão, também não precisa de ser executada num dispositivo como o HoloLens 2. Um caso de utilização para tal implementação pode ser se você quiser controlar sessões através de um mecanismo central. Por exemplo, pode-se construir uma aplicação web, onde vários tablets e dispositivos HoloLens podem entrar. Em seguida, a aplicação pode apresentar opções nos tablets, como o modelo CAD a exibir. Se um utilizador fizer uma seleção, esta informação é comunicada a todos os dispositivos HoloLens para criar uma experiência partilhada.

## <a name="session-phases"></a>Fases de sessão

Cada sessão passa por várias fases.

### <a name="session-startup"></a>Arranque de sessão

Quando pede à ARR para [criar uma nova sessão,](../how-tos/session-rest-api.md#create-a-session)a primeira coisa que faz é devolver uma sessão [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Este UUID permite-lhe consultar informações sobre a sessão. A UUID e algumas informações básicas sobre a sessão são persistidos durante 30 dias, para que possa consultar essa informação mesmo depois de a sessão ter sido interrompida. Neste momento, o estado da **sessão** será reportado como **Start .**

Em seguida, a Azure Remote Rendering tenta encontrar um servidor que possa hospedar a sua sessão. Há dois parâmetros para esta busca. Em primeiro lugar, só reservará servidores na sua [região.](../reference/regions.md) Isto porque a latência da rede em todas as regiões pode ser demasiado elevada para garantir uma experiência decente. O segundo fator é o *tamanho* desejado que especificou. Em cada região, há um número limitado de servidores que podem cumprir o pedido de tamanho [*Standard*](../reference/vm-sizes.md) ou [*Premium.*](../reference/vm-sizes.md) Consequentemente, se todos os servidores do tamanho solicitado estiverem atualmente a ser utilizados na sua região, a criação de sessão falhará. A razão do fracasso [pode ser questionada.](../how-tos/session-rest-api.md#get-sessions-properties)

> [!IMPORTANT]
> Se solicitar um tamanho de servidor *Standard* e o pedido falhar devido à elevada procura, isso não implica que solicitar um servidor *Premium* também falhe. Portanto, se for uma opção para si, pode tentar recorrer ao tamanho do servidor *Premium.*

Quando o serviço encontra um servidor adequado, tem de copiar a máquina virtual adequada (VM) para o transformar num anfitrião de renderização remota Azure. Este processo demora vários minutos. Em seguida, o VM é iniciado e o estado da **sessão** transita para **Ready**.

Neste momento, o servidor está exclusivamente à espera da sua entrada. Este é também o ponto a partir do qual você é cobrado para o serviço.

### <a name="connecting-to-a-session"></a>Ligação a uma sessão

Uma vez que a sessão esteja *pronta,* pode *ligá-la.* Enquanto está ligado, o dispositivo pode enviar comandos para carregar e modificar modelos. Cada anfitrião ARR só serve um dispositivo cliente de cada vez, por isso, quando um cliente se conecta a uma sessão, tem controlo exclusivo sobre o conteúdo renderizado. Isso também significa que o desempenho da renderização nunca variará por razões fora do seu controlo.

> [!IMPORTANT]
> Embora apenas um cliente possa *ligar-se* a uma sessão, informações básicas sobre sessões, como o seu estado atual, podem ser consultadas sem se conectarem.

Enquanto um dispositivo está ligado a uma sessão, as tentativas de ligação por outros dispositivos falharão. No entanto, uma vez que o dispositivo ligado se desconecte, voluntariamente ou devido a algum tipo de falha, a sessão aceitará outro pedido de ligação. Todo o estado anterior (modelos carregados e tal) é descartado de modo a que o próximo dispositivo de ligação obtenha uma ardósia limpa. Assim, as sessões podem ser reutilizadas muitas vezes, por diferentes dispositivos, e pode ser possível ocultar a sobrecarga da sessão do utilizador final na maioria dos casos.

> [!IMPORTANT]
> O servidor remoto nunca altera o estado dos dados do lado do cliente. Todas as mutações de dados (tais como atualizações de transformação e pedidos de carga) devem ser realizadas pela aplicação do cliente. Todas as ações atualizam imediatamente o estado do cliente.

### <a name="session-end"></a>Fim da sessão

Quando solicita uma nova sessão, especifica um tempo máximo de *arrendamento,* normalmente no intervalo de uma a oito horas. Esta é a duração durante a qual o anfitrião aceitará a sua entrada.

Há duas razões regulares para uma sessão terminar. Ou solicita manualmente que a sessão seja interrompida ou o tempo máximo de arrendamento expira. Em ambos os casos, qualquer ligação ativa ao hospedeiro é imediatamente fechada, e o serviço é desligado nesse servidor. O servidor é então devolvido à piscina Azure e pode ser requisitado para outros fins. Parar uma sessão não pode ser desfeito ou cancelado. Consultando o estado da **sessão** numa sessão parada irá devolver **Stop** ou **Expirado,** dependendo se foi desligado manualmente ou porque o tempo máximo de locação foi atingido.

Uma sessão também pode ser interrompida por causa de alguma falha.

Em todos os casos, não será cobrado mais uma vez que uma sessão seja interrompida.

> [!WARNING]
> Se se liga a uma sessão, e por quanto tempo, não afeta a faturação. O que paga pelo serviço depende da duração da *sessão,* o que significa o tempo que um servidor é reservado exclusivamente para si, e as capacidades de hardware solicitadas (o [tamanho atribuído).](../reference/vm-sizes.md) Se iniciar uma sessão, conecte-se durante cinco minutos e, em seguida, não pare a sessão, de modo que continue a funcionar até que o seu contrato expire, será cobrado o tempo completo de locação. Inversamente, o *tempo máximo de locação* é principalmente uma rede de segurança. Não importa se você solicita uma sessão com um tempo de arrendamento de oito horas, em seguida, use-a apenas por cinco minutos, se você parar manualmente a sessão depois.

#### <a name="extend-a-sessions-lease-time"></a>Prolongar o tempo de arrendamento de uma sessão

Você pode [estender o tempo](../how-tos/session-rest-api.md#modify-and-query-session-properties) de arrendamento de uma sessão ativa, se se verificar que você precisa mais tempo.

## <a name="example-code"></a>Código de exemplo

O código abaixo mostra uma simples implementação de iniciar uma sessão, à espera do estado *pronto,* de ligação e, em seguida, desligar e desligar novamente.

```cs
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
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
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

Múltiplos `AzureFrontend` e `AzureSession` casos podem ser mantidos, manipulados e consultados a partir do código. Mas apenas um dispositivo pode ligar-se a `AzureSession` um de cada vez.

A vida útil de uma máquina virtual não está ligada ao `AzureFrontend` caso ou ao `AzureSession` caso. `AzureSession.StopAsync` deve ser chamado para parar uma sessão.

O ID da sessão persistente pode ser consultado através `AzureSession.SessionUUID()` e em cache localmente. Com este ID, uma aplicação pode `AzureFrontend.OpenSession` ligar-se a essa sessão.

Quando `AzureSession.IsConnected` é verdade, `AzureSession.Actions` devolve um exemplo de , que contém as `RemoteManager` funções para carregar [modelos](models.md), manipular [entidades](entities.md)- e [consultar informações](../overview/features/spatial-queries.md) sobre a cena renderizada.

## <a name="api-documentation"></a>Documentação da API

* [C# Classe AzureSssion](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azuresession)
* [C# AzureFrontend.CreateNewRenderingSessionAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azurefrontend.createnewrenderingsessionasync)
* [C# AzureFrontend.OpenRenderingSession()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.azurefrontend.openrenderingsession)
* [Classe C++ AzureSssion](https://docs.microsoft.com/cpp/api/remote-rendering/azuresession)
* [C++ AzureFrontend::CreateNewRenderingSessionAsync](https://docs.microsoft.com/cpp/api/remote-rendering/azurefrontend#createnewrenderingsessionasync)
* [C++ AzureFrontend::OpenRenderingSession](https://docs.microsoft.com/cpp/api/remote-rendering/azurefrontend#openrenderingsession)

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Modelos](models.md)
