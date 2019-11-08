---
title: Diretrizes de SEO do Azure Marketplace
description: Fornece orientação sobre como maximizar a SEO (otimização do mecanismo de pesquisa).
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pabutler
ms.openlocfilehash: 7115798faadc3209413d22a384433417ec0ddff0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819572"
---
# <a name="azure-marketplace-seo-guidance"></a>Diretrizes de SEO do Azure Marketplace

Este artigo explica como maximizar a descoberta da sua oferta por meio da funcionalidade de pesquisa no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Explicação geral do algoritmo

O Microsoft Marketplaces utiliza o Pesquisa Cognitiva do Azure para capacitar os recursos de pesquisa do site. O algoritmo é baseado na frequência de termo – frequência de documento inversa ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). O [analisador Lucene](https://lucene.apache.org/core/) padrão é usado.

Em geral, todos os campos de texto, categorias e setores e incluídos no peso da relevância. Os termos especializados que são usados raramente por aplicativos, mas frequentemente em seu aplicativo, gerarão uma pontuação de correspondência mais alta com a pesquisa. Assim, incluir termos como "VM" ofereceria pouco benefício, ao passo que "Azure Search" seria muito mais especializado.
Abaixo estão os campos mais relevantes a serem considerados.

 
|  Campo                   | Porta | Orientação                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nome da Oferta               |  Elevado      | Exato ou próximo de uma correspondência completa com a consulta de pesquisa resultará em alta classificação.                       |
| Nome do Editor           |  Elevado      | Exato ou próximo de uma correspondência completa com a consulta de pesquisa resultará em alta classificação.                       |
| Descrição resumida        |  Médio    | A nomenclatura fornecida de aplicativos e nomes de Publicador quase garantirá uma classificação alta, talvez não seja a mais relevante. Nesse caso, uma breve descrição é crítica. Mantenha o texto conciso e para o ponto. Palavras-chave e termos de pesquisa esperados devem ser incluídos para obter o melhor resultado.  Por exemplo, "Este é o melhor Retail POS criado totalmente sobre o Dynamics 365" é menos eficaz do que o "Retail POS (ponto de venda) para o Dynamics 365".  | 
| Descrição longa         |  Baixa       | A descrição oferece uma maneira de entrar em mais detalhes. As descrições mais eficazes são de tamanho razoável e palavras-chave são usadas.  Uma descrição para o ponto que usa palavras-chave beneficiará mais do que o longo, o texto demorado. Verifique se os termos principais, como "IoT", estão presentes na descrição.  |
| Categorias de Produtos       | Médio     |  As categorias de produtos são determinadas por uma combinação de opções de Publicador e da Microsoft. Selecione essas categorias adequadamente para que os usuários possam encontrar facilmente os aplicativos na categoria correta. |
|  |  |  |


## <a name="other-tips"></a>Outras dicas

-   A pesquisa sugere obter atividade pesada do usuário. Ele prioriza correspondências com o nome/editor do aplicativo. Descrição curta se torna o campo de chave para quando o termo de pesquisa não é uma correspondência exata com o nome do aplicativo/Publicador.
-   Os documentos para download não estão incluídos no peso da pesquisa.
-   A aquisição e o uso reais de seus aplicativos também afetarão a classificação da pesquisa. Por exemplo, dois aplicativos equivalentes em que um tem muito mais usuários receberão uma classificação mais alta.
