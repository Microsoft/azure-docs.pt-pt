---
title: 'Tutorial: Executar um livro de jogadas em Azure Sentinel'
description: 'Tutorial: Este artigo descreve como executar um livro de jogadas em Azure Sentinel.'
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
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585089"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Tutorial: Criar respostas de ameaça automatizadas no Azure Sentinel



Este tutorial ajuda-o a usar playbooks de segurança em Azure Sentinel para definir respostas de ameaça automatizadas a questões relacionadas com a segurança detetadas pelo Azure Sentinel.


> [!div class="checklist"]
> * Compreender os livros de jogadas
> * Criar um livro de jogadas
> * Executar um livro de jogadas
> * Automatizar respostas de ameaças


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>O que é um livro de segurança em Azure Sentinel?

Um livro de segurança é uma coleção de procedimentos que podem ser executados a partir de Azure Sentinel em resposta a um alerta. Um manual de segurança pode ajudar a automatizar e orquestrar a sua resposta, e pode ser executado manualmente ou definido para ser executado automaticamente quando os alertas específicos são disparados. Os playbooks de segurança em Azure Sentinel são baseados em [Aplicações Lógicas Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), o que significa que você obtém todo o poder, personalizabilidade e modelos incorporados de Apps Lógicas. Cada livro de jogadas é criado para a subscrição específica que escolher, mas quando olhar para a página Playbooks, verá todos os playbooks em quaisquer subscrições selecionadas.

> [!NOTE]
> Os playbooks alavancam as Aplicações Lógicas Azure, pelo que as tarifas se aplicam. Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

Por exemplo, se estiver preocupado com o acesso de atacantes maliciosos aos recursos da sua rede, pode definir um alerta que procura endereços IP maliciosos a aceder à sua rede. Depois, pode criar um livro que faça o seguinte:
1. Quando o alerta for acionado, abra um bilhete no ServiceNow ou em qualquer outro sistema de bilhética de TI.
2. Envie uma mensagem para o seu canal de operações de segurança nas Equipas microsoft ou na Slack para garantir que os seus analistas de segurança estão cientes do incidente.
3. Envie toda a informação no alerta para o seu administrador de rede sénior e administrador de segurança. A mensagem de e-mail também inclui dois botões de opção do utilizador **Bloquear** ou **Ignorar**.
4. O livro continua a correr depois de uma resposta ser recebida dos administradores.
5. Se os administradores escolherem o **Bloco,** o endereço IP está bloqueado na firewall e o utilizador é desativado em Azure AD.
6. Se os administradores escolherem **O Ignore,** o alerta está fechado em Azure Sentinel e o incidente está encerrado no ServiceNow.

Os livros de segurança podem ser executados manualmente ou automaticamente. Executá-los manualmente significa que quando se recebe um alerta, pode optar por executar um livro de jogadas a pedido como resposta ao alerta selecionado. Executá-los automaticamente significa que, ao ser autor da regra da correlação, decidiu-a para executar automaticamente um ou mais playbooks quando o alerta é acionado.


## <a name="create-a-security-playbook"></a>Criar um livro de segurança

Siga estes passos para criar um novo manual de segurança em Azure Sentinel:

1. Abra o painel **Azure Sentinel.**
2. Under **Management**, selecione **Playbooks**.

   ![Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na página **Azure Sentinel - Playbooks,** clique em **Adicionar** botão.

   ![Criar uma aplicação lógica](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na página de **aplicações Create Logic,** escreva as informações solicitadas para criar a sua nova aplicação lógica e clique em **Criar**. 

5. No [**Logic App Designer,**](../logic-apps/logic-apps-overview.md)selecione o modelo que pretende utilizar. Se selecionar um modelo que exija credenciais, terá de as fornecer. Em alternativa, pode criar um novo livro em branco do zero. Selecione **App lógica em branco**. 

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Você é levado para o Logic App Designer onde você pode construir novo ou editar o modelo. Para mais informações sobre a criação de um livro de jogadas com [Aplicações Lógicas.](../logic-apps/logic-apps-create-logic-apps-from-templates.md)

7. Se estiver a criar um livro em branco, no campo **Procurar todos os conectores e acionadores,** *digite O Sentinel,* e selecione **Quando é desencadeada uma resposta a um alerta Azure Sentinel**. <br>Depois de criado, o novo livro de jogadas aparece na lista de **Playbooks.** Se não aparecer, clique em **Refresh**.

1. Utilize as funções de **Entidades Get,** que lhe permitem obter as entidades relevantes de dentro da lista de **Entidades,** tais como contas, endereços IP e anfitriões. Isto permitir-lhe-á executar ações em entidades específicas.

7. Agora, pode definir o que acontece quando aciona o playbook. Pode adicionar uma ação, condição lógica, mudar as condições de caso ou loops.

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Como executar um livro de segurança

Podes fazer um livro a pedido.

Para executar um livro de jogadas a pedido:

1. Na página de **incidentes,** selecione um incidente e clique em **Ver todos os detalhes**.

2. No separador **Alertas,** clique no alerta em que pretende executar o livro de jogadas e percorra todo o caminho para a direita e clique em **ver os playbooks** e selecione um livro de jogadas para **executar** a partir da lista de playbooks disponíveis na subscrição. 



## <a name="automate-threat-responses"></a>Automatizar respostas de ameaças

As equipas SIEM/SOC podem ser inundadas regularmente com alertas de segurança. O volume de alertas gerados é tão grande que os administradores de segurança disponíveis estão sobrecarregados. Isto resulta demasiadas vezes em situações em que muitos alertas não podem ser investigados, deixando a organização vulnerável a ataques que passam despercebidos. 

Muitos, se não a maioria, destes alertas estão em conformidade com padrões recorrentes que podem ser abordados por ações de reparação específicas e definidas. O Azure Sentinel já lhe permite definir a sua reparação em playbooks. Também é possível definir a automatização em tempo real como parte da definição de playbook para permitir automatizar totalmente uma resposta definida a determinados alertas de segurança. Utilizando a automatização em tempo real, as equipas de resposta podem reduzir significativamente a sua carga de trabalho automatizando totalmente as respostas de rotina a tipos recorrentes de alertas, permitindo-lhe concentrar-se mais em alertas únicos, analisando padrões, caça de ameaças e muito mais.

Para automatizar respostas:

1. Selecione o alerta para o qual pretende automatizar a resposta.
1. Na página de **'Editar',** sob automatização em **tempo real,** escolha o **manual de jogadas acionado** que pretende executar quando esta regra de alerta for correspondida.
1. Selecione **Guardar**.

   ![automação em tempo real](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeste a executar um livro de jogadas em Azure Sentinel. Continue a [procurar proativamente ameaças](hunting.md) usando O Sentinel a azure.


