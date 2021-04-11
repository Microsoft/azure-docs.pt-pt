---
title: Configurar o glossário de negócios no Catálogo de Dados Azure
description: Como-a-artigo destacando o glossário de negócios no Catálogo de Dados Azure para definir e usar um vocabulário de negócio comum para marcar ativos de dados registados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 6137072b443d54dfc63c2be69e20228d428ad1b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674721"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Criar o glossário de negócios para marcação governada

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução

O Azure Data Catalog permite a descoberta de fontes de dados, para que possa facilmente descobrir e compreender as fontes de dados de que necessita para realizar análises e tomar decisões. Estas capacidades são o maior impacto quando você pode encontrar e entender a mais ampla gama de fontes de dados disponíveis.

Uma funcionalidade do Catálogo de Dados que promove uma maior compreensão dos dados dos ativos está a marcar. Ao utilizar a marcação, pode associar palavras-chave a um ativo ou a uma coluna, o que por sua vez facilita a descoberta do ativo através da pesquisa ou navegação. A marcação também o ajuda a entender mais facilmente o contexto e a intenção do ativo.

No entanto, a marcação pode, por vezes, causar problemas próprios. Alguns exemplos de problemas que a marcação pode introduzir são:

* A utilização de abreviaturas em alguns ativos e texto alargado noutros. Esta inconsistência dificulta a descoberta de ativos, embora a intenção fosse marcar os ativos com a mesma etiqueta.
* Variações potenciais no sentido, dependendo do contexto. Por exemplo, uma etiqueta chamada *Receita* num conjunto de dados do cliente pode significar receitas por cliente, mas a mesma etiqueta num conjunto de dados trimestrais de vendas pode significar receitas trimestrais para a empresa.  

Para ajudar a resolver estes e outros desafios semelhantes, o Data Catalog inclui um glossário de negócios.

Ao utilizar o glossário de negócios do Data Catalog, uma organização pode documentar os principais termos de negócio e as suas definições para criar um vocabulário de negócio comum. Esta governação permite a consistência no uso de dados em toda a organização. Depois de definido um termo no glossário de negócios, pode ser atribuído a um ativo de dados no catálogo. Esta abordagem, *a marcação governada,* é a mesma abordagem que a marcação.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade glossária e privilégios

O glossário de negócios está disponível apenas na Edição Standard do Catálogo de Dados Azure. A Edição Gratuita do Catálogo de Dados não inclui um glossário, e não fornece capacidades para a marcação governada.

Pode aceder ao glossário de negócios através da opção **Glossário** no menu de navegação do portal data Catalog.  

![Catálogo de Dados - Aceda ao glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do Catálogo de Dados e membros da função de administradores glossários podem criar, editar e eliminar termos glossários no glossário de negócios. Todos os utilizadores do Catálogo de Dados podem ver o termo definições e marcar ativos com termos glossários.

![Catálogo de Dados - Adicione um novo termo glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criação de termos glossários

Os administradores do Catálogo de Dados e os administradores glossários podem criar termos glossários clicando no botão **Novo Prazo.** Cada termo glossário contém os seguintes campos:

* Uma definição de negócio para o termo
* Uma descrição que captura as regras de uso ou de negócios previstas para o ativo ou coluna
* Uma lista de partes interessadas que sabem mais sobre o termo
* O termo-mãe, que define a hierarquia em que o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termo glossário

Ao utilizar o glossário de negócios do Data Catalog, uma organização pode descrever o seu vocabulário de negócio como uma hierarquia de termos, e pode criar uma classificação de termos que melhor representa a sua taxonomia de negócio.

Um termo deve ser único a um determinado nível de hierarquia. Nomes duplicados não são permitidos. Não há limite para o número de níveis numa hierarquia, mas uma hierarquia é muitas vezes mais facilmente compreendida quando há três níveis ou menos.

O uso de hierarquias no glossário de negócios é opcional. Deixar o termo-mãe em branco para termos glossários cria uma lista plana (não hierárquica) de termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcação de ativos com termos glossários

Após termos glossários terem sido definidos dentro do catálogo, a experiência de marcação de ativos é otimizada para pesquisar o glossário como um utilizador que escreve uma etiqueta. O portal data catalog apresenta uma lista de termos glossários correspondentes para escolher. Se o utilizador selecionar um termo glossário da lista, o termo é adicionado ao ativo como uma etiqueta (também chamada de etiqueta glossária). O utilizador também pode optar por criar uma nova etiqueta digitando um termo que não está no glossário (também chamado de etiqueta de utilizador).

![Ativo de dados marcado com uma etiqueta de utilizador e duas etiquetas glossárias](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> As etiquetas de utilizador são o único tipo de etiqueta suportada na Edição Gratuita do Catálogo de Dados.

### <a name="hover-behavior-on-tags"></a>Hover comportamento em tags

No portal data catalog, os dois tipos de tags são visualmente distintos e apresentam diferentes comportamentos de hover. Quando paira sobre uma etiqueta de utilizador, pode ver o texto da etiqueta e o utilizador ou utilizadores que adicionaram a etiqueta. Quando se paira sobre uma etiqueta glossária, você também vê a definição do termo glossário e um link para abrir o glossário de negócios para ver a definição completa do termo.

### <a name="search-filters-for-tags"></a>Procurar filtros para tags

As etiquetas glossárias e as etiquetas de utilizador são pesmáveis e pode aplicá-las como filtros numa pesquisa.

## <a name="summary"></a>Resumo

Ao utilizar o glossário de negócios no Catálogo de Dados Azure, e a marcação governada que permite, pode identificar, gerir e descobrir os ativos de dados de forma consistente. O glossário de negócios pode promover a aprendizagem do vocabulário empresarial por membros da organização. O glossário também suporta a captura de metadados significativos, o que simplifica a descoberta e compreensão dos ativos.

## <a name="next-steps"></a>Passos seguintes

* [Documentação DA API PARA operações de glossário de negócios](/rest/api/datacatalog/data-catalog-glossary)
