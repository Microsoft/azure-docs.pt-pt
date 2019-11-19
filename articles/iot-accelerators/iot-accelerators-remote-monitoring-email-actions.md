---
title: Ação de email no monitoramento remoto – Azure | Microsoft Docs
description: Este guia de instruções mostra como adicionar uma ação de email a uma regra nova ou existente.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168362"
---
# <a name="add-an-email-action"></a>Adicionar uma ação de email

As ações de email ajudam a garantir que você nunca perca alertas. Você pode adicionar uma ação de email a uma regra existente ou ao criar uma nova regra.

Para concluir as etapas neste guia de instruções, você precisa de uma instância implantada do acelerador de solução de monitoramento remoto em sua assinatura do Azure.

Para criar ou modificar uma regra, você deve ser um [ **administrador**ou ter as permissões corretas](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Siga estas etapas para adicionar uma ação de email a uma regra existente:

1. Navegue até sua solução de monitoramento remoto.

1. No **painel**, navegue até a página **regras** :

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Clique na caixa de seleção ao lado da regra existente para modificar e, em seguida, clique em **Editar** na parte superior. Um painel de **regras** editável é exibido.

1. Na seção **ação** , ativar/desativar **email habilitado** para **ativado**.

1. Na primeira vez que você habilitar uma ação de email no Solution Accelerator, você deve [entrar no Outlook](#outlook).

1. Insira um endereço de email na caixa destinatário e pressione a tecla **Enter** para cada endereço de email a ser adicionado:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Insira um assunto para o email.

1. Insira qualquer anotação adicional para os destinatários do email como texto sem formatação. Você pode usar a formatação HTML se [Editar o modelo de email](#htmledit).

1. Verifique se o **status da regra** está definido como **habilitado**.

1. Clique em **Aplicar**.

## <a name="create-a-new-rule"></a>Criar uma nova regra

Siga estas etapas para adicionar uma ação de email ao criar uma nova regra:

1. Navegue até sua solução de monitoramento remoto.

1. No **painel**, navegue até a página **regras** :

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Siga as etapas na [seção criar uma regra](iot-accelerators-remote-monitoring-automate.md#create-a-rule). O siga as etapas na seção [criar uma regra avançada](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) até o ponto em que você define um **nível de severidade**. Não clique em **aplicar** ainda.

1. Na seção **ação** , ativar/desativar **email habilitado** para **ativado**.

1. Na primeira vez que você habilitar uma ação de email no Solution Accelerator, você deve [entrar no Outlook](#outlook).

1. Insira um endereço de email na caixa destinatário e pressione a tecla **Enter** para cada endereço de email a ser adicionado:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Insira um assunto para o email.

1. Insira qualquer anotação adicional para os destinatários do email como texto sem formatação. Você pode usar a formatação HTML se [Editar o modelo de email](#htmledit).

1. Verifique se o **status da regra** está definido como **habilitado**.

1. Clique em **Aplicar**.

Sua regra com uma ação de email agora está habilitada. Cada vez que a ação é disparada, um novo email é enviado aos destinatários.

## Entrar no Outlook<a name="outlook"></a>

Na primeira vez que você habilitar uma ação de email no Solution Accelerator, você deve entrar no Outlook. Essa ação configura a conta de email que envia as notificações por email.

> [!NOTE]
> Você deve criar uma conta específica do Outlook apenas para notificações do Solution Accelerator e usá-la quando habilitar sua primeira ação de email.

### <a name="contributor-role-outlook-setup"></a>Configuração do Outlook da função de colaborador

Se alguém na função **colaborador** na assinatura implantou o Solution Accelerator, o aplicativo não tem permissões suficientes para configurar e verificar ações de email por meio da interface do usuário da Web.

Antes de começar, crie uma conta do Outlook a ser usada para enviar notificações por email do Solution Accelerator.

As etapas a seguir mostram como configurar e verificar as ações de email manualmente:

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Navegue até o grupo de recursos para o Solution Accelerator.

1. Clique no **conector do Office365**:

    ![Conexão de API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Clique na faixa para iniciar o processo de autorização:

    ![autoriza](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Clique em **autorizar**. Você será solicitado a entrar. A conta que você usa para entrar deve ser o endereço de email que o aplicativo usa para enviar notificações por email:

    ![Botão autorizar](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Clique em **salvar** na parte inferior. Sua autorização será bem-sucedida se a faixa não existir.

1. Para alterar o endereço de email do qual as notificações são enviadas, clique em **Editar conexão de API**.

    ![alterar email](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Configuração do Outlook da função do proprietário

Se alguém na função de **proprietário** na assinatura tiver implantado o Solution Accelerator, o aplicativo poderá verificar se as ações de email foram configuradas corretamente por meio da interface do usuário da Web.

Antes de começar, crie uma conta do Outlook a ser usada para enviar notificações por email do Solution Accelerator.

As etapas a seguir ajudam você a entrar e configurar as ações de email:

1. Clique para entrar no Outlook. Você é levado para a portal do Azure:

   ![Entrar no Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Clique em **autorizar**. Você será solicitado a entrar. A conta que você usa para entrar deve ser o endereço de email que o aplicativo usa para enviar notificações por email:

1. Clique em **Guardar**. Retorne ao Solution Accelerator e atualize a página.

1. Se você tiver configurado a notificação por email com êxito, verá esta mensagem:

   ![Entrada bem-sucedida do Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Personalizar o HTML do email<a name="htmledit"></a>

De forma integrada, o acelerador de solução de monitoramento remoto fornece um modelo HTML básico para emails de ação. O modelo de email usa valores das configurações de ação de email. Aqui está um exemplo de email:

![exemplo de email](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

As etapas a seguir mostram como editar o modelo de email HTML. Por exemplo, você pode incluir mais informações ou adicionar imagens personalizadas:

1. Clone o repositório GitHub de monitoramento remoto Java ou .NET:

1. Navegue até o local do modelo de email:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Você pode adicionar ou remover qualquer parâmetro neste modelo para personalizar a mensagem. Você também pode adicionar, remover ou substituir chamadas conforme necessário:

    Por exemplo, no código .NET: `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Por exemplo, no código Java: `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Os parâmetros no modelo assumem a forma de `${...}`. Para excluir um parâmetro, exclua a linha necessária. Para adicionar um parâmetro, adicione uma linha com o valor a ser inserido.

1. Para adicionar imagens ou texto personalizado, atualize o arquivo eMailTemplate. HTML diretamente.

## <a name="throttling"></a>Limitação

O acelerador de solução de monitoramento remoto usa o Outlook para enviar notificações por email. O Outlook limita o número de emails enviados a [30 emails por 1 minuto](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Os clientes de email que recebem os emails também podem limitar o número de emails recebidos por minuto. Verifique com seu cliente de email específico se houver limitações. Quando você configura a notificação por email para uma regra, a regra deve calcular valores médios em um período de pelo menos um minuto e não usar valores instantâneos:

![Cálculo médio](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Passos seguintes

Este guia mostrou como adicionar uma ação de email a uma regra nova ou existente em uma solução de monitoramento remoto. O guia também mostrou você e como editar o HTML que define o formato da mensagem.

A próxima etapa sugerida é aprender a [usar alertas e corrigir problemas de dispositivo](iot-accelerators-remote-monitoring-maintain.md).
