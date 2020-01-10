---
title: Gerenciar políticas de desligamento automático no Azure DevTest Labs | Microsoft Docs
description: Saiba como definir a política de desligamento automático para um laboratório para que as máquinas virtuais sejam desligadas automaticamente quando não estiverem em uso.
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
ms.openlocfilehash: 1c13414bb252da1192f82675da5b134bf43a40f0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772640"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs
O Azure DevTest Labs permite controlar o custo e minimizar o desperdício em seus laboratórios Gerenciando políticas (configurações) para cada laboratório. Este artigo mostra como configurar a política de desligamento automático para uma conta de laboratório e definir as configurações de desligamento automático para um laboratório na conta do laboratório. Para exibir como definir cada política de laboratório, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Definir a política de desligamento automático para um laboratório
Como proprietário de um laboratório, você pode configurar um agendamento de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar os custos de computadores em execução que não estão sendo usados (ociosos). Você pode impor uma política de desligamento em todas as VMs do laboratório centralmente, mas também salvar os usuários do laboratório do esforço de configurar um agendamento para seus computadores individuais. Esse recurso permite que você defina a política em sua agenda de laboratório a partir da oferta sem controle para controle total, para os usuários do laboratório. Como proprietário de um laboratório, você pode configurar essa política executando as seguintes etapas:

1. No home page para seu laboratório, selecione **configuração e políticas**.
2. Selecione **política de desligamento automático** na seção **agendas** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir fornecem mais detalhes sobre essas opções: a política de conjunto só se aplica a novas VMs criadas no laboratório e não às VMs já existentes. 

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Definir configurações de desligamento automático
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs do laboratório são desligadas.

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Selecione **configuração e políticas**.

    ![Painel de configurações de política](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. No painel **configuração e políticas** do laboratório, selecione **desligamento automático** em **agendas**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **ativado** para habilitar essa política e **desativado** para desabilitá-la.
7. Se você habilitar essa política, especifique a hora (e o fuso horário) para desligar todas as VMs no laboratório atual.
8. Especifique **Sim** ou **não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se você escolher **Sim**, insira um ponto de extremidade de URL do webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe uma notificação e recebe a opção de atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) . 
9. Selecione **Guardar**.

    Por padrão, uma vez habilitada, essa política se aplica a todas as VMs no laboratório atual. Para remover essa configuração de uma VM específica, abra o painel Gerenciamento da VM e altere sua configuração de **desligamento** automático.
    
> [!NOTE]
> Se você atualizar a agenda de desligamento automático para seu laboratório ou para uma máquina virtual de laboratório específica dentro de 30 minutos da hora agendada atual, o tempo de desligamento atualizado será aplicado na agenda do dia seguinte. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define uma agenda e pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir ou recusar a agenda do laboratório. Essa opção concede aos usuários do laboratório controle total sobre o agendamento de desligamento automático de suas VMs. Os usuários do laboratório não veem nenhuma alteração na página agenda de desligamento automático da VM.

