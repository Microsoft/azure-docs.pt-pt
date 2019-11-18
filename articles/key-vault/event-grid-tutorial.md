---
title: Receber e responder a notificações do Key Vault com a grade de eventos do Azure
description: Saiba como integrar o Key Vault com a grade de eventos do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: b30e260b2eeb0d8af0c347996cdb51685dedd046
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133329"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Receber e responder a notificações do cofre de chaves com a grade de eventos do Azure (versão prévia)

Azure Key Vault integração com a grade de eventos do Azure (atualmente em visualização) habilita a notificação do usuário quando o status de um segredo armazenado em um cofre de chaves é alterado. Para obter uma visão geral desse recurso, consulte [monitoramento Key Vault com a grade de eventos](event-grid-overview.md).

Este guia descreve como receber notificações de Key Vault por meio da grade de eventos e como responder a alterações de status por meio da automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves em sua assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [definir e recuperar um segredo de Azure Key Vault usando CLI do Azure](quick-create-cli.md).

## <a name="concepts"></a>Conceitos

A grade de eventos é um serviço de eventos para a nuvem. Seguindo as etapas neste guia, você assinará eventos para Key Vault e rotear eventos para automação. Quando um dos segredos no cofre de chaves está prestes a expirar, a grade de eventos é notificada sobre a alteração de status e faz um HTTP POST para o ponto de extremidade. Um gancho da Web dispara uma execução de automação de um script do PowerShell.

![Fluxograma HTTP POST](media/image1.png)

## <a name="create-an-automation-account"></a>Criar uma conta de Automatização

