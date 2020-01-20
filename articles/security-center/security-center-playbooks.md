---
title: Playbook de Segurança no Centro de Segurança do Azure | Microsoft Azure
description: Este documento ajuda-o a utilizar os playbooks de segurança no Centro de Segurança do Azure para automatizar as respostas a incidentes de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: b4a2617bec145cf735058bc13c01bf8ebdfe6550
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275506"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Playbook de Segurança no Centro de Segurança do Azure (Pré-visualização)
Este documento ajuda-o a utilizar os playbooks de segurança no Centro de Segurança do Azure para responder a problemas relacionados com segurança.

> [!NOTE]
> O recurso de guias estratégicos de segurança foi renomeado para automação do fluxo de trabalho. Verifique a página de [automação do fluxo de trabalho (versão prévia)](https://docs.microsoft.com/azure/security-center/workflow-automation) para obter informações de conteúdo atualizadas. Esse conteúdo arquivado temporário está disponível para clientes que demonstraram isso anteriormente e serão removidos gradualmente. Qualquer recurso na versão prévia é guiado pelos [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="what-is-security-playbook-in-security-center"></a>O que é o playbook de segurança do Centro de Segurança?
O playbook de segurança é uma coleção de procedimentos que podem ser executados no Centro de Segurança quando um alerta selecionado aciona um determinado playbook. Pode ajudar a automatizar e orquestrar as suas respostas a alertas de segurança específicos que o Centro de Segurança detete. Os Playbooks de Segurança do Centro de Segurança baseiam-se no [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), o que significa que pode utilizar os modelos disponibilizados na categoria de segurança nos modelos do Logic Apps, os quais podem ser modificados consoante as suas necessidades, ou pode criar playbooks novos com o [fluxo de trabalho do Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) e o Centro de Segurança como o acionador.

> [!NOTE]
> O Playbook tira partido do Azure Logic Apps, pelo que se aplicam custos. Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Como criar um playbook de segurança a partir do Centro de Segurança?
Siga estes passos para criar um playbook de segurança novo a partir do Centro de Segurança:

1.  Abra o dashboard do **Centro de Segurança**.
2.  Na secção **Automatização e Orquestração**, no painel do lado esquerdo, e clique em **Playbooks (Pré-visualização)** .

    ![Aplicação Lógica](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. Na página **Centro de Segurança - Playbooks (Pré-visualização)** , clique no botão **Adicionar**.

    ![Criar uma aplicação lógica](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Na página **Criar Aplicação lógica**, escreva as informações pedidas para criar a sua aplicação lógica nova e clique no botão **Criar**. Após a conclusão da criação, o playbook novo é apresentado na lista. Se não for apresentado, clique no botão **Atualizar**. Quando o vir, clique no mesmo para começar a editá-lo.

    ![Criar uma aplicação lógica](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Surge o **Estruturador da Aplicação Lógica**. Clique em **Aplicação Lógica em Branco** para criar um playbook novo. Também pode selecionar **Segurança**, nas categorias, e utilizar um dos modelos.

    ![Estruturador da Aplicação Lógica](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. No campo **Procurar todos os conectores e acionadores**, escreva *Centro de Segurança do Azure* e selecione **Quando é acionada uma resposta a um alerta do Centro de Segurança do Azure**.

    ![Acionador](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Agora, pode definir o que acontece quando aciona o playbook. Pode adicionar uma ação, uma condição lógica e condições e ciclos de switch cases.

    ![Estruturador da Aplicação Lógica](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Como executar um playbook de segurança do Centro de Segurança?

Pode executar um playbook de segurança no Centro de Segurança se quiser orquestrar, obter mais informações de outros serviços ou corrigir problemas. Para aceder aos playbooks, siga estes passos:

1.  Abra o dashboard do **Centro de Segurança**.
2.  No painel do lado esquerdo, em **Deteção de Ameaças**, clique en **Incidentes e alertas de segurança**.

    ![Alertas](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Clique no alerta que pretende investigar.
4.  Na parte superior da página do alerta, clique no botão **Executar playbooks**.

    ![Executar o playbook](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Na página Playbooks, selecione o playbook que quer executar e clique no botão **Executar**. Se quiser ver o playbook antes do acionamento, pode clicar no mesmo, o que abre o estruturador.

    ![Playbooks](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Histórico

Depois de executar o playbook, também pode aceder a execuções anteriores e a passos que contêm mais informações sobre o estado dos playbooks executados anteriormente. O histórico é contextualizado por alerta, o que significa que o histórico do playbook que vê nesta página está correlacionado com o alerta que o aciona.

![Histórico](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Para ver mais detalhes sobre a execução de um determinado playbook, clique no mesmo, sendo apresentada a página de execução da Aplicação Lógica com o fluxo de trabalho completo.

![Detalhes](./media/security-center-playbooks/security-center-playbooks-fig14.png)

Neste fluxo de trabalho, pode ver quanto tempo demorou a execução de cada tarefa e expandir cada uma para ver o resultado.

### <a name="changing-an-existing-playbook"></a>Alterar playbooks existentes

Pode alterar playbooks existentes no Centro de Segurança para adicionar ações ou condições. Para tal, basta clicar no nome do playbook que quer alterar, no separador Playbooks, e o Estruturador da Aplicação Lógica é aberto.

> [!NOTE]
> Para obter mais informações sobre como utilizar o Azure Logic Apps para criar o seu próprio playbook, leia [Criar o seu primeiro fluxo de trabalho de aplicação lógica para automatizar processos entre aplicações na cloud e serviços cloud](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app).


## <a name="see-also"></a>Ver também
Neste documento, aprendeu a utilizar os playbooks no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
