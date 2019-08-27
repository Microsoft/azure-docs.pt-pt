---
title: Executar um guia estratégico na visualização do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como executar um guia estratégico no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.date: 8/21/2019
ms.author: rkarlin
ms.openlocfilehash: 08c88df2f8ccc69f213687209d1d003f3bb1c7b8
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018790"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Tutorial: Configurar respostas de ameaças automatizadas na visualização do Azure Sentinel

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda você a usar guias estratégicos de segurança no Azure Sentinel para definir respostas automatizadas de ameaças a problemas relacionados à segurança detectados pelo Azure Sentinel.


> [!div class="checklist"]
> * Entender os guias estratégicos
> * Criar um guia estratégico
> * Executar um guia estratégico
> * Automatizar respostas de ameaças


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>O que é um guia estratégico de segurança no Azure Sentinel?

Um guia estratégico de segurança é uma coleção de procedimentos que podem ser executados do Sentinela do Azure em resposta a um alerta. Um guia estratégico de segurança pode ajudar a automatizar e orquestrar sua resposta e pode ser executado manualmente ou definido para ser executado automaticamente quando alertas específicos são disparados. Os guias estratégicos de segurança no Azure Sentinel são baseados em [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), o que significa que você obtém toda a potência, a personalização e os modelos internos de aplicativos lógicos. Cada manual é criado para a assinatura específica que você escolher, mas ao examinar a página de guias estratégicos, você verá todos os guias estratégicos em todas as assinaturas selecionadas.

> [!NOTE]
> Os guias estratégicos utilizam os aplicativos lógicos do Azure, portanto, os encargos são Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

Por exemplo, se você estiver preocupado com invasores mal-intencionados acessarem seus recursos de rede, poderá definir um alerta que procura endereços IP mal-intencionados acessando sua rede. Em seguida, você pode criar um guia estratégico que faça o seguinte:
1. Quando o alerta for disparado, abra um tíquete no ServiceNow ou qualquer outro sistema de tíquete de ti.
2. Envie uma mensagem ao canal de operações de segurança no Microsoft Teams ou na margem de atraso para verificar se os analistas de segurança estão cientes do incidente.
3. Envie todas as informações no alerta para o administrador de rede sênior e administrador de segurança. A mensagem de email também inclui dois botões de opção do usuário **Bloquear** ou **ignorar**.
4. O guia estratégico continua a ser executado depois que uma resposta é recebida dos administradores.
5. Se os administradores escolherem **Bloquear**, o endereço IP será bloqueado no firewall e o usuário será desabilitado no Azure AD.
6. Se os administradores escolherem **ignorar**, o alerta será fechado no Azure Sentinel e o incidente será fechado no ServiceNow.

Os guias estratégicos de segurança podem ser executados de forma manual ou automática. Executá-los manualmente significa que, quando você receber um alerta, poderá optar por executar um guia estratégico sob demanda como uma resposta ao alerta selecionado. Executá-las automaticamente significa que, ao criar a regra de correlação, você a define para executar automaticamente um ou mais guias estratégicos quando o alerta é disparado.


## <a name="create-a-security-playbook"></a>Criar um guia estratégico de segurança

Siga estas etapas para criar um novo manual de segurança no Azure Sentinel:

1. Abra o painel **do Sentinela do Azure** .
2. Em **Gerenciamento**, selecione **Guias estratégicos**.

   ![Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na página **Azure Sentinel-guias estratégicos (versão prévia)** , clique no botão **Adicionar** .

   ![Criar uma aplicação lógica](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na página **criar aplicativo lógico** , digite as informações solicitadas para criar seu novo aplicativo lógico e clique em **criar**. 

5. No [**designer do aplicativo lógico**](../logic-apps/logic-apps-overview.md), selecione o modelo que você deseja usar. Se você selecionar um modelo que precisa de credenciais, precisará fornecê-los. Como alternativa, você pode criar um novo manual em branco do zero. Selecione **aplicativo lógico em branco**. 

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Você é levado ao designer do aplicativo lógico, onde você pode criar um novo ou editar o modelo. Para obter mais informações sobre como criar um guia estratégico com [aplicativos lógicos](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Se você estiver criando um manual em branco, no campo **Pesquisar todos os conectores e gatilhos** , digite *Azure Sentinel*e selecione **quando uma resposta a um alerta do Azure Sentinel é**disparada. <br>Depois de criado, o novo guia estratégico aparece na lista **Guias estratégicos** . Se ele não aparecer, clique em **Atualizar**. 

7. Agora, pode definir o que acontece quando aciona o playbook. Você pode adicionar uma ação, condição lógica, condições de caso de alternância ou loops.

   ![Estruturador da Aplicação Lógica](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Como executar um guia estratégico de segurança

Você pode executar um guia estratégico sob demanda.

Para executar um guia estratégico sob demanda:

1. Na página **incidentes** , selecione um incidente e clique em **Exibir detalhes completos**.

2. Na guia **alertas** , clique no alerta no qual você deseja executar o guia estratégico e role até a direita e clique em **Exibir guias estratégicos** e selecione um guia estratégico para **executar** na lista de guias estratégicos disponíveis na assinatura. 



## <a name="automate-threat-responses"></a>Automatizar respostas de ameaças

As equipes SIEM/SOC podem ser inundadas com alertas de segurança regularmente. O volume de alertas gerados é tão grande, que os administradores de segurança disponíveis estão sobrecarregados. Isso resulta muito frequentemente em situações em que muitos alertas não podem ser investigados, deixando a organização vulnerável a ataques que passam despercebidos. 

Muitos, se não a maioria, desses alertas estão em conformidade com padrões recorrentes que podem ser resolvidos por ações de correção específicas e definidas. O Azure Sentinel já permite que você defina sua correção nos guias estratégicos. Também é possível definir a automação em tempo real como parte de sua definição de guia estratégico para permitir que você automatize completamente uma resposta definida para alertas de segurança específicos. Usando a automação em tempo real, as equipes de resposta podem reduzir significativamente sua carga de trabalho automatizando completamente as respostas de rotina para tipos recorrentes de alertas, permitindo que você se concentre mais em alertas exclusivos, análise de padrões, busca de ameaças e muito mais.

Para automatizar as respostas:

1. Selecione o alerta para o qual você deseja automatizar a resposta.
1. Na página **Editar regra de alerta** , em **automação em tempo real**, escolha o **guia estratégico** disparado que você deseja executar quando essa regra de alerta for correspondida.
1. Selecione **Guardar**.

   ![automação em tempo real](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a executar um guia estratégico no Azure Sentinel. Continue na [busca de ameaças de forma proativa usando o](hunting.md) Azure Sentinel.


