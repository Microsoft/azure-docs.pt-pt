---
title: Solicitar acesso just-in-time-aplicativos gerenciados do Azure
description: Descreve como os editores de aplicativos gerenciados do Azure solicitam acesso just-in-time a um aplicativo gerenciado.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: fdd1dcf5ab6b58a91534a465a8c92c10de0d2097
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528963"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure

Os consumidores do seu aplicativo gerenciado podem ser relutados para conceder a você acesso permanente ao grupo de recursos gerenciado. Como um editor de um aplicativo de gerente, você pode preferir que os consumidores saibam exatamente quando você precisar acessar os recursos gerenciados. Para fornecer aos consumidores maior controle sobre a concessão de acesso a recursos gerenciados, os aplicativos gerenciados do Azure fornecem um recurso chamado acesso JIT (just-in-time), que está atualmente em versão prévia.

O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso.

O fluxo de trabalho para conceder acesso é:

1. Você adiciona um aplicativo gerenciado ao Marketplace e especifica que o acesso JIT está disponível.

1. Durante a implantação, o consumidor habilita o acesso JIT para essa instância do aplicativo gerenciado.

1. Após a implantação, o consumidor pode alterar as configurações de acesso JIT.

1. Você envia uma solicitação de acesso quando precisa solucionar problemas ou atualizar os recursos gerenciados.

1. O consumidor aprova sua solicitação.

Este artigo se concentra nas ações que os editores adotam para habilitar o acesso JIT e enviar solicitações. Para saber mais sobre a aprovação de solicitações de acesso JIT, consulte [aprovar o acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicionar etapa de acesso JIT à interface do usuário

Seu arquivo CreateUiDefinition. JSON é exatamente como o arquivo de interface do usuário que você cria para acesso permanente, exceto que você deve incluir uma etapa que permite aos consumidores habilitar o acesso JIT. Para saber mais sobre como publicar sua primeira oferta de aplicativo gerenciado no Azure Marketplace, confira [aplicativos gerenciados do Azure no Marketplace](publish-marketplace-app.md).

Para dar suporte à funcionalidade JIT para sua oferta, adicione o seguinte conteúdo ao seu arquivo CreateUiDefinition. JSON:

Em "etapas":

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
> O acesso JIT está em versão prévia. O esquema para a configuração de JIT pode mudar em iterações futuras.

## <a name="enable-jit-access"></a>Habilitar acesso JIT

Ao definir sua oferta no Marketplace, certifique-se de habilitar o acesso JIT.

1. Entre no portal de [publicação de parceiros de nuvem](https://cloudpartner.azure.com).

1. Forneça valores para publicar seu aplicativo gerenciado no Marketplace. Selecione **Sim** para **habilitar o acesso JIT?**

   ![Habilitar o acesso just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Você adicionou uma etapa de configuração JIT à sua interface do usuário e habilitou o acesso JIT na oferta do Marketplace. Quando os consumidores implantam seu aplicativo gerenciado, eles podem [ativar o acesso JIT para sua instância](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Pedir acesso

Quando você precisar acessar os recursos gerenciados do consumidor, envie uma solicitação para uma função específica, hora e duração. O consumidor deve, então, aprovar a solicitação.

Para enviar uma solicitação de acesso JIT:

1. Selecione **acesso JIT** para o aplicativo gerenciado que você precisa acessar.

1. Selecione **funções qualificadas**e selecione **Ativar** na coluna ação para a função desejada.

   ![Ativar solicitação de acesso](./media/request-just-in-time-access/send-request.png)

1. No formulário **Ativar função** , selecione uma hora de início e a duração da sua função como ativa. Selecione **Ativar** para enviar a solicitação.

   ![Ativar o acesso](./media/request-just-in-time-access/activate-access.png) 

1. Exiba as notificações para ver que a nova solicitação JIT foi enviada com êxito ao consumidor.

   ![Notificação](./media/request-just-in-time-access/in-progress.png)

   Agora, você deve aguardar até que o consumidor [aprove sua solicitação](approve-just-in-time-access.md#approve-requests).

1. Para exibir o status de todas as solicitações JIT de um aplicativo gerenciado, selecione **acesso JIT** e **histórico de solicitações**.

   ![Ver estado](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

A ID da entidade de segurança da conta que solicita acesso JIT deve ser incluída explicitamente na definição do aplicativo gerenciado. A conta não pode ser incluída apenas por meio de um grupo especificado no pacote. Essa limitação será corrigida em uma versão futura.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a aprovação de solicitações de acesso JIT, consulte [aprovar o acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).
