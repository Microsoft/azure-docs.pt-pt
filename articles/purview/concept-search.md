---
title: Compreender os recursos de pesquisa em Azure Purview (pré-visualização)
description: Este artigo explica como o Azure Purview permite a descoberta de dados através de funcionalidades de pesquisa.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553967"
---
# <a name="understand-search-features-in-azure-purview"></a>Compreender recursos de pesquisa em Azure Purview

Este artigo fornece uma visão geral da experiência de pesquisa em Azure Purview. A pesquisa é uma capacidade de plataforma central de Purview, que alimenta a descoberta de dados e os dados usam experiências de governação numa organização.

## <a name="search"></a>Pesquisar

A experiência de pesquisa em Purview é alimentada por um índice de pesquisa gerido. Depois de uma fonte de dados ser registada no Purview, os seus metadados são indexados pelo serviço de pesquisa para permitir uma fácil descoberta. O índice fornece capacidades de relevância de pesquisa e completa os pedidos de pesquisa consultando milhões de ativos de metadados. A pesquisa ajuda-o a descobrir, compreender e a utilizar os dados para obter o maior valor dos mesmos.

A experiência de pesquisa em Purview é um processo de três etapas:

1. A caixa de pesquisa mostra o histórico que contém palavras-chave e ativos recentemente utilizados.
1. Quando começa a digitar as teclas, a pesquisa sugere as palavras-chave e os ativos correspondentes. 
1. A página de resultados de pesquisa é mostrada com ativos correspondentes à palavra-chave inserida.

## <a name="reduce-the-time-to-discover-data"></a>Reduza o tempo para descobrir dados

A descoberta de dados é o primeiro passo para uma análise de dados ou uma carga de trabalho de governação de dados para os consumidores de dados. A descoberta de dados pode ser morosa, porque pode não saber onde encontrar os dados que pretende. Mesmo depois de encontrar os dados, pode ter dúvidas sobre se pode ou não confiar nos dados e assumir uma dependência dos mesmos. 

O objetivo da pesquisa em Azure Purview é acelerar o processo de descoberta de dados, fornecendo gestos, para encontrar rapidamente os dados que importam.

## <a name="recent-search-and-suggestions"></a>Pesquisas e sugestões recentes

Muitas vezes, pode estar a trabalhar em vários projetos ao mesmo tempo. Para facilitar o retomar de projetos anteriores, a pesquisa em Purview fornece a capacidade de ver palavras-chave de pesquisa recentes e sugestões. Além disso, pode gerir o histórico de pesquisas recentes selecionando **Ver tudo** a partir da caixa de pesquisa drop-down.

## <a name="filters"></a>Filtros

Os filtros (também conhecidos como *facetas)* são concebidos para complementar a procura. Os filtros são mostrados na página de resultados de pesquisa. Os filtros na página de resultados de pesquisa incluem classificação, etiqueta de sensibilidade, fonte de dados e proprietários. Os utilizadores podem selecionar valores específicos num filtro para ver apenas os ativos de dados correspondentes e restringir o resultado da pesquisa aos ativos correspondentes.

## <a name="hit-highlighting"></a>Detetor de ocorrências

As palavras-chave correspondentes na página de resultados de pesquisa são realçadas para facilitar a identificação do motivo pelo qual um ativo de dados foi devolvido por pesquisa. A combinação de palavras-chave pode ocorrer em vários campos, tais como nome do ativo de dados, descrição e proprietário.

Pode não ser óbvio porque é que um ativo de dados é incluído na pesquisa, mesmo com o destaque do hit habilitado. Todas as propriedades são pesquisadas por padrão. Portanto, um ativo de dados pode ser devolvido devido a uma correspondência numa propriedade de nível de coluna. E como vários utilizadores podem anotar os ativos de dados com as suas próprias classificações e descrições, nem todos os metadados são apresentados na lista de resultados de pesquisa.

## <a name="sort"></a>Ordenar

Os utilizadores têm duas opções para classificar os resultados da pesquisa. Podem ordenar pelo nome do ativo ou por relevância de pesquisa por defeito.

## <a name="search-relevance"></a>Relevância de pesquisa

Relevância é a ordem de classificação padrão na página de resultados de pesquisa. A relevância da pesquisa encontra documentos que incluem a palavra-chave de pesquisa (alguns ou todos). Os ativos que contêm muitos casos da palavra-chave de pesquisa são classificados mais alto. Além disso, os mecanismos de pontuação personalizados são constantemente sintonizados para melhorar a relevância da pesquisa.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar uma conta Azure Purview no portal Azure](create-catalog-portal.md)
* [Quickstart: Criar uma conta Azure Purview utilizando Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Quickstart: Use o Estúdio De Visão](use-purview-studio.md)
