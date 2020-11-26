---
title: Receber e responder às notificações chave do cofre com a Azure Event Grid
description: Saiba como integrar o Key Vault com a Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187379"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Receber e responder às notificações chave do cofre com a Azure Event Grid

A integração do Azure Key Vault com a Azure Event Grid permite a notificação do utilizador quando o estado de um segredo armazenado num cofre de chaves mudou. Para uma visão geral desta funcionalidade, consulte [monitoring Key Vault com Grade de Eventos](event-grid-overview.md).

Este guia descreve como receber notificações do Key Vault através da Grade de Eventos e como responder às alterações de estado através da Azure Automation.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre chave na sua assinatura Azure. Pode rapidamente criar um novo cofre de chaves seguindo os passos em [set e recuperar um segredo do Azure Key Vault usando o Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Conceitos

Event Grid é um serviço de eventos para a nuvem. Seguindo os passos deste guia, irá subscrever eventos para Key Vault e encaminhar eventos para Automação. Quando um dos segredos no cofre está prestes a expirar, a Grade de Eventos é notificada da alteração de estado e faz um HTTP POST para o ponto final. Um gancho web ativa então uma execução de automação de um script PowerShell.

![FLUXOR DE POST HTTP](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Criar uma conta de Automatização

Criar uma conta Automation através do [portal Azure:](https://portal.azure.com)

1.  Vá a portal.azure.com e faça login na sua subscrição.

1.  Na caixa de pesquisa, **insira contas de Automação.**

1.  Na secção **serviços** da lista de drop-down na barra de pesquisa, selecione **Contas de Automação**.

1.  Selecione **Adicionar**.

    ![Painel de contas de automação](../media/event-grid-tutorial-2.png)

1.  Introduza as informações necessárias no painel **de Conta Ded automação adicionar** e, em seguida, selecione **Criar**.

## <a name="create-a-runbook"></a>Criar um runbook

Depois de a sua conta Automation estar pronta, crie um livro de contas.

![Criar uma UI runbook](../media/event-grid-tutorial-3.png)

1.  Selecione a conta Automation que acabou de criar.

1.  Selecione **Runbooks** em **Automação de Processos.**

1.  Selecione **Criar um livro de bordo.**

1.  Nomeie o seu livro de execução e selecione **PowerShell** como o tipo de livro de execução.

1.  Selecione o livro de execução que criou e, em seguida, selecione o botão **Editar.**

1.  Introduza o seguinte código (para efeitos de teste) e selecione o botão **Publicar.** Esta ação devolve o resultado do pedido do CORREIO recebido.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Publicar uI runbook](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Crie um webhook para desencadear o seu novo livro de aplicações.

1.  Selecione **Webhooks** da secção **recursos** do livro que acaba de publicar.

1.  **Selecione Add Webhook**.

    ![Adicione botão Webhook](../media/event-grid-tutorial-5.png)

1.  Selecione **Criar novo Webhook**.

1. Nomeie o webhook, estabeleça uma data de validade e copie o URL.

    > [!IMPORTANT] 
    > Não pode ver a URL depois de criá-la. Certifique-se de guardar uma cópia num local seguro onde possa acessá-la durante o resto deste guia.

1. Selecione **parâmetros e executar as definições** e, em seguida, selecione **OK**. Não introduza nenhum parâmetro. Isto ativará o botão **Criar.**

1. Selecione **OK** e, em seguida, selecione **Criar**.

    ![Criar novo Webhook UI](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma subscrição de Grade de Eventos através do [portal Azure.](https://portal.azure.com)

1.  Vá ao seu cofre chave e selecione o **separador Eventos.**

    ![Separador de eventos no portal Azure](../media/event-grid-tutorial-7.png)

1.  Selecione o botão **de subscrição de evento.**

1.  Crie um nome descritivo para a subscrição.

1.  Escolha **o esquema da grelha de eventos**.

1.  **O Recurso Tópico** deve ser o cofre-chave que pretende monitorizar para alterações de estado.

1.  Para **filtrar para tipos de eventos,** deixe todas as opções selecionadas **(9 selecionadas).**

1.  No **Tipo de Ponto Final**, selecione **Webhook**.

1.  Escolha **Selecionar um ponto final.** No novo painel de contexto, cole o URL webhook a partir do [Create a webhook](#create-a-webhook) step into the **Subscriber Endpoint.**

1.  **Selecione Confirmar Seleção** no painel de contexto.

1.  Selecione **Criar**.

    ![Criar subscrição de eventos](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se a subscrição da grelha de eventos está corretamente configurada. Este teste pressupõe que subscreveu a notificação "Secret New Version Created" na [subscrição Criar uma Grade de Eventos](#create-an-event-grid-subscription), e que tem as permissões necessárias para criar uma nova versão de um segredo num cofre chave.

![Teste config da subscrição da Grade de Eventos](../media/event-grid-tutorial-9.png)

![Criar um painel secreto](../media/event-grid-tutorial-10.png)

1.  Vá ao cofre do portal Azure.

1.  Criar um novo segredo. Para efeitos de teste, desa cise a validade até ao dia seguinte.

1.  No separador **Eventos** no seu cofre de chaves, selecione a subscrição da Grelha de Eventos que criou.

1.  Em **Métricas,** verifique se um evento foi capturado. São esperados dois eventos: SecretNewVersion e SecretNearExpiry. Estes eventos validam que a Grade de Eventos capturou com sucesso a mudança de estado do segredo no seu cofre principal.

    ![Painel métrico: verifique se há eventos capturados](../media/event-grid-tutorial-11.png)

1.  Vá à sua conta de Automação.

1.  Selecione o **separador Runbooks** e, em seguida, selecione o livro de bordo que criou.

1.  Selecione o separador **Webhooks** e confirme que a última marcação de tempo "desencadeada" está dentro de 60 segundos de quando criou o novo segredo. Este resultado confirma que a Grade de Eventos fez um POST para o webhook com os detalhes do evento da mudança de estado no seu cofre chave e que o webhook foi acionado.

    ![Separador Webhooks, última marcação de tempo desencadeada](../media/event-grid-tutorial-12.png)

1. Volte ao seu runbook e selecione o **separador 'Vista Geral'.**

1. Olhe para a lista **de empregos recentes.** Devias ver que foi criado um emprego e que o estatuto está completo. Isto confirma que o webhook desencadeou o livro de execução para começar a executar o seu script.

    ![Lista de empregos recentes da Webhook](../media/event-grid-tutorial-13.png)

1. Selecione o trabalho recente e veja o pedido POST que foi enviado da Grade de Evento para o webhook. Examine o JSON e certifique-se de que os parâmetros para o seu cofre e tipo de evento estão corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento ocorrido no cofre de chaves (neste exemplo, Microsoft.KeyVault.SecretNearExpiry), o teste foi bem sucedido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="you-cant-create-an-event-subscription"></a>Não é possível criar uma subscrição de eventos

Reregister Event Grid e o fornecedor de cofre chave nos seus fornecedores de recursos de subscrição Azure. Consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Passos seguintes

Parabéns! Se seguiu corretamente todos estes passos, está agora pronto para responder programáticamente às mudanças de estado dos segredos armazenados no seu cofre principal.

Se tem usado um sistema baseado em sondagens para procurar mudanças de estado de segredos nos seus cofres chave, pode agora começar a usar esta funcionalidade de notificação. Também pode substituir o script de teste no seu livro de recortes por código para renovar programaticamente os seus segredos quando estão prestes a expirar.

Saiba mais:


- Visão geral: [Cofre de chaves de monitorização com grade de eventos Azure](event-grid-overview.md)
- Como: [Receber e-mail quando um segredo de cofre chave muda](event-grid-logicapps.md)
- [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Visão geral do cofre da chave Azure](overview.md)
- [Descrição geral do Azure Event Grid](../../event-grid/overview.md)
- [Visão geral da Azure Automation](../../automation/index.yml)