Crie uma conta de automação por meio do [portal do Azure](https://portal.azure.com):

1.  Acesse portal.azure.com e faça logon em sua assinatura.

1.  Na caixa de pesquisa, insira **contas de automação**.

1.  Na seção **Serviços** da lista suspensa na barra de pesquisa, selecione **contas de automação**.

1.  Selecione **Adicionar**.

    ![Painel de contas de automação](media/image2.png)

1.  Insira as informações necessárias no painel **adicionar conta de automação** e, em seguida, selecione **criar**.

## <a name="create-a-runbook"></a>Criar um runbook

Depois que sua conta de automação estiver pronta, crie um runbook.

![Criar uma interface do usuário do runbook](media/image3.png)

1.  Selecione a conta de automação que você acabou de criar.

1.  Selecione **Runbooks** em **automação de processo**.

1.  Selecione **criar um runbook**.

1.  Nomeie seu runbook e selecione **PowerShell** como o tipo de runbook.

1.  Selecione o runbook que você criou e, em seguida, selecione o botão **Editar** .

1.  Insira o código a seguir (para fins de teste) e selecione o botão **publicar** . Essa ação retorna o resultado da solicitação POST recebida.

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

![Publicar interface do usuário do runbook](media/image4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Crie um webhook para disparar seu runbook recém-criado.

1.  Selecione **WebHooks** na seção de **recursos** do runbook que você acabou de publicar.

1.  Selecione **Adicionar webhook**.

    ![Botão Adicionar webhook](media/image5.png)

1.  Selecione **criar novo webhook**.

1. Nomeie o webhook, defina uma data de validade e copie a URL.

    > [!IMPORTANT] 
    > Você não pode exibir a URL depois de criá-la. Lembre-se de salvar uma cópia em um local seguro, onde você pode acessá-la no restante deste guia.

1. Selecione **parâmetros e configurações de execução** e, em seguida, selecione **OK**. Não insira nenhum parâmetro. Isso habilitará o botão **criar** .

1. Selecione **OK** e, em seguida, selecione **criar**.

    ![Criar nova interface do usuário do webhook](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma assinatura de grade de eventos por meio do [portal do Azure](https://portal.azure.com).

1.  Abra o [Portal do Azure](https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

1.  Vá para o cofre de chaves e selecione a guia **eventos** . Se você não conseguir vê-lo, verifique se está usando a [versão de visualização do portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Guia eventos no portal do Azure](media/image7.png)

1.  Selecione o botão **assinatura de evento** .

1.  Crie um nome descritivo para a assinatura.

1.  Escolha o **esquema da grade de eventos**.

1.  O **recurso de tópico** deve ser o cofre de chaves que você deseja monitorar para as alterações de status.

1.  Para **filtrar os tipos de evento**, deixe todas as opções selecionadas (**9 selecionadas**).

1.  No **Tipo de Ponto Final**, selecione **Webhook**.

1.  Escolha **selecionar um ponto de extremidade**. No novo painel de contexto, Cole a URL do webhook na etapa [criar um webhook](#create-a-webhook) no campo **ponto de extremidade do assinante** .

1.  Selecione **confirmar seleção** no painel de contexto.

1.  Selecione **Criar**.

    ![Criar assinatura de evento](media/image8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se sua assinatura da grade de eventos está configurada corretamente. Este teste pressupõe que você se inscreveu na notificação "nova versão secreta criada" na [assinatura criar uma grade de eventos](#create-an-event-grid-subscription)e que você tem as permissões necessárias para criar uma nova versão de um segredo em um cofre de chaves.

![Configuração de teste da assinatura da grade de eventos](media/image9.png)

![Painel criar-a-segredo](media/image10.png)

1.  Vá para o cofre de chaves na portal do Azure.

1.  Crie um novo segredo. Para fins de teste, defina a expiração como data para o dia seguinte.

1.  Na guia **eventos** do cofre de chaves, selecione a assinatura da grade de eventos que você criou.

1.  Em **métricas**, verifique se um evento foi capturado. Dois eventos são esperados: SecretNewVersion e SecretNearExpiry. Esses eventos validam que a grade de eventos capturou com êxito a alteração de status do segredo em seu cofre de chaves.

    ![Painel de métricas: verificar eventos capturados](media/image11.png)

1.  Acesse sua conta de automação.

1.  Selecione a guia **Runbooks** e, em seguida, selecione o runbook que você criou.

1.  Selecione a guia **WebHooks** e confirme se o carimbo de data/hora "último disparado" está dentro de 60 segundos de quando você criou o novo segredo. Esse resultado confirma que a grade de eventos fez uma POSTAgem no webhook com os detalhes do evento da alteração de status no cofre de chaves e que o webhook foi disparado.

    ![Guia WebHooks, carimbo de data/hora do último disparo](media/image12.png)

1. Retorne ao seu runbook e selecione a guia **visão geral** .

1. Examine a lista de **trabalhos recentes** . Você deve ver que um trabalho foi criado e que o status foi concluído. Isso confirma que o webhook disparou o runbook para iniciar a execução do script.

    ![Lista de trabalhos recentes de webhook](media/image13.png)

1. Selecione o trabalho recente e examine a solicitação POST enviada da grade de eventos para o webhook. Examine o JSON e certifique-se de que os parâmetros para o cofre de chaves e o tipo de evento estejam corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento que ocorreu no cofre de chaves (neste exemplo, Microsoft. keyvault. SecretNearExpiry), o teste foi bem-sucedido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="you-cant-create-an-event-subscription"></a>Não é possível criar uma assinatura de evento

Registre novamente a grade de eventos e o provedor do cofre de chaves em seus provedores de recursos de assinatura do Azure. Consulte [provedores e tipos de recursos do Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Passos seguintes

Parabéns! Se você seguiu corretamente todas essas etapas, agora você está pronto para responder programaticamente às alterações de status dos segredos armazenados no cofre de chaves.

Se você estiver usando um sistema baseado em sondagem para procurar alterações de status de segredos em seus cofres de chaves, agora você pode começar a usar esse recurso de notificação. Você também pode substituir o script de teste em seu runbook por código para renovar de forma programática seus segredos quando eles estiverem prestes a expirar.

Saiba mais:


- Visão geral: [monitoramento Key Vault com a grade de eventos do Azure (versão prévia)](event-grid-overview.md)
- Como: [receber email quando um segredo do cofre de chaves for alterado](event-grid-logicapps.md)
- [Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
- [Visão geral de Azure Key Vault](key-vault-overview.md)
- [Visão geral da grade de eventos do Azure](../event-grid/overview.md)
- [Descrição geral da Automatização do Azure](../automation/index.yml)
