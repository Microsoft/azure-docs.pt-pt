---
title: Gerir políticas de autoshutdown no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como definir a política de autoshutdown para um laboratório para que as máquinas virtuais são desligadas automaticamente quando quando não estiverem em utilização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873994"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Gerir políticas de autoshutdown para um laboratório no Azure DevTest Labs
Os laboratórios DevTest do Azure permite-lhe controlar o custo e minimize o desperdício em seus laboratórios através da gestão de políticas (definições) para cada um deles. Este artigo mostra-lhe como configurar a política de autoshutdown para uma conta de laboratório e configurar as definições de autoshutdown num laboratório na conta de laboratório. Para ver como definir cada política de laboratório, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Definir a política de encerramento automático para um laboratório
Como proprietário de um laboratório, pode configurar um agendamento de encerramento para todas as VMs no seu laboratório. Ao fazer isso, pode salvar os custos de máquinas que não estão a ser utilizadas em execução (inativo). Pode impor uma política de encerramento em todas as suas VMs do laboratório centralmente, mas também guardar os seus utilizadores de laboratório o esforço de configurar um agendamento para as respetivas máquinas individuais. Esta funcionalidade permite-lhe definir a política no seu Cronograma do laboratório a partir de não oferta nenhum controle para controle total, aos seus utilizadores de laboratório. Como proprietário de um laboratório, pode configurar esta política, efetuando os seguintes passos:

