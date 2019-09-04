---
title: Migrar para um recurso de criação do Azure
titleSuffix: Azure Cognitive Services
description: Migre para um recurso de criação do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259686"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Etapas para migrar para o recurso de criação do Azure

No portal de Reconhecimento vocal (LUIS), migre todos os aplicativos que você possui para usar o recurso de criação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Opcionalmente**, faça backup dos aplicativos da lista de aplicativos do portal do Luis exportando cada aplicativo ou usando a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)de exportação.
* **Opcionalmente**, salve a lista de collaborator's de cada aplicativo. Essa lista de emails é fornecida como parte do processo de migração.
* **Necessário**, você precisa ter uma [assinatura do Azure](https://azure.microsoft.com/free/). Uma parte do processo de assinatura requer informações de cobrança. No entanto, você pode usar tipos de preço gratuitos (F0) ao usar o LUIS. Você pode, eventualmente, achar que precisa de uma camada paga, à medida que seu uso aumenta. 

Se você não tiver uma assinatura do Azure, [Inscreva-](https://azure.microsoft.com/free/)se. 

## <a name="access-the-migration-process"></a>Acessar o processo de migração

Em uma base semanal, você será solicitado a migrar seus aplicativos. Você pode cancelar esta janela sem migrar. Se você quiser migrar antes do próximo período agendado, poderá iniciar o processo de migração no ícone de **cadeado** na barra de ferramentas superior do portal do Luis. 

## <a name="app-owner-begins-the-migration-process"></a>O proprietário do aplicativo inicia o processo de migração

O processo de migração estará disponível se você for o proprietário de qualquer aplicativo LUIS. 

1. 1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.
1. A janela pop-up de migração permite que você continue a migração ou migre mais tarde. Selecione **migrar agora**. Se você optar por migrar mais tarde, terá 9 meses para migrar para a nova chave de criação no Azure.

    ![Primeira janela pop-up no processo de migração, selecione migrar agora.](./media/migrate-authoring-key/migrate-now.png)

1. Se qualquer um de seus aplicativos tiver colaboradores, você será solicitado a **enviar um email** informando sobre a migração. Este é um passo opcional. O aplicativo de email padrão é aberto com um email levemente formatado. 

    ![Enviar email de colaboradores sobre a migração](./media/migrate-authoring-key/send-collaborators-email.png)

1. Escolha criar um recurso de criação de LUIS selecionando **iniciar criando um recurso de criação para migrar seus aplicativos para o**. 

    ![Criar recurso de criação](./media/migrate-authoring-key/choose-authoring-resource.png)

1. Na próxima janela, insira as informações da chave de recurso. Depois de inserir as informações, selecione **criar recurso**. 

    ![Criar recurso de criação](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de criação**, forneça as seguintes informações: 

    * **Nome do recurso** -um nome personalizado que você escolhe, usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão.
    * **Locatário** -o locatário ao qual sua assinatura do Azure está associada. 
    * **Nome da assinatura** -a assinatura que será cobrada para o recurso.
    * **Grupo de recursos** -um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem que você agrupe recursos do Azure para acesso e gerenciamento. 
    * **Local** -a escolha de local é baseada na seleção do **grupo de recursos** .
    * **Tipo de preço** – o tipo de preço determina a transação máxima por segundo e mês. 

1. Quando o recurso de criação é criado, a mensagem de êxito é mostrada. Selecione **fechar** para fechar a janela pop-up.

    ![O recurso de criação foi criado com êxito.](./media/migrate-authoring-key/migration-success.png)

    A lista **meus aplicativos** mostra os aplicativos migrados para o novo recurso de criação. 

    Você não precisa saber a chave do recurso de criação para continuar editando seus aplicativos no portal do LUIS. Se você planeja editar seus aplicativos programaticamente, precisará dos valores de chave de criação. Esses valores são exibidos na página **gerenciar > recursos do Azure** no portal do Luis e também estão disponíveis no portal do Azure na página **chaves** do recurso.  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Após o processo de migração, adicione colaboradores ao recurso de criação

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md). 

## <a name="next-steps"></a>Passos Seguintes


* Examinar [conceitos](luis-concept-keys.md) sobre criação e chaves de tempo de execução
* Examine [como atribuir chaves](luis-how-to-azure-subscription.md) e adicionar [colaboradores](luis-how-to-collaborate.md)
