---
title: Descubra e explore dados em ADF usando a Purview
description: Saiba como descobrir, explore dados na Azure Data Factory usando a Purview
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386276"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Descubra e explore dados em ADF usando a Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste artigo, registará uma Conta Azure Purview para uma Fábrica de Dados. Essa ligação permite-lhe descobrir os ativos do Azure Purview e interagir com eles através das capacidades da ADF. 

Pode executar as seguintes tarefas na ADF: 
- Use a caixa de pesquisa no topo para encontrar os ativos do Purview com base em palavras-chave 
- Compreender os dados com base em metadados, linhagens, anotações 
- Ligue esses dados à sua fábrica de dados com serviços ou conjuntos de dados ligados 

## <a name="prerequisites"></a>Pré-requisitos 
- [Conta Azure Purview](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Ligue uma conta de imagem Azure à Fábrica de Dados](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Utilização da Azure Purview na Fábrica de Dados 

A utilização Azure Purview na Data Factory requer que tenha acesso a essa conta Purview. Data Factory passa através da sua permissão de Purview. Como exemplo, se tiver um papel de curador, poderá editar metadados digitalizados pela Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Descoberta de dados: conjuntos de dados de pesquisa 

Para descobrir dados registados e digitalizados pela Azure Purview, pode utilizar a barra de pesquisa no centro superior do portal Data Factory. Certifique-se de que seleciona O Azure Purview para procurar todos os dados da sua organização. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Screenshot para executar sobre conjuntos de dados.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Ações que pode realizar ao longo de conjuntos de dados com recursos da Data Factory 
Pode criar diretamente o Linked Service, Dataset ou dataflow sobre os dados que procura por Azure Purview.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Screenshot que mostra como pode criar diretamente Linked Service, Dataset ou dataflow sobre os dados que procura por Azure Purview.":::

##  <a name="nextsteps"></a>Próximos passos 

- [Registar e digitalizar os ativos da Azure Data Factory em Azure Purview](../purview/register-scan-azure-synapse-analytics.md)
- [Como pesquisar dados em Azure Purview Data Catalog](../purview/how-to-search-catalog.md)