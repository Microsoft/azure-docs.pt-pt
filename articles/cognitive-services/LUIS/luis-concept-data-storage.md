---
title: Armazenamento de dados
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS armazena dados criptografados num arquivo de dados do Azure correspondente para a região especificada pela chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812950"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento de dados e remoção nos serviços cognitivos de compreensão de idiomas (LUIS)
LUIS armazena dados criptografados num arquivo de dados do Azure correspondente para a região especificada pela chave. Estes dados são armazenados durante 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e eliminar a aplicação
Os utilizadores têm controlo total sobre [exportação](luis-how-to-start-new-app.md#export-app) e [eliminar](luis-how-to-start-new-app.md#delete-app) a aplicação. 

## <a name="utterances"></a>Expressões

Expressões com podem ser armazenados em dois locais diferentes. 

* Durante **o processo de criação**, expressões são criadas e armazenadas na intenção. Expressões com em objetivos são necessários para uma aplicação LUIS com êxito. Depois da aplicação for publicada e recebe as consultas no ponto final, cadeia de consulta do pedido de ponto final, `log=false`, determina se a expressão de ponto final é armazenado. Se o ponto final é armazenado, torna-se parte das expressões de aprendizagem ativa encontrado no **crie** seção do portal, no **rever expressões de ponto final** secção. 
* Quando **rever expressões de ponto final**e adicionar uma expressão para uma intenção, a expressão já não é armazenado como parte de expressões com os ponto final ser revisto. Este é adicionado ao objetivos da aplicação. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Eliminar expressão de exemplo a partir de um objetivo
Eliminar usadas para treinamento de expressões de exemplo [LUIS](luis-reference-regions.md). Se eliminar uma expressão de exemplo da sua aplicação LUIS, ele é removido do serviço web LUIS e não está disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Eliminar expressões com revisão de aprendizagem ativa

Pode eliminar expressões com na lista de expressões de utilizador que LUIS sugere no  **[página de expressões de ponto final de revisão](luis-how-to-review-endpoint-utterances.md)** . A eliminar expressões com desta lista evita que sejam sendo sugerida, mas não a eliminá-los a partir de registos.

Se não quiser que as expressões de aprendizagem ativa, pode [desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Desativar a aprendizagem ativa também desativa o registo.

### <a name="disable-logging-utterances"></a>Desativar a expressão de registo
[Desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning) é desativa o registo.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Eliminar uma conta
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
