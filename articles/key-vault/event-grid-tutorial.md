---
title: Receba e responda a notificações chave do cofre com a Grelha de Eventos Azure
description: Saiba como integrar o Key Vault com a Grelha de Eventos Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 5eaf4cf702e56df932a61ab277dff6b34d97854d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185034"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Receber e responder a notificações chave do cofre com a Grelha de Eventos Azure (pré-visualização)

A integração do Cofre chave Azure com a Azure Event Grid (atualmente em pré-visualização) permite a notificação do utilizador quando o estado de um segredo armazenado num cofre chave mudou. Para uma visão geral desta funcionalidade, consulte [o Cofre de Chaves de Monitorização com Grelha](event-grid-overview.md)de Eventos .

Este guia descreve como receber notificações do Cofre chave através da Grelha de Eventos e como responder às alterações de estado através da Automação Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre chave na sua assinatura Azure. Você pode rapidamente criar um novo cofre chave seguindo os passos em [set e recuperar um segredo de Azure Key Vault usando Azure CLI](quick-create-cli.md).

## <a name="concepts"></a>Conceitos

Event Grid é um serviço de eventos para a nuvem. Seguindo os passos deste guia, subscreverá eventos para Key Vault e eventos de rota para automação. Quando um dos segredos do cofre chave está prestes a expirar, a Grelha de Eventos é notificada da mudança de estado e faz um POST HTTP para o ponto final. Um gancho web então desencadeia uma execução de Automação de um script PowerShell.

![HTTP POST flowchart](media/image1.png)

## <a name="create-an-automation-account"></a>Criar uma conta de Automatização

