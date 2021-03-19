---
title: 'Tutorial: Use livros de reprodução com regras de automação em Azure Sentinel'
description: Utilize este tutorial para o ajudar a usar playbooks juntamente com as regras de automação no Azure Sentinel para automatizar a sua resposta a incidentes e remediar ameaças à segurança.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600629"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Tutorial: Use livros de reprodução com regras de automação em Azure Sentinel

Este tutorial mostra-lhe como usar playbooks juntamente com regras de automação para automatizar a sua resposta de incidente e remediar ameaças de segurança detetadas pelo Azure Sentinel. Quando completar este tutorial poderá:

> [!div class="checklist"]
>
> * Criar uma regra de automação
> * Criar um livro de jogadas
> * Adicionar ações a um livro de jogadas
> * Anexar um livro de jogadas a uma regra de automação ou a uma regra de análise para automatizar a resposta à ameaça

## <a name="what-are-automation-rules-and-playbooks"></a>O que são regras de automação e livros de jogadas?

As regras de automação ajudam-no a triagem de incidentes em Azure Sentinel. Pode usá-los para atribuir automaticamente incidentes ao pessoal certo, fechar incidentes ruidosos ou falsos positivos conhecidos, alterar a sua gravidade e adicionar etiquetas. São também o mecanismo através do qual pode executar playbooks em resposta a incidentes.

Os playbooks são coleções de procedimentos que podem ser executados a partir de Azure Sentinel em resposta a um alerta ou incidente. Um livro de jogadas pode ajudar a automatizar e orquestrar a sua resposta, e pode ser configurado para ser executado automaticamente quando são gerados alertas ou incidentes específicos, estando ligados a uma regra de análise ou a uma regra de automação, respectivamente. Também pode ser executado manualmente a pedido.

Os playbooks em Azure Sentinel são baseados em fluxos de trabalho construídos em [Azure Logic Apps](../logic-apps/logic-apps-overview.md), o que significa que você obtém toda a potência, personalização e modelos incorporados de Apps Lógicas. Cada livro de jogadas é criado para a subscrição específica a que pertence, mas o ecrã **playbooks** mostra-lhe todos os playbooks disponíveis em todas as subscrições selecionadas.

> [!NOTE]
> Como os playbooks utilizam as Aplicações Lógicas Azure, podem ser aplicadas taxas adicionais. Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

Por exemplo, se pretender impedir que os utilizadores potencialmente comprometidos se movam pela sua rede e roubem informações, pode criar uma resposta automatizada e multifacetada a incidentes gerados por regras que detetem utilizadores comprometidos. Começa-se por criar um livro de jogadas que toma as seguintes ações:

1. Quando o livro de jogadas é chamado por uma regra de automação que lhe passa um incidente, o livro abre um bilhete no [ServiceNow](/connectors/service-now/) ou em qualquer outro sistema de bilhética informática.

1. Envia uma mensagem para o seu canal de operações de segurança em [Microsoft Teams](/connectors/teams/) ou [Slack](/connectors/slack/) para se certificar de que os seus analistas de segurança estão cientes do incidente.

1. Também envia todas as informações do incidente numa mensagem de correio eletrónico para a sua administração de rede sénior e administração de segurança. A mensagem de e-mail incluirá botões de opção **de utilizador Block** e **Ignore.**

1. O livro de jogadas aguarda até que uma resposta seja recebida dos administradores, e depois continua com os seus próximos passos.

1. Se os administradores escolherem **o Bloco,** envia um comando para Azure AD para desativar o utilizador e outro para a firewall para bloquear o endereço IP.

1. Se os administradores escolherem **o Ignore,** o livro de jogadas encerra o incidente em Azure Sentinel e o bilhete no ServiceNow.

Para ativar o livro de jogadas, criará uma regra de automação que funciona quando estes incidentes são gerados. Esta regra tomará estes passos:

1. A regra altera o estado do incidente para **Ative**.

1. Atribui o incidente ao analista encarregado de gerir este tipo de incidente.

1. Adiciona a etiqueta "utilizador comprometido".

