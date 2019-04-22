---
title: Configurar o glossário de negócios no catálogo de dados do Azure
description: Realce o glossário comercial no catálogo de dados do Azure para definir e usar um vocabulário de negócios comuns à marca artigo que mostra como os recursos de dados registados.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 0337b2a7a228052dacfb35982ee6e8d62372fb29
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264221"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Configurar o glossário comercial para regem-se de marcação

## <a name="introduction"></a>Introdução

O catálogo de dados do Azure permite a deteção de origem de dados, para que possa facilmente detetar e compreender as origens de dados que precisa para executar uma análise e tomar decisões. Esses recursos tornam o maior impacto quando pode encontrar e compreender a mais ampla variedade de origens de dados disponíveis.

Marcação de um recurso de catálogo de dados que promove a maior compreensão dos dados de recursos. Ao utilizar a marcação, pode associar palavras-chave com um recurso ou uma coluna, que por sua vez faz com que seja mais fácil de descobrir o elemento através de pesquisa ou navegação. Etiquetagem também ajuda a mais facilmente compreender o contexto e o propósito do ativo.

No entanto, a etiquetagem, às vezes, pode causar problemas por conta própria. Alguns exemplos dos problemas que pode introduzir a marcação são:

* O uso de abreviações alguns ativos e de texto expandido em outras pessoas. Essa inconsistência destrói a deteção de recursos, mesmo que o objetivo era etiquetar os ativos com a mesma etiqueta.
* Variações de potencial em significado, dependendo do contexto. Por exemplo, uma etiqueta denominada *receita* num cliente conjunto de dados pode significar a receita por cliente, mas a mesma etiqueta num conjunto de dados de vendas trimestral pode significar a receita trimestral para a empresa.  

Para ajudar a solucionar esse e outros desafios semelhante, o catálogo de dados inclui um glossário comercial.

Ao utilizar o glossário de negócios do catálogo de dados, uma organização pode documentar os termos de negócios essenciais e as respetivas definições para criar um vocabulário de negócios comuns. Este governação permite consistência na utilização de dados em toda a organização. Depois de um termo é definido no glossário comercial, podem ser atribuído a um recurso de dados no catálogo. Essa abordagem *regem-se de marcação*, é a mesma abordagem como marcação.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade de glossário e privilégios

O glossário comercial está disponível apenas a Standard Edition do catálogo de dados Azure. Edição gratuita do catálogo de dados não inclui um glossário e ele não fornece capacidades para marcar o regulados.

Pode acessar o glossário de negócios por meio da **glossário** opção no menu de navegação do portal do catálogo de dados.  

![Acessando o glossário comercial](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do catálogo de dados e os membros da função de administradores do glossário podem criar, editar e eliminar Glossário de termos no glossário de negócios. Todos os utilizadores do catálogo de dados podem ver as definições de prazo e os ativos de etiqueta com os termos do glossário.

![Adicionar um novo termo do glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criar os termos do glossário

Os administradores do catálogo de dados e administradores do glossário podem criar termos do glossário clicando a **novo termo** botão. Cada termo do glossário contém os seguintes campos:

* Uma definição de negócios para o termo
* Uma descrição que captura o uso pretendido ou regras de negócios para o recurso ou uma coluna
* Uma lista de participantes que precisa saber mais sobre o termo
* Termo principal, que define a hierarquia em que o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termo do glossário

Ao utilizar o glossário de negócios do catálogo de dados, uma organização possa descrever o vocabulário de negócios como uma hierarquia de termos e, pode criar uma classificação de termos que melhor representa a taxonomia comercial.

Um termo tem de ser exclusivo num determinado nível de hierarquia. Não são permitidos nomes duplicados. Não há limite para o número de níveis numa hierarquia, mas uma hierarquia, muitas vezes, é mais facilmente compreendida quando existem três níveis ou menos.

A utilização de hierarquias no glossário comercial é opcional. Deixar em branco do campo de termo de principal para termos do glossário cria uma lista fixa (não hierárquico) de termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Identificação de recursos com os termos do glossário

Depois de termos do glossário foram definidos no catálogo, a experiência de identificação de recursos é otimizada para o glossário de pesquisa, como um usuário digita uma etiqueta. O portal do catálogo de dados apresenta uma lista de termos do glossário correspondente à sua escolha. Se o usuário seleciona um termo do glossário da lista, o termo é adicionado ao elemento como uma etiqueta (também chamada de uma etiqueta do glossário). O utilizador também pode optar por criar uma nova etiqueta ao escrever um termo que não está a ser o glossário (também chamado de uma etiqueta de utilizador).

![Recurso de dados etiquetados com etiqueta de utilizador de uma e duas etiquetas de glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Etiquetas de utilizador são o único tipo de etiqueta suportada na edição gratuita do catálogo de dados.

### <a name="hover-behavior-on-tags"></a>Comportamento de passagem de mouse em etiquetas

No portal do catálogo de dados, os dois tipos de etiquetas são comportamentos de passagem de Mouse diferentes visualmente diferente e presentes. Quando coloque o cursor sobre uma etiqueta de utilizador, pode ver o texto da etiqueta e o utilizador ou utilizadores que adicionaram a marca. Quando coloque o cursor sobre uma etiqueta do glossário, também verá a definição de termo do glossário e um link para abrir o glossário comercial para ver a definição completa do termo.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa para etiquetas

Etiquetas de glossário e etiquetas de utilizador são pesquisáveis e pode aplicá-las como filtros numa pesquisa.

## <a name="summary"></a>Resumo

Ao utilizar o glossário comercial no catálogo de dados do Azure e a etiquetagem governados permite, pode identificar, gerir e detetar recursos de dados de forma consistente. O glossário comercial pode promover a aprendizagem do vocabulário de negócios por membros da organização. O glossário também suporta a captura de metadados significativos, que simplifica a deteção de recursos e compreensão.

## <a name="next-steps"></a>Passos Seguintes

* [Documentação da REST API para operações de glossário de negócios](/rest/api/datacatalog/data-catalog-glossary)
