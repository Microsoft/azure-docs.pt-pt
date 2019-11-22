---
title: Migrar para o recurso do Azure para criação
titleSuffix: Azure Cognitive Services
description: Migre para uma chave de recurso de criação do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 44baac5eb4e8887594ba05498901ba664380005f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280762"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de criação de recursos do Azure

A autenticação de criação de Reconhecimento vocal (LUIS) mudou de uma conta de email para um recurso do Azure. Embora não seja necessário no momento, a alternância para um recurso do Azure será imposta no futuro.

## <a name="why-migrate"></a>Por que migrar?

O uso de um recurso do Azure para a criação permite que você, como o proprietário do recurso, controle o acesso à criação. Você pode criar e nomear recursos de criação para gerenciar diferentes grupos de autores. 

Por exemplo, se você tiver dois tipos de aplicativos LUIS que você está criando, com membros diferentes, poderá criar dois recursos de criação diferentes e atribuir colaboradores. O recurso de criação do Azure controla a autorização. 

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_. Após a migração, a função de _colaborador_ do Azure é usada para a mesma funcionalidade.

## <a name="what-is-migrating"></a>O que é migrar?

A migração inclui:

* Todos os usuários de LUIS, proprietários e colaboradores.
* **Todos os** aplicativos.
* Uma migração **unidirecional** .

O proprietário não pode escolher um subconjunto de aplicativos para migrar e o processo não é reversível. 

A migração não é: 

* Um processo que coleta colaboradores e move ou adiciona automaticamente o recurso de criação do Azure. Você, como o proprietário do aplicativo, precisa concluir esta etapa. Esta etapa requer permissões para o recurso apropriado.
* Um processo para criar e atribuir um recurso de tempo de execução de previsão. Se você precisar de um recurso de tempo de execução de previsão, isso será [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e não será alterado. 

## <a name="how-are-the-apps-migrating"></a>Como os aplicativos são migrados?

O [portal do Luis](https://www.luis.ai) fornece o processo de migração. 

Você será solicitado a migrar se:

* Você tem aplicativos no sistema de autenticação de email para criação.
* E você é o proprietário do aplicativo. 

Você pode atrasar o processo de migração cancelando a janela. Periodicamente, você será solicitado a migrar até migrar ou o prazo de migração for passado. Você pode iniciar o processo de migração no ícone de bloqueio da barra de navegação superior.

## <a name="migration-for-the-app-owner"></a>Migração para o proprietário do aplicativo

### <a name="before-you-migrate"></a>Antes de migrar

* **Opcionalmente**, faça backup dos aplicativos da lista de aplicativos do portal do Luis exportando cada aplicativo ou usando a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)de exportação.
* **Opcionalmente**, salve a lista de collaborator's de cada aplicativo. Essa lista de emails é fornecida como parte do processo de migração.
* **Necessário**, você precisa ter uma [assinatura do Azure](https://azure.microsoft.com/free/). Uma parte do processo de assinatura requer informações de cobrança. No entanto, você pode usar o tipo de preço gratuito (`F0`) ao usar o LUIS. 

A **criação de seu aplicativo Luis é gratuita**, indicada pela camada de `F0`. Saiba [mais sobre os tipos de preço](luis-boundaries.md#key-limits).

Se você não tiver uma assinatura do Azure, [Inscreva-](https://azure.microsoft.com/free/)se. 

### <a name="migration-steps"></a>Passos de migração

Siga [estas etapas de migração](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Após a migração 

Após o processo de migração, todos os seus aplicativos LUIS agora são atribuídos a um único recurso de criação de LUIS.

Você pode criar mais recursos de criação e atribuir na página **gerenciar > do Azure Resources** no _portal do Luis_. 

Você pode adicionar colaboradores ao recurso de criação do _portal do Azure_, na página de controle de **acesso (iam)** para esse recurso. Consulte [Adicionar acesso de colaborador](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) para obter mais informações.

|Portal|Objetivo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Crie recursos de previsão e criação.<br>* Atribuir colaboradores.|
|[LUIS](https://www.luis.ai)|* Migre para novos recursos de criação.<br>* Atribuir ou cancelar a atribuição de recursos de previsão e de criação a aplicativos da página **gerenciar > recursos do Azure** .| 

## <a name="migration-for-the-app-contributor"></a>Migração para o colaborador do aplicativo

Cada usuário do LUIS precisa migrar, incluindo colaboradores/contribuidores. 

### <a name="before-the-app-is-migrated"></a>Antes que o aplicativo seja migrado

Você pode optar por exportar um aplicativo no qual você é um colaborador e, em seguida, importar o aplicativo de volta para o LUIS. O processo de importação cria um novo aplicativo com uma nova ID do aplicativo, para o qual você é o proprietário.

### <a name="after-the-app-is-migrated"></a>Depois que o aplicativo for migrado

O proprietário do aplicativo precisa [Adicionar seu email ao recurso de criação do Azure como um colaborador](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource). 

Após o processo de migração, todos os aplicativos que você possui estão disponíveis na página **meus aplicativos** do portal do Luis.  

## <a name="troubleshooting"></a>Resolução de problemas

As chaves de criação do LUIS só ficam visíveis no portal do LUIS após a conclusão do processo de migração. Se você criar as chaves de criação, como com a CLI do LUIS, o usuário ainda precisará concluir o processo de migração. 

## <a name="next-steps"></a>Passos seguintes

* [Como migrar seu aplicativo para um recurso de criação](luis-migration-authoring-steps.md)