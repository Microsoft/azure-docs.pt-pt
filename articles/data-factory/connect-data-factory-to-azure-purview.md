---
title: Ligar um Data Factory ao Azure Purview
description: Saiba como ligar uma Fábrica de Dados ao Azure Purview
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 36eac4c60e0d7fd54ec304f0f17c2ecb454f0629
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063454"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Ligue a Fábrica de Dados à Azure Purview (Pré-visualização)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como ligar a Data Factory ao Azure Purview e como reportar a linhagem de dados das atividades da Azure Data Factory Copiar dados, fluxo de dados e executar pacote SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Ligue a Fábrica de Dados à Azure Purview
O Azure Purview é um novo serviço de cloud para uso pelos utilizadores de dados que gerem centralmente a governação de dados em todos os seus dados, abrangendo ambientes em nuvem e on-prem. Pode ligar a sua Data Factory ao Azure Purview e a ligação permite-lhe utilizar o Azure Purview para capturar dados de linhagem do pacote Copy, Data flow e Execute SSIS. Tem duas formas de ligar a fábrica de dados à Azure Purview:
### <a name="register-azure-purview-account-to-data-factory"></a>Registar conta Azure Purview para a Data Factory
1. No portal ADF, vá ao **Manage**  ->  **Azure Purview**. Selecione **Ligar a uma conta Desemis.** 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Screenshot para registar uma conta Desemis.":::
2. Pode escolher **a partir da subscrição Azure** ou entrar **manualmente.** **A partir da subscrição do Azure,** pode selecionar a conta a que tem acesso. 
3. Uma vez ligado, deverá poder ver o nome da conta ''s Purview' na **conta 'Face ao's.** 
4. Pode utilizar a barra de pesquisa no centro superior do portal Azure Data Factory para procurar dados. 

Se vir um aviso no portal Azure Data Fator depois de registar a conta Azure Purview na Data Factory, siga os passos abaixo para corrigir o problema:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Screenshot para aviso de registo de uma conta Desaview.":::

1. Vá ao portal Azure e encontre a sua fábrica de dados. Escolha a secção "Tags" e veja se existe uma etiqueta chamada **catalogUri.** Caso contrário, desligue e reconecte a conta Azure Purview no portal ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Screenshot para tags de registar uma conta Purview.":::

2. Verifique se a permissão é concedida para registar uma conta Azure Purview na Data Factory. Veja [como ligar a Azure Data Factory e a Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Registar fábrica de dados em Azure Purview
Para saber como registar a Data Factory em Azure Purview, consulte [Como ligar a Azure Data Factory e a Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Reportar dados de linhagem à Azure Purview
Quando os clientes executam a atividade de copy, data flow ou executar pacote SSIS na Azure Data Factory, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para obter a linhagem da Azure Data Factory, consulte a linhagem da [fábrica de dados.](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)

## <a name="next-steps"></a>Passos seguintes
[Guia do utilizador da linhagem do catálogo](../purview/catalog-lineage-user-guide.md)

[Tutorial: Empurrar dados de linhagem da Fábrica de Dados para a Azure Purview](turorial-push-lineage-to-purview.md)