Criar uma conta de Automação através do [portal Azure:](https://portal.azure.com)

1.  Vá para portal.azure.com e faça login na sua subscrição.

1.  Na caixa de pesquisa, **introduza Contas de Automação**.

1.  Na secção **Serviços** da lista de abandono na barra de pesquisa, selecione **Contas de Automação**.

1.  Selecione **Adicionar**.

    ![Painel de contas de automação](media/image2.png)

1.  Introduza as informações necessárias no painel **da Conta Add Automation** e, em seguida, selecione **Criar**.

## <a name="create-a-runbook"></a>Criar um runbook

Depois da sua conta Deautomação estar pronta, crie um livro de execução.

![Criar um livro de corridas UI](media/image3.png)

1.  Selecione a conta Automation que acabou de criar.

1.  Selecione **Runbooks** em **Automação de Processos**.

1.  Selecione **Criar um livro de execução**.

1.  Nomeie o seu livro de execução e selecione **PowerShell** como o tipo de livro de execução.

1.  Selecione o livro de execução que criou e, em seguida, selecione o botão **Editar.**

1.  Introduza o seguinte código (para efeitos de teste) e selecione o botão **Publicar.** Esta ação devolve o resultado do pedido do POST recebido.

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

![Publicar livro de execução UI](media/image4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Crie um webhook para desencadear o seu livro de execução recém-criado.

1.  Selecione **Webhooks** da secção **Recursos** do livro de execução que acaba de publicar.

1.  **Selecione Adicionar Webhook**.

    ![Adicionar botão Webhook](media/image5.png)

1.  Selecione **Criar um novo Webhook**.

1. Nomeie o webhook, estabeleça uma data de validade e copie o URL.

    > [!IMPORTANT] 
    > Não se pode ver a URL depois de a criar. Certifique-se de que guarda uma cópia num local seguro onde poderá aceder-lhe durante o resto deste guia.

1. **Selecione Parâmetros e executar definições** e, em seguida, selecione **OK**. Não introduza parâmetros. Isto ativará o botão **Criar.**

1. Selecione **OK** e, em seguida, selecione **Criar**.

    ![Criar novo Webhook UI](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma subscrição da Rede de Eventos através do [portal Azure.](https://portal.azure.com)

1.  Vá ao seu cofre de chaves e selecione o separador **Eventos.** Se não conseguir vê-lo, certifique-se de que está a utilizar a [versão de pré-visualização do portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Separador de eventos no portal Azure](media/image7.png)

1.  Selecione o botão **de subscrição** do evento.

1.  Crie um nome descritivo para a subscrição.

1.  Escolha a **Grelha de Eventos Schema**.

1.  **O Recurso tópico** deve ser o cofre chave que pretende monitorizar para alterações de estado.

1.  Para filtrar os tipos de **eventos,** deixe todas as opções selecionadas **(9 selecionadas).**

1.  No **Tipo de Ponto Final**, selecione **Webhook**.

1.  Escolha **Selecione um ponto final**. No novo painel de contexto, colhe o URL webhook do lado da [Criação de um](#create-a-webhook) passo webhook no campo **Delineio do Assinante.**

1.  Selecione **Confirmar Seleção** no painel de contexto.

1.  Selecione **Criar**.

    ![Criar subscrição de eventos](media/image8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se a subscrição da Grelha de Eventos está corretamente configurada. Este teste pressupõe que subscreveu a notificação "Secret New Version Created" na [subscrição Create a Event Grid](#create-an-event-grid-subscription), e que tem as permissões necessárias para criar uma nova versão de um segredo num cofre chave.

![Config de teste da assinatura da Grelha de Eventos](media/image9.png)

![Crie um painel secreto](media/image10.png)

1.  Vá ao seu cofre chave no portal Azure.

1.  Criar um novo segredo. Para efeitos de teste, determina a expiração até ao dia seguinte.

1.  No separador **Eventos** no seu cofre de chaves, selecione a subscrição da Grelha de Eventos que criou.

1.  Segundo **as Métricas,** verifique se um evento foi capturado. Esperam-se dois eventos: SecretNewVersion e SecretNearExpiry. Estes eventos validam que a Rede de Eventos capturou com sucesso a mudança de estado do segredo no seu cofre chave.

    ![Painel de métricas: verifique se há eventos capturados](media/image11.png)

1.  Vá à sua conta de Automação.

1.  Selecione o separador **'Livros de Execução'** e, em seguida, selecione o livro de execução que criou.

1.  Selecione o separador **Webhooks** e confirme que o carimbo de tempo "último desencadeado" está dentro de 60 segundos a quando criou o novo segredo. Este resultado confirma que a Rede de Eventos fez um POST no webhook com os detalhes do evento da mudança de estado no seu cofre chave e que o webhook foi acionado.

    ![Separador Webhooks, Último carimbo de tempo desencadeado](media/image12.png)

1. Volte ao seu livro de execução e selecione o separador **Overview.**

1. Veja a lista de **Empregos Recentes.** Devias ver que foi criado um trabalho e que o estatuto está completo. Isto confirma que o webhook desencadeou o livro de execução para começar a executar o seu script.

    ![Lista de Empregos Recentes webhook](media/image13.png)

1. Selecione o trabalho recente e veja o pedido do POST que foi enviado da Rede de Eventos para o webhook. Examine o JSON e certifique-se de que os parâmetros para o seu cofre chave e tipo de evento estão corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento que ocorreu no cofre da chave (neste exemplo, Microsoft.KeyVault.SecretNearExpiry), o teste foi bem sucedido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="you-cant-create-an-event-subscription"></a>Não pode criar uma subscrição de evento

Reregistre a Rede de Eventos e o fornecedor chave do cofre nos seus fornecedores de recursos de subscrição Azure. Consulte [os fornecedores e tipos](../azure-resource-manager/management/resource-providers-and-types.md)de recursos Azure .

## <a name="next-steps"></a>Passos seguintes

Parabéns! Se seguiu corretamente todos estes passos, está agora pronto para responder programáticamente às mudanças de estado dos segredos armazenados no seu cofre chave.

Se tem usado um sistema baseado em sondagens para procurar mudanças de estado nos seus cofres chave, pode agora começar a usar esta funcionalidade de notificação. Também pode substituir o script de teste no seu livro de execução por código para renovar programaticamente os seus segredos quando estão prestes a expirar.

Saiba mais:


- Visão geral: Cofre de chaves de monitorização com grelha de [eventos Azure (pré-visualização)](event-grid-overview.md)
- Como: [Receber e-mail quando um cofre chave muda](event-grid-logicapps.md)
- [Esquema de evento sinuoso do evento Azure Event Grid para cofre de chaves Azure (pré-visualização)](../event-grid/event-schema-key-vault.md)
- [Visão geral do cofre da chave azure](key-vault-overview.md)
- [Visão geral da Grelha de Eventos Azure](../event-grid/overview.md)
- [Descrição geral da Automatização do Azure](../automation/index.yml)
