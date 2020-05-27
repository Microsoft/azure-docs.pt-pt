---
title: Obtenha mais itens ou registos com paginação
description: Configurar a paginação para exceder o limite de tamanho da página padrão para ações de conector em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 9f114dd0428e13b3e1a205fea353b38b1f8a6f97
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835364"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenha mais dados, itens ou registos usando paginação em Aplicações Lógicas Azure

Quando recuperar dados, itens ou registos utilizando uma ação de conector em [Aplicações Lógicas Azure,](../logic-apps/logic-apps-overview.md)poderá obter conjuntos de resultados tão grandes que a ação não devolve todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho da página padrão do conector. Neste caso, a ação devolve apenas a primeira página dos resultados. Por exemplo, o tamanho da página padrão para a ação do conector SQL Server **Get rows** é 2048, mas pode variar com base em outras definições.

Algumas ações permitem ligar um ajuste de *paginação* para que a sua aplicação lógica possa obter mais resultados até ao limite de paginação, mas devolva esses resultados como uma única mensagem quando a ação terminar. Quando utiliza a paginação, deve especificar um *valor-limiar,* que é o número-alvo dos resultados que pretende que a ação regresse. A ação recupera resultados até atingir o limiar especificado. Quando o seu número total de itens é inferior ao limiar especificado, a ação recupera todos os resultados.

Ligar a definição de paginação recupera páginas de resultados com base no tamanho da página de um conector. Este comportamento significa que, às vezes, pode obter mais resultados do que o limiar especificado. Por exemplo, ao utilizar a ação SQL Server **Get rows,** que suporta a definição de paginação:

* O tamanho da página padrão da ação é de 2048 registos por página.
* Suponha que tenha 10.000 registos e especifique 5.000 registos como mínimo.
* Paginação recebe páginas de registos, por isso, para obter pelo menos o mínimo especificado, a ação devolve 6144 registos (3 páginas x 2048 registos), e não 5000 registos.

Aqui está uma lista com apenas alguns dos conectores onde você pode exceder o tamanho da página padrão para ações específicas:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Equipas da Microsoft](https://docs.microsoft.com/connectors/teams/)
* [Base de dados Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica e a ação em que você quer ligar a paginação. Se não tem uma aplicação lógica, consulte [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ligar a paginação

Para determinar se uma ação suporta paginação no Logic App Designer, verifique as definições da ação para a **definição de Paginação.** Este exemplo mostra como ligar a paginação na ação das **linhas Get** do SQL Server.

1. No canto superior direito da ação, escolha o botão elipses (**...**) e selecione **Definições**.

   ![Abra as definições da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação apoiar a paginação, a ação mostra o cenário **paginação.**

1. Mude a definição de **Paginação** de **Off** para **On**. Na propriedade **Threshold,** especifique um valor inteiro para o número de resultados que pretende que a ação devolva.

   ![Especificar número mínimo de resultados para devolver](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **"Done".**

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando liga a paginação para uma ação que suporta esta capacidade, a definição de fluxo de trabalho da sua aplicação lógica inclui a `"paginationPolicy"` propriedade juntamente com a `"minimumItemCount"` propriedade da `"runtimeConfiguration"` ação, por exemplo:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Obter suporte

Para perguntas, visite o [Microsoft Q&Uma página de perguntas para aplicações lógicas do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
