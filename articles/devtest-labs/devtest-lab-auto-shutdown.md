---
title: Gerir políticas de autoshutdown em Azure DevTest Labs e Compute VMs Microsoft Docs
description: Aprenda a definir a política de autoshutdown para um laboratório para que as máquinas virtuais sejam automaticamente desligadas quando não estão a ser utilizadas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318971"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configurar autoshutdown para laboratório e calcular máquinas virtuais em Azure DevTest Labs

Este artigo explica como configurar definições de autoshutdown para VMs de laboratório em DevTest Labs e Compute VMs.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configurar auto-mente para VMs de laboratório (DevTest Labs)

A Azure DevTest Labs permite controlar os custos e minimizar o desperdício nos seus laboratórios, gerindo políticas (configurações) para cada laboratório. Este artigo mostra-lhe como configurar a política de autorredução para um laboratório.  Também mostra como configurar as definições de autorredução para um VM de laboratório. Para ver como definir todas as políticas de laboratório, consulte [as políticas de laboratório da Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Definir política de autoshutdown para um laboratório

Como dono de laboratório, pode configurar um horário de encerramento para todos os VMs do seu laboratório. Ao fazê-lo, pode poupar custos de funcionamento de máquinas que não estão a ser utilizadas (inativas). Pode impor uma política de encerramento em todos os seus VMs de laboratório centralmente e também poupar aos seus utilizadores de laboratório o esforço de configurar um horário para as suas máquinas individuais. Esta funcionalidade permite-lhe definir a política no seu horário de laboratório que vai desde permitir que os utilizadores de laboratório tenham controlo total sobre o horário de paragem dos seus VM até não controlar a paragem dos seus VM. Como proprietário de laboratório, pode configurar esta política tomando os seguintes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **a política de encerramento automático** na secção **Agendas** do menu esquerdo.
3. Selecione uma das opções. As seguintes secções dão-lhe mais detalhes sobre estas opções:

    ![Opções de política de encerramento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> As alterações à política de encerramento aplicam-se apenas aos novos VM criados em laboratório e não aos VM já existentes.

### <a name="configure-autoshutdown-settings"></a>Configurar definições de autorredução

A política de autoshutdown ajuda a minimizar os resíduos de laboratório, permitindo-lhe especificar a hora em que os VM deste laboratório são desligados.

Para ver ou alterar as políticas de um laboratório, siga estes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **desligar automaticamente** na secção **Agendas** do menu esquerdo.
3. Selecione **On** para ativar esta política e **desligue-a** para desativá-la.
     ![Detalhes de paragem automática](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Se ativar esta política, especifique a hora (e o fuso horário) para desligar todos os VMs do laboratório atual.
5. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes da hora de autoshutdown especificada. Se escolher **Sim,** insira um ponto final url webhook ou endereço de e-mail especificando onde deseja que a notificação seja publicada ou enviada. O utilizador recebe a notificação e é-lhe dada a opção de atrasar a paralisação. Para mais informações, consulte a secção [Notificações.](#notifications)
6. Selecione **Guardar**.

    Por padrão, uma vez ativado, esta política aplica-se a todos os VMs no laboratório atual. Para remover esta definição de um VM específico, abra o painel de gestão do VM e altere a sua definição **de paragem automática.**

> [!NOTE]
> Se atualizar o horário de auto-depósito para o seu laboratório ou uma máquina virtual de laboratório específica dentro de 30 minutos da hora programada atual, o tempo de paragem atualizado será aplicado para o horário do dia seguinte.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O utilizador define um horário e pode optar por não o fazer

Se definir o seu laboratório para esta política, os utilizadores do laboratório podem substituir ou optar por não cumprir o horário do laboratório. Esta opção confere aos utilizadores de laboratório o controlo total sobre o horário de paragem automática dos seus VM. Os utilizadores do laboratório não vêem qualquer alteração na sua página de programação de paragem automática VM.

![Opção de política de encerramento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O utilizador define um horário e não pode optar por não o fazer

Se definir o seu laboratório para esta apólice, os utilizadores de laboratório podem anular o horário do laboratório. No entanto, não podem optar por não fazer política de autorredução. Esta opção garante que todas as máquinas do seu laboratório estão sob um horário de autorredução. Os utilizadores de laboratório podem atualizar o horário de autorredução dos seus VMs e configurar notificações de paragem.

![Opção de política de encerramento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O utilizador não tem controlo sobre o horário definido pela administração do laboratório

Se definir o seu laboratório para esta política, os utilizadores de laboratório não podem anular ou optar fora do horário do laboratório. Esta opção oferece ao laboratório o controlo completo do horário de todas as máquinas do laboratório. Os utilizadores de laboratório só podem configurar notificações de paragem automática para os seus VMs.

![Opção de política de encerramento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configurar auto-mente para VMs computetivos

1. Na página **da máquina Virtual,** selecione **desligar automaticamente** no menu esquerdo na secção **Operações.**
2. Na página **de encerramento automático,** selecione **On** para ativar esta política e **desligue-a** para desativá-la.
3. Se ativar esta política, especifique a **hora** (e o **fuso horário)** em que o VM deve ser desligado.
4. Escolha **Sim** ou **Não** para a opção de enviar uma notificação 30 minutos antes da hora de autorredução especificada. Se escolher **Sim,** insira um ponto final url webhook ou endereço de e-mail especificando onde deseja que a notificação seja publicada ou enviada. O utilizador recebe a notificação e é-lhe dada a opção de atrasar a paralisação. Para mais informações, consulte a secção [Notificações.](#notifications)
5. Selecione **Guardar**.

    ![Configurar a paragem automática para um VM computetado](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Ver registos de atividade para atualizações de encerramento automático

Quando atualizar a definição de autoshutdown, verá a atividade registada no registo de atividades para o VM.

1. No [portal Azure,](https://portal.azure.com)navegue para a página inicial para o seu VM.
2. Selecione registo de **atividade** a partir do menu esquerdo.
3. Remover **Recurso: o meu comutavm** dos filtros.
4. Confirme que vê o funcionamento dos **horários do Add ou modificar** o registo de atividades. Se não o vir, espere um pouco e refresque o registo de atividades.

    ![Entrada de registo de atividade](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Selecione a operação **de programação adicionar ou modificar** para ver as seguintes informações na página **Resumo:**

    - Nome da operação (Adicionar ou modificar horários)
    - A data e hora em que a definição de autoshutdown foi atualizada.
    - O endereço de e-mail do utilizador que atualizou a definição.

        ![Resumo da entrada de registo de atividade](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Mude para o separador Alterar o **histórico** na página **'Adicionar' ou modificar os horários,** vê o histórico de alteração para a definição. No exemplo seguinte, o tempo de paragem foi alterado das 19:00 para as 18:00 de 10 de abril de 2020 às 15:18:47 EST. E a configuração foi desativada às 15:25:09 EST.

    ![Log de atividade - alterar histórico](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Para ver mais detalhes sobre a operação, mude para o separador **JSON** na página **'Adicionar' ou modifique os horários.**

## <a name="notifications"></a>Notificações

Uma vez configurado o autoshutdown, as notificações serão enviadas aos utilizadores do laboratório 30 minutos antes do autoshutdown desencadeado se algum dos seus VMs for afetado. Esta opção dá aos utilizadores de laboratório a oportunidade de salvar o seu trabalho antes do encerramento. A notificação também fornece links para cada VM para as seguintes ações, caso alguém precise de continuar a trabalhar no seu VM.

- Salte o autoshutdown para esta hora
- Soneca o autoshutdown por uma hora
- Soneca o autoshutdown por 2 horas

A Notificação é enviada para o url webhook se um webhook foi especificado.  Se um endereço de e-mail foi especificado nas definições de autoshutdown, um e-mail será enviado para esse endereço de e-mail. Os Webhooks permitem-lhe construir ou configurar integrações que subscrevam determinados eventos. Quando um desses eventos for desencadeado, a DevTest Labs enviará uma carga HTTP POST para o URL configurado do webhook. Para obter mais informações sobre como responder a webhooks, consulte [Azure Functions HTTP triggers and bindings overview](../azure-functions/functions-bindings-http-webhook.md) ou [add a HTTP trigger for Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

Recomendamos que utilize ganchos web porque são amplamente suportados por várias aplicações como Azure Logic Apps e Slack.  Os Webhooks permitem-lhe implementar a sua própria forma de enviar notificações. Como exemplo, este artigo explica-lhe como configurar a notificação de auto-aterragem para enviar um e-mail ao proprietário do VM utilizando as Apps Azure Logic. Primeiro, vamos rapidamente ver os passos básicos para permitir a notificação de autoshutdown no seu laboratório.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Crie uma aplicação lógica que receba notificações de email

[A Azure Logic Apps](../logic-apps/logic-apps-overview.md) fornece muitos conectores que facilitam a integração de um serviço com outros clientes, como o Office 365 e o Twitter. Ao nível elevado, os passos para a criação de uma App Lógica para notificação por e-mail podem ser divididos em quatro fases:

- Criar uma aplicação lógica.
- Configure o modelo incorporado.
- Integre-se com o seu cliente de e-mail
- Pegue a URL webhook.

### <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Para começar, crie uma aplicação lógica na sua subscrição Azure utilizando os seguintes passos:

1. Selecione **+ Crie um recurso** no menu esquerdo, selecione **Integração** e selecione **a Aplicação Lógica.**

    ![Novo menu de aplicativos de lógica](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na **Aplicação Lógica - Criar** página, siga estes passos:
    1. Insira um **nome** para a aplicação lógica.
    2. Selecione a sua **subscrição Azure**.
    3. Crie um novo **grupo de recursos** ou selecione um grupo de recursos existente.
    4. Selecione uma **localização** para a aplicação lógica.

        ![Nova app lógica - configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **Notificações** , selecione **Ir para recurso** na notificação.

    ![Ir para recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **o designer de aplicativos Logic** na categoria **Ferramentas de Implementação.**

    ![Selecione HTTP Pedido/Resposta](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **HTTP Request-Response,** selecione **Utilize este modelo.**

    ![Selecione Utilize esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o seguinte JSON na secção **de Esquema JSON do Corpo de Pedido:**

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
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
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
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![Screenshot que mostra o "Request Body JSON Schema".](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ Novo passo** no designer, e siga estes passos:
    1. Pesquisa rumo **ao Office 365 Outlook - Envie um e-mail**.
    2. Selecione **Enviar um e-mail** de **Ações**.

        ![Enviar opção de e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **Iniciar sôms** para iniciar sôms na sua conta de e-mail.
    4. Selecione **campo TO** e escolha o proprietário.
    5. Selecione **SUBJECT** , e insira um assunto da notificação por e-mail. Por exemplo: "Encerramento da máquina vmName para Laboratório: nome lab."
    6. Selecione **BODY** , e defina o conteúdo do corpo para notificação de e-mail. Por exemplo: "vmName está programado para desligar em 15 minutos. Ignore esta paralisação clicando: URL. Atraso de paragem por uma hora: atrasoUrl60. Atraso de paragem por 2 horas: atrasoUrl120."

        ![Pedido corpo JSON Schema](./media/devtest-lab-auto-shutdown/email-options.png)
8. Selecione **Guardar** na barra de ferramentas. Agora, pode copiar o **URL HTTP POST**. Selecione o botão de cópia para copiar o URL para a área de transferência.

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Passos seguintes

Para aprender a definir todas as políticas, consulte [as políticas de laboratório da Azure DevTest Labs](devtest-lab-set-lab-policy.md).
