---
title: Gerir fontes de dados em Azure Purview (Pré-visualização)
description: Saiba como registar novas fontes de dados, gerir recolhas de fontes de dados e ver fontes em Azure Purview (Preview).
author: mamccrea
ms.author: mamccrea
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: ef54c40715c874364beb188d8850a9ce953aa112
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554622"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Gerir fontes de dados em Azure Purview (Pré-visualização)

Neste artigo, aprende-se a registar novas fontes de dados, a gerir recolhas de fontes de dados e a visualizar fontes em Azure Purview (Preview). A Azure Purview suporta as seguintes fontes de dados:

* Servidor SQL no local
* Armazenamento do Azure Data Lake Ger1 
* Armazenamento do Azure Data Lake Ger2
* Armazenamento de Blobs do Azure
* Azure Data Explorer
* BD SQL do Azure
* Azure SQL DB Caso Gerido
* Azure Synapse Analytics (anteriormente SQL DW)
* Azure Cosmos DB
* Power BI

## <a name="register-a-new-source"></a>Registar uma nova fonte

Utilize os seguintes passos para registar uma nova fonte.

1. Abra o Estúdio de Visão e selecione o **azulejo de fontes de registo.**

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. Selecione **Registar-se** e, em seguida, selecione um tipo de origem. Este exemplo utiliza o Azure Blob Storage. Selecione **Continuar**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Selecione um tipo de fonte de dados na página 'Fontes registo'":::

1. Preencha o formulário na página **'Fontes do Registo'.** Selecione um nome para a sua fonte e insira as informações relevantes. Se escolheu **a subscrição do Azure** como método de seleção da sua conta, as fontes da sua subscrição aparecem numa lista de dropdown. Em alternativa, pode introduzir manualmente as informações da sua fonte.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulário para informações sobre fontes de dados":::

1. Selecione **Concluir**.

## <a name="view-sources"></a>Ver fontes

Pode ver todas as fontes registadas no separador **Fontes** do Azure Purview Studio. Existem dois tipos de vista: vista para mapa e vista de lista.

### <a name="map-view"></a>Vista do mapa

Na vista do Mapa, pode ver todas as suas fontes e coleções. Na imagem a seguir, existe uma fonte de armazenamento Azure Blob. A partir de cada azulejo de origem, pode editar a fonte, iniciar uma nova digitalização ou ver detalhes da origem.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Vista do mapa de fonte de dados do Azure Purview":::

### <a name="list-view"></a>Vista de lista

Na vista lista, pode ver uma lista de fontes ordenada. Passe sobre a fonte para opções para editar, iniciar uma nova digitalização ou apagar.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Vista da lista de fontes de dados do Azure Purview":::

## <a name="manage-collections"></a>Gerir coleções

Pode agrupar as suas fontes de dados em coleções. Para criar uma nova coleção, selecione **+ Nova coleção** na página *Fontes* do Azure Purview Studio. Dê à coleção um nome e *selecione Nenhum* como o Pai. A nova coleção aparece na vista do mapa.

Para adicionar fontes a uma coleção, selecione o lápis **Editar** na fonte e escolha uma coleção no menu **Select a collection** drop-down.

Para criar uma hierarquia de coleções, atribuir coleções de nível superior como pai a coleções de nível inferior. Na imagem seguinte, *Fabrikam* é um dos pais da coleção *Finance,* que contém uma fonte de dados de armazenamento Azure Blob. Pode colapsar ou expandir as coleções clicando no círculo ligado à seta entre os níveis.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Uma hierarquia de coleções no Azure Purview Studio":::

Pode remover fontes de uma hierarquia selecionando *Nenhuma* para o progenitor. Fontes não-envisenciais estão agrupadas numa caixa pontilhada na vista do mapa, sem setas que as liguem aos pais.

## <a name="next-steps"></a>Passos seguintes

Saiba como registar e digitalizar várias fontes de dados:

* [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)
* [Inquilino do Power BI](register-scan-power-bi-tenant.md)
* [Base de Dados SQL do Azure](register-scan-azure-sql-database.md)