1. Na home page para seu laboratório, selecione **Konfigurace a zásady**.
2. Selecione **automaticamente a política de encerramento** no **agendas** secção do menu à esquerda.
3. Selecione uma das opções. As secções seguintes fornecem mais detalhes sobre estas opções: Definir a política aplica-se apenas às novas VMs criadas no laboratório e não às VMs já existentes. 

    ![Automática encerrar as opções de política](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Configurar as configurações de encerramento automático
A política de autoshutdown ajuda a minimizar o desperdício de laboratório, permitindo que especifique o tempo que VMs este laboratório são encerradas.

Para ver (e alterar) as políticas num laboratório, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.   
4. Selecione **Konfigurace a zásady**.

    ![Painel de definições de política](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. O laboratório **Konfigurace a zásady** painel, selecione **encerramento automático** sob **agendas**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **nos** para ativar esta política, e **desativar** desabilitá-lo.
7. Se ativar esta política, especifique o tempo (e o fuso horário) para encerrar a todas as VMs do laboratório atual.
8. Especifique **Sim** ou **não** para a opção Enviar uma notificação a 15 minutos antes da data de autoshutdown especificado. Se escolher **Sim**, introduza um ponto de final do URL do webhook ou especificando onde pretende que a notificação para ser publicado ou enviados do endereço de e-mail. O utilizador recebe a notificação e é dada a opção para atrasar o encerramento. Para obter mais informações, consulte a [notificações](#notifications) secção. 
9. Selecione **Guardar**.

    Por predefinição, uma vez ativada, esta política aplica-se a todas as VMs do laboratório atual. Para remover esta definição de uma VM específica, abra o painel de gestão da VM e altere seu **Autoshutdown** definição.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Define uma agenda e pode optar por sair do utilizador
Se definir o seu laboratório para esta política, os utilizadores de laboratório podem substituir ou recusar o cronograma do laboratório. Esta opção concede aos utilizadores de laboratório controlo total sobre o agendamento de encerramento automático das suas VMs. Os utilizadores de laboratório não vejam nenhuma alteração na página de agendamento de encerramento da VM automática.

![Automática encerrar a opção de política - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Define uma agenda e não é possível optar por sair do utilizador
Se definir o seu laboratório para esta política, os utilizadores de laboratório podem substituir o cronograma do laboratório. No entanto, eles não podem desativar política de encerramento automático. Esta opção certifica-se de que todas as máquinas no seu laboratório está sob um cronograma de encerramento automático. Os utilizadores de laboratório podem atualizar o agendamento de encerramento automático das suas VMs e configurar notificações de encerrado para baixo.

![Automática encerrar a opção de política - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Utilizador não tem nenhum controle sobre o agendamento definido pelo administrador de laboratório
Se definir o seu laboratório para esta política, os utilizadores de laboratório não é possível substituir ou recusar o cronograma do laboratório. Essa opção oferece administração de laboratório, o controle completo sobre a agenda para cada computador no laboratório. Os utilizadores de laboratório só podem configurar notificações de encerramento automático para as suas VMs.

![Automática encerrar a opção de política - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notificações
Depois de configurar pelo proprietário do laboratório de autoshutdown, as notificações serão enviadas para os utilizadores de laboratório 15 minutos antes do autoshutdown acionado se qualquer uma das suas VMs serão afetadas. Esta opção dá a chance de salvar seu trabalho antes do encerramento de utilizadores de laboratório. A notificação também fornece ligações para cada VM para as seguintes ações:

- Ignorar o autoshutdown neste momento
- Suspender o autoshutdown durante uma hora ou 2 horas, para que possa continuar a trabalhar na VM.

Notificação é enviada através do ponto de final de hook de web configuradas ou um endereço de e-mail especificado por proprietários de laboratório nas definições de autoshutdown. Os Webhooks permitem-lhe criar ou configurar integrações subscrevem determinados eventos. Quando um desses eventos é acionado, o DevTest Labs enviará um payload de HTTP POST para o URL do webhook de configurado. Para obter mais informações sobre os webhooks, consulte [criar um webhook ou uma função de Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Recomendamos que utilize webhooks porque está amplamente suportados por vários aplicativos (por exemplo, Slack, Azure Logic Apps e assim por diante.) e permite que implemente sua própria forma de envio de notificações. Por exemplo, este artigo explica como obter autoshutdown notificação de e-mails com o Azure Logic Apps. Em primeiro lugar, vamos rapidamente os passos básicos para ativar a notificação de autoshutdown em seu laboratório.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Criar uma aplicação lógica que recebe notificações de e-mail
[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) fornece muitos conetores de out-of-the-box que torna mais fácil integram um serviço com outros clientes, como o Office 365 e o twitter. Em alto nível, os passos para configurar uma aplicação lógica para notificação de e-mail podem ser divididos em quatro fases: 

- Crie uma aplicação lógica. 
- Configure o modelo interno.
- Integrar com o seu cliente de e-mail
- Obtenha o URL do Webhook.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
Para começar, crie uma aplicação lógica na sua subscrição do Azure utilizando os seguintes passos:

1. Selecione **+ criar um recurso** no menu da esquerda, selecione **integração**e selecione **aplicação lógica**. 

    ![Novo menu de aplicação lógica](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Sobre o **aplicação lógica - criar** página, siga estes passos: 
    1. Introduza um **nome** para a aplicação lógica.
    2. Selecione a sua **subscrição** do Azure.
    3. Criar uma nova **grupo de recursos** ou selecione um grupo de recursos existente. 
    4. Selecione um **localização** para a aplicação lógica. 

        ![Nova aplicação lógica - definições](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Na **notificações**, selecione **Ir para recurso** na notificação. 

    ![Ir para recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **Estruturador da aplicação lógica** sob **ferramentas de implantação** categoria.

    ![Selecione a solicitação/resposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Sobre o **solicitação-resposta de HTTP** página, selecione **utilizar este modelo**. 

    ![Selecione esta opção de modelo de utilização](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o JSON seguinte para o **pedir esquema JSON do corpo** secção: 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Esquema JSON do corpo do pedido](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ novo passo** no designer e siga estes passos:
    1. Procure **Office 365 Outlook - enviar um e-mail**. 
    2. Selecione **enviar um e-mail** partir **ações**. 
    
        ![Enviar a opção de e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **iniciar sessão** para iniciar sessão na sua conta de e-mail. 
    4. Selecione **TO** campo e escolha o proprietário.
    5. Selecione **assunto**e um assunto da notificação de e-mail de entrada. Por exemplo: "Encerramento da máquina vmName para laboratório: labName."
    6. Selecione **corpo**e definir o conteúdo do corpo de notificação por e-mail. Por exemplo: "vmName está agendada para encerrar em 15 minutos. Ignore este encerramento ao clicar em: URL. Encerramento de atraso durante uma hora: delayUrl60. Encerramento de atraso durante 2 horas: delayUrl120. "

        ![Esquema JSON do corpo do pedido](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecione **Guardar** na barra de ferramentas. Agora, pode copiar o **URL do HTTP POST**. Selecione o botão de cópia para copiar o URL para a área de transferência. 

    ![URL do WebHook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber como definir todas as políticas, veja [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).
