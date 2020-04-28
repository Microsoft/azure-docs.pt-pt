---
title: Pedir acesso just-in-time
description: Descreve como os editores de Aplicações Geridas azure solicitam acesso just-in-time a uma aplicação gerida.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651387"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Ativar e solicitar acesso just-in-time para aplicações geridas azure

Os consumidores da sua aplicação gerida podem estar relutantes em conceder-lhe acesso permanente ao grupo de recursos geridos. Como editor de uma aplicação de gestor, pode preferir que os consumidores saibam exatamente quando precisa aceder aos recursos geridos. Para dar aos consumidores um maior controlo sobre a concessão de acesso a recursos geridos, a Azure Managed Applications fornece uma funcionalidade chamada acesso just-in-time (JIT), que está atualmente em pré-visualização.

O acesso ao JIT permite-lhe solicitar um acesso elevado aos recursos de uma aplicação gerida para resolução ou manutenção de problemas. Você sempre tem acesso apenas a leitura aos recursos, mas por um período de tempo específico você pode ter um maior acesso.

O fluxo de trabalho para a concessão de acesso é:

1. Adiciona uma aplicação gerida ao mercado e especifica que o acesso ao JIT está disponível.

1. Durante a implantação, o consumidor permite o acesso ao IJ por exemplo da aplicação gerida.

1. Após a implementação, o consumidor pode alterar as definições para o acesso ao JIT.

1. Envia um pedido de acesso quando necessita de resolver problemas ou atualizar os recursos geridos.

1. O consumidor aprova o seu pedido.

Este artigo centra-se nas ações que os editores tomam para permitir o acesso ao JIT e submeter pedidos. Para saber sobre a aprovação de pedidos de acesso jit, consulte [Approve acesso just-in-time em Aplicações Geridas Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicione o passo de acesso jit à UI

O ficheiro CreateUiDefinition.json é exatamente como o ficheiro UI que cria para acesso permanente, exceto que deve incluir um passo que permita aos consumidores ativar o acesso ao JIT. Para saber mais sobre a publicação da sua primeira oferta de aplicação gerida no Azure Marketplace, consulte [as Aplicações Geridas do Azure no Mercado.](publish-marketplace-app.md)

Para suportar a capacidade do JIT para a sua oferta, adicione o seguinte conteúdo ao seu ficheiro CreateUiDefinition.json:

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
> O acesso ao JIT está em pré-visualização. O esquema para a configuração do JIT pode mudar em futuras iterações.

## <a name="enable-jit-access"></a>Ativar o acesso ao JIT

Ao definir a sua oferta no mercado, certifique-se de que permite o acesso ao JIT.

1. Inscreva-se no portal de [publicação Cloud Partner](https://cloudpartner.azure.com).

1. Forneça valores para publicar a sua aplicação gerida no mercado. Selecione **Sim** para **ativar o acesso ao JIT?**

   ![Ativar acesso just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Adicionou um passo de configuração JIT ao seu UI e permitiu o acesso ao JIT na oferta de marketplace. Quando os consumidores implementam a sua aplicação gerida, podem ligar o acesso ao [JIT por exemplo.](approve-just-in-time-access.md#enable-during-deployment)

## <a name="request-access"></a>Pedir acesso

Quando precisa de aceder aos recursos geridos do consumidor, envia um pedido de uma função, tempo e duração específicos. O consumidor deve então aprovar o pedido.

Para enviar um pedido de acesso JIT:

1. Selecione **Acesso JIT** para a aplicação gerida a que precisa de aceder.

1. Selecione **Funções Elegíveis**e selecione **Ativar** na coluna ACTION para o papel que deseja.

   ![Ativar pedido de acesso](./media/request-just-in-time-access/send-request.png)

1. No formulário **'Activate Role',** selecione uma hora e duração de início para que o seu papel esteja ativo. Selecione **Ativar** para enviar o pedido.

   ![Ativar o acesso](./media/request-just-in-time-access/activate-access.png) 

1. Consulte as notificações para ver se o novo pedido de IJ é enviado com sucesso ao consumidor.

   ![Notificação](./media/request-just-in-time-access/in-progress.png)

   Agora, deve esperar que o consumidor [aprove o seu pedido.](approve-just-in-time-access.md#approve-requests)

1. Para ver o estado de todos os pedidos de JIT para uma aplicação gerida, selecione **JIT Access** and **Request History**.

   ![Ver estado](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

A identificação principal da conta que solicita o acesso ao JIT deve ser explicitamente incluída na definição de aplicação gerida. A conta não pode ser incluída apenas através de um grupo especificado no pacote. Esta limitação será fixada numa versão futura.

## <a name="next-steps"></a>Passos seguintes

Para conhecer os pedidos de aprovação do acesso ao JIT, consulte [Approve access just-in-time in Azure Managed Applications](approve-just-in-time-access.md).
