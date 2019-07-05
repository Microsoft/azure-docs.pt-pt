---
title: Quando utilizar entidades de Enumeração e ações de entidade definido com um modelo de aprendiz de conversação - serviços cognitivos do Azure | Documentos da Microsoft
titleSuffix: Azure
description: Saiba quando é apropriado utilizar entidades de Enumeração e ações de entidade definido com um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e990ebe89f4446a0226aa0e0f73ffd900e5b021a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592950"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quando utilizar entidades de Enumeração e ações de conjunto de entidades

Neste tutorial explicam quando deve utilizar entidades do ENUM (enumeração) e as ações de SET_ENTITY.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do conjunto de entidades](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>O que é abrangido

Neste tutorial, irá introduzir dois novos recursos. Um novo tipo de entidade chamada ENUM (Abreviação de enumeração) e um novo tipo de ação chamado SET_ENTITY, que pode fazer referência a um dos seguintes valores de enumeração e, como o nome indica, define a entidade a este valor. Como aprenderá abaixo, esses novos recursos são utilizados em conjunto e Explicaremos o que elas são e como usá-las abaixo. Antes de entrarmos em detalhes, é importante compreender que problema esses recursos ajudam a resolver.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problema

Há casos em que o significado de palavras depende do contexto de conversas.  Normalmente as palavras-chave etiquetadas adquiridas e extraiu utilizando um serviço de compreensão de idiomas, mas nestes casos desses sistemas não podem ser capazes de aprender com exemplos etiquetados.

Imagine que overhear parte de uma conversa entre pessoas que estejam próximas e apenas ouve a palavra "Sim". Não saberia o que "Sim" está a aceitar ou confirmar, uma vez que não ouve a pergunta-lhe pedido antes dele. A pergunta feita antes é o contexto, que dão significado à resposta. Da mesma forma, desde "Sim" é uma resposta comum a várias perguntas diferentes, não pode ser aprendido ao fornecer exemplos de como faria com [personalizado treinado](04-introduction-to-entities.md) entidades porque, em seguida, ele seria saiba que identifique todos os "Sim" como essa entidade.

### <a name="example"></a>Exemplo

Vamos esclarecer ainda mais com o exemplo seguinte:

Bot: Gosta dos serviços cognitivos do Azure?
Utilizador: Sim Bot: Gosta ice cream?
Utilizador: Sim

Nos tutoriais anteriores, vimos [personalizado treinado](04-introduction-to-entities.md) entidades e suas idéias inicial podem ser criar uma entidade com o nome "likesCogServices" e etiquetar o primeiro "Sim" como esta entidade.  No entanto, o sistema teria também etiqueta a segunda "Sim". Quando vamos tentar corrigir a etiqueta da segunda "Sim" para "likesIceCream", que, em seguida, criaria um conflito de duas entradas mesmo "Sim" coisas diferentes do significado e ficavam presas.

É nestes casos em que precisa usar entidades de Enumeração e ações de SET_ENTITY.

## <a name="when-to-use-enums-or-setentity-actions"></a>Quando usar ENUMs ou SET_ENTITY ações

Utilize estas regras abaixo para saber quando utilizar entidades de Enumeração e ações de SET_ENTITY:

- Detecção ou definição de entidade é dependente do contexto
- Número de valores possíveis é fixo (Sim e não seria dois valores)

Em outras palavras, a utilizá-los para todos os pedidos terminou de fechamento, como as perguntas de confirmação que sempre resultam em ou No.

> [!NOTE]
> Atualmente, temos a limitação de até 5 valores por entidade de enumeração. Cada valor usa uma das ranhuras no limite de 64 atual. Consulte [cl-valores-e-limites](../cl-values-and-boundaries.md)

Exemplo: Bot: A sua encomenda está correto?
Utilizador: Sim

Quando os valores possíveis da entidade estão abertas e não corrigido, terá de utilizar como um recurso alternativo [esperado entidade](05-expected-entity.md).

Exemplo: Bot: O que é o seu nome?
Utilizador: Bot de Matt: O que é a cor favorita?
Utilizador: Prateado

Esses prompts são considerados abertas, uma vez que poderia ser respondidas com valores arbitrários.

## <a name="what"></a>O quê

### <a name="enum-entities"></a>Entidades de Enumeração

Entidades de Enumeração são criadas como outras entidades. À semelhança das entidades "programáticas", não é possível Etiquetar palavras como essas entidades. Em vez disso, deve ser definidas por meio de código ou ações SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Definir ações de entidades

Conforme mencionado acima, ações de "Definir entidades" simplesmente define uma entidade para um valor de enum conhecidos. É possível atingir os mesmos resultados ao criar uma ação de retorno de chamada de API e usar o Gerenciador de memória para definir a entidade para um valor. Por exemplo, `memory.Set(entityName, entityValue)`. Ter de escrever esse código e criar essas ações se tornaria entediante e difícil de gerenciar - para que tenha de aprendiz de conversação ações especiais para facilitar esse trabalho e gerar automaticamente estas ações quando são utilizadas. Ter esses como ações independentes preserva a capacidade de criar estes sem a ser juntamente com outras ações ou o código no seu bot.

- Só é possível criar o conjunto de ações de entidades quando nos Referimos como um valor de uma entidade de enumeração, pelo que deve criar primeiro uma entidade de enumeração.
- Ações de entidades do conjunto são também "não-espera" uma vez que eles têm nenhuma saída visível e tem de ser seguido de segurança de uma ação "wait", que o utilizador pode ver.
- Ações de entidades do conjunto são imutáveis, que significa que não é possível editá-los após a criação.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Geração automática de ação

Se existir uma entidade de enumeração no seu modelo, o aprendiz de conversação criar ações para cada uma das possíveis valores de marcador de posição e disponibilizá-los selecionar durante o treinamento. Após a seleção, a ação seria criada automaticamente para.

Por exemplo, se eu criar uma entidade de enumeração com os valores "Sim" e "Não":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Mesmo sem criar explicitamente ações para essa nova enumeração, verá duas novas ações disponíveis durante o treinamento:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Criar um Bot através destas novas funcionalidades

### <a name="requirements"></a>Requisitos

Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

Criaremos um bot para simular a ordenação de fast-food. Que pode ter valores discretos para tamanhos de drinks e fritas (pequeno/médio/grande) e perguntas de confirmação com Sim / não respostas. Ambos destas entidades cumprem as duas regras acima de ser dependente do contexto respostas e valores fixos.

### <a name="create-the-model"></a>Criar o modelo

1. Na IU da web, clique em "Importar"
2. Selecione o tutorial com o nome "Entidade-Tutorial-Enum-conjunto"

Isto irá navegar para a página de gestão de modelo.
Tenha em atenção que o modelo já contém algumas entidades de enumeração e definir as ações de entidades.

### <a name="create-the-first-dialog"></a>Criar a primeira caixa de diálogo

1. No painel de navegação esquerdo, clique em "Caixas de diálogo Train", em seguida, clique no botão "Train caixa de diálogo Novo".
2. Como o tipo de utilizador na, "Olá, eu gostaria de ordem um coca-cola e fritas.".
3. Clique no botão "Pontuação ações"

   > O utilizador não especificou tamanhos para a bebida ou fritas, portanto, precisamos de peça-lhes.

4. Selecione a ação com a resposta: "Bebida que tamanho deseja?"
5. Como o tipo de utilizador na, "grandes"
6. Clique no botão "Pontuação ações"
7. Selecione a ação SET_ENTITY - "drinkSize: GRANDE"
8. Selecione a ação com a resposta: "Qual tamanho fritas faria isso como?"
9. Como o tipo de utilização no, "hum, mostrar os uma média.
10. Clique no botão "Pontuação ações"
11. Selecione a ação SET_ENTITY - "friesSize: MÉDIA"
12. Selecione a ação com a resposta: "Gostaria que qualquer condiments?"
13. Como o tipo de utilização em "Sim"
14. Clique no botão "Pontuação ações"
15. Selecione a ação SET_ENTITY - "condimentsConfirmation: SIM"
16. Selecione a ação com a resposta: "Ok, tenho uma encomenda de uma grande bebida e fritas médias. É nesse correto?"
17. Como o tipo de utilização em "Sim"
18. Clique no botão "Pontuação ações"
19. Selecione a ação SET_ENTITY - "orderConfirmation: SIM"
20. Selecione a ação com a resposta: "Ok, o número do pedido é 58. Aguarde por aí."
21. Clique em "Guardar" para fechar a caixa de diálogo

Acabou de criar a primeira caixa de diálogo com entidades de Enumeração e ações de SET_ENTITY. Pode fazer muitas combinações de mais do usuário especificar a informação parcial ou fazer experiências com outros tipos de perguntas terminou de fecho.

> [!NOTE]
> Durante o treinamento, verá marcadores de posição para as ações de SET_ENTITY como
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> mas, quando criar sessão caixas de diálogo ou usando implementado bots os utilizadores não verão estes.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
