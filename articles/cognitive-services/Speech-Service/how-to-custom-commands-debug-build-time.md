---
title: Depurar erros ao autorizar uma aplicação de Comandos Personalizados (Pré-visualização)
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se a depurar erros ao autorizar a aplicação de Comandos Personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b0a26b4d3f0f59f8e05c5990bbc64ee55f12f124
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307740"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Depurar erros ao autorizar uma aplicação de Comandos Personalizados

Este artigo descreve como depurar quando vê erros ao construir a aplicação Comandos Personalizados. 

## <a name="errors-when-creating-an-application"></a>Erros ao criar uma aplicação
Os Comandos Personalizados também criam uma aplicação no [LUIS](https://www.luis.ai/) ao criar uma aplicação de Comandos Personalizados. 

[O LUIS limita 500 aplicações por recurso de autoria.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits) A criação da aplicação LUIS poderá falhar se estiver a utilizar um recurso de autoria que já tenha 500 aplicações. 

Certifique-se de que o recurso de autoria LUIS selecionado tem menos de 500. Caso contrário, pode recorrer a um novo recurso de autoria LUIS, mudar para outro, ou tentar limpar as suas aplicações LUIS.  

## <a name="errors-when-deleting-an-application"></a>Erros ao apagar uma aplicação
### <a name="cant-delete-luis-application"></a>Não pode apagar a aplicação LUIS
Ao eliminar uma aplicação de Comandos Personalizados, os Comandos Personalizados podem também tentar eliminar a aplicação LUIS associada à aplicação Comandos Personalizados.

Se a eliminação da aplicação LUIS falhou, por favor, vá à sua conta [LUIS](https://www.luis.ai/) para eliminá-las manualmente.

### <a name="toomanyrequests"></a>TooManyRequests
Quando tenta eliminar uma grande quantidade de aplicações de uma só vez, é provável que veja erros de 'TooManyRequests'. Isto significa que os seus pedidos de supressão são estrangulados pelo Azure. 

Por favor, refresque a sua página e tente eliminar menos aplicações.

## <a name="errors-when-modifying-an-application"></a>Erros ao modificar uma aplicação

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Não é possível eliminar um parâmetro ou um Ponto Final web
Não é permitido apagar um parâmetro quando este está a ser utilizado. Por favor, remova qualquer referência do parâmetro em quaisquer respostas de fala, frases de amostra, condições, ações e tente novamente.

### <a name="cant-delete-a-web-endpoint"></a>Não é possível eliminar um Web Endpoint
Não está autorizado a eliminar um Ponto Final web quando este estiver a ser utilizado. Por favor, remova qualquer ação **call Web Endpoint** que utilize este Web Endpoint antes de remover um Ponto Final web.

## <a name="errors-when-training-an-application"></a>Erros ao treinar uma aplicação
### <a name="build-in-intents"></a>Construir em intenções
LUIS tem build-in Sim/Sem intenções. Tendo frases de amostra com apenas "sim", "não" falharia no treino. 

| Palavra-chave | Variações | 
| ------- | --------- | 
| Yes | Claro, ok. |
| No | Não, não. | 

### <a name="common-sample-sentences"></a>Frases-amostra comuns
Os Comandos Personalizados não permitem frases comuns de amostra partilhadas entre diferentes comandos. O treino de uma aplicação pode falhar se algumas frases de amostra de um comando já estiverem definidas noutro comando. 

Por favor, certifique-se de que não tem frases comuns partilhadas entre diferentes comandos. 

Para obter as melhores práticas de equilibrar as suas frases de amostra em diferentes comandos, consulte [as melhores práticas do LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)

### <a name="empty-sample-sentences"></a>Frases de amostra vazias
Precisa de pelo menos uma amostra de uma frase para cada Comando.

### <a name="undefined-parameter-in-sample-sentences"></a>Parâmetro indefinido nas frases da amostra
Um ou mais parâmetros são usados nas frases da amostra, mas não definidos.

### <a name="training-takes-too-long"></a>O treino demora muito tempo.
O treino DO LUIS destina-se a aprender rapidamente com menos exemplos. Não adicione muitas frases de exemplo. 

Se tiver muitas frases de exemplo são semelhantes, defina um parâmetro, abstrate-as num padrão e adicione-as a Frases exemplo.

Por exemplo, pode definir um parâmetro {veículo} para as frases de exemplo abaixo, e apenas adicionar "Reserve um {veículo}" a Frases exemplo.

| Frases de exemplo | Padrão | 
| ------- | ------- | 
| Reserve um carro | Reserve um {veículo} | 
| Reserve um voo | Reserve um {veículo} |
| Reserve um táxi | Reserve um {veículo} |

Para as melhores práticas de treino do LUIS, consulte [as melhores práticas do LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)

## <a name="cant-update-luis-key"></a>Não é possível atualizar a chave LUIS
### <a name="reassign-to-e0-authoring-resource"></a>Reatribuir ao recurso de autoria E0
A LUIS não suporta a reatribuição da aplicação LUIS ao recurso de autoria E0.

Se precisar de alterar o seu recurso de autoria de F0 para E0, ou mudar para um recurso E0 diferente, por favor recrie a aplicação.

### <a name="save-button-is-disabled"></a>O botão de poupança está desativado
Se nunca atribuir um recurso de previsão LUIS à sua aplicação, o botão Guardar será desativado quando tentar alterar o seu recurso de autoria sem adicionar um recurso de previsão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)
