---
title: Armazenamento de dados-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS armazena dados criptografados num arquivo de dados do Azure correspondente para a região especificada pela chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639233"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento de dados e remoção nos serviços cognitivos de compreensão de idiomas (LUIS)
LUIS armazena dados criptografados num arquivo de dados do Azure correspondente para a região especificada pela chave. Estes dados são armazenados durante 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e eliminar a aplicação
Os utilizadores têm controlo total sobre [exportação](luis-how-to-start-new-app.md#export-app) e [eliminar](luis-how-to-start-new-app.md#delete-app) a aplicação. 

## <a name="utterances"></a>Expressões

Declarações pode ser armazenado em dois locais diferentes. 

* Durante **o processo de criação**, os declarações são criados e armazenados na intenção. Declarações em tentativas são necessárias para um aplicativo LUIS bem-sucedido. Depois que o aplicativo é publicado e recebe consultas no ponto de extremidade, o QueryString da solicitação `log=false`do ponto de extremidade, determina se o ponto de extremidade expressão está armazenado. Se o ponto de extremidade estiver armazenado, ele se tornará parte do declarações de aprendizado ativo encontrado na seção **Build** do portal, na seção **Review Endpoint declarações** . 
* Quando você **examina o ponto de extremidade declarações**e adiciona um expressão a uma intenção, o expressão não é mais armazenado como parte do ponto de extremidade declarações a ser revisado. Ele é adicionado às intenções do aplicativo. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Excluir declarações de exemplo de uma intenção

Eliminar usadas para treinamento de expressões de exemplo [LUIS](luis-reference-regions.md). Se eliminar uma expressão de exemplo da sua aplicação LUIS, ele é removido do serviço web LUIS e não está disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Excluir declarações em análise do aprendizado ativo

Pode eliminar expressões com na lista de expressões de utilizador que LUIS sugere no  **[página de expressões de ponto final de revisão](luis-how-to-review-endpoint-utterances.md)** . A eliminar expressões com desta lista evita que sejam sendo sugerida, mas não a eliminá-los a partir de registos.

Se não quiser o declarações de aprendizado ativo, você poderá [desabilitar o aprendizado ativo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Desabilitar o aprendizado ativo também desabilita o registro em log.

### <a name="disable-logging-utterances"></a>Desabilitar declarações de registro em log
[Desabilitar o aprendizado ativo desabilita](luis-how-to-review-endpoint-utterances.md#disable-active-learning) o registro em log.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Excluir uma conta
Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com os seus registos e as expressões de exemplo. Os dados são retidos durante 60 dias antes da conta e os dados são eliminados permanentemente.

A eliminar a conta está disponível a partir da **definições** página. Selecione o nome da sua conta na barra de navegação direita superior para ir para o **definições** página.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como uma assinatura expirada
Para efeitos de retenção de dados e eliminação, uma aplicação LUIS inativa pode _discrição da Microsoft_ seja tratado como uma assinatura expirada. Uma aplicação é considerada inativa se cumpre os seguintes critérios para os últimos 90 dias: 

* Teve **nenhuma** as chamadas feitas a ela.
* Não foi modificado.
* Não tem uma chave atual atribuído a ele.
* Não tenha tido um utilizador iniciar sessão na mesma.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a exportar e eliminar uma aplicação](luis-how-to-start-new-app.md)
