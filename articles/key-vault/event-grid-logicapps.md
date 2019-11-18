---
title: Email quando Key Vault status das alterações de segredo
description: Guia para usar aplicativos lógicos para responder a Key Vault alterações de segredos
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ccc4aaed8e5827fbc06b252c8c88b814d9a31fb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135020"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Usar aplicativos lógicos para receber email sobre alterações de status de segredos do cofre de chaves

Neste guia, você aprenderá a responder a Azure Key Vault eventos que são recebidos por meio da [grade de eventos do Azure](../event-grid/index.yml) usando o [aplicativo lógico do Azure](../logic-apps/index.yml). No final, você terá um aplicativo lógico do Azure configurado para enviar um email de notificação toda vez que um segredo for criado no Azure Key Vault.

Para obter uma visão geral da integração da grade de eventos do Azure Key Vault/Azure, consulte [monitorando Key Vault com a grade de eventos do Azure (versão prévia)](event-grid-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de email de qualquer provedor de email com suporte dos aplicativos lógicos do Azure (como o Office 365 Outlook). Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](/connectors)
- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves em sua assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [definir e recuperar um segredo de Azure Key Vault usando CLI do Azure](quick-create-cli.md).

## <a name="create-a-logic-app-via-event-grid"></a>Criar um aplicativo lógico por meio da grade de eventos

Primeiro, crie um aplicativo lógico com o manipulador de grade de eventos e assine Azure Key Vault eventos "SecretNewVersionCreated".

Para criar uma assinatura da grade de eventos do Azure, siga estas etapas:

1. Abra o portal do Azure usando o seguinte link: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true 
1. Na portal do Azure, vá para o cofre de chaves, selecione **eventos > introdução** e clique em **aplicativos lógicos**

    
    ![Página de Key Vault eventos](./media/eventgrid-logicapps-kvsubs.png)

1. No **Designer de aplicativos lógicos** , valide a conexão e clique em **continuar** 
 
    ![Designer de aplicativo lógico-conexão](./media/eventgrid-logicappdesigner1.png)

1. Na tela **quando um evento de recurso ocorre** , faça o seguinte:
    - Deixe o nome da **assinatura** e do **recurso** como padrão.
    - Selecione **Microsoft. keyvault. Vaults** para o **tipo de recurso**.
    - Selecione **Microsoft. keyvault. SecretNewVersionCreated para o** **tipo de evento item-1**.

    ![Designer de aplicativo lógico – manipulador de eventos](./media/eventgrid-logicappdesigner2.png)

1. Selecione **+ nova etapa** isso abrirá uma janela para escolher uma ação.
1. Procure por **E-mail**. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Outlook do Office 365**. Os passos para outros fornecedores de e-mail são semelhantes.
1. Selecione a ação **enviar um email (v2)** .

   ![Designer de aplicativo lógico-adicionar email](./media/eventgrid-logicappdesigner3.png)

1. Crie seu modelo de email:
    - **Para:** Insira o endereço de email para receber os emails de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste.
    - **Assunto** e **Corpo**: escreva o texto da sua mensagem de e-mail. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos. Você pode recuperar os dados do evento usando `@{triggerBody()?['Data']}`.

    Seu modelo de email pode ser semelhante a este exemplo.

    ![Designer de aplicativo lógico-adicionar email](./media/eventgrid-logicappdesigner4.png)

8. Clique em **salvar como**.
9. Insira um **nome** para o novo aplicativo lógico e clique em **criar**.
    
    ![Designer de aplicativo lógico-adicionar email](./media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testar e verificar

1.  Vá para o cofre de chaves na portal do Azure e selecione **eventos > assinaturas de evento**.  Verificar se uma nova assinatura foi criada
    
    ![Designer de aplicativo lógico-adicionar email](./media/eventgrid-logicapps-kvnewsubs.png)

1.  Vá para o cofre de chaves, selecione **segredos**e selecione **+ gerar/importar**. Crie um novo segredo para fins de teste nomeie a chave e mantenha os parâmetros restantes em suas configurações padrão.

    ![Key Vault-criar segredo](./media/eventgrid-logicapps-kv-create-secret.png)

1. Na tela **criar um segredo** , forneça qualquer nome, qualquer valor e selecione **criar**.

Quando o segredo for criado, um email será recebido nos endereços configurados.

## <a name="next-steps"></a>Passos seguintes

- Visão geral: [monitoramento Key Vault com a grade de eventos do Azure (versão prévia)](event-grid-overview.md)
- Como: [rotear notificações do cofre de chaves para a automação do Azure](event-grid-tutorial.md).
- [Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
- Saiba mais sobre o [Azure Event Grid](../event-grid/index.yml).
- Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](../logic-apps/index.yml).