1. Finalmente, chama o livro de jogadas que acabaste de criar. (São[necessárias permissões especiais para este passo](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Os playbooks podem ser executados automaticamente em resposta a incidentes, criando regras de automação que chamam os playbooks como ações, como no exemplo acima. Também podem ser executados automaticamente em resposta aos alertas, dizendo à regra de análise para executar automaticamente um ou mais livros de jogadas quando o alerta é gerado. 

Também pode optar por executar um livro manualmente a pedido, como resposta a um alerta selecionado.

Obtenha uma introdução mais completa e detalhada para automatizar a resposta de ameaça usando [regras de automação](automate-incident-handling-with-automation-rules.md) e [playbooks](automate-responses-with-playbooks.md) em Azure Sentinel.

> [!IMPORTANT]
>
> - **As regras** de automação , e a utilização do gatilho de **incidentes** para os playbooks, estão atualmente em **PRÉ-VISUALIZAÇÃO**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="create-a-playbook"></a>Criar um livro de jogadas

Siga estes passos para criar um novo livro de jogadas em Azure Sentinel:

### <a name="prepare-the-playbook-and-logic-app"></a>Prepare o livro de jogadas e a App Lógica

1. A partir do menu de navegação **Azure Sentinel,** **selecione Automation**.

1. No menu superior, selecione **Criar** e **Adicionar novo livro de jogadas**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Adicione um novo livro de jogadas":::

    Um novo separador de navegador irá abrir e levá-lo ao **Assistente de aplicações lógicas Criar.**

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Criar uma aplicação lógica":::

1. Insira o seu grupo **de Subscrição** e **Recursos** e dê ao seu livro de jogadas um nome com o nome **da aplicação Logic**.

1. Para **a Região**, selecione a região Azure onde as informações da Sua Aplicação Lógica devem ser armazenadas.

1. Se pretender monitorizar a atividade deste livro de jogadas para fins de diagnóstico, marque a caixa **de verificação de analítica de registo de registo** e insira o nome do espaço de trabalho Log **Analytics.**

1. Se pretender aplicar etiquetas no seu livro de jogadas, clique em **Seguinte : Tags >** (não ligados a tags aplicadas pelas regras de automação. [Saiba mais sobre tags).](../azure-resource-manager/management/tag-resources.md) Caso contrário, clique em **'Rever + Criar' (Revisão+ Criação).** Confirme os detalhes que forneceu e clique em **Criar**.

1. Enquanto o seu livro de jogadas está a ser criado e implementado (isto levará alguns minutos), será levado para um ecrã chamado **Microsoft.EmptyWorkflow**. Quando a mensagem "A sua implementação está completa", clique **em Ir para o recurso.**

1. Você será levado para o seu novo design de [apps lógicas,](../logic-apps/logic-apps-overview.md)onde você pode começar a desenhar o fluxo de trabalho. Você verá um ecrã com um pequeno vídeo introdutório e alguns gatilhos e modelos de Aplicação Lógica comumente usados. [Saiba mais](../logic-apps/logic-apps-create-logic-apps-from-templates.md) sobre a criação de um livro com as Aplicações Lógicas.

1. Selecione o modelo **de Aplicação lógica em branco.**

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galeria de modelo de design de aplicativos de lógica":::

### <a name="choose-the-trigger"></a>Escolha o gatilho

Todos os livros devem começar com um gatilho. O gatilho define a ação que iniciará o livro de jogadas e o esquema que o livro de jogadas espera receber.

1. Na barra de pesquisa, procure por Azure Sentinel. Selecione **Azure Sentinel** quando aparecer nos resultados.

1. No **separador Triggers** resultante, verá os dois gatilhos oferecidos por Azure Sentinel:
    - Quando uma resposta a um Alerta sentinela Azure é desencadeada
    - Quando é acionada a regra de criação de incidentes do Azure Sentinel

   Escolha o gatilho que corresponde ao tipo de livro de jogadas que está a criar.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Escolha um gatilho para o seu livro de jogadas":::

### <a name="add-actions"></a>Adicionar ações

Agora pode definir o que acontece quando se chama o livro de jogadas. Pode adicionar ações, condições lógicas, loops ou mudar as condições de caixa, tudo selecionando **Novo passo**. Esta seleção abre uma nova moldura no designer, onde pode escolher um sistema ou uma aplicação para interagir ou uma condição a definir. Introduza o nome do sistema ou aplicação na barra de pesquisa na parte superior da moldura e, em seguida, escolha entre os resultados disponíveis.

Em cada um destes passos, clicar em qualquer campo exibe um painel com dois menus: **Conteúdo dinâmico** e **Expressão**. A partir do menu **de conteúdo Dinâmico,** pode adicionar referências aos atributos do alerta ou incidente que foi passado para o livro de jogadas, incluindo os valores e atributos de todas as entidades envolvidas. A partir do menu **Expressão,** pode escolher entre uma grande biblioteca de funções para adicionar lógica adicional aos seus passos.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Estruturador da Aplicação Lógica":::

Esta imagem mostra as ações e condições que adicionaria na criação do livro de jogadas descrito no exemplo no início deste documento. A única diferença é que no livro de jogadas aqui mostrado, está a usar o gatilho de **alerta** em vez do gatilho do **incidente**. Isto significa que você vai chamar este livro de jogadas de uma regra de análise diretamente, não de uma regra de automação. Ambas as formas de chamar um livro de jogadas serão descritas abaixo.

## <a name="automate-threat-responses"></a>Automatizar respostas de ameaças

Criou o seu livro de jogadas e definiu o gatilho, definiu as condições e prescreveu as ações que irá tomar e as saídas que produzirá. Agora é necessário determinar os critérios em que irá funcionar e configurar o mecanismo de automatização que o irá executar quando esses critérios forem cumpridos.

### <a name="respond-to-incidents"></a>Responder a incidentes

Usa um livro de jogadas para responder a um **incidente** criando uma [regra de automação](automate-incident-handling-with-automation-rules.md) que será executada quando o incidente é gerado, e por sua vez vai chamar o livro de jogadas.

Para criar uma regra de automação:

1. A partir da lâmina **Automation** no menu de navegação Azure Sentinel, selecione **Criar** a partir do menu superior e, em seguida, **adicionar nova regra**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Adicione uma nova regra":::

1. Abre-se o novo painel **de regras de automação.** Insira um nome para a sua regra.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Criar uma regra de automação":::

1. Se pretender que a regra da automatização produza efeitos apenas em determinadas regras de análise, especifique quais, modificando a condição de **nome da regra If Analytics.**

1. Adicione quaisquer outras condições de que pretenda que a ativação desta regra de automação dependa. Clique **em Adicionar a condição** e escolha as condições da lista de drop-down. A lista de condições é povoada por detalhes de alerta e áreas de identificação de entidades.

1. Escolha as ações que deseja que esta regra de automação tome. As ações disponíveis incluem **Atribuir proprietário,** **alterar o estado,** **alterar a gravidade,** **adicionar tags** e executar o livro de **jogadas**. Pode adicionar o máximo de ações que quiser.

1. Se adicionar uma ação **de playbook Run,** será solicitado que escolha entre a lista de playbooks disponíveis. Apenas os playbooks que começam com o gatilho do **incidente** podem ser executados a partir das regras de automação, pelo que só eles aparecerão na lista.

    > [!IMPORTANT]
    > Azure Sentinel deve ter permissões explícitas para executar livros de reprodução a partir de regras de automação. Se um livro de jogadas aparecer "acinzentado" na lista de suspensos, significa que o Sentinel não tem permissão para o grupo de recursos desse livro de jogadas. Clique no link **de permissões do livro de jogadas Manage** para atribuir permissões.
    > No painel **de permissões Manage** que se abre, marque as caixas de verificação dos grupos de recursos que contêm os livros de reprodução que pretende executar e clique em **Aplicar**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Gerir permissões":::
    > - Você próprio deve ter permissões **de proprietário** em qualquer grupo de recursos ao qual pretende conceder permissões do Azure Sentinel, e deve ter o papel de Contribuinte **de Aplicação Lógica** em qualquer grupo de recursos que contenha playbooks que pretenda executar.
    > - Em uma implantação multi-inquilino, se o livro que você quer executar é em um inquilino diferente, você deve conceder a Azure Sentinel permissão para executar o livro de jogadas no inquilino do livro de jogadas.
    >    1. A partir do menu de navegação Azure Sentinel no inquilino dos playbooks, selecione **Definições**.
    >    1. Na lâmina **Definições,** selecione o **separador Definições** e, em seguida, o expansor de permissões do Livro de **Reprodução.**
    >    1. Clique no botão **de permissões configurar** para abrir o painel **de permissões De gestão** acima mencionado e continue como descrito lá.

1. Desaça uma data de validade para a sua regra de automação se quiser que tenha uma.

1. Introduza um número sob **Ordem** para determinar onde na sequência das regras de automação esta regra será executada.

1. Clique em **Aplicar**. Acabou-se!

[Descubra outras formas](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) de criar regras de automação.

### <a name="respond-to-alerts"></a>Responder a alertas

Utiliza um livro de jogadas para responder a um **alerta** criando uma **regra de análise,** ou editando uma existente, que corre quando o alerta é gerado, e selecionando o seu livro de jogadas como uma resposta automatizada no [assistente de regras](tutorial-detect-threats-custom.md)de análise .

1. A partir da lâmina **Analytics** no menu de navegação Azure Sentinel, selecione a regra de análise para a qual deseja automatizar a resposta e clique em **Editar** no painel de detalhes.

1. No **assistente de regras Analytics - Edite a página de regras existente,** selecione o separador **de resposta automatizado.**

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Separador de resposta automatizado":::

1. Escolha o seu livro de jogadas na lista de drop-down. Pode escolher mais do que um livro de jogadas, mas apenas os playbooks que usam o gatilho de **alerta** estarão disponíveis.

1. No **separador 'Rever' e criar,** selecione **Guardar**.

## <a name="run-a-playbook-on-demand"></a>Executar um manual de procedimentos a pedido

Também pode executar um livro de jogadas a pedido.

 > [!NOTE]
 > Apenas os livros de reprodução que utilizem o gatilho de **alerta** podem ser executados a pedido.

Para executar um livro de jogadas a pedido:

1. Na página **Incidentes,** selecione um incidente e clique em **Ver todos os detalhes**.

2. No separador **Alertas,** clique no alerta em que pretende executar o livro de jogadas e percorra todo o caminho até à direita e clique em **Ver playbooks** e selecione um livro de jogadas para **executar** a partir da lista de playbooks disponíveis na subscrição.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar playbooks e regras de automação no Azure Sentinel para responder a ameaças. 
- Aprenda a [caçar proactivamente ameaças](hunting.md) usando Azure Sentinel.
