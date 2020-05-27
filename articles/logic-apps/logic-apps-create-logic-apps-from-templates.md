---
title: Criar fluxos de trabalho de aplicações lógicas mais rapidamente usando modelos pré-construídos
description: Construa rapidamente fluxos de trabalho de aplicações lógicas utilizando modelos pré-construídos fornecidos por Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 8f9513c3ac28b8f65d9023fc529927b208323ca1
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834463"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Create logic app workflows from prebuilt templates (Criar fluxos de trabalho de aplicações lógicas a partir de modelos pré-criados)

Para começar a criar fluxos de trabalho mais rapidamente, as Aplicações Lógicas fornecem modelos, que são aplicações lógicas pré-construídas que seguem padrões comumente usados. Use estes modelos conforme fornecido ou edite-os para se adaptar ao seu cenário.

Aqui estão algumas categorias de modelos:

| Tipo de modelo | Descrição | 
| ------------- | ----------- | 
| Modelos de nuvem empresarial | Para integrar o Azure Blob, Dynamics CRM, Salesforce, Box e inclui outros conectores para as necessidades da sua nuvem empresarial. Por exemplo, pode usar estes modelos para organizar leads de negócio ou fazer o back-up dos seus dados de ficheiros corporativos. | 
| Modelos de produtividade pessoal | Melhorar a produtividade pessoal, definindo lembretes diários, transformando itens de trabalho importantes em listas de tarefas a fazer, e automatizando tarefas longas até um único passo de aprovação do utilizador. | 
| Modelos de nuvem de consumo | Para integrar serviços de redes sociais como Twitter, Slack e e-mail. Útil para fortalecer as iniciativas de marketing das redes sociais. Estes modelos também incluem tarefas como a cópia em nuvem, o que aumenta a produtividade poupando tempo em tarefas tradicionalmente repetitivas. | 
| Modelos de pacotede integração empresarial | Para configurar os gasodutos VETER (validar, extrair, transformar, enriquecer, rota) recebendo um documento X12 EDI sobre as AS2 e transformando-se em XML, e manuseando mensagens X12, EDIFACT e AS2. | 
| Modelos de padrão de protocolo | Para implementar padrões de protocolo, tais como a resposta de pedido sobre http e integrações em FTP e SFTP. Use estes modelos conforme fornecido, ou baseie-os para padrões de protocolo complexos. | 
||| 

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter mais informações sobre a construção de uma aplicação lógica, consulte [Criar uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-apps-from-templates"></a>Criar aplicações lógicas a partir de modelos

1. Se ainda não o fez, inscreva-se no [portal Azure.](https://portal.azure.com "Portal do Azure")

2. A partir do menu principal do Azure, escolha **Criar uma**aplicação lógica de  >  **integração empresarial**de  >  **Logic App**recursos.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | *seu nome lógica-app* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) para esta aplicação lógica e organize todos os recursos associados a esta app. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | **Desligado** (padrão) ou **Ligado** | Instale [o registo de diagnóstico](../logic-apps/monitor-logic-apps-log-analytics.md) para a sua aplicação lógica utilizando [registos do Monitor Azure](../log-analytics/log-analytics-overview.md). Requer que já tenha um espaço de trabalho log Analytics. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 

5. Percorra o vídeo de introdução e os gatilhos comuns para **os modelos**. Escolha um modelo pré-construído. Por exemplo:

   ![Escolha um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Para criar a sua aplicação lógica do zero, escolha **a App Lógica Em Branco.**

   Quando selecionar um modelo pré-construído, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-construído](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Para continuar com o modelo selecionado, escolha **Utilizar este modelo**. 

7. Com base nos conectores do modelo, é-lhe solicitado que execute qualquer um destes passos:

   * Inscreva-se com as suas credenciais para sistemas ou serviços referenciados pelo modelo.

   * Criar ligações para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurar estas ligações, escolha **Continuar**.

   Por exemplo:

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Quando terminar, a sua aplicação lógica abre e aparece no Logic Apps Designer.

   > [!TIP]
   > Para voltar ao visualizador do modelo, escolha Modelos na barra de **ferramentas** de design. Esta ação descarta quaisquer alterações não guardadas, pelo que uma mensagem de aviso parece confirmar o seu pedido.

8. Continue a construir a sua aplicação lógica.

   > [!NOTE] 
   > Muitos modelos incluem conectores que podem já ter propriedades necessárias pré-povoadas. No entanto, alguns modelos podem ainda exigir que forneça valores antes de poder implementar corretamente a aplicação lógica. Se tentar implantar sem completar os campos de propriedade em falta, obtém uma mensagem de erro. 

## <a name="update-logic-apps-with-templates"></a>Atualizar aplicativos lógicos com modelos

1. No [portal Azure,](https://portal.azure.com "Portal do Azure")encontre e abra a sua aplicação lógica no Th Logic App Designer.

2. Na barra de ferramentas de design, escolha **Modelos**. Esta ação descarta quaisquer alterações não guardadas, pelo que aparece uma mensagem de aviso para que possa confirmar que pretende continuar. Para confirmar, escolha **OK**. Por exemplo:

   ![Escolha "Modelos"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Percorra o vídeo de introdução e os gatilhos comuns para **os modelos**. Escolha um modelo pré-construído. Por exemplo:

   ![Escolha um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Quando selecionar um modelo pré-construído, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-construído](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Para continuar com o modelo selecionado, escolha **Utilizar este modelo**. 

5. Com base nos conectores do modelo, é-lhe solicitado que execute qualquer um destes passos:

   * Inscreva-se com as suas credenciais para sistemas ou serviços referenciados pelo modelo.

   * Criar ligações para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurar estas ligações, escolha **Continuar**.

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A sua aplicação lógica abre e aparece no Logic Apps Designer.

8. Continue a construir a sua aplicação lógica. 

   > [!TIP]
   > Se não guardou as suas alterações, pode descartar o seu trabalho e voltar à sua aplicação lógica anterior. Na barra de ferramentas de design, escolha **'Descartar'.**

> [!NOTE] 
> Muitos modelos incluem conectores que podem já ter propriedades necessárias pré-povoadas. No entanto, alguns modelos podem ainda exigir que forneça valores antes de poder implementar corretamente a aplicação lógica. Se tentar implantar sem completar os campos de propriedade em falta, obtém uma mensagem de erro.

## <a name="deploy-logic-apps-built-from-templates"></a>Implementar aplicativos lógicos construídos a partir de modelos

Depois de fazer as alterações no modelo, pode guardar as suas alterações. Esta ação também publica automaticamente a sua aplicação lógica.

Na barra de ferramentas do estruturador, escolha **Guardar**.

![Poupe e publique a sua aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Obter suporte

* Para perguntas, visite o [Microsoft Q&Uma página de perguntas para aplicações lógicas do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

Saiba sobre a construção de aplicações lógicas através de exemplos, cenários, histórias de clientes e walkthroughs.

> [!div class="nextstepaction"]
> [Reveja exemplos de aplicações lógicas, cenários e walkthroughs](../logic-apps/logic-apps-examples-and-scenarios.md)