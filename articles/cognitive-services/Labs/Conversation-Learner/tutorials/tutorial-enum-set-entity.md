---
title: Quando usar entidades ENUM e definir ações de entidade com um modelo de Conversation Learner – serviços cognitivas do Azure | Microsoft Docs
titleSuffix: Azure
description: Saiba quando é apropriado usar entidades ENUM e definir ações de entidade com um modelo de Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256489"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quando usar entidades ENUM e definir ações de entidade

Este tutorial explicará quando você deve usar entidades ENUM (Enumeração) e ações SET_ENTITY.

## <a name="video"></a>Vídeo

[![Definir visualização do tutorial de entidade](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>O que é coberto

Este tutorial apresentará dois novos recursos. Um novo tipo de entidade chamada ENUM (curta para Enumeração) e um novo tipo de ação chamado SET_ENTITY, que pode se referir a um desses valores de enum e, como o nome indica, define a entidade para esse valor. Como você aprenderá abaixo, esses novos recursos são usados juntos e explicaremos o que eles são e como usá-los abaixo. Antes de entrarmos nos detalhes, é importante entender o problema que esses recursos ajudam a resolver.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problema

Há casos em conversas em que o significado das palavras depende do contexto.  Normalmente, as palavras-chave rotuladas são aprendidas e extraídas usando um serviço de reconhecimento de idioma, mas nesses casos, esses sistemas podem não ser capazes de aprender a usar exemplos rotulados.

Imagine que você overhear parte de uma conversa entre pessoas próximas e ouve apenas a palavra "Sim". Você não sabe o que "Sim" está concordando ou confirmando, pois você não ouviu a pergunta feita antes dela. A pergunta feita antes é o contexto, que dá significado à resposta. Da mesma forma, como "Sim" é uma resposta comum a muitas perguntas diferentes, ele não pode ser aprendido fornecendo exemplos como você faria com entidades [treinadas personalizadas](04-introduction-to-entities.md) porque, em seguida, aprenderiam a rotular cada "Sim" como essa entidade.

### <a name="example"></a>Exemplo

Vamos esclarecer ainda mais o seguinte exemplo:

Bot Você gosta de serviços cognitivas do Azure?
Usuário Sim bot: Você gosta de sorvetes?
Usuário Sim

Nos tutoriais anteriores, examinamos as entidades [treinadas personalizadas](04-introduction-to-entities.md) e sua ideia inicial pode ser criar uma entidade chamada "likesCogServices" e rotular o primeiro "Sim" como essa entidade.  No entanto, o sistema também rotularia o segundo "Sim". Quando tentamos corrigir o rótulo do segundo "Sim" para "likesIceCream", criamos um conflito de duas mesmas entradas "Sim", o que significa coisas diferentes e estaria preso.

Nesses casos, você precisa usar as entidades ENUM e as ações SET_ENTITY.

## <a name="when-to-use-enums-or-set_entity-actions"></a>Quando usar ENUMs ou ações SET_ENTITY

Use estas regras abaixo para saber quando usar entidades ENUM e ações SET_ENTITY:

- A detecção ou configuração de entidade é dependente de contexto
- O número de valores possíveis é fixo (Sim e não seriam dois valores)

Em outras palavras, use-as para quaisquer prompts de fechamento, como perguntas de confirmação que sempre resultem em Sim ou não.

> [!NOTE]
> No momento, temos uma limitação de até 5 valores por entidade enum. Cada valor usa um dos slots no limite de 64 atual. Consulte [CL-Values-and-fronteiras](../cl-values-and-boundaries.md)

Exemplo: Bot Seu pedido está correto?
Usuário Sim

Quando os valores possíveis da entidade forem abertos e não forem corrigidos, você precisará usar um recurso alternativo, como a [entidade esperada](05-expected-entity.md).

Exemplo: Bot Qual é o seu nome?
Usuário Bot de Matt: Qual é sua cor favorita?
Usuário Prateado

Esses prompts são considerados em aberto porque podem ser respondidos com valores arbitrários.

## <a name="what"></a>O quê

### <a name="enum-entities"></a>ENUMERAr entidades

As entidades de enumeração são criadas exatamente como as outras entidades. Semelhante às entidades "programáticas", não é possível rotular palavras como essas entidades. Em vez disso, eles devem ser definidos por meio de ações de código ou SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Definir ações de entidade

Conforme mencionado acima, as ações de "definir entidade" simplesmente definem uma entidade como um valor de enumeração conhecido. Você pode obter os mesmos resultados criando uma ação de retorno de chamada de API e usando o Gerenciador de memória para definir a entidade como um valor. Por exemplo, `memory.Set(entityName, entityValue)`. Ter que escrever esse código e criar essas ações se tornaria entediante e difícil de gerenciar. portanto, Conversation Learner tem ações especiais para facilitar esse trabalho e gerar automaticamente essas ações quando elas são usadas. Ter esses como ações independentes preserva a capacidade de compor esses sem estar acoplado a outras ações ou código em seu bot.

- As ações de entidade Set só podem ser criadas quando se referir a um valor de uma entidade enum, portanto, você deve criar uma entidade enum primeiro.
- As ações de entidade Set também são "não Await", pois não têm nenhuma saída visível e precisam ser seguidas por uma ação de "espera" que o usuário possa ver.
- As ações de entidade set são imutáveis, o que significa que você não pode editá-las após a criação.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Geração de ação automática

Se uma entidade de enumeração existir em seu modelo, Conversation Learner criará ações de espaço reservado para cada um dos valores possíveis e torná-los disponíveis para seleção durante o treinamento. Após a seleção, a ação seria criada automaticamente para você.

Por exemplo, se eu criar uma entidade enum com os valores "Yes" e "no":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Mesmo sem a criação explícita de ações para esse novo enum, você veria duas novas ações disponíveis durante o treinamento:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Criar um bot usando esses novos recursos

### <a name="requirements"></a>Requisitos

Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

Criaremos um bot para simular a ordenação rápida de alimentos. Ele terá valores discretos para os tamanhos de bebidas e mais (pequeno/médio/grande) e perguntas de confirmação com respostas de Sim/não. Ambas as entidades atendem às duas regras acima de serem valores fixos e respostas dependentes de contexto.

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "importar"
2. Selecione o tutorial chamado "tutorial-enum-Set-Entity"

Isso irá navegar até a página de gerenciamento de modelos.
Observe que o modelo já contém algumas entidades enum e define ações de entidade.

### <a name="create-the-first-dialog"></a>Criar a primeira caixa de diálogo

1. No painel de navegação esquerdo, clique em "treinar caixas de diálogo" e, em seguida, clique no botão "nova caixa de diálogo treinar".
2. Como o usuário digitado, "Olá, gostaria de pedir um Coke e mais".
3. Clique no botão "ações de Pontuação"

   > O usuário não especificou tamanhos para o bebida ou o mais, portanto, precisamos fazer isso.

4. Selecione a ação com resposta: "Que tamanho de bebida você gostaria?"
5. Como o usuário digita, "grande"
6. Clique no botão "ações de Pontuação"
7. Selecione a ação SET_ENTITY-"bebidas: VÁRIOS
8. Selecione a ação com resposta: "Qual tamanho mais você gostaria?"
9. Como o tipo de uso em, "um, torná-lo médio.
10. Clique no botão "ações de Pontuação"
11. Selecione a ação SET_ENTITY-"friesSize: MÉDIO
12. Selecione a ação com resposta: "Você gostaria de qualquer condiments?"
13. Como o tipo de uso em, "Sim"
14. Clique no botão "ações de Pontuação"
15. Selecione a ação SET_ENTITY-"condimentsConfirmation: OK
16. Selecione a ação com resposta: "Ok, tenho um pedido para uma grande bebida e mais MÉDIAs. Isso está correto? "
17. Como o tipo de uso em, "Sim"
18. Clique no botão "ações de Pontuação"
19. Selecione a ação SET_ENTITY-"orderConfirmation: OK
20. Selecione a ação com resposta: "Ok, seu número de pedido é 58. Aguarde. "
21. Clique em "salvar" para fechar a caixa de diálogo

Você acabou de criar sua primeira caixa de diálogo usando entidades de enumeração e ações de SET_ENTITY. Você pode fazer muitas outras combinações do usuário especificando informações parciais ou experimentando outros tipos de perguntas de fechamento.

> [!NOTE]
> Durante o treinamento, você verá espaços reservados para as ações SET_ENTITY, como
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Mas, ao criar caixas de diálogo de log ou usar bots implantados, os usuários não os verão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
