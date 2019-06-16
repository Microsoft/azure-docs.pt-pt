---
title: Obtenha mais dados, itens ou registos com a paginação - Azure Logic Apps
description: Configurar a paginação deve exceder o limite de tamanho de página predefinido para ações de conector no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64476545"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obter mais dados, itens ou registos ao utilizar a paginação no Azure Logic Apps

Quando recuperar os dados, itens ou registos utilizando uma ação de conector no [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), poderá obter conjuntos de resultados tão grandes que a ação não retorna todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho de página predefinido do conector. Neste caso, a ação devolve apenas a primeira página de resultados. Por exemplo, o tamanho de página predefinido para do conector SQL Server **obter linhas** ação é 2048, mas podem variar com base nas outras definições.

Algumas ações permitem-lhe ativar uma *paginação* definição para que a aplicação lógica pode obter mais resultados até ao limite de paginação, mas retornar esses resultados como uma única mensagem quando a ação for concluída. Quando utilizar a paginação, tem de especificar um *limiar* valor, que é o número de destino de resultados que quer que a ação a devolver. A ação obtém resultados até atingir o limiar especificado. Quando o número total de itens é inferior ao limiar especificado, a ação obtém todos os resultados.

Ativando as páginas de recupera de definição de paginação de resultados com base no tamanho da página de um conector. Este comportamento significa que, às vezes, poderá obter resultados mais do que o limiar especificado. Por exemplo, ao utilizar o SQL Server **obter linhas** ação, que suporta a definição de paginação:

* Tamanho de página predefinido da ação é 2048 registros por página.
* Suponha que tiver 10 000 registos e especifica 5000 registos como o mínimo.
* Paginação obtém páginas de registos, então, para obter, pelo menos, o mínimo especificado, a ação devolve 6144 registos (3 páginas x 2048 registos), não 5000 registos.

Aqui está uma lista com apenas alguns dos conectores onde pode exceder o tamanho de página predefinido para ações específicas:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Equipas da Microsoft](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A aplicação lógica e a ação em que pretende ativar paginação. Se não tiver uma aplicação lógica, consulte [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ativar paginação

Para determinar se uma ação suporta paginação no Estruturador da aplicação lógica, verifique as definições da ação para o **paginação** definição. Este exemplo mostra como ativar a paginação no SQL Server **obter linhas** ação.

1. No canto superior direito da ação, selecione as reticências ( **...** ) e selecione **definições**.

   ![Abrir definições da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação suporta paginação, a ação mostra os **paginação** definição.

1. Alterar o **paginação** definição do **desativar** para **no**. Na **limiar** propriedade, especifique um valor inteiro para o número de destino de resultados que pretende que a ação a devolver.

   ![Especifique o número mínimo de resultados a devolver](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **feito**.

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando ativar paginação para uma ação que oferece suporte a esse recurso, a definição de fluxo de trabalho da sua aplicação lógica inclui o `"paginationPolicy"` propriedade juntamente com o `"minimumItemCount"` propriedade nessa ação `"runtimeConfiguration"` propriedade, por exemplo:

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

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
