---
title: Armazenamento de dados - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS armazena dados encriptados numa loja de dados Azure correspondente à região especificada pela chave.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220019"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento e remoção de dados em Serviços Cognitivos de Compreensão linguística (LUIS)
A LUIS armazena dados encriptados numa loja de dados Azure correspondente à região especificada pela chave. Estes dados são armazenados por 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e excluir app
Os utilizadores têm total controlo sobre [a exportação](luis-how-to-start-new-app.md#export-app) e [a apreensão](luis-how-to-start-new-app.md#delete-app) da aplicação. 

## <a name="utterances"></a>Expressões

As expressões podem ser armazenadas em dois lugares diferentes. 

* Durante **o processo de autoria,** as expressões são criadas e armazenadas na Intenção. São necessárias declarações de intenções para uma aplicação DE SUCESSO DA LUIS. Uma vez publicada a aplicação e recebe consultas no ponto final, a `log=false`consulta do pedido final, determina se a expressão do ponto final é armazenada. Se o ponto final for armazenado, torna-se parte das expressões de aprendizagem ativa encontradas na secção **Construção** do portal, na secção de declarações de ponto final de **Revisão.** 
* Quando **se revê as declarações finais**, e adiciona uma expressão a uma intenção, a expressão já não é armazenada como parte das declarações finais a rever. É adicionado às intenções da app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Apagar pronunciações de exemplo de uma intenção

Eliminar as declarações de exemplo utilizadas para a formação [do LUIS](luis-reference-regions.md). Se eliminar um exemplo da sua aplicação LUIS, é removido do serviço web LUIS e não está disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Eliminar as declarações em análise da aprendizagem ativa

Pode eliminar as declarações da lista de declarações do utilizador que a LUIS sugere na página de **[declarações](luis-how-to-review-endpoint-utterances.md)** de ponto final de Revisão . Eliminar as declarações desta lista impede que sejam sugeridas, mas não as apaga dos registos.

Se não quiser declarações de aprendizagem ativa, pode [desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Desativar a aprendizagem ativa também desativa a exploração madeireira.

### <a name="disable-logging-utterances"></a>Desativar as expressões de exploração madeireira
[Desativar a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning) é desativar o registo.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Eliminar uma conta
Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com as suas expressões e registos de exemplo. Os dados são conservados durante 60 dias antes de a conta e os dados serem eliminados permanentemente.

A conta de aparas está disponível na página **Definições.** Selecione o nome da sua conta na barra de navegação superior direita para chegar à página **Definições.**

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como subscrição vencida
Para efeitos de retenção e eliminação de dados, uma aplicação INATIVA LUIS pode, _a critério da Microsoft,_ ser tratada como uma subscrição caducada. Uma aplicação é considerada inativa se cumprir os seguintes critérios nos últimos 90 dias: 

* Não **lhe** fizeram nenhuma chamada.
* Não foi modificado.
* Não tem uma chave atual atribuída a ela.
* Não teve um utilizador a fazer um sinal.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre exportar e apagar uma app](luis-how-to-start-new-app.md)
