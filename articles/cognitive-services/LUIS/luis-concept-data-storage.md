---
title: Armazenamento de dados - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS armazena dados encriptados numa loja de dados Azure correspondente à região especificada pela chave.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 12693fb11556380e62df277be093ce20c02ff372
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018037"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Armazenamento e remoção de dados em Serviços Cognitivos de Compreensão linguística (LUIS)
A LUIS armazena dados encriptados numa loja de dados Azure correspondente à região especificada pela chave. Estes dados são armazenados por 30 dias. 

## <a name="export-and-delete-app"></a>Exportar e eliminar aplicativo
Os utilizadores têm total controlo sobre [a exportação](luis-how-to-start-new-app.md#export-app) e [eliminação](luis-how-to-start-new-app.md#delete-app) da app. 

## <a name="utterances"></a>Expressões

As expressões podem ser armazenadas em dois lugares diferentes. 

* Durante **o processo de autoria,** as expressões são criadas e armazenadas na Intenção. São necessárias declarações em intenções para uma aplicação LUIS bem sucedida. Uma vez publicada a aplicação e recebe consultas no ponto final, a consulta do pedido de ponto `log=false` final, determina se a expressão do ponto final é armazenada. Se o ponto final for armazenado, torna-se parte das expressões de aprendizagem ativa encontradas na secção **Build** do portal, na secção de comentários de ponto final de **Revisão.** 
* Quando **analisa as proclamações finais**, e adiciona uma expressão a uma intenção, a expressão já não é armazenada como parte das proclamações do ponto final a rever. É adicionado às intenções da aplicação. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Apagar palavras de exemplo de uma intenção

Apagar palavras de exemplo utilizadas para o treino [LUIS.](luis-reference-regions.md) Se eliminar um exemplo da sua aplicação LUIS, este é removido do serviço web LUIS e não está disponível para exportação.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Apagar expressões em revisão da aprendizagem ativa

Pode eliminar as declarações da lista de declarações de utilizador que a LUIS sugere na **[página de comentários de ponto final](luis-how-to-review-endpoint-utterances.md)** do ponto final do Ponto de Vista . A eliminação de declarações desta lista impede que sejam sugeridas, mas não as apaga dos registos.

Se não quer declarações de aprendizagem ativa, pode [desativar a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) A desativação da aprendizagem ativa também desativa o registo.

### <a name="disable-logging-utterances"></a>Desativar as expressões de registo
[A desativação da aprendizagem ativa](luis-how-to-review-endpoint-utterances.md#disable-active-learning) é desativar o registo.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Eliminar uma conta
Se não estiver migrado, pode eliminar a sua conta e todas as suas aplicações serão eliminadas juntamente com as suas palavras de exemplo e registos. Os dados são conservados por 90 dias antes de a conta e os dados serem eliminados permanentemente.

A eliminação da conta está disponível na página **Definições.** Selecione o nome da sua conta na barra de navegação superior direita para chegar à página **Definições.**

## <a name="delete-an-authoring-resource"></a>Eliminar um recurso de autoria
Se tiver [migrado para um recurso de autoria,](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)eliminando o próprio recurso do portal Azure irá eliminar todas as suas aplicações associadas a esse recurso, juntamente com as suas expressões de exemplo e registos. Os dados são conservados durante 90 dias antes de serem eliminados permanentemente.    

Para eliminar o seu recurso, vá ao [portal Azure](https://ms.portal.azure.com/#home) e selecione o seu recurso de autoria LUIS. Vá ao **separador 'Vista Geral'** e clique no botão **Eliminar** na parte superior da página. Em seguida, confirme que o seu recurso foi apagado. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Inatividade de dados como subscrição caducada
Para efeitos de retenção e eliminação de dados, uma aplicação LUIS inativa pode, a _critério da Microsoft,_ ser tratada como uma subscrição expirada. Uma aplicação é considerada inativa se cumprir os seguintes critérios para os últimos 90 dias: 

* **Não** lhe fizeram chamadas.
* Não foi modificado.
* Não tem uma chave atual atribuída.
* Não teve um sinal de utilizador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre exportação e eliminação de uma app](luis-how-to-start-new-app.md)
