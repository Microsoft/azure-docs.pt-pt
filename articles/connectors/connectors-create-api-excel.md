---
title: Gerenciar dados, planilhas e tabelas no Excel online
description: Gerenciar dados em planilhas e tabelas no Excel online for Business ou Excel online para OneDrive usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445871"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerenciar dados do Excel online com aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector do [Excel online for Business](/connectors/excelonlinebusiness/) ou [Excel online para onedrive](/connectors/excelonline/) , você pode criar tarefas e fluxos de trabalho automatizados com base em seus dados no Excel online para empresas ou onedrive. Esse conector fornece ações que ajudam você a trabalhar com seus dados e gerenciar planilhas, por exemplo:

* Crie novas planilhas e tabelas.
* Obtenha e gerencie planilhas, tabelas e linhas.
* Adicione linhas únicas e colunas de chave.

Você pode usar as saídas dessas ações com ações para outros serviços. Por exemplo, se você usar uma ação que cria planilhas por semana, poderá usar outra ação que envia emails de confirmação usando o conector do Outlook para Office 365.

Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> O [Excel online for Business](/connectors/excelonlinebusiness/) e o [Excel online para](/connectors/excelonline/) conectores do onedrive funcionam com aplicativos lógicos do Azure e diferem do [conector do Excel para PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta do Office 365](https://www.office.com/) para sua conta corporativa ou conta Microsoft pessoais

  Os dados do Excel podem existir como um arquivo CSV (valores separados por vírgula) em uma pasta de armazenamento, por exemplo, no OneDrive. 
  Você também pode usar o mesmo arquivo CSV com o [conector de arquivo simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar seus dados do Excel online. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho de **recorrência** .

## <a name="add-excel-action"></a>Adicionar ação do Excel

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico, se ainda não estiver aberto.

1. No gatilho, escolha **nova etapa**.

1. Na caixa de pesquisa, digite "Excel" como filtro. Na lista ações, selecione a ação desejada.

   > [!NOTE]
   > O designer do aplicativo lógico não pode carregar tabelas que tenham 100 ou mais colunas. Se possível, reduza o número de colunas na tabela selecionada para que o designer possa carregar a tabela.

1. Se solicitado, entre na sua conta do Office 365.

   Suas credenciais autorizam seu aplicativo lógico a criar uma conexão com o Excel online e acessar seus dados.

1. Continue fornecendo os detalhes necessários para a ação selecionada e criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelos arquivos OpenAPI (anteriormente Swagger) do conector, consulte estas páginas de referência do conector:

* [Excel online para empresas](/connectors/excelonlinebusiness/)
* [Excel online para OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
