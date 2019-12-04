---
title: Conectar-se ao SharePoint por meio de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram e gerenciam recursos no SharePoint Online ou no SharePoint Server local usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789261"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorar e gerenciar recursos do SharePoint com aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do SharePoint, você pode criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam recursos, como arquivos, pastas, listas, itens, pessoas e assim por diante, no SharePoint Online ou no SharePoint Server local, por exemplo:

* Monitorar quando arquivos ou itens são criados, alterados ou excluídos.
* Criar, obter, atualizar ou excluir itens.
* Adicionar, obter ou excluir anexos. Obtenha o conteúdo dos anexos.
* Criar, copiar, atualizar ou excluir arquivos. 
* Atualizar propriedades do arquivo. Obtenha o conteúdo, os metadados ou as propriedades de um arquivo.
* Listar ou extrair pastas.
* Obter listas ou exibições de lista.
* Definir status de aprovação de conteúdo.
* Resolver pessoas.
* Enviar solicitações HTTP para o SharePoint.
* Obter valores de entidade.

Você pode usar gatilhos que obtêm respostas do SharePoint e disponibilizam a saída para outras ações. Você pode usar ações em seus aplicativos lógicos para executar tarefas no SharePoint. Você também pode fazer com que outras ações usem a saída de ações do SharePoint. Por exemplo, se você buscar regularmente arquivos do SharePoint, poderá enviar mensagens para sua equipe usando o conector de margem de atraso.
Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O endereço do site do SharePoint e as credenciais do usuário

  Suas credenciais autorizam seu aplicativo lógico a criar uma conexão e acessar sua conta do SharePoint. 

* Antes de poder conectar aplicativos lógicos a sistemas locais, como o SharePoint Server, você precisa [instalar e configurar um gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode especificar para usar a instalação do gateway ao criar a conexão do SharePoint Server para seu aplicativo lógico.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta do SharePoint. Para começar com um gatilho do SharePoint, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do SharePoint, inicie seu aplicativo lógico com um gatilho, como um gatilho do Salesforce, se você tiver uma conta do Salesforce.

  Por exemplo, você pode iniciar seu aplicativo lógico com o gatilho **quando um registro é criado** do Salesforce. 
  Esse gatilho é acionado cada vez que um novo registro, como um cliente potencial, é criado no Salesforce. 
  Em seguida, você pode seguir esse gatilho com a ação **criar arquivo** do SharePoint. Dessa forma, quando o novo registro for criado, seu aplicativo lógico criará um arquivo no SharePoint com informações sobre esse novo registro.

## <a name="connect-to-sharepoint"></a>Ligar ao SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "SharePoint" como filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação do SharePoint, escolha **nova etapa**. 
   Na caixa de pesquisa, digite "SharePoint" como seu filtro. 
   Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Quando for solicitado a entrar, forneça as informações de conexão necessárias. Se você estiver usando o SharePoint Server, certifique-se de selecionar **conectar por meio do gateway de dados local**. Quando tiver terminado, escolha **Create** (Criar).

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/sharepoint/)do conector.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
