---
title: Enviar um e-mail ação dentro de monitorização remota - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como adicionar uma ação de e-mail para uma regra de nova ou existente.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 693da06ce14a4854d5db49f588fa29d791060166
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108108"
---
# <a name="add-an-email-action"></a>Adicionar uma ação de e-mail

E-mail ações ajudam a tornar-se de que nunca mais perca alertas. Pode adicionar uma ação de e-mail para uma regra existente, ou quando criar uma nova regra.

Para concluir os passos neste guia de procedimentos, terá uma instância implementada do solution accelerator monitorização remota na sua subscrição do Azure.

Para criar ou modificar uma regra, tem de ser um [ **administrador**, ou tem as permissões corretas](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Siga estes passos para adicionar uma ação de e-mail para uma regra existente:

1. Navegue até à sua solução de monitorização remota.

1. Do **Dashboard**, navegue para o **regras** página:

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Clique na caixa de verificação junto a regra existente para modificar e, em seguida, clique em **editar** na parte superior. Um editável **regra** painel é apresentada.

1. Na **ação** secção, alternar **E-Mail ativado** para **no**.

1. Na primeira vez que habilitar uma ação de e-mail no solution accelerator, deve [inicie sessão no Outlook](#outlook).

1. Introduza um endereço de e-mail na caixa destinatário de e prima a **Enter** chave para cada endereço de e-mail adicionar:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Introduza um assunto do e-mail.

1. Introduza quaisquer notas adicionais para os destinatários de e-mail como texto simples. Pode usar o HTML em formatação se [editar o modelo de e-mail](#htmledit).

1. Certifique-se de que o **estado da regra** está definida como **ativado**.

1. Clique em **Aplicar**.

## <a name="create-a-new-rule"></a>Criar uma nova regra

Siga estes passos para adicionar uma ação de e-mail quando criar uma nova regra:

1. Navegue até à sua solução de monitorização remota.

1. Do **Dashboard**, navegue para o **regras** página:

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Siga os passos a [crie uma seção de regra](iot-accelerators-remote-monitoring-automate.md#create-a-rule). A seguir os passos a [criar uma regra avançada](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) secção até ao ponto em que definir um **nível de gravidade**. Não clique **aplicar** ainda.

1. Na **ação** secção, alternar **E-Mail ativado** para **no**.

1. Na primeira vez que habilitar uma ação de e-mail no solution accelerator, deve [inicie sessão no Outlook](#outlook).

1. Introduza um endereço de e-mail na caixa destinatário de e prima a **Enter** chave para cada endereço de e-mail adicionar:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Introduza um assunto do e-mail.

1. Introduza quaisquer notas adicionais para os destinatários de e-mail como texto simples. Pode usar o HTML em formatação se [editar o modelo de e-mail](#htmledit).

1. Certifique-se de que o **estado da regra** está definida como **ativado**.

1. Clique em **Aplicar**.

A regra com uma ação de e-mail está agora ativada. Sempre que a ação é disparada, um novo e-mail é enviado para os destinatários.

## Inicie sessão no Outlook <a name="outlook"></a>

Na primeira vez que habilitar uma ação de e-mail no seu solution accelerator, tem de iniciar sessão no Outlook. Esta ação configura a conta de e-mail que envia as notificações por e-mail.

> [!NOTE]
> Deve criar uma conta de Outlook específica apenas para notificações do solution accelerator e utilizar essa conta ao ativar a sua primeira ação de e-mail.

### <a name="contributor-role-outlook-setup"></a>Configuração de Outlook da função de contribuinte

Se alguém na **contribuinte** função na subscrição implantado o solution accelerator, a aplicação não tem permissões suficientes para configurar e verificar as ações de e-mail através da IU Web.

Antes de começar, crie uma conta do Outlook para utilizar para enviar notificações por e-mail a partir do solution accelerator.

Os passos seguintes mostram como configurar e verificar as ações de e-mail manualmente:

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Navegue para o grupo de recursos do solution accelerator.

1. Clique nas **conector do Office 365**:

    ![Ligação de API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. Clique na faixa para iniciar o processo de autorização:

    ![autorizar](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. Clique em **autorizar**. Lhe for pedido para iniciar sessão. A conta que utiliza para iniciar sessão deve ser que o endereço de e-mail a aplicação utiliza para enviar notificações por e-mail:

    ![Autorizar o botão](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Clique em **guardar** na parte inferior. A autorização será bem-sucedida se a faixa já não existe.

1. Para alterar o endereço de e-mail a partir do qual as notificações são enviadas do, clique em **ligação editar API**.

    ![alterar o e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>Configuração de Outlook da função de proprietário

Se alguém na **proprietário** função na subscrição implantado o solution accelerator, a aplicação pode verificar as ações de e-mail foram corretamente definidas através da IU Web.

Antes de começar, crie uma conta do Outlook para utilizar para enviar notificações por e-mail a partir do solution accelerator.

Os seguintes passos ajudá-lo a iniciar sessão e configurar as ações de e-mail:

1. Clique para iniciar sessão no Outlook. É levado para o portal do Azure:

   ![Inicie sessão no Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Clique em **autorizar**. Lhe for pedido para iniciar sessão. A conta que utiliza para iniciar sessão deve ser que o endereço de e-mail a aplicação utiliza para enviar notificações por e-mail:

1. Clique em **Guardar**. Regresse do solution accelerator e atualize a página.

1. Se configurou com êxito a notificação de e-mail, verá esta mensagem:

   ![Início de sessão de Outlook concluída com êxito](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Personalizar a mensagem de e-mail HTML <a name="htmledit"></a>

Out-of-the-box, o acelerador de solução de monitorização remota fornece um modelo HTML básico para e-mails de ação. O modelo de e-mail utiliza valores das definições de ação de e-mail. Eis um e-mail de exemplo:

![exemplo de e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

Os passos seguintes mostram como editar o modelo de e-mail HTML. Por exemplo, pode incluir mais informações ou adicionar imagens personalizadas:

1. Clone o repositório de Java ou .NET GitHub de monitorização remota:

1. Navegue para a localização do modelo de e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Pode adicionar ou remover quaisquer parâmetros neste modelo para personalizar a mensagem. Também pode adicionar, remover ou substituir as chamadas conforme necessário:

    Por exemplo, no código .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Por exemplo, no código Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parâmetros no modelo assumir a forma de `${...}`. Para eliminar um parâmetro, elimine a linha necessária. Para adicionar um parâmetro, adicione uma linha com o valor a inserir.

1. Para adicionar texto personalizado ou de imagens, atualize o ficheiro de EmailTemplate.HTML diretamente.

## <a name="throttling"></a>Limitação

O acelerador de solução de monitorização remota utiliza o Outlook para enviar notificações por e-mail. Outlook limita o número de e-mails enviados para o [30 e-mails por 1 minuto](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Clientes de correio eletrónico a receber os e-mails também podem limitar o número de e-mails recebidos por minuto. Contacte o seu cliente de e-mail específico sobre limitações. Ao configurar notificação por e-mail para uma regra, a regra deve calcular valores médios ao longo de um período de, pelo menos, um minuto e não utilize valores instantâneas:

![Cálculo de média](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Passos Seguintes

Este guia de mostrar como adicionar uma ação de e-mail para uma regra de nova ou existente numa solução de monitorização remota. O guia também mostrou que e como editar o HTML que define o formato de mensagem.

A próxima etapa sugerida é saber [como utilizar alertas e corrigir problemas com dispositivos](iot-accelerators-remote-monitoring-maintain.md).
