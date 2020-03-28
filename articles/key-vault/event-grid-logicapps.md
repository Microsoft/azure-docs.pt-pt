---
title: E-mail quando o estado do cofre chave das mudanças secretas
description: Guia para usar apps lógicas para responder às mudanças de segredos do Cofre chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 8d43a254ad79a13320fa2c5a19cf4bc8d6e2c968
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78199755"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Use aplicativos lógicos para receber e-mail sobre mudanças de estado dos segredos do cofre

Neste guia você aprenderá a responder a eventos azure key vault que são recebidos via [Azure Event Grid](../event-grid/index.yml) usando [Aplicativos Azure Logic](../logic-apps/index.yml). No final, terá uma aplicação lógica Azure configurada para enviar um e-mail de notificação sempre que um segredo é criado no Cofre chave azure.

Para uma visão geral da integração da Chave Abóbada azul / Azure Event Grid, consulte [o Cofre de Chaves de Monitorização com a Grelha de Eventos Azure (pré-visualização)](event-grid-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de e-mail de qualquer fornecedor de e-mail que seja suportado por Aplicações Lógicas Azure (como o Office 365 Outlook). Esta conta de e-mail é utilizada para enviar notificações de eventos. Para obter uma lista completa dos conectores suportados do Logic Apps, consulte a [Descrição geral de conectores](/connectors)
- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre chave na sua assinatura Azure. Você pode rapidamente criar um novo cofre chave seguindo os passos em [set e recuperar um segredo de Azure Key Vault usando Azure CLI](quick-create-cli.md).

## <a name="create-a-logic-app-via-event-grid"></a>Criar uma Aplicação Lógica via Grelha de Eventos

Primeiro, crie a Logic App com o manipulador de grelhas de eventos e subscreva os eventos "SecretNewVersionCreated" do Cofre de Chaves azure.

Para criar uma subscrição da Rede de Eventos Azure, siga estes passos:

1. No portal Azure, vá ao seu cofre chave, selecione **Eventos > Começar** e clique em **Aplicações Lógicas**

    
    ![Key Vault - página de eventos](./media/eventgrid-logicapps-kvsubs.png)

1. Em **Aplicações Lógica Designer** valida a ligação e clique Em **Continuar** 
 
    ![Logic App Designer - conexão](./media/eventgrid-logicappdesigner1.png)

1. No **ecrã Quando ocorre um evento** de recurso, faça o seguinte:
    - Deixe a **Subscrição** e **o Nome** do Recurso como padrão.
    - Selecione **Microsoft.KeyVault.vaults** para o **Tipo de Recursos**.
    - Selecione **Microsoft.KeyVault.SecretNewVersionCriado** para **item do tipo de evento - 1**.

    ![Logic App Designer - manipulador de eventos](./media/eventgrid-logicappdesigner2.png)

1. Selecione **+ Novo Passo** Isto abrirá uma janela para escolher uma ação.
1. Procure por **E-mail**. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Este tutorial utiliza o **Office 365 Outlook.** Os passos para outros fornecedores de e-mail são semelhantes.
1. Selecione a ação **Enviar por email (V2).**

   ![Logic App Designer - adicione e-mail](./media/eventgrid-logicappdesigner3.png)

1. Construa o seu modelo de e-mail:
    - **Para:** Insira o endereço de e-mail para receber os e-mails de notificação. Neste tutorial, utilize uma conta de e-mail a que tenha acesso para fins de teste.
    - **Assunto** e **Corpo**: escreva o texto da sua mensagem de e-mail. Selecione as propriedades de JSON na ferramenta do seletor para incluir conteúdo dinâmico baseado em dados de eventos. Pode recuperar os dados do `@{triggerBody()?['Data']}`evento utilizando .

    O seu modelo de e-mail pode parecer este exemplo.

    ![Logic App Designer - adicione e-mail](./media/eventgrid-logicappdesigner4.png)

8. Clique em **Guardar como**.
9. Introduza um **nome** para nova aplicação lógica e clique em **Criar**.
    
    ![Logic App Designer - adicione e-mail](./media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testar e verificar

1.  Vá ao seu cofre chave no portal Azure e selecione **Eventos > Assinaturas de Eventos**.  Verifique se uma nova subscrição foi criada
    
    ![Logic App Designer - adicione e-mail](./media/eventgrid-logicapps-kvnewsubs.png)

1.  Vá ao seu cofre chave, selecione **Segredos,** e selecione **+ Generate/Import**. Crie um novo segredo para fins de teste, nomeie a chave e mantenha os parâmetros restantes nas suas definições predefinidas.

    ![Cofre chave - Criar Segredo](./media/eventgrid-logicapps-kv-create-secret.png)

1. No ecrã **Criar um** ecrã secreto forneça qualquer nome, qualquer valor e selecione **Criar**.

Quando o segredo for criado, um e-mail será recebido nos endereços configurados.

## <a name="next-steps"></a>Passos seguintes

- Visão geral: Cofre de chaves de monitorização com grelha de [eventos Azure (pré-visualização)](event-grid-overview.md)
- Como: [Encaminha as notificações do cofre chave para a Automação Azure](event-grid-tutorial.md).
- [Esquema de evento sinuoso do evento Azure Event Grid para cofre de chaves Azure (pré-visualização)](../event-grid/event-schema-key-vault.md)
- Saiba mais sobre o [Azure Event Grid](../event-grid/index.yml).
- Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](../logic-apps/index.yml).
