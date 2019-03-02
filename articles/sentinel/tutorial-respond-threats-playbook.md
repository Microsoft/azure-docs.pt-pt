---
title: Executar um playbook de sentinela na pré-visualização no Azure | Documentos da Microsoft
description: Este artigo descreve como executar um playbook no sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 360ff53838b8be5cf91ee097b7c2566544ea9098
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244350"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Tutorial: Configurar respostas de ameaças automatizada sentinela na pré-visualização no Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda-o a utilizar os playbooks de segurança no Azure sentinela para definir as respostas de ameaças automatizadas para problemas de segurança detetados pelo Azure sentinela.


> [!div class="checklist"]
> * Compreender os playbooks
> * Criar um playbook
> * Executar um playbook


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>O que é um playbook de segurança no Azure sentinela?

Um playbook de segurança é uma coleção de procedimentos que pode ser executada a partir do Azure sentinela em resposta a um alerta. Um playbook de segurança pode ajudar a automatizar e orquestrar as suas respostas e pode ser executado manualmente ou definido para ser executada automaticamente quando são acionadas alertas específicos. Playbooks de segurança no Azure sentinela são baseiam [do Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), que significa que obtém todo o poder, capacidade de personalização e modelos internos do Logic Apps. Cada playbook é criado para a subscrição específica que escolher, mas quando examinar a página Playbooks, verá todos os playbooks em quaisquer subscrições selecionadas.

> [!NOTE]
> Playbooks de tirar partido do Azure Logic Apps, por isso são aplicáveis encargos. Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

Por exemplo, se estiver preocupado com invasores acedem aos seus recursos de rede, pode definir um alerta que procura por endereços IP maliciosos acedam à rede. Em seguida, pode criar um playbook que faça o seguinte:
1. Quando o alerta é acionado, abra um pedido de suporte no ServiceNow ou qualquer outro sistema de tíquetes de TI.
2. Envie uma mensagem para o canal de operações de segurança no Microsoft Teams ou no Slack para certificar-se de que os analistas de segurança estão cientes do incidente.
3. Enviar todas as informações no alerta ao administrador de rede Seniores administração e segurança. A mensagem de e-mail também inclui dois botões de opção do usuário **bloco** ou **ignorar**.
4. O playbook continua a executar após é recebida uma resposta dos administradores.
5. Se os administradores escolher **bloco**, o endereço IP está bloqueado na firewall e o utilizador está desabilitado no Azure AD.
6. Se os administradores escolher **ignorar**, o alerta estiver fechado no sentinela do Azure e o incidente é fechado no ServiceNow.

Playbooks de segurança podem ser executados manual ou automaticamente. Executá-los manualmente, significa que, quando receber um alerta, pode optar por executar uma playbook sob demanda como uma resposta para o alerta selecionado. Executá-los automaticamente, significa que ao criar a regra de correlação, defina-o para executar automaticamente uma ou mais playbooks quando o alerta é acionado.


## <a name="create-a-security-playbook"></a>Criar um playbook de segurança

Siga estes passos para criar um playbook de segurança de novo no Azure sentinela:

1.  Abra o **Azure sentinela** dashboard.
2.  Sob **gerenciamento**, selecione **Playbooks**.

   ![Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbook.png)

3. Na **sentinela de Azure - Playbooks (pré-visualização)** página, clique em **Add** botão.

   ![Criar uma aplicação lógica](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na **Criar aplicação lógica** página, escreva as informações pedidas para criar a sua aplicação lógica nova e clique em **criar**. 

5. Na [ **Estruturador da aplicação lógica**](../logic-apps/logic-apps-overview.md), selecione o modelo que pretende utilizar. Se selecionar um modelo que exige credenciais, terá de fornecê-los. Em alternativa, pode criar um playbook novo em branco do zero. Selecione **aplicação lógica em branco**. 

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. É direcionado para o Estruturador da aplicação lógica em que pode criar novo ou editar o modelo. Para obter mais informações sobre como criar um playbook com [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Se estiver a criar um playbook em branco, no **procurar todos os conectores e acionadores** , digite *Azure sentinela*e selecione **quando uma resposta a um alerta de sentinela de Azure é oacionadas**. <br>Depois de criado, o playbook novo é apresentado na **Playbooks** lista. Se não aparecer, clique em **atualizar**. 

7. Agora, pode definir o que acontece quando aciona o playbook. Pode adicionar uma ação, condição lógica, condições de caso de comutador ou loops.

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Como executar um playbook de segurança

Pode executar um playbook a pedido.

Para executar uma playbook sob demanda:

1. Na **casos** página, selecione um caso e clique em **ver os detalhes completos**.

2. Na **alertas** separador, clique no alerta que pretende executar o playbook e desloque-se todo o caminho para a direita e clique em **ver playbooks** e selecione um manual a ser **executar** do lista de playbooks disponíveis na subscrição. 




## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a executar um playbook no sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes: Neste tutorial, aprendeu a executar um playbook no sentinela do Azure. Continuar para o [como proativamente hunt relativamente a ameaças](hunting.md) usando sentinela do Azure.
> [!div class="nextstepaction"]
> [Procurar ameaças](hunting.md) proativamente encontrar ameaças na sua rede.

