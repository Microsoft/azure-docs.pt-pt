---
title: Automação de fluxo de trabalho (versão prévia) na central de segurança do Azure | Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151705"
---
# <a name="workflow-automation-preview"></a>Automação de fluxo de trabalho (visualização)

Cada programa de segurança inclui vários fluxos de trabalho para resposta a incidentes. Esses processos podem incluir a notificação de participantes relevantes, a inicialização de um processo de gerenciamento de alterações e a aplicação de etapas de correção específicas. Os especialistas em segurança recomendam que você automatize o máximo possível de etapas desses procedimentos. A automação reduz a sobrecarga. Ele também pode melhorar sua segurança, garantindo que as etapas do processo sejam feitas de forma rápida, consistente e de acordo com seus requisitos predefinidos.

Este artigo descreve o recurso de automação de fluxo de trabalho (versão prévia) da central de segurança do Azure. Esse recurso de visualização pode disparar aplicativos lógicos sobre alertas de segurança e recomendações. Por exemplo, talvez você queira que a central de segurança envie por email um usuário específico quando ocorrer um alerta. Você também aprenderá a criar aplicativos lógicos usando [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Se você usou anteriormente a exibição de guias estratégicos (versão prévia) na barra lateral, encontrará os mesmos recursos junto com a funcionalidade expandida na página nova automação de fluxo de trabalho (versão prévia).


## <a name="requirements"></a>Requisitos

* Para trabalhar com fluxos de trabalho de aplicativos lógicos do Azure, você deve ter as seguintes funções/permissões de aplicativos lógicos:

    * Permissões de [operador de aplicativo lógico](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) são necessárias ou acesso de leitura/gatilho do aplicativo lógico (essa função não pode criar ou editar aplicativos lógicos; *executar* apenas os existentes)

    * As permissões de [colaborador do aplicativo lógico](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) são necessárias para a criação e modificação do aplicativo lógico

* Se você quiser usar conectores de aplicativos lógicos, talvez precise de credenciais adicionais para entrar em seus respectivos serviços (por exemplo, suas instâncias de Outlook/equipes/margem de atraso)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Criar um aplicativo lógico e definir quando ele deve ser executado automaticamente 

1. Na barra lateral da central de segurança, selecione **automação de fluxo de trabalho (versão prévia)** .

    [Lista de ![de automação de fluxo de trabalho](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Nessa página, você pode criar novas regras de automação, bem como habilitar, desabilitar ou excluir aquelas existentes.  
1. Para definir um novo fluxo de trabalho, clique em **Adicionar automação de fluxo de trabalho**. 

    Um painel é exibido com as opções para sua nova automação. Aqui você pode inserir:
    1. Um nome e uma descrição para a automação.
    1. Os gatilhos que iniciarão esse fluxo de trabalho automático. Por exemplo, talvez você queira que seu aplicativo lógico seja executado quando um alerta de segurança que contenha "SQL" for gerado.
    1. O aplicativo lógico que será executado quando suas condições de disparo forem atendidas. 

        [Lista de ![de automação de fluxo de trabalho](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Na seção ações, clique em **criar um novo** para iniciar o processo de criação do aplicativo lógico.

    Você será levado para os aplicativos lógicos do Azure.

    [![criar um novo aplicativo lógico](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Insira um nome, grupo de recursos e local e clique em **criar**.

1. Em seu novo aplicativo lógico, você pode escolher entre modelos predefinidos internos da categoria segurança. Ou você pode definir um fluxo de eventos personalizado para ocorrer quando esse processo é disparado.

    No designer do aplicativo lógico, há suporte para os seguintes gatilhos dos conectores da central de segurança:

    * **Quando uma recomendação da central de segurança do Azure é criada ou disparada (visualização)**
    * **Quando um alerta da central de segurança do Azure é criado ou disparado (visualização)**
    
    > [!NOTE]
    > Se você estiver usando o gatilho herdado "quando uma resposta a um alerta da central de segurança do Azure for disparada", seus aplicativos lógicos não serão iniciados pelo recurso de automação do fluxo de trabalho. Em vez disso, use qualquer um dos gatilhos mencionados acima. 

    [![aplicativo lógico de exemplo](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir seu aplicativo lógico, retorne ao painel de definição de automação de fluxo de trabalho ("Adicionar automação de fluxo de trabalho"). Clique em **Atualizar** para garantir que seu novo aplicativo lógico esteja disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione seu aplicativo lógico e salve a automação. Observe que o menu suspenso do aplicativo lógico mostra apenas os aplicativos lógicos com suporte aos conectores da central de segurança mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Disparar um aplicativo lógico manualmente

Você também pode executar aplicativos lógicos manualmente ao exibir uma recomendação de segurança.

Para executar manualmente um aplicativo lógico, abra uma recomendação e clique em disparar aplicativo lógico (versão prévia):

[![disparar um aplicativo lógico manualmente](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar aplicativos lógicos, executá-los manualmente na central de segurança e automatizar sua execução. 

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [Recomendações de segurança na central de segurança do Azure](security-center-recommendations.md)
- [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
- [Sobre os aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Conectores de aplicativos lógicos](https://docs.microsoft.com/connectors/)
