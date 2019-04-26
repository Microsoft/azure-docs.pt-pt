---
title: Ligar ao SharePoint a partir do Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que monitorizar e gerir recursos no SharePoint Online ou o SharePoint Server no local com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60450748"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorizar e gerir recursos do SharePoint com o Azure Logic Apps

Com o Azure Logic Apps e o conector do SharePoint, pode criar tarefas automatizadas e fluxos de trabalho que monitorizar e gerir recursos, tais como ficheiros, pastas, listas, itens, pessoas, e assim por diante, no SharePoint Online ou no SharePoint Server no local, por exemplo:

* Monitor para quando os ficheiros ou itens são criados, alterados ou excluídos.
* Criar, obter, atualizar ou eliminar itens.
* Adicionar, obter ou eliminar anexos. Obter o conteúdo de anexos.
* Criar, copiar, atualizar ou eliminar ficheiros. 
* Atualize as propriedades do ficheiro. Obter o conteúdo, metadados ou as propriedades para um ficheiro.
* Pastas de lista ou de extração.
* Obter listas ou vistas de lista.
* Definir o estado de aprovação de conteúdo.
* Resolva pessoas.
* Envie solicitações HTTP para o SharePoint.
* Obter valores da entidade.

Pode usar acionadores que obtém as respostas do SharePoint e disponibilizar a saída para outras ações. Pode utilizar ações nas suas aplicações lógicas para executar tarefas no SharePoint. Pode também ter outras ações utilizar a saída de ações do SharePoint. Por exemplo, se regularmente obter ficheiros do SharePoint, pode enviar mensagens para a sua equipa, utilizando o conector do Slack.
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Suas credenciais de utilizador e o endereço de site do SharePoint

  As suas credenciais autorizar a aplicação lógica para criar uma ligação e aceder à sua conta do SharePoint. 

* Antes de poder ligar aplicações lógicas para sistemas no local, como o SharePoint Server, precisa [instalar e configurar um gateway de dados no local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, pode especificar a utilizar a instalação de gateway ao criar a ligação ao servidor do SharePoint para a aplicação lógica.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do SharePoint. Para começar com um acionador do SharePoint, [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação do SharePoint, inicie a aplicação lógica com um acionador, tal como um acionador de Salesforce, se tiver uma conta do Salesforce.

  Por exemplo, pode começar a sua aplicação lógica com o **quando é criado um registo** acionador do Salesforce. 
  Este acionador é acionado sempre que um novo registo, como uma oportunidade potencial, é criado no Salesforce. 
  Em seguida, pode seguir este acionador com o SharePoint **criar ficheiro** ação. Dessa forma, quando é criado o novo registo, a aplicação lógica cria um ficheiro no SharePoint com informações sobre o novo Registro.

## <a name="connect-to-sharepoint"></a>Ligar ao SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "sharepoint" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação do SharePoint, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "sharepoint" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Quando lhe for pedido para iniciar sessão, forneça as informações de ligação necessárias. Se estiver a utilizar o SharePoint Server, certifique-se de que seleciona **ligar através do gateway de dados no local**. Quando tiver terminado, escolha **Create** (Criar).

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/sharepoint/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
