---
title: Descubra, conecte e explore dados em Sinapse usando a Azure Purview
description: Guia sobre como descobrir dados, conectá-los e explorá-los em Sinapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c6b25fd3615fa76bc76e6d360f4c76a21a9ad02
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918401"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Descubra, conecte e explore dados em Sinapse usando a Azure Purview 

> [!IMPORTANT]
> A integração entre a Azure Synapse Analytics e a Azure Purview está atualmente em Pré-Visualização. Se estiver interessado em experimentar o Azure Purview em Synapse, por favor, conecte-se com o seu Representante de Vendas da Microsoft. 

Neste documento, você vai aprender o tipo de interações que você pode realizar ao registar uma Conta Azure Purview em Synapse. 

## <a name="prerequisites"></a>Pré-requisitos 

- [Conta Azure Purview](../../purview/create-catalog-portal.md) 
- [Espaço de trabalho sinapse](../quickstart-create-workspace.md) 
- [Ligue uma conta de visão Azure à Sinapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Usando a azure purview em Synapse 

A utilização Azure Purview em Synapse requer que tenha acesso a essa conta Purview. Sinapse passa através da sua permissão de Purview. Como exemplo, se tiver um papel de curador, poderá editar metadados digitalizados pela Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Descoberta de dados: conjuntos de dados de pesquisa 

Para descobrir dados registados e digitalizados pela Azure Purview, pode utilizar a barra de pesquisa no centro superior do espaço de trabalho da Synapse. Certifique-se de que seleciona O Azure Purview para procurar todos os dados da sua organização. 

## <a name="azure-purview-actions"></a>Ações de Azure Purview 

Aqui está uma lista das funcionalidades do Azure Purview que estão disponíveis na Sinapse: 
- **Visão geral** dos metadados 
- Ver e editar **esquema** dos metadados com classificações, termos glossários, tipos de dados e descrições 
- Ver **linhagem** para entender as dependências e fazer análise de impacto. Para mais informações sobre, consulte [a linhagem](../../purview/catalog-lineage-user-guide.md)
- Ver e editar **Contactos** para saber quem é proprietário ou especialista em um conjunto de dados 
- **Relacionado** a compreender as dependências hierárquicas de um conjunto de dados específico. Esta experiência é útil para navegar através da hierarquia de dados.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Ações que pode realizar sobre conjuntos de dados com recursos sinapses 

### <a name="connect-data-to-synapse"></a>Ligar dados à Sinapse 

- Pode criar um **novo serviço ligado** à Synapse. Essa ação será necessária para copiar dados para a Sinapse ou tê-los no seu centro de dados (para fontes de dados suportadas como o ADLSg2) 
- Para objetos como ficheiros, pastas ou tabelas, pode criar diretamente um **novo conjunto de dados de integração** e alavancar um serviço ligado existente se já for criado 

Ainda não podemos deduzir se existe um conjunto de dados de serviço ou integração existente. 

###  <a name="develop-in-synapse"></a>Desenvolver em Sinapse 

Existem três ações que podes realizar: **Novo Script SQL,** **Novo Caderno** e Novo Fluxo **de Dados.** 

Com **o Novo Script SQL,** dependendo do tipo de suporte, pode: 
- Veja as 100 melhores linhas para entender a forma dos dados. 
- Criar uma tabela externa a partir da base de dados Sinapse SQL 
- Carregue os dados numa base de dados Sinapse SQL 
 
Com **novo caderno,** pode: 
- Carregue os dados num DataFrame de Faíscas 
- Crie uma Mesa de Faísca (se fizer isso em formato Parquet, também cria uma mesa de bilhar SQL sem servidor). 
 
Com **o novo fluxo de dados,** pode criar um conjunto de dados de integração que pode ser usado como fonte num pipeline de fluxo de dados. O fluxo de dados é uma capacidade de desenvolvedor sem código para realizar a transformação de dados. Para mais informações sobre [a utilização do fluxo de dados na Sinapse.](../quickstart-data-flow.md)

##  <a name="nextsteps"></a>Próximos passos 

- [Registar e digitalizar os ativos da Azure Synapse em Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Como pesquisar dados em Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)