---
title: O que é o Personalizador?
description: Personalizer é um serviço baseado na nuvem que lhe permite escolher a melhor experiência para mostrar aos seus utilizadores, aprendendo com o seu comportamento em tempo real.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalização, personalizador Azure, machine learning
ms.openlocfilehash: b2577502907b69e134651c93ab7a98fc51e9aaa6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169254"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O Azure Personalizer é um serviço baseado na nuvem que ajuda as suas aplicações a escolher o melhor item de conteúdo para mostrar aos seus utilizadores. Pode utilizar o serviço Personalizar para determinar que produto sugerir aos compradores ou descobrir a posição ideal para um anúncio. Depois de o conteúdo ser mostrado ao utilizador, a sua aplicação monitoriza a reação do utilizador e reporta uma pontuação de recompensa de volta ao serviço Personaler. Isto garante uma melhoria contínua do modelo de machine learning e a capacidade do Personalizer de selecionar o melhor item de conteúdo com base na informação contextual que recebe.

> [!TIP]
> O conteúdo é qualquer unidade de informação, como texto, imagens, URL, e-mails ou qualquer outra coisa que queira selecionar e mostrar aos seus utilizadores.

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](quickstart-personalizer-sdk.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como fazer**](how-to-settings.md) contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.  
* [**Os conceitos**](how-personalizer-works.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.  
* [**Os tutoriais**](tutorial-use-personalizer-web-app.md) são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.  

Antes de começar, experimente [personalizer com esta demonstração interativa.](https://personalizationdemo.azurewebsites.net/)

## <a name="how-does-personalizer-select-the-best-content-item"></a>Como é que o Personalizer seleciona o melhor item de conteúdo?

O personalização usa a **aprendizagem** de reforço para selecionar o melhor item _(ação_) com base no comportamento coletivo e pontuações de recompensa em todos os utilizadores. As ações são os itens de conteúdo, tais como artigos noticiosos, filmes específicos ou produtos.

A chamada **Rank** leva o item de ação, juntamente com as características da ação, e as funcionalidades de contexto para selecionar o item de ação de topo:

* **Ações com funcionalidades** - itens de conteúdo com funcionalidades específicas de cada item
* **Funcionalidades de contexto** - características dos seus utilizadores, seu contexto ou seu ambiente ao usar a sua app

A chamada Rank devolve o ID do produto de conteúdo, __ação,__ para mostrar ao utilizador, no campo **De ID de Ação de Recompensa.**

A __ação__ mostrada ao utilizador é escolhida com modelos de machine learning, que tentam maximizar a quantidade total de recompensas ao longo do tempo.

### <a name="sample-scenarios"></a>Cenários de exemplo

Vamos dar uma olhada em alguns cenários onde o Personalizer pode ser usado para selecionar o melhor conteúdo para renderizar para um utilizador.

|Tipo do conteúdo|Ações (com características)|Características de contexto|ID de ação de recompensa devolvido<br>(mostrar este conteúdo)|
|--|--|--|--|
|Lista de notícias|a. `The president...` (nacional, política, [texto])<br>b. `Premier League ...` (global, desporto, [texto, imagem, vídeo])<br> c. `Hurricane in the ...` (regional, meteorológico, [texto,imagem]|As notícias do dispositivo são lidas a partir de<br>Mês, ou temporada<br>|um `The president...`|
|Lista de filmes|1. `Star Wars` (1977, [ação, aventura, fantasia], George Lucas)<br>2. `Hoop Dreams` (1994, [documentário, desporto], Steve James<br>3. `Casablanca` (1942, [romance, drama, guerra], Michael Curtiz)|Filme de dispositivo é assistido a partir de<br>tamanho do ecrã<br>Tipo de utilizador<br>|3. `Casablanca`|
|Lista de produtos|i. `Product A` (3 kg, $$$,entrega em 24 horas)<br>ii. `Product B` (20 kg, $$, 2 semanas de envio com alfândega)<br>iii. `Product C` (3 kg, $$$, entrega em 48 horas)|As compras de dispositivos são lidas a partir de<br>Nível de gastos do utilizador<br>Mês, ou temporada|ii. `Product B`|

Personalizar a aprendizagem de reforço usada para selecionar a melhor ação, conhecida como _ID de ação de recompensa._ O modelo de aprendizagem automática utiliza: 

* Um modelo treinado - informação previamente recebida do serviço personalizado usado para melhorar o modelo de machine learning
* Dados atuais - ações específicas com funcionalidades e características de contexto

## <a name="when-to-use-personalizer"></a>Quando usar o Personalizar

[A API](https://go.microsoft.com/fwlink/?linkid=2092082) do **Ranking** de Personalização é chamada sempre que a sua aplicação apresenta conteúdo. Isto é conhecido como um **evento,** notado com um _ID de evento._

[A API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **de Recompensa** do Personaler pode ser chamada em tempo real ou retardada para se adaptar melhor à sua infraestrutura. Determina a pontuação da recompensa com base nas necessidades do seu negócio. A pontuação da recompensa é entre 0 e 1. Isso pode ser um único valor, como 1 para o bem, e 0 para o mal, ou um número produzido por um algoritmo que crias tendo em conta os teus objetivos e métricas de negócio.

## <a name="content-requirements"></a>Requisitos de conteúdo

Utilize o Personalizar quando o seu conteúdo:

* Tem um conjunto limitado de ações ou itens (máx. Se tiver uma lista maior, [utilize um motor de recomendação](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) para reduzir a lista para 50 itens para cada vez que ligar para o Rank no serviço Personalizar.
* Tem informações que descrevem o conteúdo que pretende classificar: _ações com funcionalidades_ e _funcionalidades de contexto_.
* Tem um mínimo de ~1k/dia de eventos relacionados com conteúdo para personalizer ser eficaz. Se o Personaler não receber o tráfego mínimo necessário, o serviço demora mais tempo a determinar o único item de conteúdo.

Uma vez que o Personalizer utiliza informação coletiva em quase tempo real para devolver o único item de conteúdo melhor, o serviço não:
* Persistir e gerir informações sobre o perfil do utilizador
* Registar as preferências ou históricos dos utilizadores individuais
* Requerer conteúdo limpo e rotulado

## <a name="how-to-design-for-and-implement-personalizer"></a>Como projetar e implementar o Personalizar

1. [Design](concepts-features.md) e plano para conteúdos, **_ações_** e **_contexto._** Determine o algoritmo de recompensa para a pontuação da **_recompensa._**
1. Cada [recurso personalizante](how-to-settings.md) que cria é considerado um Ciclo de Aprendizagem. O loop receberá as chamadas Rank e Reward para esse conteúdo ou experiência do utilizador.

    |Tipo de recurso| Objetivo|
    |--|--|
    |[Modo Aprendiz](concept-apprentice-mode.md) `E0`|Treine o modelo Personalizer sem afetar a sua aplicação existente e, em seguida, implemente o comportamento de aprendizagem online para um ambiente de produção|
    |Standard, `S0`|Comportamento de aprendizagem on-line em um ambiente de produção|
    |Livre, `F0`| Experimente o comportamento de aprendizagem online num ambiente não produtivo|

1. Adicione Personalizer à sua aplicação, website ou sistema:
    1. Adicione uma chamada **rank** ao Personaler na sua aplicação, website ou sistema para determinar o melhor, único item _de conteúdo_ antes que o conteúdo seja mostrado ao utilizador.
    1. Exibir o melhor item de _conteúdo_ único, que é o _ID_ de ação de recompensa devolvido, para o utilizador.
    1. Aplicar _lógica de negócio_ para recolher informações sobre como o utilizador se comportou, para determinar a pontuação da **recompensa,** tais como:

    |Comportamento|Pontuação de recompensa calculada|
    |--|--|
    |O utilizador selecionou melhor item de _conteúdo_ único (ID de ação de recompensa)|**1**|
    |Utilizador selecionado outro conteúdo|**0**|
    |O utilizador fez uma pausa, percorrendo indecisamente, antes de selecionar o melhor e único item de _conteúdo_ (ID de ação de recompensa)|**0,5**|

    1. Adicione uma chamada **de Recompensa** enviando uma pontuação de recompensa entre 0 e 1
        * Imediatamente após mostrar o seu conteúdo
        * Ou algum tempo mais tarde num sistema offline
    1. [Avalie o seu loop](concepts-offline-evaluation.md) com uma avaliação offline após um período de utilização. Uma avaliação offline permite testar e avaliar a eficácia do Serviço Personalizar sem alterar o seu código ou afetar a experiência do utilizador.

## <a name="reference"></a>Referência 

* [Personalizar C#/.NET SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Personaler Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Personalizer JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Personaler Python SDK](/python/api/overview/azure/cognitiveservices/personalizer)
* [APIs REST](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como funciona](how-personalizer-works.md) 
>  o Personaler [O que é Aprendizagem de Reforço?](concepts-reinforcement-learning.md)
