---
title: Gerir dados, folhas de cálculo e tabelas no Excel Online
description: Gerir dados em folhas de cálculo e tabelas no Excel Online para Negócios ou Excel Online para oneDrive utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445871"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerir os dados online do Excel com aplicações lógicas azure

Com [aplicações azure logic](../logic-apps/logic-apps-overview.md) e o excel online para o conector [de negócios](/connectors/excelonlinebusiness/) ou Excel Online para conector [OneDrive,](/connectors/excelonline/) pode criar tarefas e fluxos de trabalho automatizados com base nos seus dados no Excel Online para Negócios ou OneDrive. Este conector fornece ações que o ajudam a trabalhar com os seus dados e a gerir folhas de cálculo, por exemplo:

* Crie novas folhas de cálculo e mesas.
* Obter e gerir folhas de cálculo, mesas e filas.
* Adicione linhas únicas e colunas de teclas.

Em seguida, pode utilizar as saídas destas ações com ações para outros serviços. Por exemplo, se utilizar uma ação que crie folhas de cálculo todas as semanas, pode utilizar outra ação que envia e-mail de confirmação utilizando o conector Do Outlook do Office 365.

Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> O [Excel Online para Negócios](/connectors/excelonlinebusiness/) e Excel Online para conectores [OneDrive](/connectors/excelonline/) funcionam com aplicações lógicas Azure e diferem do [conector Excel para PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta office 365](https://www.office.com/) para a sua conta de trabalho ou conta pessoal da Microsoft

  Os seus dados excel podem existir como um ficheiro de valor separado de vírem (CSV) numa pasta de armazenamento, por exemplo, no OneDrive. 
  Também pode utilizar o mesmo ficheiro CSV com o [conector de ficheiro plano](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder aos seus dados Excel Online. Este conector fornece apenas ações, de modo a iniciar a sua aplicação lógica, selecione um gatilho separado, por exemplo, o gatilho **Recurrence.**

## <a name="add-excel-action"></a>Adicionar ação Excel

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Sob o gatilho, escolha **novo passo**.

1. Na caixa de pesquisa, introduza "excel" como filtro. Na lista de ações, selecione a ação que deseja.

   > [!NOTE]
   > O Logic App Designer não pode carregar tabelas com 100 ou mais colunas. Se possível, reduza o número de colunas na tabela selecionada para que o designer possa carregar a tabela.

1. Se solicitado, inscreva-se na sua conta do Office 365.

   As suas credenciais autorizam a sua aplicação lógica para criar uma ligação ao Excel Online e aceder aos seus dados.

1. Continue a fornecer os detalhes necessários para a ação selecionada e construindo o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelos ficheiros OpenAPI (ex-Swagger) do conector, consulte estas páginas de referência do conector:

* [Excel Online para Negócios](/connectors/excelonlinebusiness/)
* [Excel Online para OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
