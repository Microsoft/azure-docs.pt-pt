---
title: Criar fluxos de trabalho a partir de modelos - Azure Logic Apps | Documentos da Microsoft
description: Criar fluxos de trabalho mais rápidos com modelos de aplicações lógicas no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193454"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Criar fluxos de trabalho de aplicação de lógica a partir de modelos pré-criados

Para ajudá-lo à criação de fluxos de trabalho mais rapidamente, o Logic Apps fornece modelos, que são as aplicações de lógicas pré-criadas que se seguem padrões comumente usados. Utilizar estes modelos, como a fornecida ou editá-los para se ajustarem ao seu cenário.

Aqui estão algumas categorias de modelo:

| Tipo de modelo | Descrição | 
| ------------- | ----------- | 
| Modelos do Enterprise cloud | Para a integração de Blobs do Azure, Dynamics CRM, Salesforce, caixa e inclui outros conectores para necessidades de cloud da sua empresa. Por exemplo, pode utilizar estes modelos para organizar as oportunidades potenciais de negócio ou fazer backup dos dados de ficheiros empresarial. | 
| Modelos de produtividade pessoal | Aumentar a produtividade pessoal ao definir lembretes diários, ativar importante os itens de trabalho em listas de tarefas e automatizando tarefas demoradas para baixo para uma etapa de aprovação de utilizador único. | 
| Modelos de cloud do consumidor | Para integrar serviços de redes sociais como o Twitter, Slack e e-mail. Útil para reforçar a mídia social iniciativas de marketing. Estes modelos também incluem tarefas, como a nuvem a copiar, o que aumenta a produtividade, economizando tempo em tarefas tradicionalmente repetitivas. | 
| Modelos de pacote de integração do Enterprise | Para configurar VETER (validar, extrair, transformar, melhorar, encaminhar) pipelines, receber uma X12 documento EDI através de AS2 e transformar para XML e o processamento de X12, EDIFACT e nas mensagens AS2. | 
| Modelos de padrão de protocolo | Para implementar padrões de protocolo, como solicitação-resposta por HTTP e integrações no FTP e SFTP. Utilizar estes modelos, como a fornecida ou criar nos mesmos padrões complexos de protocolo. | 
||| 

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter mais informações sobre a criação de uma aplicação lógica, consulte [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Criar aplicações lógicas a partir de modelos

1. Se ainda não o fez, inicie sessão para o [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu principal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | *nome-da-sua-aplicação-lógica* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para esta aplicação lógica e para organizar todos os recursos associados esta aplicação. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | **Desativar** (predefinição) ou **no** | Ative [registo de diagnósticos](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) para a sua aplicação lógica através de [registos do Azure Monitor](../log-analytics/log-analytics-overview.md). Requer que já tem uma área de trabalho do Log Analytics. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 

5. Desloque-se após os disparadores de comuns e vídeo de introdução para **modelos**. Escolha um modelo criados previamente. Por exemplo:

   ![Escolha um modelo de aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Para criar a sua aplicação lógica do zero, escolha **aplicação lógica em branco**.

   Quando seleciona um modelo pré-criados, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-criados](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Para continuar com o modelo selecionado, escolha **Utilize este modelo**. 

7. Com base nos conectores no modelo, são solicitados a executar qualquer um destes passos:

   * Inicie sessão com as suas credenciais para sistemas ou serviços que são referenciados pelo modelo.

   * Crie ligações para quaisquer serviços ou sistemas referidos pelo modelo. Para criar uma ligação, indique um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurou a estas ligações, escolha **continuar**.

   Por exemplo:

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Quando tiver terminado, a aplicação lógica abre e é apresentado no estruturador de aplicações lógicas.

   > [!TIP]
   > Para voltar para o Visualizador de modelo, escolha **modelos** na barra de ferramentas de designer. Esta ação elimina as alterações não guardadas, portanto, é apresentada uma mensagem de aviso confirmar o seu pedido.

8. Continue a criar a sua aplicação lógica.

   > [!NOTE] 
   > Muitos modelos incluem os conectores que podem já ter pré-preenchido propriedades necessárias. No entanto, alguns modelos ainda podem exigir que forneça os valores antes de poder implementar a aplicação lógica corretamente. Se tentar implementar sem concluir os campos de propriedade em falta, receberá uma mensagem de erro. 

## <a name="update-logic-apps-with-templates"></a>Atualizar aplicações lógicas com modelos

1. Na [portal do Azure](https://portal.azure.com "portal do Azure"), encontrar e abrir a aplicação lógica no th Estruturador da aplicação lógica.

2. Na barra de ferramentas da estruturador, escolha **modelos**. Esta ação elimina as alterações não guardadas, portanto, é apresentada uma mensagem de aviso para que pode confirmar que pretende continuar. Para confirmar, escolha **OK**. Por exemplo:

   ![Escolha "Modelos"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Desloque-se após os disparadores de comuns e vídeo de introdução para **modelos**. Escolha um modelo criados previamente. Por exemplo:

   ![Escolha um modelo de aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Quando seleciona um modelo pré-criados, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-criados](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Para continuar com o modelo selecionado, escolha **Utilize este modelo**. 

5. Com base nos conectores no modelo, são solicitados a executar qualquer um destes passos:

   * Inicie sessão com as suas credenciais para sistemas ou serviços que são referenciados pelo modelo.

   * Crie ligações para quaisquer serviços ou sistemas referidos pelo modelo. Para criar uma ligação, indique um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurou a estas ligações, escolha **continuar**.

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Agora, a aplicação lógica abre e é apresentado no estruturador de aplicações lógicas.

8. Continue a criar a sua aplicação lógica. 

   > [!TIP]
   > Se ainda não guardar as suas alterações, pode eliminar o seu trabalho e retornar à sua aplicação lógica anterior. Na barra de ferramentas da estruturador, escolha **descartar**.

> [!NOTE] 
> Muitos modelos incluem os conectores que poderão ter já pré-preenchido propriedades necessárias. No entanto, alguns modelos ainda podem exigir que forneça os valores antes de poder implementar a aplicação lógica corretamente. Se tentar implementar sem concluir os campos de propriedade em falta, receberá uma mensagem de erro.

## <a name="deploy-logic-apps-built-from-templates"></a>Implementar aplicações lógicas criadas a partir de modelos

Depois de efetuar as alterações ao modelo, pode guardar as alterações. Esta ação publica também automaticamente a sua aplicação lógica.

Na barra de ferramentas do estruturador, escolha **Guardar**.

![Guardar e publicar a aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a criação de aplicações lógicas por meio de exemplos, cenários, histórias de clientes e orientações passo a passo.

> [!div class="nextstepaction"]
> [Reveja exemplos de aplicação lógica, os cenários e instruções passo a passo](../logic-apps/logic-apps-examples-and-scenarios.md)