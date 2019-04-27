---
title: Criar uma nova aplicação
titleSuffix: Language Understanding - Azure Cognitive Services
description: Criar e gerir as suas aplicações na página de Web de compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: daffbe3f3158bb232f7db7ac90d766661e937643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197509"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Criar uma nova aplicação LUIS no portal do LUIS
Existem duas formas para criar uma aplicação do LUIS. Pode criar uma aplicação LUIS no [LUIS](https://www.luis.ai) portal, ou por meio do LUIS criação [APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Com o portal de LUIS
Pode criar uma nova aplicação no portal do LUIS de várias formas:

* Começar com uma aplicação vazia e crie objetivos, expressões e entidades.
* Comece com uma aplicação vazia e adicione um [domínio pré-criado](luis-how-to-use-prebuilt-domains.md).
* Importe uma aplicação do LUIS de um ficheiro JSON que já contém intenções, expressões e entidades.

## <a name="using-the-authoring-apis"></a>Com as APIs de criação
Pode criar uma nova aplicação com as APIs de criação de duas formas:

* [Iniciar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) com uma aplicação vazia e crie objetivos, expressões e entidades.
* [Iniciar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) com um domínio pré-criado.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Criar nova aplicação LUIS

1. No **as minhas aplicações** página, selecione **criar nova aplicação**.

    ![Lista de aplicações LUIS](./media/luis-create-new-app/apps-list.png)


2. Na caixa de diálogo, nome de seu aplicativo "TravelAgent".

    ![Criar nova caixa de diálogo da aplicação](./media/luis-create-new-app/create-app.png)

3. Escolha sua cultura de aplicação (para aplicação de TravelAgent, selecione inglês) e, em seguida, selecione **feito**. 

    > [!NOTE]
    > A cultura não pode ser alterada assim que a aplicação for criada. 

## <a name="import-an-app-from-file"></a>Importar uma aplicação a partir do ficheiro

1. No **as minhas aplicações** página, selecione **importar a nova aplicação**.
1. Na caixa de diálogo pop-up, selecione um ficheiro JSON de aplicação válido e, em seguida, selecione **feito**.

### <a name="import-errors"></a>Importar erros

Erros possíveis são: 

* Já existe uma aplicação com esse nome. Importe novamente o aplicativo e defina a **nome opcional** para um novo nome. 

## <a name="export-app-for-backup"></a>Exportar a aplicação para cópia de segurança

1. No **as minhas aplicações** página, selecione **exportar**.
1. Selecione **exportar como JSON**. O browser transfere a versão da aplicação do Active Directory.
1. Adicione esse arquivo ao sistema de cópia de segurança para arquivar o modelo.

## <a name="export-app-for-containers"></a>Exportar a aplicação para contentores

1. No **as minhas aplicações** página, selecione **exportar**.
1. Selecione **exportar como contentor** , em seguida, selecione a ranhura publicada (produção ou estágio) que pretende exportar.
1. Utilize este ficheiro com o seu [contentor LUIS](luis-container-howto.md). 

    Se estiver interessado em exportar um preparado, mas não ainda modelo publicado para utilizar com o contentor de LUIS, vá para o **versões** página e exportar a partir daí. 

## <a name="delete-app"></a>Eliminar aplicação

1. No **as minhas aplicações** , selecione as reticências (...) no final da linha da aplicação.
1. Selecione **eliminar** no menu.
1. Selecione **Ok** na janela de confirmação.

## <a name="next-steps"></a>Passos Seguintes

Sua primeira tarefa na aplicação é [adicionar intenções](luis-how-to-add-intents.md).
