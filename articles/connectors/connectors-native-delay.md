---
title: Adicionar um atraso no logic apps | Documentos da Microsoft
description: Descrição geral do atraso e atraso-até ações e como utilizá-los com uma aplicação lógica do Azure.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448034"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Comece com o atraso e o atraso-até ações
Ao utilizar o atraso e "atraso-até" ações, pode completar cenários de fluxo de trabalho.

Pode, por exemplo:

* Aguarde até um dia da semana para enviar uma atualização de estado por e-mail.
* Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo termine antes de a retomar e obter o resultado.

Para começar a utilizar a ação de atraso numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Utilizar as ações de atraso

Uma ação é uma operação que é executada pelo fluxo de trabalho que está definido numa aplicação lógica. 
[Saiba mais sobre as ações](../connectors/apis-list.md).

Eis um exemplo de sequência de como usar um passo de atraso numa aplicação lógica:

1. Depois de adicionar um acionador, clique em **novo passo** para adicionar uma ação.
2. Procure **atraso** para apresentar as ações de atraso. Neste exemplo, selecionaremos **atraso**.
   
    ![Ações de atraso](./media/connectors-native-delay/using-action-1.png)
3. Conclua qualquer uma das propriedades de ação para configurar o atraso.
   
    ![Configuração de atraso](./media/connectors-native-delay/using-action-2.png)
4. Clique em **guardar** para publicar e ativar a aplicação lógica.

## <a name="action-details"></a>Detalhes da ação
O acionador de periodicidade tem as seguintes propriedades que podem ser configuradas.

### <a name="delay-action"></a>Ação de atraso
Esta ação atrasa a execução de um determinado intervalo de tempo.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Contagem de * |count |O número de unidades de tempo de atraso |
| Unit* |unidade |A unidade de tempo: `Second`, `Minute`, `Hour`, ou `Day` |

<br>

### <a name="delay-until-action"></a>Atraso-até a ação
Esta ação atrasa a execução até uma data/hora especificada.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Ano * |timestamp |Ano até à atraso até (GMT) |
| Mês * |timestamp |O mês de atraso até (GMT) |
| Dia * |timestamp |O dia de atraso até (GMT) |

<br>

## <a name="next-steps"></a>Passos Seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pode explorar os outros conectores disponíveis no logic apps examinando nossos [lista APIs](apis-list.md).

