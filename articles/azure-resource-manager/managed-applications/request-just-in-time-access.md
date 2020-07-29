---
title: Pedir acesso just-in-time
description: Descreve como os editores de Aplicações Geridas Azure solicitam o acesso a uma aplicação gerida no tempo.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651387"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Ativar e solicitar acesso just-in-time para aplicações geridas azure

Os consumidores da sua aplicação gerida podem estar relutantes em conceder-lhe acesso permanente ao grupo de recursos geridos. Como editor de uma aplicação de gestor, você pode preferir que os consumidores saibam exatamente quando você precisa aceder aos recursos geridos. Para dar aos consumidores um maior controlo sobre a concessão de acesso a recursos geridos, a Azure Managed Applications fornece uma funcionalidade chamada acesso just-in-time (JIT), que está atualmente em pré-visualização.

O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução de problemas ou manutenção. Sempre tem acesso apenas de leitura aos recursos, mas por um período específico pode ter um maior acesso.

O fluxo de trabalho para a concessão de acesso é:

1. Adicione uma aplicação gerida ao mercado e especifique que o acesso ao JIT está disponível.

1. Durante a implantação, o consumidor permite o acesso ao JIT para este caso da aplicação gerida.

1. Após a implementação, o consumidor pode alterar as definições para o acesso ao JIT.

1. Envia um pedido de acesso quando precisa de resolver problemas ou atualizar os recursos geridos.

1. O consumidor aprova o seu pedido.

Este artigo centra-se nas ações que os editores tomam para permitir o acesso ao JIT e submeter pedidos. Para saber mais sobre a aprovação dos pedidos de acesso ao JIT, consulte [aprovar o acesso just-in-time nas Aplicações Geridas Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicione passo de acesso JIT à UI

O seu CreateUiDefinition.jsem ficheiro é exatamente igual ao ficheiro UI que cria para acesso permanente, exceto deve incluir um passo que permita aos consumidores o acesso ao JIT. Para saber mais sobre a publicação da sua primeira oferta de aplicações geridas no Azure Marketplace, consulte [as Aplicações Geridas Azure no Mercado.](publish-marketplace-app.md)

Para suportar a capacidade JIT da sua oferta, adicione o seguinte conteúdo ao seu CreateUiDefinition.jsno ficheiro:

Em "passos":

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
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> O acesso ao JIT está em pré-visualização. O esquema para a configuração do JIT pode mudar em iterações futuras.

## <a name="enable-jit-access"></a>Permitir o acesso ao JIT

Ao definir a sua oferta no mercado, certifique-se de que permite o acesso ao JIT.

1. Inscreva-se no [portal de publicação Cloud Partner](https://cloudpartner.azure.com).

1. Forneça valores para publicar a sua aplicação gerida no mercado. Selecione **Sim** para **Ativar o Acesso JIT?**

   ![Ativar o acesso just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Adicionou um passo de configuração JIT à sua UI e permitiu o acesso ao JIT na oferta do mercado. Quando os consumidores implementam a sua aplicação gerida, podem [ligar o acesso ao JIT, por exemplo.](approve-just-in-time-access.md#enable-during-deployment)

## <a name="request-access"></a>Pedir acesso

Quando precisa de aceder aos recursos geridos pelo consumidor, envia um pedido para uma função específica, tempo e duração. O consumidor deve então aprovar o pedido.

Para enviar um pedido de acesso JIT:

1. Selecione **JIT Access** para a aplicação gerida a que necessita aceder.

1. Selecione **Funções Elegíveis**e selecione **Ative** na coluna ACTION para a função desejada.

   ![Ativar pedido de acesso](./media/request-just-in-time-access/send-request.png)

1. No formulário **'Activar',** selecione uma hora de início e duração para que a sua função esteja ativa. **Selecione Ativar** para enviar o pedido.

   ![Ativar o acesso](./media/request-just-in-time-access/activate-access.png) 

1. Consulte as notificações para ver se o novo pedido do JIT é enviado com êxito ao consumidor.

   ![Notificação](./media/request-just-in-time-access/in-progress.png)

   Agora, deve esperar que o consumidor [aprove o seu pedido.](approve-just-in-time-access.md#approve-requests)

1. Para visualizar o estado de todos os pedidos de JIT para uma aplicação gerida, selecione **JIT Access** and **Request History**.

   ![Ver estado](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

A identificação principal da conta que solicita o acesso ao JIT deve ser explicitamente incluída na definição de aplicação gerida. A conta não pode ser incluída apenas através de um grupo especificado no pacote. Esta limitação será corrigida numa futura libertação.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a aprovação dos pedidos de acesso ao JIT, consulte [aprovar o acesso just-in-time nas Aplicações Geridas Azure](approve-just-in-time-access.md).
