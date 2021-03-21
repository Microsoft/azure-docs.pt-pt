---
title: E-mail quando o estado do Cofre chave do segredo muda
description: Guia para usar Apps lógicas para responder às mudanças de segredos do Cofre
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013994"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Use Aplicativos Lógicos para receber e-mail sobre alterações de estado dos segredos chave do cofre

Neste guia você aprenderá a responder aos eventos Azure Key Vault que são recebidos através da [Azure Event Grid](../../event-grid/index.yml) utilizando [apps Azure Logic.](../../logic-apps/index.yml) No final, terá uma aplicação lógica Azure configurada para enviar um e-mail de notificação sempre que um segredo é criado no Azure Key Vault.

Para uma visão geral da integração do Azure Key Vault / Azure Event Grid, consulte [o Cofre de Chaves de Monitorização com Azure Event Grid](event-grid-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de e-mail de qualquer fornecedor de e-mail que seja suportado por Azure Logic Apps (como o Office 365 Outlook). Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](/connectors)
- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre chave na sua assinatura Azure. Pode rapidamente criar um novo cofre de chaves seguindo os passos em [set e recuperar um segredo do Azure Key Vault usando o Azure CLI](../secrets/quick-create-cli.md).
- Rede de Eventos Registada como fornecedor de recursos, consulte os [registos dos fornecedores de recursos](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Criar uma App Lógica através da Grelha de Eventos

Em primeiro lugar, crie a Logic App com o manipulador de grelha de eventos e subscreva os eventos "SecretNewVersionCreated" do Azure Key Vault.

Para criar uma subscrição da Azure Event Grid, siga estes passos:

1. No portal Azure, vá ao cofre da chave, selecione **Eventos > Começar** e clique em **Aplicações Lógicas**

    
    ![Key Vault - página de eventos](../media/eventgrid-logicapps-kvsubs.png)

1. No **Logic Apps Designer** valida a ligação e clique em **Continuar** 
 
    ![Logic App Designer - conexão](../media/eventgrid-logicappdesigner1.png)

1. No ecrã **Quando ocorre um evento de recurso,** faça o seguinte:
    - Deixe **a Subscrição** e **o Nome do Recurso** como padrão.
    - Selecione **Microsoft.KeyVault.vaults** para o **Tipo de Recurso**.
    - Selecione **Microsoft.KeyVault.SecretNewVersionCreated** para **Item tipo de evento - 1**.

    ![Logic App Designer - manipulador de eventos](../media/eventgrid-logicappdesigner2.png)

1. **Selecione + Novo Passo** Isto abrirá uma janela para escolher uma ação.
1. Procure por **E-mail**. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza **o Office 365 Outlook**. Os passos para outros fornecedores de e-mail são semelhantes.
1. Selecione a ação **Enviar um e-mail (V2).**

   ![Logic App Designer - envie e-mail](../media/eventgrid-logicappdesigner3.png)

1. Construa o seu modelo de e-mail:
    - **Para:** Insira o endereço de e-mail para receber os e-mails de notificação. Neste tutorial, utilize uma conta de e-mail a que pode aceder para fazer o teste.
    - **Assunto** e **Corpo**: escreva o texto da sua mensagem de e-mail. Selecione as propriedades JSON da ferramenta de seletor para incluir conteúdo dinâmico com base nos dados de eventos. Pode recuperar os dados do evento utilizando `@{triggerBody()?['Data']}` .

    O seu modelo de e-mail pode parecer este exemplo.

    ![Logic App Designer - corpo de e-mail](../media/eventgrid-logicappdesigner4.png)

8. Clique **em Guardar como**.
9. Introduza um **nome** para nova aplicação lógica e clique em **Criar**.
    
    ![Logic App Designer - criar](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testar e verificar

1.  Vá ao cofre chave no portal Azure e selecione **Eventos > Subscrições de Eventos.**  Verifique se uma nova subscrição foi criada
    
    ![Logic App Designer - teste e verificar](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Vá ao cofre de chaves, selecione **Secrets,** e selecione **+ Gerar/Importar**. Crie um novo segredo para fins de teste nomeie a chave e mantenha os parâmetros restantes nas definições predefinidos.

    ![Cofre chave - Criar Segredo](../media/eventgrid-logicapps-kv-create-secret.png)

1. No **Ecrã Secreto,** forneça qualquer nome, qualquer valor e selecione **Criar**.

Quando o segredo for criado, um e-mail será recebido nos endereços configurados.

## <a name="next-steps"></a>Passos seguintes

- Visão geral: [Cofre de chaves de monitorização com grade de eventos Azure](event-grid-overview.md)
- Como: [Encaminhar notificações de cofre chave para a Azure Automation](event-grid-tutorial.md).
- [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Saiba mais sobre o [Azure Event Grid](../../event-grid/index.yml).
- Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](../../logic-apps/index.yml).