![Opção de política de desligamento automático-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define uma agenda e não pode recusar
Se você definir o laboratório para essa política, os usuários do laboratório poderão substituir a agenda do laboratório. No entanto, eles não podem recusar a política de desligamento automático. Essa opção garante que cada computador em seu laboratório esteja sob um agendamento de desligamento automático. Os usuários do laboratório podem atualizar o agendamento de desligamento automático de suas VMs e configurar notificações de encerramento.

![Opção de política de desligamento automático-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o agendamento definido pelo administrador do laboratório
Se você definir seu laboratório para essa política, os usuários do laboratório não poderão substituir nem recusar a agenda do laboratório. Essa opção oferece ao administrador do laboratório o controle total sobre o agendamento de cada computador no laboratório. Os usuários do laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notificações
Após o desligamento automático configurado pelo proprietário do laboratório, as notificações serão enviadas para os usuários do laboratório 30 minutos antes do desligamento automático ser disparado se qualquer uma de suas VMs for afetada. Essa opção dá aos usuários do laboratório a oportunidade de salvar seu trabalho antes do desligamento. A notificação também fornece links para cada VM para as seguintes ações:

- Ignorar o desligamento automático para este tempo
- Adiar o desligamento automático por uma hora ou 2 horas, para que eles possam continuar trabalhando na VM.

A notificação é enviada por meio do ponto de extremidade de gancho da Web configurado ou de um endereço de email especificado pelos proprietários do laboratório nas configurações de desligamento automático. WebHooks permitem que você crie ou configure integrações que assinam determinados eventos. Quando um desses eventos for disparado, o DevTest Labs enviará uma carga HTTP POST para a URL configurada do webhook. Para obter mais informações sobre WebHooks, consulte [criar um webhook ou uma função de API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Recomendamos que você use os ganchos da Web porque eles são amplamente suportados por vários aplicativos (por exemplo, margem de atraso, aplicativos lógicos do Azure e assim por diante) e permite que você implemente sua própria maneira de enviar notificações. Por exemplo, este artigo explica como obter notificações de desligamento automático de emails usando aplicativos lógicos do Azure. Primeiro, vamos percorrer rapidamente as etapas básicas para habilitar a notificação de desligamento automático em seu laboratório.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Criar um aplicativo lógico que recebe notificações por email
Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) fornecem muitos conectores prontos para uso que facilitam a integração de um serviço com outros clientes, como o Office 365 e o Twitter. No alto nível, as etapas para configurar um aplicativo lógico para notificação por email podem ser divididas em quatro fases: 

- Crie um aplicativo lógico. 
- Configure o modelo interno.
- Integre com seu cliente de email
- Obtenha a URL do webhook.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica
Para começar, crie um aplicativo lógico em sua assinatura do Azure usando as seguintes etapas:

1. Selecione **+ criar um recurso** no menu à esquerda, selecione **integração**e selecione **aplicativo lógico**. 

    ![Novo menu de aplicativo lógico](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na página **aplicativo lógico – criar** , siga estas etapas: 
    1. Insira um **nome** para o aplicativo lógico.
    2. Selecione a sua **subscrição** do Azure.
    3. Criar uma nova **grupo de recursos** ou selecione um grupo de recursos existente. 
    4. Selecione um **local** para o aplicativo lógico. 

        ![Novo aplicativo lógico-configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **notificações**, selecione **ir para o recurso** na notificação. 

    ![Ir para recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **Designer de aplicativo lógico** na categoria **ferramentas de implantação** .

    ![Selecionar solicitação/resposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **solicitação-resposta http** , selecione **usar este modelo**. 

    ![Selecione usar esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o JSON a seguir na seção **esquema JSON do corpo da solicitação** : 

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
    
    ![Pedir Esquema JSON do Corpo](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ nova etapa** no designer e siga estas etapas:
    1. Pesquise pelo **Office 365 Outlook-enviar um email**. 
    2. Selecione **enviar um email** de **ações**. 
    
        ![Opção Enviar email](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **entrar** para entrar em sua conta de email. 
    4. Selecione **para** o campo e escolha proprietário.
    5. Selecione **assunto**e insira um assunto da notificação por email. Por exemplo: "desligamento do Machine vmName for Lab: labName."
    6. Selecione **corpo**e defina o conteúdo do corpo para notificação por email. Por exemplo: "vmName está agendado para desligar em 15 minutos. Ignore esse desligamento clicando em: URL. Atrasar o desligamento por uma hora: delayUrl60. Atraso no desligamento por 2 horas: delayUrl120. "

        ![Pedir Esquema JSON do Corpo](./media/devtest-lab-auto-shutdown/email-options.png)
1. Selecione **Guardar** na barra de ferramentas. Agora, você pode copiar a **URL http post**. Selecione o botão Copiar para copiar a URL para a área de transferência. 

    ![URL do webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Passos seguintes
Para saber como definir todas as políticas, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).
