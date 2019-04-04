---
title: Adicione a ação de consulta no logic apps | Documentos da Microsoft
description: Descrição geral da ação de consulta para realizar ações como a matriz de filtro.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893572"
---
# <a name="get-started-with-the-query-action"></a>Comece com a ação de consulta
Ao utilizar a ação de consulta, pode trabalhar com lotes e matrizes para executar fluxos de trabalho para:

* Crie uma tarefa para todos os registos de alta prioridade a partir de uma base de dados.
* Guarde todos os anexos em PDF para e-mails para um blob do Azure.

Para começar a utilizar a ação de consulta numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Utilize a ação de consulta
Uma ação é uma operação que é executada pelo fluxo de trabalho que está definido numa aplicação lógica. 
[Saiba mais sobre as ações](../connectors/apis-list.md).  

A ação de consulta tem atualmente uma operação, chamada da matriz de filtro, que é exposta no designer. Isto permite-lhe consultar uma matriz e retornar um conjunto de resultados filtrados.

Eis como pode adicioná-lo numa aplicação lógica:

1. Selecione o **novo passo** botão.
2. Escolher **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **filtro** à lista de **matriz de filtro** ação.
   
    ![Selecione a ação de consulta](./media/connectors-native-query/using-action-1.png)
4. Selecione uma matriz a filtrar. (A captura de ecrã seguinte mostra a matriz de resultados de uma pesquisa do Twitter.)
5. Crie uma condição para avaliar em cada item. (Filtra tweets de utilizadores que têm mais de 100 seguidores de captura de ecrã seguinte.)
   
    ![Concluir a ação de consulta](./media/connectors-native-query/using-action-2.png)
   
    A ação produzirá uma nova matriz que contém apenas os resultados que cumprem os requisitos de filtro.
6. Clique no canto superior esquerdo da barra de ferramentas para guardar e a aplicação lógica irá guardar e publicar (ativar).

\* Se estiver chamando um ponto final HTTP e receber uma resposta JSON, utilize o _Parse JSON_ ação para analisar a resposta JSON. Sem realizar este passo _matriz de filtro_ verá apenas o corpo e não compreender a estrutura do JSON payload.

## <a name="query-action"></a>Ação de consulta
Aqui estão os detalhes para a ação que este conector suporta. O conector tem uma ação possível.

| Ação | Descrição |
| --- | --- |
| Filtrar matriz |Avalia uma condição para cada item numa matriz e retorna os resultados |

## <a name="action-details"></a>Detalhes da ação
A ação de consulta vem com uma ação possível. As tabelas seguintes descrevem os campos de entrada necessários e opcionais para a ação e os detalhes de resultado correspondente, que estão associados com a ação.

### <a name="filter-array"></a>Filtrar matriz
Seguem-se os campos de entrada para a ação, o que faz uma solicitação de saída de HTTP.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| De * |de |A matriz a filtrar |
| Condição * |onde |A condição para avaliar para cada item |

<br>

### <a name="output-details"></a>Detalhes da saída
Seguem-se detalhes de saída para a resposta HTTP.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Matriz filtrado |array |Uma matriz que contém um objeto para cada resultado filtrado |

## <a name="next-steps"></a>Passos Seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pode explorar os outros conectores disponíveis no logic apps examinando nossos [lista APIs](apis-list.md).

