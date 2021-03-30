---
title: Obtenha mais itens ou registos com paginação
description: Configurar a paginação para exceder o limite de tamanho da página padrão para as ações de conector em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87090268"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenha mais dados, itens ou registos utilizando a paginação em Azure Logic Apps

Quando recupera dados, itens ou registos utilizando uma ação de conector em [Azure Logic Apps](../logic-apps/logic-apps-overview.md), poderá obter conjuntos de resultados tão grandes que a ação não devolve todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho da página padrão do conector. Neste caso, a ação devolve apenas a primeira página dos resultados. Por exemplo, o tamanho padrão da página para a ação de **linhas get** do conector SQL é 2048, mas pode variar com base em outras definições.

Algumas ações permitem-lhe ligar uma *definição de paginação* para que a sua aplicação lógica possa obter mais resultados até ao limite de paginação, mas devolva esses resultados como uma única mensagem quando a ação terminar. Quando utilizar a paginação, deve especificar um valor *limiar,* que é o número-alvo de resultados que pretende que a ação devolva. A ação recupera os resultados até atingir o limiar especificado. Quando o seu número total de itens é inferior ao limiar especificado, a ação recupera todos os resultados.

Ligar a definição de paginação recupera páginas de resultados com base no tamanho da página de um conector. Este comportamento significa que, por vezes, pode obter mais resultados do que o seu limiar especificado. Por exemplo, ao utilizar a ação SQL Server **Get rows,** que suporta a definição de paginação:

* O tamanho da página padrão da ação é de 2048 registos por página.
* Suponha que tenha 10.000 registos e especifique 5.000 registos como mínimo.
* A paginação obtém páginas de registos, de modo a obter pelo menos o mínimo especificado, a ação devolve 6144 registos (3 páginas x 2048 registos), e não 5000 registos.

Aqui está uma lista com apenas alguns dos conectores onde pode exceder o tamanho da página padrão para ações específicas:

* [Armazenamento de Blobs do Azure](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Equipas da Microsoft](/connectors/teams/)
* [Banco de Dados do Oráculo](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica e a ação onde você quer ligar a paginação. Se não tiver uma aplicação lógica, consulte [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="turn-on-pagination"></a>Ligue a paginação

Para determinar se uma ação suporta a paginação no Logic App Designer, verifique as definições da ação para a **definição de Pagination.** Este exemplo mostra como ligar a paginação na ação de **linhas get** do SQL Server.

1. No canto superior direito da ação, escolha o botão elipses **(...**) e selecione **Definições**.

   ![Abra as definições da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação apoiar a paginação, a ação mostra a **definição de Pagination.**

1. Alterar a **definição de Pagination** de **Off** para **On**. Na propriedade **Threshold,** especifique um valor inteiro para o número de resultados-alvo que deseja que a ação devolva.

   ![Especificar o número mínimo de resultados para devolver](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **'Fazer'.**

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando liga a paginação para uma ação que suporta esta capacidade, a definição de fluxo de trabalho da sua aplicação lógica inclui a `"paginationPolicy"` propriedade juntamente com a `"minimumItemCount"` propriedade na propriedade dessa `"runtimeConfiguration"` ação, por exemplo:

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

Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
