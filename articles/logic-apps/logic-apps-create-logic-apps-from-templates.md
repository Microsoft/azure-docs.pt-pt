---
title: Crie fluxos de trabalho de aplicativos lógicos mais rapidamente usando modelos pré-construídos
description: Construa rapidamente fluxos de trabalho de aplicativos de lógica usando modelos pré-construídos fornecidos por Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593097"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Create logic app workflows from prebuilt templates (Criar fluxos de trabalho de aplicações lógicas a partir de modelos pré-criados)

Para começar a criar fluxos de trabalho mais rapidamente, as Aplicações Lógicas fornecem modelos, que são aplicações lógicas pré-construídas que seguem padrões comumente usados. Utilize estes modelos conforme fornecido ou edite-os para se adaptar ao seu cenário.

Aqui estão algumas categorias de modelos:

| Tipo de modelo | Description | 
| ------------- | ----------- | 
| Modelos de nuvem de empresa | Para integrar a Azure Blob, Dynamics CRM, Salesforce, Box, e inclui outros conectores para as necessidades da sua nuvem empresarial. Por exemplo, pode usar estes modelos para organizar leads de negócios ou fazer o back up dos seus dados de ficheiros corporativos. | 
| Modelos de produtividade pessoal | Melhorar a produtividade pessoal definindo lembretes diários, transformando itens de trabalho importantes em listas de tarefas e automatizando tarefas longas até um único passo de aprovação do utilizador. | 
| Modelos de nuvem de consumo | Para integrar serviços de redes sociais como Twitter, Slack e e-mail. Útil para fortalecer iniciativas de marketing nas redes sociais. Estes modelos também incluem tarefas como a cópia em nuvem, que aumenta a produtividade poupando tempo em tarefas tradicionalmente repetitivas. | 
| Modelos de pacote de integração empresarial | Para configurar os gasodutos VETER (validar, extrair, transformar, enriquecer, rota), receber um documento X12 EDI sobre AS2 e transformar-se em XML, e manusear mensagens X12, EDIFACT e AS2. | 
| Modelos de padrões de protocolo | Para a implementação de padrões de protocolo, tais como resposta de pedido sobre HTTP e integrações em FTP e SFTP. Use estes modelos conforme fornecido, ou construa neles para padrões de protocolo complexos. | 
||| 

