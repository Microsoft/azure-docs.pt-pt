---
title: Ligue ao SharePoint a partir de Aplicações Lógicas Azure
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem recursos no SharePoint Online ou no SharePoint Server nas instalações através da utilização de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789261"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorize e gerea recursos sharePoint com aplicações lógicas azure

Com as Aplicações Lógicas Azure e o conector SharePoint, pode criar tarefas automatizadas e fluxos de trabalho que monitorizam e gerem recursos, tais como ficheiros, pastas, listas, itens, pessoas, e assim por diante, no SharePoint Online ou no SharePoint Server nas instalações, por exemplo:

* Monitorize quando os ficheiros ou itens forem criados, alterados ou eliminados.
* Criar, obter, atualizar ou apagar itens.
* Adicione, obtenha ou apague anexos. Obtenha o conteúdo dos anexos.
* Criar, copiar, atualizar ou eliminar ficheiros. 
* Atualizar propriedades de ficheiros. Obtenha o conteúdo, metadados ou propriedades para um ficheiro.
* Listar ou extrair pastas.
* Obtenha listas ou visualizações de listas.
* Definir o estado de aprovação do conteúdo.
* Resolver pessoas.
* Envie pedidos http para SharePoint.
* Obter valores de entidade.

Pode utilizar gatilhos que obtêm respostas do SharePoint e disponibilizar a saída a outras ações. Pode utilizar ações nas suas aplicações lógicas para executar tarefas no SharePoint. Também pode ter outras ações a utilizar a saída a partir de ações do SharePoint. Por exemplo, se buscar regularmente ficheiros do SharePoint, pode enviar mensagens para a sua equipa utilizando o conector Slack.
Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O endereço do site do SharePoint e as credenciais do utilizador

  As suas credenciais autorizam a sua aplicação lógica para criar uma ligação e aceder à sua conta SharePoint. 

* Antes de poder ligar aplicações lógicas a sistemas no local, como o SharePoint Server, é necessário [instalar e configurar um portal de dados no local](../logic-apps/logic-apps-gateway-install.md). Desta forma, pode especificar utilizar a instalação do gateway quando criar a ligação SharePoint Server para a sua aplicação lógica.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SharePoint. Para começar com um gatilho SharePoint, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SharePoint, inicie a sua aplicação lógica com um gatilho, como um gatilho Salesforce, se tiver uma conta Salesforce.

  Por exemplo, pode iniciar a sua aplicação lógica com o gatilho **When a record is created** Salesforce. 
  Este gatilho dispara cada vez que um novo recorde, como um chumbo, é criado na Salesforce. 
  Em seguida, pode seguir este gatilho com a ação de ficheiroS SharePoint **Create.** Desta forma, quando o novo disco é criado, a sua aplicação lógica cria um ficheiro no SharePoint com informações sobre esse novo registo.

## <a name="connect-to-sharepoint"></a>Ligar ao SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "sharepoint" como filtro. Na lista de gatilhos, selecione o gatilho que deseja. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação SharePoint, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "sharepoint" como filtro. 
   Na lista de ações, selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Quando lhe pedirem para iniciar sessão, forneça as informações de ligação necessárias. Se estiver a utilizar o SharePoint Server, **certifique-se**de que seleciona o Connect através da gateway de dados no local . Quando tiver terminado, escolha **Create** (Criar).

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/sharepoint/)do conector .

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
