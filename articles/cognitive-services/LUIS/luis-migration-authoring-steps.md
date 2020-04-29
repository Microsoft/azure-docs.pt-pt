---
title: Migrar para um recurso de autoria Azure
titleSuffix: Azure Cognitive Services
description: Migrar para um recurso de autoria Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78194648"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Passos para migrar para o recurso de autoria azure

A partir do portal Language Understanding (LUIS), emigra todas as aplicações que possui para utilizar o recurso de autoria Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Opcionalmente,** faça backup das aplicações da lista de aplicações do portal LUIS exportando cada aplicação ou utilizar a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)de exportação.
* **Opcionalmente,** guarde a lista de colaboradores de cada aplicação. Todos os colaboradores podem ser enviados um e-mail como parte do processo de migração.
* **Necessário,** precisa de ter uma [subscrição Azure.](https://azure.microsoft.com/free/) Uma parte do processo de subscrição requer informações de faturação. No entanto, pode utilizar níveis de preços gratuitos (F0) quando utilizar o LUIS. Pode eventualmente descobrir que precisa de um nível pago, à medida que o seu uso aumenta.

Se não tiver uma assinatura Azure, [inscreva-se.](https://azure.microsoft.com/free/)

## <a name="access-the-migration-process"></a>Aceder ao processo de migração

Semanalmente, é-lhe solicitado que emigraasse as suas apps. Pode cancelar esta janela sem migrar. Se quiser migrar antes do próximo período programado, pode iniciar o processo de migração a partir do ícone **Azure** na barra de ferramentas superior esbarrada do portal LUIS.

> [!div class="mx-imgBorder"]
> ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Dono de app inicia o processo de migração

O processo de migração está disponível se for proprietário de quaisquer aplicações LUIS.

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.
1. A janela pop-up da migração permite-lhe continuar a migração ou migrar mais tarde. **Selecione Migrar agora**. Se optar por migrar mais tarde, tem 9 meses para migrar para a nova chave de autor em Azure.

    ![Primeira janela pop-up no processo de migração, selecione Migrar agora.](./media/migrate-authoring-key/migrate-now.png)

1. Opcionalmente, se alguma das suas aplicações tiver colaboradores, é-lhe solicitado que **lhes envie um e-mail** informando-os sobre a migração. Este é um passo opcional.

    Depois de ter migrado a sua conta para o Azure, as suas aplicações deixarão de estar disponíveis para colaboradores.

    Para cada colaborador e aplicação, a aplicação de e-mail padrão abre com um e-mail levemente formatado. Pode editar o e-mail antes de enviá-lo.

    O modelo de e-mail inclui o id exato da aplicação e o nome da aplicação.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Opte por criar um recurso de autoria LUIS selecionando para utilizar um recurso de autoria existente ou para criar um novo recurso de autoria.

    > [!div class="mx-imgBorder"]
    > ![Criar recurso de autoria](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. Na janela seguinte, introduza a informação da chave de recursos. Depois de introduzir a informação, selecione **Criar recurso**. Pode ter 10 recursos de autoria gratuitos por região, por subscrição.

    ![Criar recurso de autoria](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de autoria,** forneça as seguintes informações:

    * **Nome** de recurso - um nome personalizado que escolher, usado como parte do URL para as suas consultas de autor e ponto final de previsão.
    * **Inquilino** - o inquilino com quem a sua assinatura Azure está associada.
    * Nome de **subscrição** - a subscrição que será faturada para o recurso.
    * **Grupo de recursos** - um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar os recursos do Azure para acesso e gestão.
    * **Localização** - a escolha da localização baseia-se na seleção do **grupo de recursos.**
    * **Nível** de preços - o nível de preços determina a transação máxima por segundo e mês.

1. Valide o seu recurso de autoria e **emigra agora.**

    ![Criar recurso de autoria](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Quando o recurso de autor é criado, a mensagem de sucesso é mostrada. Selecione **Perto** para fechar a janela pop-up.

    ![O seu recurso de autoria foi criado com sucesso.](./media/migrate-authoring-key/migration-success.png)

    A lista **de aplicações My** mostra que as aplicações migraram para o novo recurso de autoria.

    Não é necessário conhecer a chave do recurso de autor para continuar a editar as suas apps no portal LUIS. Se planeia editar as suas apps programáticamente, precisa dos valores-chave da autoria. Estes valores são apresentados na página de **recursos Do Manage -> Azure** no portal LUIS e estão também disponíveis no portal Azure na página **Chaves** do recurso.

1. Antes de aceder às suas apps, selecione a subscrição e o recurso de autor da LUIS para ver as aplicações que pode ser autor.

    ![Selecione subscrição e recurso de autor LUIS para ver as aplicações que pode ser autora.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Colaborador da App inicia o processo de migração

Siga os mesmos passos que o proprietário da aplicação para migração. O processo cria um novo `LUIS.Authoring`recurso de autoria do tipo.

É necessário migrar a sua conta para ser adicionada como contribuinte para aplicações migratórias detidas por outras pessoas.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Após o processo de migração, adicione os contribuintes ao seu recurso de autoria

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores.](luis-how-to-collaborate.md)

## <a name="troubleshooting-errors-with-the-migration-process"></a>Erros de resolução de problemas com o processo de migração

Se receber `MissingSubscriptionRegistration` um erro no portal LUIS com uma barra de notificação vermelha durante o processo de migração, crie um recurso do Serviço Cognitivo no [portal Azure](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Saiba mais sobre [as causas deste erro.](../../azure-resource-manager/templates/error-register-resource-provider.md#cause)

## <a name="next-steps"></a>Passos seguintes


* Rever [conceitos](luis-concept-keys.md) sobre autoria e chaves de tempo de execução
* Reveja [como atribuir chaves](luis-how-to-azure-subscription.md) e adicionar [colaboradores](luis-how-to-collaborate.md)
