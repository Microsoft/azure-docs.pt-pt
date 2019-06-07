---
title: Ativar e pedir acesso just-in-time para aplicações geridas do Azure
description: Descreve como os publicadores de aplicações geridas do Azure pedem acesso just-in-time a uma aplicação gerida.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481778"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Ativar e pedir acesso just-in-time para aplicações geridas do Azure

Os consumidores do seu aplicativo gerenciado podem estar relutantes em conceder acesso permanente ao grupo de recursos gerido. Enquanto fabricante de uma aplicação do manager, poderá preferir que os consumidores sabem exatamente quando precisa de aceder os recursos geridos. Fornecer aos clientes mais controle sobre a concessão do acesso a recursos geridos, as aplicações geridas do Azure fornece um recurso chamado acesso de (JIT) just-in-time, o que está atualmente em pré-visualização.

Acesso JIT permite-lhe pedir acesso elevado aos recursos de um aplicativo gerenciado para resolução de problemas ou manutenção. Sempre terá acesso só de leitura para os recursos, mas durante um período de tempo específico pode ter mais acesso.

O fluxo de trabalho para a concessão de acesso é:

1. Adicionar uma aplicação gerida no Marketplace e especificar que o acesso JIT está disponível.

1. Durante a implementação, o consumidor permite o acesso JIT para essa instância da aplicação gerida.

1. Após a implementação, o consumidor pode alterar as definições de acesso JIT.

1. Enviar um pedido de acesso quando tiver de resolver problemas ou atualizar os recursos gerenciados.

1. O consumidor aprova o pedido.

Este artigo enfoca as ações que os publicadores seguir para ativar o acesso JIT e submeter pedidos. Para saber mais sobre a aprovação de pedidos de acesso JIT, veja [aprovar o acesso just-in-time nas aplicações geridas do Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicionar passo de acesso JIT à interface do Usuário

O ficheiro Createuidefinition é exatamente como o arquivo da interface do Usuário criado para ter acesso permanente, exceto pelo fato tem de incluir um passo que permite que os consumidores de ativar o acesso JIT. Para saber mais sobre como publicar o seu primeiro aplicativo gerenciado oferta no Azure Marketplace, veja [aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).

Para suportar a capacidade JIT para a sua oferta, adicione o seguinte conteúdo para o ficheiro Createuidefinition:

Nos "passos":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
Em "saídas":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Acesso JIT está em pré-visualização. O esquema para a configuração de JIT poderia alterar no futuro iterações.

## <a name="enable-jit-access"></a>Ativar o acesso JIT

Ao definir a sua oferta no marketplace, certifique-se de que ativar o acesso JIT.

1. Inicie sessão para o [portal de publicação do parceiro de Cloud](https://cloudpartner.azure.com).

1. Fornece valores para publicar a sua aplicação gerida no marketplace. Selecione **Sim** para **ativar o acesso JIT?**

   ![Ativar o acesso just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Adicionou um passo de configuração de JIT à sua interface do Usuário e tiver ativado o acesso JIT na oferta do marketplace. Quando os consumidores implantar seu aplicativo gerenciado, eles podem [ativar o acesso JIT às respetivas instâncias](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Pedir acesso

Quando precisar de aceder a recursos geridos do consumidor, envia um pedido para uma função específica, o tempo e a duração. O consumidor, em seguida, terá de aprovar o pedido.

Para enviar um pedido de acesso JIT:

1. Selecione **acesso JIT** para a aplicação gerida, precisa acessar.

1. Selecione **funções elegíveis**e selecione **ativar** na coluna ação para a função que pretende.

   ![Ativar o pedido de acesso](./media/request-just-in-time-access/send-request.png)

1. Sobre o **ativar função** de formulário, selecione uma hora de início e a duração de sua função ser o Active Directory. Selecione **Activate** para enviar o pedido.

   ![Ativar o acesso](./media/request-just-in-time-access/activate-access.png) 

1. Ver as notificações para ver que o novo pedido JIT é enviado com êxito para o consumidor.

   ![Notificação](./media/request-just-in-time-access/in-progress.png)

   Agora, tem de aguardar o consumidor [aprovar o pedido](approve-just-in-time-access.md#approve-requests).

1. Para ver o estado de todos os pedidos JIT para uma aplicação gerida, selecione **acesso JIT** e **histórico de pedidos**.

   ![Ver estado](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

O ID de principal da conta pedir o acesso JIT têm de ser explicitamente incluído na definição da aplicação gerida. A conta apenas não pode ser incluída através de um grupo especificado no pacote. Esta limitação será corrigida numa versão futura.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a aprovação de pedidos de acesso JIT, veja [aprovar o acesso just-in-time nas aplicações geridas do Azure](approve-just-in-time-access.md).
