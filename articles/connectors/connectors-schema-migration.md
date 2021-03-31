---
title: Migrar aplicativos para o esquema mais recente
description: Como migrar definições de JSON para a versão mais recente do esquema de definição de linguagem de definição de fluxo de trabalho
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87281451"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrar aplicativos lógicos para a versão mais recente do esquema

Para mover as suas aplicações lógicas existentes para o esquema mais recente, siga estes passos: 

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

2. No menu da sua aplicação lógica, escolha **visão geral.** Na barra de ferramentas, escolha **Update Schema**.

   > [!NOTE]
   > Quando escolhe **a Atualização de Esquemas,** a Azure Logic Apps executa automaticamente os passos de migração e fornece a saída de código para si. Pode utilizar esta saída para atualizar a definição de aplicação lógica. No entanto, certifique-se de que segue as melhores práticas como descrito na secção **de Boas Práticas.**

   ![Atualizar Schema](./media/connectors-schema-migration/update-schema.png)

   A página 'Update Schema' aparece e mostra um link para um documento que descreve as melhorias no novo esquema.

## <a name="best-practices"></a>Melhores práticas

Aqui estão algumas das melhores práticas para migrar as suas aplicações lógicas para a versão mais recente do esquema:

* Copie o script migrado para uma nova aplicação lógica. Não substitua a versão antiga até completar os seus testes e confirmar que a sua aplicação migratória funciona como esperado.

* Teste a sua aplicação lógica **antes** de colocar em produção.

* Depois de terminar a migração, comece a atualizar as suas aplicações lógicas para utilizar as [APIs geridas](../connectors/apis-list.md) sempre que possível. Por exemplo, comece a usar o Dropbox v2 em todos os lugares que utiliza o DropBox v1.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [migrar manualmente as suas aplicações Lógicas](../logic-apps/logic-apps-schema-2016-04-01.md)

