---
title: Gerir dados, folhas de cálculo e tabelas no Excel Online
description: Gerir dados em folhas de cálculo e tabelas no Excel Online para Negócios ou Excel Online para o OneDrive utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400728"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerir dados do Excel Online com apps Azure Logic

Com [aplicações lógicas Azure](../logic-apps/logic-apps-overview.md) e o [conector Excel Online para business](/connectors/excelonlinebusiness/) ou excel online para o conector [OneDrive,](/connectors/excelonline/) pode criar tarefas e fluxos de trabalho automatizados com base nos seus dados no Excel Online para Negócios ou OneDrive. Este conector fornece ações que o ajudam a trabalhar com os seus dados e a gerir folhas de cálculo, por exemplo:

* Crie novas folhas de cálculo e tabelas.
* Obter e gerir folhas de cálculo, mesas e linhas.
* Adicione linhas únicas e colunas-chave.

Em seguida, pode utilizar os resultados destas ações com ações para outros serviços. Por exemplo, se utilizar uma ação que cria folhas de cálculo todas as semanas, pode utilizar outra ação que envia e-mail de confirmação utilizando o conector Office 365 Outlook.

Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> O [Excel Online para Negócios](/connectors/excelonlinebusiness/) e Excel Online para conectores [OneDrive](/connectors/excelonline/) funcionam com aplicações Azure Logic e diferem do [conector Excel para PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de trabalho ou escola](https://www.office.com/) para a sua conta de trabalho ou conta pessoal da Microsoft

  Os seus dados Excel podem existir como um ficheiro de valor separado de vírgula (CSV) numa pasta de armazenamento, por exemplo, no OneDrive. 
  Também pode utilizar o mesmo ficheiro CSV com o [conector de ficheiro plano](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder aos seus dados Excel Online. Este conector fornece apenas ações, por isso, para iniciar a sua aplicação lógica, selecione um gatilho separado, por exemplo, o gatilho **de Recorrência.**

## <a name="add-excel-action"></a>Adicionar ação do Excel

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer, se não já estiver aberta.

1. Sob o gatilho, escolha **novo passo.**

1. Na caixa de pesquisa, introduza "excel" como filtro. Na lista de ações, selecione a ação desejada.

   > [!NOTE]
   > O Logic App Designer não consegue carregar tabelas com 100 ou mais colunas. Se possível, reduza o número de colunas na tabela selecionada para que o designer possa carregar a tabela.

1. Se solicitado, inscreva-se na sua conta de trabalho ou escola.

   As suas credenciais autorizam a sua aplicação lógica a criar uma ligação ao Excel Online e a aceder aos seus dados.

1. Continue a fornecer os detalhes necessários para a ação selecionada e construindo o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelos ficheiros OpenAPI (anteriormente Swagger) do conector, consulte estas páginas de referência do conector:

* [Excel Online para Negócios](/connectors/excelonlinebusiness/)
* [Excel Online para OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
