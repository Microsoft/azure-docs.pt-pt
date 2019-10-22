---
title: Obter mais itens ou registros com paginação-aplicativos lógicos do Azure
description: Configurar paginação para exceder o limite de tamanho de página padrão para ações de conector nos aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: e86600312490c77ed492cb28a359add0fed90596
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679897"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obter mais dados, itens ou registros usando paginação em aplicativos lógicos do Azure

Quando você recupera dados, itens ou registros usando uma ação de conector em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você pode obter conjuntos de resultados tão grandes que a ação não retorna todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho de página padrão do conector. Nesse caso, a ação retorna apenas a primeira página de resultados. Por exemplo, o tamanho de página padrão para a ação **obter linhas** do conector de SQL Server é 2048, mas pode variar com base em outras configurações.

Algumas ações permitem que você ative uma configuração de *paginação* para que seu aplicativo lógico possa recuperar mais resultados até o limite de paginação, mas retornar esses resultados como uma única mensagem quando a ação for concluída. Ao usar a paginação, você deve especificar um valor de *limite* , que é o número de destino de resultados que você deseja que a ação retorne. A ação recupera os resultados até atingir o limite especificado. Quando o número total de itens é menor que o limite especificado, a ação recupera todos os resultados.

A ativação da configuração de paginação recupera páginas de resultados com base no tamanho da página de um conector. Esse comportamento significa que às vezes você pode obter mais resultados do que o limite especificado. Por exemplo, ao usar a ação SQL Server **obter linhas** , que dá suporte à configuração de paginação:

* O tamanho da página padrão da ação é de 2048 registros por página.
* Suponha que você tenha 10.000 registros e especifique 5000 registros como o mínimo.
* A paginação Obtém páginas de registros, portanto, para obter pelo menos o mínimo especificado, a ação retorna 6144 registros (3 páginas x 2048 registros), e não registros 5000.

Aqui está uma lista com apenas alguns dos conectores onde você pode exceder o tamanho de página padrão para ações específicas:

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

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico e a ação em que você deseja ativar a paginação. Se você não tiver um aplicativo lógico, consulte [início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ativar paginação

Para determinar se uma ação dá suporte à paginação no designer do aplicativo lógico, verifique as configurações da ação para a configuração de **paginação** . Este exemplo mostra como ativar a paginação na ação **obter linhas** do SQL Server.

1. No canto superior direito da ação, escolha o botão de reticências ( **...** ) e selecione **configurações**.

   ![Abrir as configurações da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação der suporte à paginação, a ação mostrará a configuração de **paginação** .

1. Altere a configuração de **paginação** de **desativado** para **ativado**. Na propriedade **Threshold** , especifique um valor inteiro para o número de destino de resultados que você deseja que a ação retorne.

   ![Especificar o número mínimo de resultados a serem retornados](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **concluído**.

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho – paginação

Quando você ativa a paginação para uma ação que dá suporte a esse recurso, a definição de fluxo de trabalho do aplicativo lógico inclui a propriedade `"paginationPolicy"` juntamente com a propriedade `"minimumItemCount"` na propriedade `"runtimeConfiguration"` da ação, por exemplo:

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