Se não tiver uma subscrição do Azure, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter mais informações sobre a construção de uma aplicação lógica, consulte [Criar uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-apps-from-templates"></a>Criar aplicações lógicas a partir de modelos

1. Se ainda não o fez, inscreva-se no [portal Azure](https://portal.azure.com "Portal do Azure").

2. A partir do menu Azure principal, escolha **Criar uma** App lógica de  >  **integração**  >  **empresarial de** recursos.

   ![Portal do Azure, Novo, Integração Empresarial, Aplicação Lógica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições da tabela abaixo desta imagem:

   ![Indicar os detalhes da aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | *seu-lógica-app-nome* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *o-nome-da-sua-subscrição-do-Azure* | Selecione a subscrição do Azure que pretende utilizar. | 
   | **Grupo de recursos** | *o-nome-do-seu-grupo-de-recursos-do-Azure* | Crie ou selecione um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) para esta aplicação lógica e para organizar todos os recursos associados a esta app. | 
   | **Localização** | *a-região-do-seu-datacenter-do-Azure* | Selecione a região do datacenter para implementar a sua aplicação lógica, como, por exemplo, E.U.A. Oeste. | 
   | **Log Analytics** | **Off** (predefinido) ou **On** | Confiúdo [de registo de diagnóstico](../logic-apps/monitor-logic-apps-log-analytics.md) para a sua aplicação lógica utilizando [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md). Requer que já tenha um espaço de trabalho log analytics. | 
   |||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard**. Desta forma, a aplicação lógica aparece automaticamente no seu dashboard do Azure e abre-se após a implementação. Escolha **Criar**.

   > [!NOTE]
   > Se não quiser afixar a sua aplicação lógica, tem de localizá-la e abri-la manualmente após a implementação, para poder continuar.

   Após o Azure implementar a aplicação lógica, abre-se o Estruturador de Aplicações Lógicas, que mostra uma página com um vídeo de introdução. 
   Abaixo do vídeo, estão disponíveis modelos para padrões de aplicações lógicas comuns. 

5. Percorra o vídeo de introdução e os gatilhos comuns para **os Modelos**. Escolha um modelo pré-construído. Por exemplo:

   ![Escolha um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Para criar a sua aplicação lógica de raiz, escolha **a Blank Logic App**.

   Ao selecionar um modelo pré-construído, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-construído](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Para continuar com o modelo selecionado, escolha **Utilize este modelo.** 

7. Com base nos conectores do modelo, é solicitado que realize qualquer um destes passos:

   * Inscreva-se com as suas credenciais em sistemas ou serviços que são referenciados pelo modelo.

   * Criar ligações para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurar estas ligações, escolha **Continue**.

   Por exemplo:

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Quando terminar, a sua aplicação lógica abre e aparece no Logic Apps Designer.

   > [!TIP]
   > Para voltar ao visualizador do modelo, escolha **Modelos** na barra de ferramentas do designer. Esta ação descarta quaisquer alterações não guardadas, pelo que uma mensagem de aviso parece confirmar o seu pedido.

8. Continue a construir a sua aplicação lógica.

   > [!NOTE] 
   > Muitos modelos incluem conectores que podem já ter preopulado propriedades necessárias. No entanto, alguns modelos podem ainda exigir que forneça valores antes de poder implementar corretamente a aplicação lógica. Se tentar implantar sem completar os campos de propriedade desaparecidos, obtém uma mensagem de erro. 

## <a name="update-logic-apps-with-templates"></a>Atualizar aplicativos lógicos com modelos

1. No [portal Azure,](https://portal.azure.com "Portal do Azure")encontre e abra a sua aplicação lógica no th Logic App Designer.

2. Na barra de ferramentas do designer, escolha **Modelos.** Esta ação descarta quaisquer alterações não guardadas, pelo que aparece uma mensagem de aviso para que possa confirmar que pretende continuar. Para confirmar, escolha **OK**. Por exemplo:

   ![Escolha "Modelos"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Percorra o vídeo de introdução e os gatilhos comuns para **os Modelos**. Escolha um modelo pré-construído. Por exemplo:

   ![Escolha um modelo de aplicativo lógico](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Ao selecionar um modelo pré-construído, pode ver mais informações sobre esse modelo. 
   Por exemplo:

   ![Escolha um modelo pré-construído](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Para continuar com o modelo selecionado, escolha **Utilize este modelo.** 

5. Com base nos conectores do modelo, é solicitado que realize qualquer um destes passos:

   * Inscreva-se com as suas credenciais em sistemas ou serviços que são referenciados pelo modelo.

   * Criar ligações para quaisquer serviços ou sistemas referenciados pelo modelo. Para criar uma ligação, forneça um nome para a sua ligação e, se necessário, selecione o recurso que pretende utilizar. 

   * Se já configurar estas ligações, escolha **Continue**.

   ![Criar ligações](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A sua aplicação lógica abre e aparece no Logic Apps Designer.

8. Continue a construir a sua aplicação lógica. 

   > [!TIP]
   > Se não tiver guardado as suas alterações, pode descartar o seu trabalho e regressar à sua aplicação lógica anterior. Na barra de ferramentas do designer, escolha **Descartar.**

> [!NOTE] 
> Muitos modelos incluem conectores que podem já ter propriedades pré-povoadas necessárias. No entanto, alguns modelos podem ainda exigir que forneça valores antes de poder implementar corretamente a aplicação lógica. Se tentar implantar sem completar os campos de propriedade desaparecidos, obtém uma mensagem de erro.

## <a name="deploy-logic-apps-built-from-templates"></a>Implementar aplicativos lógicos construídos a partir de modelos

Depois de fazer as alterações ao modelo, pode guardar as suas alterações. Esta ação também publica automaticamente a sua aplicação lógica.

Na barra de ferramentas do estruturador, escolha **Guardar**.

![Guarde e publique a sua aplicação lógica](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Obter suporte

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

Conheça a construção de aplicativos lógicos através de exemplos, cenários, histórias de clientes e passeios.

> [!div class="nextstepaction"]
> [Rever exemplos de aplicativos de lógica, cenários e walkthroughs](../logic-apps/logic-apps-examples-and-scenarios.md)
