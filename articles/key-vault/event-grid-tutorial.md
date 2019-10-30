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
ms.openlocfilehash: 56490ede89a5859ef73d9110b46ea55fd9b96d54
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033556"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Como: receber e responder a notificações do Key Vault com a grade de eventos do Azure (versão prévia)

Key Vault integração com a grade de eventos do Azure, atualmente em visualização, permite que os usuários sejam notificados quando o status de um segredo armazenado no cofre de chaves for alterado. Para obter uma visão geral do recurso, consulte [monitoramento Key Vault com a grade de eventos do Azure](event-grid-overview.md).

Este guia mostrará como receber notificações de Key Vault por meio da grade de eventos do Azure e como responder a alterações de status com a automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves em sua assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [definir e recuperar um segredo de Azure Key Vault usando CLI do Azure](quick-create-cli.md)

## <a name="concepts"></a>Conceitos

O Azure Event Grid é um serviço de eventos para a cloud. Neste guia, você assinará eventos para o cofre de chaves e eventos de rota para a automação do Azure. Quando um dos segredos no cofre de chaves está prestes a expirar, a grade de eventos é notificada sobre a alteração de status e faz um HTTP POST para o ponto de extremidade. Um gancho da Web dispara uma execução de automação do Azure do script do PowerShell.

![imagem](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Crie uma conta de automação do Azure por meio do [portal do Azure](https://portal.azure.com).

1.  Acesse portal.azure.com e faça logon em sua assinatura.

1.  Na caixa de pesquisa, digite "contas de automação".

1.  Na seção "serviços" do menu suspenso da barra de pesquisa, selecione "contas de automação".

1.  Clique em Adicionar.

    ![](media/image2.png)

1.  Preencha as informações necessárias na folha "adicionar conta de automação" e selecione "criar".

## <a name="create-a-runbook"></a>Criar um runbook

Depois que sua conta de automação do Azure estiver pronta, crie um runbook.

![](media/image3.png)

1.  Selecione a conta de automação que você acabou de criar.

1.  Selecione "Runbooks" na seção automação de processo.

1.  Clique em "criar um runbook".

1.  Nomeie seu runbook e selecione "PowerShell" como o tipo de runbook.

1.  Clique no runbook que você criou e selecione o botão "Editar".

1.  Insira o código a seguir (para fins de teste) e clique no botão "publicar". Isso produzirá o resultado da solicitação POST recebida.

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

![](media/image4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Agora, crie um webhook para disparar seu runbook recém-criado.

1.  Selecione "WebHooks" na seção de recursos do runbook que você acabou de publicar.

1.  Clique em "Adicionar webhook".

    ![](media/image5.png)

1.  Selecione "criar novo webhook".

1. Nomeie o webhook, defina uma data de validade e **Copie a URL**.

    > [!IMPORTANT] 
    > Você não pode exibir a URL depois de criá-la. Lembre-se de salvar uma cópia de um local seguro onde você pode acessá-la no restante deste guia.

1. Clique em "parâmetros e configurações de execução" e selecione "OK". Não insira nenhum parâmetro. Isso habilitará o botão "criar".

1. Selecione "OK" e selecione "criar".

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma assinatura de grade de eventos por meio do [portal do Azure](https://portal.azure.com).

1.  Abra o portal do Azure usando o seguinte link: https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Vá para o cofre de chaves e selecione a guia "eventos". Se você não conseguir ver a guia eventos, certifique-se de estar usando a [versão de visualização do portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Clique no botão "+ assinatura de evento".

1.  Crie um nome descritivo para a assinatura.

1.  Escolha "esquema de grade de eventos".

1.  O "recurso de tópico" deve ser o cofre de chaves que você deseja monitorar para as alterações de status.

1.  Para "filtrar para tipos de evento", deixe todas as marcadas ("9 selecionadas").

1.  Para "tipo de ponto de extremidade", selecione "webhook".

1.  Selecione "selecionar um ponto de extremidade". No novo painel de contexto, Cole a URL do webhook da etapa [criar uma webhook](#create-a-webhook) no campo "ponto de extremidade do Assinante".

1.  Selecione "confirmar seleção" no painel de contexto.

1.  Selecione "criar".

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se a sua assinatura da grade de eventos é definida como propriedade.  Esse teste pressupõe que você se inscreveu na notificação "nova versão secreta criada" na [assinatura criar uma grade de eventos](#create-an-event-grid-subscription)e que tem os privilégios necessários para criar uma nova versão de um segredo em um cofre de chaves.

![](media/image9.png)

![](media/image10.png)

1.  Vá para o cofre de chaves na portal do Azure

1.  Crie um novo segredo. Para fins de teste, defina vencimento como data para o dia seguinte.

1.  Navegue até a guia eventos em seu cofre de chaves.

1.  Selecione a assinatura da grade de eventos que você criou.

1.  Em métricas, consulte se um evento foi capturado. Dois eventos são esperados: SecretNewVersion e SecretNearExpiry. Isso valida que a grade de eventos capturou com êxito a alteração de status do segredo em seu cofre de chaves.

    ![](media/image11.png)

1.  Vá para sua conta de automação do Azure.

1.  Selecione a guia "Runbooks" e selecione o runbook que você criou.

1.  Selecione a guia "WebHooks" e confirme se o carimbo de data/hora "último disparado" está dentro de 60 segundos de quando você criou o novo segredo.  Isso confirma que a grade de eventos fez uma POSTAgem no webhook com os detalhes do evento da alteração de status no cofre de chaves e o webhook foi disparado.

    ![](media/image12.png)

1. Retorne ao seu runbook e selecione a guia "visão geral".

1. Examine a lista de trabalhos recentes. Você deve ver que um trabalho foi criado e que o status foi concluído.  Isso confirma que o webhook disparou o runbook para iniciar a execução do script.

    ![](media/image13.png)

1. Selecione o trabalho recente e examine a solicitação POST enviada da grade de eventos para o webhook. Examine o JSON e certifique-se de que os parâmetros para o cofre de chaves e o tipo de evento estejam corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento que ocorreu no cofre de chaves (neste exemplo, Microsoft. keyvault. SecretNearExpiry), o teste foi bem-sucedido.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-create-event-subscription"></a>Não é possível criar a assinatura de evento

Registre novamente a grade de eventos e o provedor de Key Vault em seus provedores de recursos de assinatura do Azure. Consulte [provedores e tipos de recursos do Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Passos seguintes

Parabéns! Se você seguiu todas as etapas acima, agora você está pronto para responder programaticamente às alterações de status dos segredos armazenados no cofre de chaves.

Se você estiver usando um sistema baseado em sondagem para procurar alterações de status de segredos em seus cofres de chaves, migre para o usando esse recurso de notificação. Você também pode substituir o script de teste em seu runbook por código para renovar programaticamente seus segredos quando eles estiverem prestes a expirar.

Mais informações:

- [Visão geral de Azure Key Vault](key-vault-overview.md)
- [Visão geral da grade de eventos do Azure](../event-grid/overview.md)
- [Key Vault de monitoramento com a grade de eventos do Azure (versão prévia)](event-grid-overview.md)
- [Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
- [Descrição geral da Automatização do Azure](../automation/index.yml)