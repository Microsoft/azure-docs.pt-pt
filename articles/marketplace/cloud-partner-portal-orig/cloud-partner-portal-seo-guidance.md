---
title: Orientação do SEO do Azure Marketplace
description: Fornece orientações para maximizar a otimização do motor de busca (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280155"
---
# <a name="azure-marketplace-seo-guidance"></a>Orientação do SEO do Azure Marketplace

Este artigo explica como maximizar a descoberta da sua oferta através da funcionalidade de pesquisa no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Explicação geral do algoritmo

Os mercados da Microsoft utilizam a Pesquisa Cognitiva Azure para alimentar as capacidades de pesquisa do site. O algoritmo baseia-se na frequência de documentos inversos de frequência de termo[(TF-IDF).](https://en.wikipedia.org/wiki/Tf–idf) O [Analisador Lucene](https://lucene.apache.org/core/) padrão é usado.

Em geral, todos os campos de texto, categorias e indústrias e incluídos na ponderação da relevância. Termos especializados que são utilizados com pouca frequência por apps mas que frequentemente na sua aplicação gerarão uma pontuação de correspondência mais alta com pesquisa. Assim, incluir termos como "VM" ofereceria poucos benefícios, enquanto que a "pesquisa Azure" seria muito mais especializada.
Abaixo estão os campos mais relevantes a considerar.

 
|  Campo                   | Importância | Orientação                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nome da Oferta               |  Alta      | Exatamente ou perto de um jogo completo com consulta de pesquisa vai render alta classificação.                       |
| Nome do Editor           |  Alta      | Exatamente ou perto de um jogo completo com consulta de pesquisa vai render alta classificação.                       |
| Descrição Breve        |  Médio    | Dado o nome de apps e nomes de editores quase garantirá um alto ranking, pode não ser o mais relevante. Neste caso, uma breve descrição é crítica. Mantenha o texto conciso e ao ponto. As palavras-chave e os termos de pesquisa esperados devem ser incluídos para melhor resultado.  Por exemplo, "Este é o melhor POS de retalho construído totalmente em cima da Dynamics 365" é menos eficaz do que "Retail POS (ponto de venda) para a Dynamics 365".  | 
| Descrição longa         |  Baixa       | A descrição oferece uma maneira de entrar em mais profundidade. As descrições mais eficazes são de comprimento razoável e palavras-chave são usadas.  Uma descrição a ponto utilizando palavras-chave beneficiará mais do que um texto longo e longo. Certifique-se de que os termos-chave, como "IoT", estão presentes na descrição.  |
| Categorias de Produtos       | Médio     |  As categorias de produtos são determinadas por uma combinação de escolhas de editores e Microsoft. Selecione estas categorias adequadamente para que os utilizadores possam facilmente encontrar as aplicações na categoria correta. |
|  |  |  |


## <a name="other-tips"></a>Outras Dicas

-   A pesquisa sugere que obtém uma atividade pesada do utilizador. Dá prioridade aos jogos com o nome/editor da aplicação. A descrição curta torna-se o campo-chave para quando o termo de pesquisa não é uma correspondência exata com o nome editor/app.
-   Os documentos para download não estão incluídos na ponderação da pesquisa.
-   A aquisição e utilização reais das suas aplicações também terá impacto no ranking de pesquisa. Por exemplo, duas aplicações equivalentes onde uma tem muito mais utilizadores obterão um ranking mais elevado.
