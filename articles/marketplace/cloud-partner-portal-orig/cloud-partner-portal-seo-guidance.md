---
title: Guia de publicador do Azure Marketplace SEO | Documentos da Microsoft
description: Fornece orientações sobre maximizando a otimização do mecanismo de pesquisa (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cacc7c0b269e8006903961049caf3cd7e3bee449
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834341"
---
<a name="azure-marketplace-seo-publisher-guide"></a>Guia de publicador do Azure Marketplace SEO
=======================================

### <a name="general-explanation-of-algorithm"></a>Geral explicação do algoritmo

O marketplace utiliza o Azure Search para alimentar as capacidades de pesquisa do site. O algoritmo baseia-se a frequência de documento de frequência – inverso do termo ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). A norma [analisador de Lucene](https://lucene.apache.org/core/) é utilizado.

Em geral, todo o texto de campos, categorias e setores e incluídos no weightage da relevância. Termos especializados que são utilizados com pouca frequência por aplicações, mas com frequência na sua aplicação irão gerar uma melhor classificação de correspondência com a pesquisa. Então, incluindo termos como "VM" ofereceria pouco benefício, ao passo que "O Azure search" seria muito mais especializado.
Seguem-se os campos mais relevantes a ter em consideração.

 
|  Campo                   | Importância | Orientação                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nome da Oferta               |  Elevado      | EXACT ou próximo de uma correspondência completa com a pesquisa consulta resultará em classificação elevada.                       |
| Nome do Publicador           |  Elevado      | EXACT ou próximo de uma correspondência completa com a pesquisa consulta resultará em classificação elevada.                       |
| Breve descrição        |  Médio    | Dada a nomenclatura de aplicações e o publicador nomes quase irão garantir que uma classificação alta, talvez não seja o mais relevante. Neste caso, uma descrição breve é fundamental. Mantenha o texto conciso e para o ponto. Palavras-chave e os termos de pesquisa esperado devem ser incluídos para melhor resultado.  Por exemplo "Este é o melhor POS de varejo baseado-se totalmente no Dynamics 365" será menos eficiente do que "o POS de varejo (ponto de venda) para o Dynamics 365".  | 
| Descrição longa         |  Baixa       | Descrição oferece uma forma de entrar em mais detalhes. As descrições mais eficazes são de razoável e palavras-chave são utilizadas.  Uma descrição para ponto usando palavras-chave se beneficiarão mais do que há muito tempo, texto longo. Certifique-se de que a chaves termos, como "IoT", estão presentes na descrição.  |
| Categorias de Produtos       | Médio     |  Categorias de produtos são determinadas por uma combinação de opções do publicador e a Microsoft. Selecione estas categorias apropriadamente para que os usuários podem facilmente encontrar as aplicações na categoria correta. |
|  |  |  |


### <a name="other-tips"></a>Outras dicas

-   Pesquisa sugere obtém a atividade do utilizador pesada. Atribui prioridades aos coincide com o nome/fabricante da aplicação. Breve descrição torna-se o campo de chave para quando o termo de pesquisa não é uma correspondência exata com o nome do publicador/aplicação.
-   Documentos para download não estão incluídos na pesquisa weightage.
-   Sua e utilização de aquisição de real de aplicações irão afetar a classificação de pesquisa também. Por exemplo, duas aplicações equivalentes em que uma tem muito mais utilizadores obterão uma classificação mais elevada.
