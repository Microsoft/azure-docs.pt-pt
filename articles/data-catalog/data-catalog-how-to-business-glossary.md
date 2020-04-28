---
title: Configurar o glossário de negócios no Catálogo de Dados Azure
description: Como fazer o artigo destacando o glossário de negócios no Catálogo de Dados Azure para definir e utilizar um vocabulário de negócio comum para etiquetar ativos de dados registados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68976798"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Criar o glossário de negócios para a marcação governada

## <a name="introduction"></a>Introdução

O Azure Data Catalog permite a descoberta de fontes de dados, para que possa facilmente descobrir e compreender as fontes de dados que precisa para realizar análises e tomar decisões. Estas capacidades têm o maior impacto quando se pode encontrar e compreender a mais ampla gama de fontes de dados disponíveis.

Uma funcionalidade de Data Catalog que promove uma maior compreensão dos dados dos ativos é a marcação. Ao utilizar a marcação, pode associar palavras-chave a um ativo ou coluna, o que por sua vez facilita a descoberta do ativo através da pesquisa ou navegação. A marcação também o ajuda a entender mais facilmente o contexto e a intenção do ativo.

No entanto, a marcação pode, por vezes, causar problemas por si só. Alguns exemplos de problemas que a marcação pode introduzir são:

* A utilização de abreviaturas em alguns ativos e texto expandido noutros. Esta inconsistência dificulta a descoberta de ativos, embora a intenção fosse marcar os ativos com a mesma etiqueta.
* Variações potenciais no significado, dependendo do contexto. Por exemplo, uma etiqueta chamada *Receita* num conjunto de dados do cliente pode significar receita por parte do cliente, mas a mesma etiqueta num conjunto trimestral de dados de vendas pode significar receitas trimestrais para a empresa.  

Para ajudar a enfrentar estes e outros desafios semelhantes, o Data Catalog inclui um glossário de negócios.

Ao utilizar o glossário de negócios do Data Catalog, uma organização pode documentar termos de negócio chave e suas definições para criar um vocabulário de negócio comum. Esta governação permite a consistência na utilização de dados em toda a organização. Depois de definido um termo no glossário de negócios, pode ser atribuído a um ativo de dados no catálogo. Esta abordagem, *regida,* é a mesma abordagem que a marcação.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade e privilégios glossários

O glossário de negócios está disponível apenas na Edição Padrão do Catálogo de Dados Azure. A Edição Gratuita do Catálogo de Dados não inclui um glossário, e não fornece capacidades para a marcação governada.

Pode aceder ao glossário de negócios através da opção **Glossário** no menu de navegação do portal Data Catalog.  

![Catálogo de Dados - Aceda ao glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do Data Catalog e os membros da função de administradores glossários podem criar, editar e eliminar termos glossários no glossário do negócio. Todos os utilizadores do Data Catalog podem ver as definições de termo e etiquetar os ativos com termos glossários.

![Catálogo de Dados - Adicione um novo termo glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criação de termos glossários

Os administradores do Data Catalog e os administradores glossários podem criar termos glossários clicando no botão **New Term.** Cada termo glossário contém os seguintes campos:

* Uma definição de negócio para o termo
* Uma descrição que captura as regras de utilização ou negócios pretendidas para o ativo ou coluna
* Uma lista de interessados que mais sabem sobre o termo
* O termo-mãe, que define a hierarquia em que o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termo glossário

Utilizando o glossário de negócios do Data Catalog, uma organização pode descrever o seu vocabulário empresarial como uma hierarquia de termos, e pode criar uma classificação de termos que melhor representem a sua taxonomia empresarial.

Um termo deve ser único a um determinado nível de hierarquia. Nomes duplicados não são permitidos. Não há limite para o número de níveis numa hierarquia, mas muitas vezes uma hierarquia é mais facilmente compreendida quando há três níveis ou menos.

O uso de hierarquias no glossário empresarial é opcional. Deixar o campo de termo-mãe em branco para termos glossários cria uma lista plana (não hierárquica) de termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagging ativos com termos glossários

Depois de definidos termos glossários dentro do catálogo, a experiência de marcação de ativos é otimizada para pesquisar o glossário como um utilizador tipo uma etiqueta. O portal Data Catalog apresenta uma lista de termos glossários correspondentes para escolher. Se o utilizador selecionar um termo glossário da lista, o termo é adicionado ao ativo como etiqueta (também chamada de etiqueta glossária). O utilizador também pode optar por criar uma nova etiqueta digitando um termo que não está no glossário (também chamado de etiqueta de utilizador).

![Ativo de dados marcado com uma etiqueta de utilizador e duas etiquetas glossárias](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> As etiquetas de utilizador são o único tipo de etiqueta suportada na Edição Gratuita do Catálogo de Dados.

### <a name="hover-behavior-on-tags"></a>Hover comportamento em tags

No portal Data Catalog, os dois tipos de tags são visualmente distintos e apresentam diferentes comportamentos hover. Quando paira sobre uma etiqueta de utilizador, pode ver o texto da etiqueta e o utilizador ou utilizadores que adicionaram a etiqueta. Quando paira sobre uma etiqueta glossária, também vê a definição do termo glossário e um link para abrir o glossário de negócios para ver a definição completa do termo.

### <a name="search-filters-for-tags"></a>Pesquisar filtros para etiquetas

As etiquetas glossárias e as etiquetas de utilizador são pesquisáveis e pode aplicá-las como filtros numa pesquisa.

## <a name="summary"></a>Resumo

Ao utilizar o glossário de negócios no Catálogo de Dados Azure, e a marcação regida que permite, pode identificar, gerir e descobrir os ativos de dados de forma consistente. O glossário de negócios pode promover a aprendizagem do vocabulário empresarial por membros da organização. O glossário também suporta a captura de metadados significativos, o que simplifica a descoberta e compreensão de ativos.

## <a name="next-steps"></a>Passos seguintes

* [Documentação rest API para operações glossárias empresariais](/rest/api/datacatalog/data-catalog-glossary)
