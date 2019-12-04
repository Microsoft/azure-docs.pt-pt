---
title: Adicionar a ação de consulta em aplicativos lógicos
description: Visão geral da ação de consulta para executar ações como matriz de filtro
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/20/2016
tags: connectors
ms.openlocfilehash: 2e5c9d371a280b776699b2e10d3e8e94b5f41f6f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787137"
---
# <a name="get-started-with-the-query-action"></a>Introdução à ação de consulta

Usando a ação de consulta, você pode trabalhar com lotes e matrizes para realizar fluxos de trabalho para:

* Crie uma tarefa para todos os registros de alta prioridade de um banco de dados.
* Salve todos os anexos em PDF para emails em um blob do Azure.

Para começar a usar a ação de consulta em um aplicativo lógico, consulte [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Usar a ação de consulta

Uma ação é uma operação que é executada pelo fluxo de trabalho que é definido em um aplicativo lógico. 
[Saiba mais sobre ações](../connectors/apis-list.md).  

A ação de consulta atualmente tem uma operação, chamada de matriz de filtro, que é exposta no designer. Isso permite consultar uma matriz e retornar um conjunto de resultados filtrados.

Veja como você pode adicioná-lo em um aplicativo lógico:

1. Selecione o botão **nova etapa** .
2. Escolha **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, digite **filtro** para listar a ação **Filtrar matriz** .
   
    ![Selecionar a ação de consulta](./media/connectors-native-query/using-action-1.png)
4. Selecione uma matriz para filtrar. (A captura de tela a seguir mostra a matriz de resultados de uma pesquisa do Twitter.)
5. Crie uma condição a ser avaliada em cada item. (A captura de tela a seguir filtra tweets de usuários que têm mais de 100 seguidores.)
   
    ![Concluir a ação de consulta](./media/connectors-native-query/using-action-2.png)
   
    A ação produzirá uma nova matriz que contém apenas os resultados que atenderam aos requisitos de filtro.
6. Clique no canto superior esquerdo da barra de ferramentas para salvar e seu aplicativo lógico será salvo e publicado (ativado).

\* se você estiver chamando um ponto de extremidade HTTP e recebendo uma resposta JSON, use a ação _analisar JSON_ para analisar a resposta JSON. Sem levar essa etapa, o _filtro da matriz_ verá apenas o corpo e não entenderá a estrutura da carga JSON.

## <a name="query-action"></a>Ação de consulta
Aqui estão os detalhes da ação com a qual este conector dá suporte. O conector tem uma ação possível.

| Ação | Descrição |
| --- | --- |
| Filtrar matriz |Avalia uma condição para cada item em uma matriz e retorna os resultados |

## <a name="action-details"></a>Detalhes da ação
A ação de consulta vem com uma ação possível. As tabelas a seguir descrevem os campos de entrada obrigatórios e opcionais para a ação e os detalhes de saída correspondentes associados ao uso da ação.

### <a name="filter-array"></a>Filtrar matriz
Veja a seguir os campos de entrada para a ação, que faz uma solicitação de saída HTTP.
Um * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Terem |De |A matriz a ser filtrada |
| Problema |posição |A condição a ser avaliada para cada item |

<br>

### <a name="output-details"></a>Detalhes da saída
A seguir estão os detalhes de saída para a resposta HTTP.

| Nome da propriedade | Data type | Descrição |
| --- | --- | --- |
| Matriz filtrada |array |Uma matriz que contém um objeto para cada resultado filtrado |

## <a name="next-steps"></a>Passos seguintes
Agora, experimente a plataforma e [crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Você pode explorar os outros conectores disponíveis em aplicativos lógicos examinando nossa [lista de APIs](apis-list.md).

