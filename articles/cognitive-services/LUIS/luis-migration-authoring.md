---
title: Migrar para o recurso Azure para autoria
titleSuffix: Azure Cognitive Services
description: Migrar para uma chave de recursos de autoria Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194514"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de autoria de recursos Azure

A compreensão linguística (LUIS) a autenticação passou de uma conta de e-mail para um recurso Azure. Embora não seja atualmente necessário, a mudança para um recurso Azure será aplicada no futuro.

## <a name="why-migrate"></a>Por que migrar?

A utilização de um recurso Azure para autoria permite-lhe, enquanto proprietário do recurso, controlar o acesso à autoria. Você pode criar e nomear recursos de autor para gerir diferentes grupos de autores.

Por exemplo, você é o proprietário de 2 aplicações LUIS, e você tem diferentes membros que são colaboradores em cada app. Pode criar dois recursos de autoria diferentes e atribuir cada app a cada recurso separado. Em seguida, atribua cada membro como contribuinte para o recurso de autor adequado, dependendo da aplicação em que colaboram. O recurso de autoria Azure controla a autorização.

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_ a nível da aplicação LUIS. Após a migração, o papel azure de _contribuinte_ é usado para a mesma funcionalidade, mas no nível de recursos Azure.

## <a name="what-is-migrating"></a>O que é migrar?

A migração inclui:

* Todos os utilizadores do LUIS, proprietários e colaboradores.
* **Todas as** aplicações.
* Uma migração **só de ida.**

O proprietário não pode escolher um subconjunto de apps para migrar e o processo não é reversível.

A migração não é:

* Um processo que recolhe colaboradores e se move automaticamente ou adiciona ao recurso de autoria Azure. Você, como proprietário da aplicação, precisa completar este passo. Este passo requer permissões ao recurso apropriado.
* Um processo para criar e atribuir um recurso de tempo de execução de previsão. Se precisar de um recurso de tempo de execução de previsão, este é [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e é inalterado.

## <a name="how-are-the-apps-migrating"></a>Como estão as aplicações a migrar?

O [portal LUIS](https://www.luis.ai) fornece o processo de migração.

Será-lhe pedido que migrasse se:

* Tem aplicações no sistema de autenticação de e-mail para autoria.
* E tu és o dono da aplicação.

Pode atrasar o processo de migração cancelando pela janela. É-lhe pedido periodicamente que emigra até migrar ou o prazo de migração seja ultrapassado. Pode iniciar o processo de migração a partir do ícone de bloqueio da barra de navegação superior.

## <a name="migration-for-the-app-owner"></a>Migração para o proprietário da app

### <a name="before-you-migrate"></a>Antes de migrar

* **Necessário,** precisa de ter uma [subscrição Azure.](https://azure.microsoft.com/free/) Uma parte do processo de subscrição requer informações de faturação. No entanto, pode`F0`utilizar o nível de preços Gratuitos quando utilizar o LUIS.
* **Opcionalmente,** faça backup das aplicações da lista de aplicações do portal LUIS exportando cada aplicação ou utilizar a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)de exportação.
* **Opcionalmente,** guarde a lista de colaboradores de cada aplicação. Esta lista de e-mails é fornecida como parte do processo de migração.


A autoria da **sua aplicação LUIS é gratuita**, indicada pelo `F0` nível. Saiba [mais sobre os níveis de preços.](luis-boundaries.md#key-limits)

Se não tiver uma assinatura Azure, [inscreva-se.](https://azure.microsoft.com/free/)

### <a name="migration-steps"></a>Passos de migração

Siga [estes passos de migração.](luis-migration-authoring-steps.md)

### <a name="after-you-migrate"></a>Depois de migrar

Após o processo de migração, todas as suas aplicações LUIS são agora atribuídas a um único recurso de autoria LUIS.

Pode criar mais recursos de autoria e atribuir a partir da página de **recursos Do Gere -> Azure** no _portal LUIS._

Pode adicionar colaboradores ao recurso de autoria do _portal Azure,_ na página de Controlo de **Acesso (IAM)** para esse recurso. Para mais informações, consulte [adicionar acesso ao contribuinte.](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)

|Portal|Objetivo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Criar recursos de previsão e autoria.<br>* Atribuir contribuintes.|
|[LUIS](https://www.luis.ai)|* Migrar para novos recursos de autoria.<br>* Atribuir ou designar a previsão e autorizar recursos para aplicações da página de **recursos Manage -> Azure.**|

## <a name="migration-for-the-app-contributor"></a>Migração para o contribuinte da aplicação

Todos os utilizadores do LUIS precisam de migrar, incluindo colaboradores/colaboradores. Um colaborador deve migrar para ter acesso à app.

> [!Note]
> Se o proprietário da app LUIS migrar e adicionar o colaborador como colaborador no recurso Azure, o colaborador continuará a não ter acesso à app a menos que também emigrasse.

### <a name="before-the-app-is-migrated"></a>Antes da aplicação ser migrada

Pode optar por exportar uma app em que é colaborador e, em seguida, importar a app de volta para O LUIS. O processo de importação cria uma nova app com um novo ID de aplicação, para o qual é o proprietário.

### <a name="after-the-app-is-migrated"></a>Depois da aplicação ser migrada

O proprietário da aplicação precisa [adicionar o seu e-mail ao recurso de autor do Azure como colaborador.](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

Após o processo de migração, quaisquer aplicações que possua estão disponíveis na página das **minhas apps** do portal LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Resolução de problemas no processo de migração para a autoria do LUIS

* As chaves de autoria luis só são visíveis no portal LUIS após o processo de migração estar concluído. Se criar as chaves de autor, como é o caso do CLI LUIS, o utilizador ainda precisa de concluir o processo de migração no portal LUIS.
* Se um utilizador migrado adicionar um utilizador não migrado como contribuinte no seu recurso azul, o utilizador não migrado não terá acesso às aplicações a menos que migrasse.
* Se um utilizador não migrado não for proprietário de nenhuma aplicação, mas for colaborador de outras aplicações que são propriedade de outras e os proprietários tenham sido submetidos ao processo de migração, este utilizador terá de migrar para ter acesso às apps.
* Se um utilizador não migrado adicionar outro utilizador migrado como colaborador à sua aplicação, ocorrerá um erro, uma vez que não será capaz de adicionar um utilizador migrado como colaborador a uma aplicação. O utilizador não migrado terá então de passar pelo processo de migração e criar um recurso azul e adicionar o utilizador migrado como contribuinte a esse recurso.

Recebe um erro durante o processo de migração se:
* A sua subscrição não o autoriza a criar recursos de Serviços Cognitivos
* A sua migração impacta negativamente qualquer tempo de execução de aplicações. Ao migrar, quaisquer colaboradores são removidos das suas apps e você é removido como um colaborador de outras apps. Este processo significa que as chaves que atribuiu também são removidas. A migração fica bloqueada se tiver chaves atribuídas noutras aplicações. Retire a chave que atribuiu com segurança antes de migrar. Se sabe que a chave que atribuiu não é usada no tempo de funcionação, então precisa removê-la para poder progredir na migração.

Aceda à lista de recursos Azure da sua aplicação utilizando o seguinte formato URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Passos seguintes

* [Como migrar a sua app para um recurso de autoria](luis-migration-authoring-steps.md)
