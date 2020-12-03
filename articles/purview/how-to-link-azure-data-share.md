---
title: Ligar à Azure Data Share
description: Este artigo descreve como ligar uma conta Azure Data Share com a Azure Purview para pesquisar ativos e rastrear a linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555362"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Como ligar a Azure Data Share e a Azure Purview

Este artigo discute como ligar a sua conta [Azure Data Share](../data-share/overview.md) com a Azure Purview e governar os conjuntos de dados partilhados (tanto de saída como de entrada) na sua propriedade de dados. Várias personalidades de governação de dados podem descobrir e rastrear a linhagem de dados através de fronteiras como organizações, departamentos e até centros de dados.

## <a name="common-scenarios"></a>Cenários Comuns

A Linhagem de Partilha de Dados destina-se a fornecer informações detalhadas para a análise de causa de raiz e análise de impacto.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Cenário 1: 360 visualização de conjuntos de dados partilhados dentro/fora para uma organização parceira ou departamento interno

Os oficiais de dados podem ver uma lista de todos os conjuntos de dados que são partilhados bi-direcionalmente com as suas organizações parceiras. Eles podem pesquisar e descobrir os conjuntos de dados pelo nome da organização e ver uma visão completa de todas as ações de saída e entrada.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Cenário 2: Análise da causa raiz - dependência a montante dos conjuntos de dados que entram na organização (visão do consumidor das ações recebidas)

Um relatório tem informações incorretas devido a problemas de dados a montante de uma conta de Partilha de Dados externa. Os engenheiros de dados podem compreender as falhas a montante, ser informados sobre as razões, e contactar ainda mais o proprietário da ação para corrigir os problemas que causam a sua discrepância de dados.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Cenário 3: Análise de impacto em conjuntos de dados que vão para fora da organização (visão do fornecedor das ações cessantes)

Os produtores de dados querem saber quem será impactado ao alterar o seu conjunto de dados. Utilizando a linhagem, um produtor de dados pode facilmente compreender o impacto dos parceiros internos ou externos a jusante que estão a consumir dados usando a Azure Data Share.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share e Experiência conectada de Purview

Para ligar a sua Azure Data Share e a conta Azure Purview, faça o seguinte:

1. Criar uma conta Desemis. Todas as informações da linhagem Data Share serão recolhidas por uma conta Purview. Pode utilizar uma existente ou criar uma nova conta Purview.

1. Ligue a sua Partilha de Dados Azure à sua conta Purview.

    1. No portal 'Toview', pode ir ao **Centro de Gestão** e ligar a sua Partilha de Dados Azure na secção **de ligações externas.**
    1. Selecione **+ Novo** na barra superior, encontre a sua Partilha de Dados Azure na barra lateral pop-up e adicione a conta Data Share. Executar um trabalho instantâneo depois de ligar a sua Partilha de Dados à conta Purview, para que os ativos e informações de linhagem de Data Share seja visível em Purview.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Centro de gestão para ligar a Azure Data Share":::

1. Execute a sua foto instantânea em Azure Data Share.

    - Uma vez estabelecida a ligação de partilha Azure Data com o Azure Purview, pode executar uma imagem instantânea para as suas ações existentes. 
    - Se não tiver ações existentes, vá ao portal Azure Data Share para [partilhar os seus dados](../data-share/share-your-data.md) [e subscrever uma partilha de dados.](../data-share/subscribe-to-data-share.md)
    - Uma vez que o instantâneo de ações esteja completo, pode ver os ativos e a linhagem de Partilha de Dados associados em Purview.

1. Descubra contas de partilha de dados e partilhe informações na sua conta Purview.

    - Na página inicial da conta 'Purview', **selecione Procurar por tipo de ativo** e selecione o azulejo **Azure Data Share.** Pode procurar um nome de conta, nome de partilha, partilha de instantâneos ou organização de parceiros. Caso contrário, aplique filtros na página de resultados de pesquisa para nome de conta, tipo de partilha (enviado vs ações recebidas).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Partilha de dados Azure na página de resultados de pesquisa":::

    >[!Important]
    >Para que os ativos da Partilha de Dados apareçam em Purview, um trabalho instantâneo deve ser executado depois de ligar a sua Partilha de Dados à Purview.

1. Rastreio da linhagem de conjuntos de dados partilhados com a Azure Data Share.

    - A partir da página de resultados de pesquisa de Purview, escolha o instantâneo de partilha de dados (recebido/enviado) e selecione o **separador Linhagem,** para ver um gráfico de linhagem com dependências a montante e a jusante.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Linhagem de conjuntos de dados partilhados usando a Azure Data Share":::

## <a name="next-steps"></a>Passos seguintes

- [Guia do utilizador da linhagem do catálogo](catalog-lineage-user-guide.md)
- [Link para Azure Data Factory para linhagem](how-to-link-azure-data-factory.md)
