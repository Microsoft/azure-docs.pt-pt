---
title: Resoluções de entidade num modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar resoluções de entidade no Aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ebe555bfd7b34efd87d400d786049964665c76e6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451064"
---
# <a name="entity-resolvers"></a>Resoluções de entidade

Este tutorial mostra como utilizar resoluções de entidade no Aprendiz de conversação

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do entidades resoluções](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Tipo de resolução é uma propriedade opcional de entidades personalizadas.
- Resoluções de entidade utilizam o poder de reconhecedores a entidade com formação prévia no LUIS para fornecer detalhes adicionais e clareza para a sua entidade personalizada.

## <a name="steps"></a>Passos

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na IU da Web, clique no botão "Novo modelo".
2. No campo "Nome", escreva "Resoluções de entidade", prima enter ou clique no botão "Criar".

### <a name="create-a-pair-of-entities"></a>Criar um par de entidades

1. No painel esquerdo, clique em "Entidades", em seguida, clique no botão "Nova entidade".
2. No campo "Nome da entidade", escreva "saída".
3. Na lista pendente "Tipo de resolução", selecione "datetimeV2".
4. Clique no botão "Criar".
5. Clique no botão "OK" depois de ler o pop-up de informações.
6. Seguindo as mesmas etapas, crie outra entidade com o nome "return" e que também tem um tipo de resolução "datetimeV2".

### <a name="create-a-pair-of-actions"></a>Criar um par de ações

1. No painel esquerdo, clique em "Ações", em seguida, clique no botão "Nova ação".
2. Na "resposta do Bot" tipo de campo, "está deixando no $departure e retornando no $return".
    - IMPORTANTE - ao digitar "$[entityName]" tem de pressionar enter ou clique na entidade na lista pendente, caso contrário Aprendiz de conversação irá considerar essa texto em vez de uma entidade.
    - Observe que o campo "Entidades necessárias" também terá estas entidades, e eles não podem ser removidos. Isto impede que esta ação se torne disponível até que ambos necessários que entidades estão presentes.
3. Clique no botão "Criar".
4. Clique no botão "Nova ação" novamente para criar uma segunda ação.
5. Em "do Bot de resposta" campo de tipo, "Quando está a planear viajar?".
6. No "Entidades Disqualifying" tipo de campo, "saída" e também tipo, "return".
    - Estes dizer ao nosso Bot não efetuar esta ação se qualquer uma destas entidades contêm um valor.
7. Clique no botão "Criar".


### <a name="training"></a>Formação

1. Assista a "treinamento: [Status]" na parte superior esquerda parte da página e aguardar que ele seja "concluída".
    - Pode clicar na ligação "Atualização" se ação demorar demasiado tempo.
    - "Concluído" Estado de preparação é necessário para que nossa resolvedores de entidade funciona quando preparar o modelo.
2. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, clique no botão "Train caixa de diálogo Novo".
3. Na primeira expressão de utilizador, "Programar-me um vôo" de tipo. 
4. Clique no botão "Pontuação ações".
5. Selecione a resposta, "Quando está a planear viajar?".
6. Como o utilizador responda com, "deixando amanhã e retornando Domingo próxima semana".
    - Observe como a utilização de aprendiz de conversação detetou a que duas entidades "Previamente treinados data", em que o utilizador ative.
7. No painel de "Detecção de entidade", selecione o texto "amanhã" e classifique-o como "saída"
8. Etiqueta também o texto "Domingo próxima semana" como "return"
9. Clique no botão "Pontuação ações".
    - Observe como o painel de "Memória" contém as datas de mudança e retorno.
    - Coloque o cursor sobre cada um deles e observe como as entidades são objetos de data que capturar claramente a data do calendário real em vez de "Domingo" ou "amanhã".
10. Selecione o "estão a deixar em..." Resposta de bot.
11. Clique no botão "Guardar".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
