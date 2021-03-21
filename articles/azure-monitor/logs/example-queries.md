---
title: Log Analytics in Azure Monitor oferece conjuntos de consultas de exemplo que você pode executar por conta própria ou usar como ponto de partida para suas próprias consultas.
description: Consultas que pode começar e modificar para as suas necessidades
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e4f20032febe1c4afe9cb0964c1b448eaa018103
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030856"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Consultas de exemplo no Azure Monitor Log Analytics
O Log Analytics oferece conjuntos de consultas de exemplo que pode executar por conta própria ou usar como ponto de partida para as suas próprias consultas. Este artigo descreve consultas de exemplo e como usá-las.

Se não está familiarizado com o Log Analytics ou com a linguagem de consulta KQL, as consultas de exemplo são uma ótima maneira de começar. Eles podem fornecer informações instantâneas sobre um recurso e fornecer uma boa maneira de começar a aprender e usar kQL, reduzindo assim o tempo que leva para começar a usar Log Analytics. Recolhemos e curamos mais de 250 consultas de exemplo projetadas para lhe fornecer valor instantâneo e esse número de consultas de exemplo está continuamente a crescer.

## <a name="in-context-queries"></a>Consultas em contexto

A nova experiência filtra e sugere consultas em contexto. Por outras palavras, o sistema mostra automaticamente apenas consultas relevantes para o âmbito que selecionou.

- Para um **único recurso** – as consultas são filtradas de acordo com o tipo de recurso.
- Para um **grupo de recursos** - as consultas são filtradas de acordo com os recursos do grupo de recursos específicos.
- Para um **espaço de trabalho** – as consultas são filtradas de acordo com as soluções instaladas no espaço de trabalho.

Este comportamento é consistente para todos os âmbitos do Log Analytics. Se não estiver a ver uma consulta de exemplo para o tipo de recurso que deseja, pode ser devido a filtros devido ao facto de estar em contexto. Uma secção posterior descreve como remover a deteção em contexto para que possa ver todas as consultas possíveis.

> [!TIP]
> Quanto mais recursos tiver no seu âmbito, mais tempo o portal filtra e mostra o diálogo de consulta de amostra.

## <a name="example-query-user-interface"></a>Interface de utilizador de consulta de exemplo

Você pode obter consultas de exemplo de dois locais diferentes.

### <a name="example-query-dialog"></a>Diálogo de consulta de exemplo

Quando inseriu pela primeira vez a experiência Log Analytics, o *diálogo de consultas exemplo* é mostrado automaticamente.  Também pode ser acedido clicando no canto superior direito do ecrã em **consultas exemplo**.

![Sidebar - consultas de exemplo](media/example-queries/sidebar-2.png)

O diálogo de consulta de exemplo aparece então como mostrado abaixo:  

![Tela de consultas de exemplo](media/example-queries/example-query-start.png)

A imagem anterior mostra o ecrã de registos para uma instância Azure Key Vault. Como mencionado anteriormente neste artigo, as consultas são mostradas em contexto.  Como resultado, a imagem mostra apenas exemplos relacionados com o Key Vault. Se selecionar uma subscrição completa, então são apresentadas consultas para todos os tipos de recursos dessa subscrição.  

Cada consulta de exemplo é representada por um cartão. Pode digitalizar rapidamente as consultas para encontrar o que precisa. Pode executar a consulta diretamente a partir do diálogo ou optar por carregá-la ao editor de consulta para afinar e ajustar adicionais.

### <a name="sidebar-query-experience"></a>Experiência de consulta de barra lateral

Todas as mesmas funcionalidades da experiência do diálogo podem ser acedidas a partir do painel de consultas na barra lateral esquerda do Log Analytics. Pode pairar sobre um nome de consulta para obter a descrição da consulta e funcionalidade adicional.

![Screenshot que mostra o painel de consultas.](media/example-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Consultas de descoberta e filtragem

As opções nesta secção estão disponíveis tanto na experiência de consulta de diálogo como na barra lateral, mas com uma interface de utilizador ligeiramente diferente.  

### <a name="use-favorites"></a>Use os favoritos

Você pode favorito consultas frequentemente usadas para lhe dar acesso mais rápido.

> [!TIP]
> Recolher e visualizar consultas mais tarde é uma boa maneira de começar. Encontre as consultas de que necessita e clique na estrela ao lado da consulta para adicioná-la aos Favoritos. Se mais tarde achar que a consulta não lhe é útil, pode desacompanha-la.  

### <a name="filtering-and-group-by"></a>Filtragem e grupo por

Enquanto os filetes de experiência de diálogo de consulta para mostrar apenas consultas com o contexto certo, pode optar por remover o filtro e ver todas as consultas.

### <a name="group-by"></a>Agrupar por

Altere o agrupamento das consultas clicando no *grupo por* lista de down-down:

![Exemplo consultas screen groupby](media/example-queries/example-query-groupby.png)

O diálogo suporta o agrupamento:

- **Tipo de recurso** – Um recurso definido no Azure, como uma máquina Virtual. Consulte a Referência da [Tabela do Monitor Azure](/azure/azure-monitor/reference/tables/tables-resourcetype) para obter um mapeamento completo das tabelas Azure Monitor Logs/Log Analytics para o tipo de recurso.  
- **Categoria** – Um tipo de informação como *Segurança* ou *Auditoria.* As categorias são idênticas às categorias definidas no painel lateral das tabelas. Consulte a referência da tabela do [monitor Azure](/azure/azure-monitor/reference/tables/tables-category) para obter uma lista completa de categorias.  
- **Solução** – Uma solução Azure Monitor associada às consultas
- **Tópico** – O tópico da consulta de exemplo, como *Registos de Atividade ou registos* *de aplicações.* A propriedade tópico é única para exemplo consultas e pode diferir de acordo com o tipo de recurso específico.

Os valores de agrupamento também funcionam como uma tabela ativa de conteúdos. Ao clicar num dos valores no lado esquerdo do ecrã, as consultas visualizam diretamente o item clicado.

### <a name="filter"></a>Filtro

Também pode filtrar as consultas de acordo com o **grupo por** valores mencionados anteriormente. No diálogo de consulta de exemplo, os filtros encontram-se na parte superior.

![Exemplo de filtro de tela de consultas](media/example-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Combinando grupo por e filtro

O filtro e o grupo por funcionalidade são projetados para funcionar em conjunto. Proporcionam flexibilidade na forma como as consultas são organizadas. Por exemplo, se utilizar um grupo de recursos com vários recursos, é possível que queira filtrar para um tipo específico de recurso e organizar as consultas resultantes por tópico.

## <a name="sample-query-dialog-appearance-behavior"></a>Comportamento de aparição de diálogo de consulta de amostra

Se você é um KQL pro e prefere chegar diretamente ao editor de consulta, você pode alternar o diálogo de consulta "off". Com o toggle desligado, o diálogo de consulta de exemplo não carrega quando o ecrã do Log Analytics carrega.

![Exemplos On-Off](media/example-queries/examples-on-off.png)

Pode sempre aceder à experiência popup de consulta de amostras a partir do botão *de consultas Exemplo* na barra superior do Log Analytics.

## <a name="query-explorer"></a>Explorador de consultas

A experiência do explorador de consultas para guardar e partilhar consultas geradas pelo utilizador permanece inalterada por enquanto.

## <a name="next-steps"></a>Passos seguintes

[Começar com consultas KQL](./get-started-queries.md)