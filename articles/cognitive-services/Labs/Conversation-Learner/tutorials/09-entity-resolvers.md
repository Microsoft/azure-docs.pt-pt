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
ms.openlocfilehash: cca78e2536a922165f40bbcbabcae005021aa70b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167852"
---
# <a name="entity-resolvers"></a>Resoluções de entidade

Este tutorial mostra como utilizar resoluções de entidade no Aprendiz de conversação

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do entidades resoluções](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Tipo de resolução é uma propriedade opcional de entidades personalizadas.
- Resoluções de entidade utilizam o poder de reconhecedores a entidade com formação prévia no LUIS para fornecer detalhes adicionais e clareza para a sua entidade personalizada.

## <a name="steps"></a>Passos

Comece na home page na IU da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Introduza **resoluções de entidade** para **nome**.
3. Selecione **Criar**.

### <a name="create-a-pair-of-entities"></a>Criar um par de entidades

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Introduza **mudança** para **nome da entidade**.
3. Selecione **datetimeV2** para **resolvedor tipo**.
4. Selecione **Criar**. Descartar o pop-up informativo selecionando **OK**.
5. Repita os passos 1 a 4 para criar uma segunda entidade nomeada **retornar** com **datetimeV2** tipo resolvedor.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Criar um par de ações

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **estão a sair $departure e retornando no $return** para **de resposta do Bot...** .
    - IMPORTANTE - quando digitar $[entityName], precisa pressionar enter ou clique na entidade na lista pendente; caso contrário, Aprendiz de conversação irá considerar essa texto em vez de uma entidade.
    - Tenha em atenção que o **necessário entidades** campo também terá estas entidades, e eles não podem ser removidos. Isto impede que esta ação se torne disponível até que ambos necessários que entidades estão presentes.
3. Selecione **Criar**.
4. Selecione **nova ação** para criar uma segunda ação.
5. Introduza **quando está a planear viajar?** para **de resposta do Bot...** .
6. Introduza **mudança** e **devolver** para **Disqualifying entidades**. Estes dizer ao nosso Bot não efetuar esta ação se qualquer uma destas entidades contêm um valor.
7. Selecione **Criar**.

![](../media/T09_actions.png)

### <a name="training"></a>Formação

1. Veja a **treinamento: [Status]** no canto superior esquerdo para **concluído**.
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

![](../media/T09_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
