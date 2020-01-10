---
title: Criar fluxos de trabalho de aplicativo lógico mais rapidamente usando modelos predefinidos
description: Crie rapidamente fluxos de trabalho de aplicativo lógico usando modelos predefinidos fornecidos pelos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 58c0ff8cf8a579e2b97ebbe195f47e4baef4621a
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666827"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Criar fluxos de trabalho de aplicativo lógico a partir de modelos predefinidos

Para começar a criar fluxos de trabalho com mais rapidez, os aplicativos lógicos fornecem modelos, que são aplicativos lógicos pré-criados que seguem os padrões mais usados. Use esses modelos conforme fornecido ou edite-os de acordo com seu cenário.

Aqui estão algumas categorias de modelo:

| Tipo de modelo | Descrição | 
| ------------- | ----------- | 
| Modelos de nuvem corporativa | Para integrar o blob do Azure, Dynamics CRM, Salesforce, Box e inclui outros conectores para suas necessidades de nuvem corporativa. Por exemplo, você pode usar esses modelos para organizar clientes potenciais de negócios ou fazer backup de seus dados de arquivo corporativo. | 
| Modelos de produtividade pessoal | Melhore a produtividade pessoal definindo lembretes diários, transformando itens de trabalho importantes em listas de tarefas pendentes e automatizando tarefa demorada para uma única etapa de aprovação de usuário. | 
| Modelos de nuvem do consumidor | Para integrar serviços de mídia social, como Twitter, margem de atraso e email. Útil para reforçar iniciativas de marketing de mídia social. Esses modelos também incluem tarefas como cópia em nuvem, o que aumenta a produtividade, economizando tempo em tarefas tradicionalmente repetitivas. | 
| Modelos do Enterprise Integration Pack | Para configurar pipelines VETER (validar, extrair, transformar, enriquecer, rotear), receber um documento EDI X12 sobre AS2 e transformar em XML e manipular mensagens X12, EDIFACT e AS2. | 
| Modelos de padrão de protocolo | Para implementar padrões de protocolo, como solicitação-resposta sobre HTTP e integrações em FTP e SFTP. Use esses modelos como fornecidos ou crie-os para padrões complexos de protocolo. | 
||| 

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter mais informações sobre como criar um aplicativo lógico, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Criar aplicativos lógicos a partir de modelos

1. Se você ainda não fez isso, entre no [portal do Azure](https://portal.azure.com "Portal do Azure").

2. No menu principal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | *nome-da-sua-aplicação-lógica* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) para este aplicativo lógico e organize todos os recursos associados a este aplicativo. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | **Desativado** (padrão) ou **ativado** | Ative o [log de diagnóstico](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) para seu aplicativo lógico por meio de [logs de Azure monitor](../log-analytics/log-analytics-overview.md). Requer que você já tenha um espaço de trabalho Log Analytics. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 

5. Vá além do vídeo de introdução e dos gatilhos comuns para **modelos**. Escolha um modelo predefinido. Por exemplo:

   ![Escolher um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Para criar seu aplicativo lógico do zero, escolha **aplicativo lógico em branco**.

   Ao selecionar um modelo predefinido, você pode exibir mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolher um modelo predefinido](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Para continuar com o modelo selecionado, escolha **usar este modelo**. 

7. Com base nos conectores no modelo, você será solicitado a executar qualquer uma destas etapas:

   * Entre com suas credenciais para sistemas ou serviços que são referenciados pelo modelo.

   * Crie conexões para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma conexão, forneça um nome para a conexão e, se necessário, selecione o recurso que você deseja usar. 

   * Se você já configurou essas conexões, escolha **continuar**.

   Por exemplo:

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Quando você terminar, seu aplicativo lógico será aberto e aparecerá no designer de aplicativos lógicos.

   > [!TIP]
   > Para retornar ao Visualizador de modelos, escolha **modelos** na barra de ferramentas do designer. Essa ação descarta as alterações não salvas, portanto, uma mensagem de aviso é exibida para confirmar sua solicitação.

8. Continue criando seu aplicativo lógico.

   > [!NOTE] 
   > Muitos modelos incluem conectores que podem já ter propriedades obrigatórias preenchidas previamente. No entanto, alguns modelos ainda podem exigir que você forneça valores antes de poder implantar o aplicativo lógico corretamente. Se você tentar implantar sem concluir os campos de propriedade ausentes, receberá uma mensagem de erro. 

## <a name="update-logic-apps-with-templates"></a>Atualizar aplicativos lógicos com modelos

1. Na [portal do Azure](https://portal.azure.com "Portal do Azure"), localize e abra seu aplicativo lógico no designer de aplicativo lógico.

2. Na barra de ferramentas do designer, escolha **modelos**. Essa ação descarta as alterações não salvas, portanto, uma mensagem de aviso é exibida para que você possa confirmar que deseja continuar. Para confirmar, escolha **OK**. Por exemplo:

   ![Escolha "modelos"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Vá além do vídeo de introdução e dos gatilhos comuns para **modelos**. Escolha um modelo predefinido. Por exemplo:

   ![Escolher um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Ao selecionar um modelo predefinido, você pode exibir mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolher um modelo predefinido](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Para continuar com o modelo selecionado, escolha **usar este modelo**. 

5. Com base nos conectores no modelo, você será solicitado a executar qualquer uma destas etapas:

   * Entre com suas credenciais para sistemas ou serviços que são referenciados pelo modelo.

   * Crie conexões para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma conexão, forneça um nome para a conexão e, se necessário, selecione o recurso que você deseja usar. 

   * Se você já configurou essas conexões, escolha **continuar**.

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Seu aplicativo lógico agora é aberto e aparece no designer de aplicativos lógicos.

8. Continue criando seu aplicativo lógico. 

   > [!TIP]
   > Se você não tiver salvo suas alterações, poderá descartar seu trabalho e retornar ao aplicativo lógico anterior. Na barra de ferramentas do designer, escolha **descartar**.

> [!NOTE] 
> Muitos modelos incluem conectores que talvez já tenham preenchido previamente as propriedades necessárias. No entanto, alguns modelos ainda podem exigir que você forneça valores antes de poder implantar o aplicativo lógico corretamente. Se você tentar implantar sem concluir os campos de propriedade ausentes, receberá uma mensagem de erro.

## <a name="deploy-logic-apps-built-from-templates"></a>Implantar aplicativos lógicos criados a partir de modelos

Depois de fazer as alterações no modelo, você pode salvar suas alterações. Essa ação também publica automaticamente seu aplicativo lógico.

Na barra de ferramentas do estruturador, escolha **Guardar**.

![Salvar e publicar seu aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a criação de aplicativos lógicos por meio de exemplos, cenários, histórias de clientes e orientações.

> [!div class="nextstepaction"]
> [Examinar exemplos, cenários e instruções do aplicativo lógico](../logic-apps/logic-apps-examples-and-scenarios.md)