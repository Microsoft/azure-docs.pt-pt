---
title: Conecte-se ao SharePoint a partir de apps Azure Logic
description: Automatizar tarefas e fluxos de trabalho que monitorizam e gerem recursos no SharePoint Online ou no SharePoint Server nas instalações utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283984"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorizar e gerir recursos do SharePoint com o Azure Logic Apps

Com as Apps Lógicas Azure e o conector SharePoint, pode criar tarefas e fluxos de trabalho automatizados que monitorizem e gerem recursos, tais como ficheiros, pastas, listas, itens, pessoas, e assim por diante, no SharePoint Online ou no SharePoint Server nas instalações, por exemplo:

* Monitorize quando os ficheiros ou itens forem criados, alterados ou eliminados.
* Criar, obter, atualizar ou eliminar itens.
* Adicione, obtenha ou elimine os anexos. Obtenha o conteúdo dos anexos.
* Criar, copiar, atualizar ou eliminar ficheiros. 
* Atualizar as propriedades do ficheiro. Obtenha o conteúdo, metadados ou propriedades para um ficheiro.
* Listar ou extrair pastas.
* Obtenha listas ou visualizações de listas.
* Desaver o estado de aprovação do conteúdo.
* Resolver as pessoas.
* Envie https para SharePoint.
* Obtenha valores de entidade.

Pode utilizar gatilhos que obtenham respostas do SharePoint e disponibilize a saída para outras ações. Pode utilizar ações nas suas aplicações lógicas para executar tarefas no SharePoint. Também pode ter outras ações que utilizem a produção das ações do SharePoint. Por exemplo, se for buscar regularmente ficheiros do SharePoint, pode enviar mensagens para a sua equipa utilizando o conector Slack.
Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* O endereço do site do SharePoint e as credenciais de utilizador

  As suas credenciais autorizam a sua aplicação lógica a criar uma ligação e aceder à sua conta SharePoint. 

* Antes de poder ligar aplicações lógicas a sistemas no local, como o SharePoint Server, é necessário [instalar e configurar um gateway de dados no local.](../logic-apps/logic-apps-gateway-install.md) Desta forma, pode especificar para utilizar a instalação gateway quando criar a ligação SharePoint Server para a sua aplicação lógica.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SharePoint. Para começar com um gatilho SharePoint, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SharePoint, inicie a sua aplicação lógica com um gatilho, como um gatilho Salesforce, se tiver uma conta Salesforce.

  Por exemplo, pode iniciar a sua aplicação lógica com o gatilho De vendas quando **um disco é criado.** 
  Este gatilho dispara cada vez que um novo recorde, como um chumbo, é criado na Salesforce. 
  Em seguida, pode seguir este gatilho com a ação de ficheiros SharePoint **Create.** Assim, quando o novo registo é criado, a sua aplicação lógica cria um ficheiro no SharePoint com informações sobre esse novo registo.

## <a name="connect-to-sharepoint"></a>Ligar ao SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "sharepoint" como filtro. Na lista de gatilhos, selecione o gatilho que pretende. 

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação SharePoint, escolha **Novo passo**. 
   Na caixa de pesquisa, introduza "sharepoint" como filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Quando for solicitado a iniciar sedutar, forneça as informações necessárias de ligação. Se estiver a utilizar o SharePoint Server, **certifique-se de**que seleciona Connect via gateway de dados no local . Quando tiver terminado, escolha **Create** (Criar).

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](/connectors/sharepoint/)do conector .

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

