---
title: Ação de email dentro da Monitorização Remota - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como adicionar uma ação de e-mail a uma regra nova ou existente.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168362"
---
# <a name="add-an-email-action"></a>Adicione uma ação de e-mail

As ações de e-mail ajudam a garantir que nunca perca alertas. Pode adicionar uma ação de e-mail a uma regra existente, ou quando criar uma nova regra.

Para completar os passos neste guia de como fazer, precisa de uma instância implementada do acelerador de solução de monitorização remota na sua assinatura Azure.

Para criar ou modificar uma regra, tem de ser [ **administrador,** ou ter as permissões corretas](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Siga estes passos para adicionar uma ação de e-mail a uma regra existente:

1. Navegue para a sua solução de monitorização remota.

1. Do **Painel de Instrumentos,** navegue até à página **Regras:**

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Clique na caixa de verificação ao lado da regra existente para modificar e, em seguida, clique em **Editar** na parte superior. Aparece um painel **de regras** editável.

1. Na secção **Ação,** **alternar o Email habilitado** para **On**.

1. A primeira vez que ativar uma ação de e-mail no acelerador de solução, tem de [iniciar solução no Outlook](#outlook).

1. Introduza um endereço de e-mail na caixa do destinatário e prima a tecla **'Introduzir'** para cada endereço de e-mail para adicionar:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Insira um assunto para o e-mail.

1. Introduza quaisquer notas adicionais para os destinatários do e-mail como texto simples. Pode utilizar a formatação HTML se [editar o modelo de e-mail](#htmledit).

1. Certifique-se **de** que o Estado de Regra está definido para **Ativação**.

1. Clique em **Aplicar**.

## <a name="create-a-new-rule"></a>Criar uma nova regra

Siga estes passos para adicionar uma ação de e-mail quando criar uma nova regra:

1. Navegue para a sua solução de monitorização remota.

1. Do **Painel de Instrumentos,** navegue até à página **Regras:**

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Siga os passos na [secção de criar uma regra](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Seguem os passos na [criação de uma](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) secção de regra avançada até ao ponto em que se define um **nível de Severidade**. Não clique **em Aplicar** ainda.

1. Na secção **Ação,** **alternar o Email habilitado** para **On**.

1. A primeira vez que ativar uma ação de e-mail no acelerador de solução, tem de [iniciar solução no Outlook](#outlook).

1. Introduza um endereço de e-mail na caixa do destinatário e prima a tecla **'Introduzir'** para cada endereço de e-mail para adicionar:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Insira um assunto para o e-mail.

1. Introduza quaisquer notas adicionais para os destinatários do e-mail como texto simples. Pode utilizar a formatação HTML se [editar o modelo de e-mail](#htmledit).

1. Certifique-se **de** que o Estado de Regra está definido para **Ativação**.

1. Clique em **Aplicar**.

A sua regra com uma ação por e-mail está agora ativada. Cada vez que a ação é desencadeada, um novo e-mail é enviado aos destinatários.

## <a name="sign-in-to-outlook"></a>Inscreva-se no Outlook <a name="outlook"></a>

A primeira vez que ativar uma ação de e-mail no seu acelerador de solução, tem de assinar no Outlook. Esta ação configura a conta de e-mail que envia as notificações por e-mail.

> [!NOTE]
> Deverá criar uma conta outlook específica apenas para notificações de acelerador de soluções e utilizar essa conta quando ativar a sua primeira ação de e-mail.

### <a name="contributor-role-outlook-setup"></a>Configuração do Outlook de função de contribuinte

Se alguém no papel **do contribuinte** na subscrição implementou o acelerador de solução, a aplicação não tem permissões suficientes para configurar e verificar as ações de e-mail através da UI Web.

Antes de iniciar, crie uma conta Outlook para usar para enviar notificações de e-mail do seu acelerador de solução.

Os seguintes passos mostram-lhe como configurar e verificar manualmente as ações de e-mail:

1. Navegue até ao [portal Azure.](https://portal.azure.com)

1. Navegue para o Grupo de Recursos para obter o seu acelerador de solução.

1. Clique no **conector office365:**

    ![Conexão API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Clique no banner para iniciar o processo de autorização:

    ![autorizar](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Clique **em Autorizor**. Foi-lhe pedido que assinasse. A conta que utiliza para iniciar scontabilidade deve ser o endereço de e-mail que a aplicação utiliza para enviar notificações por e-mail:

    ![Botão autorizado](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Clique em **Guardar** na parte inferior. A sua autorização será bem sucedida se o estandarte desaparecer.

1. Para alterar o endereço de e-mail de onde as notificações são enviadas, clique na **ligação API editar**.

    ![alterar e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Configuração do Outlook de função do proprietário

Se alguém na função **de proprietário** na subscrição implementou o acelerador de solução, a aplicação pode verificar que as ações de e-mail foram corretamente configuradas através da UI Web.

Antes de iniciar, crie uma conta Outlook para usar para enviar notificações de e-mail do seu acelerador de solução.

Os seguintes passos ajudam-no a iniciar sômsid e configurar ações de e-mail:

1. Clique para iniciar sinscção no Outlook. Foi levado para o portal Azure:

   ![Inscreva-se no Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Clique **em Autorizor**. Foi-lhe pedido que assinasse. A conta que utiliza para iniciar scontabilidade deve ser o endereço de e-mail que a aplicação utiliza para enviar notificações por e-mail:

1. Clique em **Guardar**. Volte ao acelerador de solução e refresque a página.

1. Se configurar com sucesso a notificação de e-mail, consulte esta mensagem:

   ![Sucesso do outlook em](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Personalize o e-mail HTML <a name="htmledit"></a>

Fora da caixa, o acelerador de solução de monitorização remota fornece um modelo html básico para e-mails de ação. O modelo de e-mail utiliza valores a partir das definições de ação de e-mail. Aqui está um e-mail de exemplo:

![exemplo de e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Os passos a seguir mostram-lhe como editar o modelo de e-mail HTML. Por exemplo, pode incluir mais informações ou adicionar imagens personalizadas:

1. Clone quer o repositório de Monitorização Remota De Java ou .NET GitHub:

1. Navegue para a localização do modelo de e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Pode adicionar ou remover quaisquer parâmetros neste modelo para personalizar a mensagem. Também pode adicionar, remover ou substituir chamadas conforme necessário:

    Por exemplo, no código .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Por exemplo, no código Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Os parâmetros do modelo assumem a forma de `${...}` . Para eliminar um parâmetro, elimine a linha necessária. Para adicionar um parâmetro, adicione uma linha com o valor a inserir.

1. Para adicionar imagens ou texto personalizado, atualize o ficheiro EmailTemplate.HTML diretamente.

## <a name="throttling"></a>Limitação

O acelerador de solução de monitorização remota utiliza o Outlook para enviar notificações por e-mail. O Outlook limita o número de e-mails enviados para [30 e-mails por 1 minuto](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Os clientes de e-mail que recebem os e-mails também podem acelerar o número de e-mails recebidos por minuto. Consulte o seu cliente de e-mail específico sobre limitações. Quando configura a notificação por e-mail para uma regra, a regra deve calcular valores médios durante um período de pelo menos um minuto, e não utilizar valores instantâneos:

![Cálculo médio](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Passos seguintes

Este guia mostrou-lhe como adicionar uma ação de e-mail a uma regra nova ou existente dentro de uma solução de Monitorização Remota. O guia também mostrou e como editar o HTML que define o formato de mensagem.

O próximo passo sugerido é [aprender a usar alertas e corrigir problemas de dispositivos](iot-accelerators-remote-monitoring-maintain.md).
