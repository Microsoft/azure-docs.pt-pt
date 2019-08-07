---
title: Configurar o Glossário de negócios no catálogo de dados do Azure
description: Artigo de instruções destacando o Glossário de negócios no catálogo de dados do Azure para definir e usar um vocabulário de negócios comum para marcar ativos de dados registrados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6ecb481245484332221e45b8f27c776291ea532d
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736400"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Configurar o Glossário de negócios para marcação governada

## <a name="introduction"></a>Introdução

O catálogo de dados do Azure permite a descoberta de fonte de dados, para que você possa descobrir e entender facilmente as fontes de dados necessárias para realizar a análise e tomar decisões. Esses recursos têm o maior impacto quando você pode encontrar e entender a maior variedade de fontes de dados disponíveis.

Um recurso de catálogo de dados que promove maior compreensão dos dados de ativos é a marcação. Ao usar a marcação, você pode associar palavras-chave a um ativo ou a uma coluna, o que, por sua vez, torna mais fácil descobrir o ativo por meio de pesquisa ou navegação. A marcação também ajuda você a entender mais facilmente o contexto e a intenção do ativo.

No entanto, a marcação pode, às vezes, causar problemas. Alguns exemplos de problemas que a marcação pode introduzir são:

* O uso de abreviações em alguns ativos e textos expandidos em outros. Essa inconsistência impede a descoberta de ativos, embora a intenção seja marcar os ativos com a mesma marca.
* Variações em potencial no significado, dependendo do contexto. Por exemplo, uma marca chamada *receita* em um conjunto de dados do cliente pode significar a receita por cliente, mas a mesma marca em um conjunto de dado de vendas trimestral pode significar receita trimestral para a empresa.  

Para ajudar a resolver esses e outros desafios semelhantes, o catálogo de dados inclui um glossário de negócios.

Usando o Glossário de negócios do catálogo de dados, uma organização pode documentar os principais termos de negócios e suas definições para criar um vocabulário de negócios comum. Essa governança permite a consistência no uso de dados em toda a organização. Depois que um termo é definido no Glossário de negócios, ele pode ser atribuído a um ativo de dados no catálogo. Essa abordagem, *marcação governada*, é a mesma abordagem que a marcação.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade e privilégios do glossário

O Glossário de negócios está disponível apenas na edição Standard do catálogo de dados do Azure. A edição gratuita do catálogo de dados não inclui um glossário e não fornece recursos para marcação controlada.

Você pode acessar o Glossário de negócios por meio da opção **Glossário** no menu de navegação do portal do catálogo de dados.  

![Acessando o Glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do catálogo de dados e os membros da função Administradores do glossário podem criar, editar e excluir os termos do glossário no Glossário de negócios. Todos os usuários do catálogo de dados podem exibir as definições de termo e os ativos de marca com os termos do glossário.

![Adicionando um novo termo do glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criando termos do glossário

Os administradores do catálogo de dados e os administradores do glossário podem criar termos do glossário clicando no botão **novo termo** . Cada termo do glossário contém os seguintes campos:

* Uma definição de negócios para o termo
* Uma descrição que captura o uso pretendido ou as regras de negócio para o ativo ou coluna
* Uma lista de participantes que conhecem mais informações sobre o termo
* O termo pai, que define a hierarquia na qual o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termos do glossário

Usando o Glossário de negócios do catálogo de dados, uma organização pode descrever seu vocabulário de negócios como uma hierarquia de termos e pode criar uma classificação de termos que melhor represente sua taxonomia de negócios.

Um termo deve ser exclusivo em um determinado nível de hierarquia. Nomes duplicados não são permitidos. Não há nenhum limite para o número de níveis em uma hierarquia, mas uma hierarquia geralmente é compreendida mais facilmente quando há três níveis ou menos.

O uso de hierarquias no Glossário de negócios é opcional. Deixar o campo de termo pai em branco para termos de Glossário cria uma lista simples (não hierárquica) de termos no Glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcando ativos com os termos do glossário

Depois que os termos do glossário tiverem sido definidos no catálogo, a experiência de ativos de marcação será otimizada para pesquisar o Glossário, pois um usuário digita uma marca. O portal do catálogo de dados exibe uma lista de termos de Glossário correspondentes para sua escolha. Se o usuário selecionar um termo de glossário na lista, o termo será adicionado ao ativo como uma marca (também chamada de marca de Glossário). O usuário também pode optar por criar uma nova marca digitando um termo que não está no Glossário (também chamado de marca de usuário).

![Ativo de dados marcado com uma marca de usuário e duas marcas de Glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> As marcas de usuário são o único tipo de marca com suporte na edição gratuita do catálogo de dados.

### <a name="hover-behavior-on-tags"></a>Comportamento de focalização nas marcas

No portal do catálogo de dados, os dois tipos de marcas são visualmente distintos e apresentam comportamentos de foco diferentes. Ao passar o mouse sobre uma marca de usuário, você pode ver o texto da marca e o usuário ou os usuários que adicionaram a marca. Ao passar o mouse sobre uma marca de Glossário, você também verá a definição do termo do glossário e um link para abrir o Glossário de negócios para exibir a definição completa do termo.

### <a name="search-filters-for-tags"></a>Pesquisar filtros para marcas

Marcas de glossário e marcas de usuário são pesquisáveis e você pode aplicá-las como filtros em uma pesquisa.

## <a name="summary"></a>Resumo

Usando o Glossário de negócios no catálogo de dados do Azure e a marcação controlada que ele habilita, você pode identificar, gerenciar e descobrir ativos de dados de maneira consistente. O Glossário de negócios pode promover o aprendizado do vocabulário de negócios por membros da organização. O Glossário também dá suporte à captura de metadados significativos, o que simplifica a descoberta de ativos e a compreensão.

## <a name="next-steps"></a>Passos Seguintes

* [Documentação da API REST para operações de Glossário de negócios](/rest/api/datacatalog/data-catalog-glossary)
