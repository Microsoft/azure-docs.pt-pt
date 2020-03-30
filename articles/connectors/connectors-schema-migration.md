---
title: Migrar aplicativos para o mais recente esquema
description: Como migrar as definições de fluxo de aplicações lógicajadas para a mais recente versão esquema de linguagem de definição de fluxo de trabalho
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666793"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrar aplicativos lógicos para a versão mais recente do esquema

Para mover as suas aplicações lógicas existentes para o mais recente esquema, siga estes passos: 

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

2. No menu da sua aplicação lógica, escolha **visão geral**. Na barra de ferramentas, escolha **'Atualizar Schema**' .

   > [!NOTE]
   > Quando escolhe o **Update Schema,** as Aplicações Lógicas Azure executam automaticamente os passos de migração e fornecem a saída de código para si. Pode utilizar esta saída para atualizar a definição de aplicações lógicas. No entanto, certifique-se de que segue as melhores práticas, conforme descrito na secção de **Boas Práticas.**

   ![Atualizar Schema](./media/connectors-schema-migration/update-schema.png)

   A página Update Schema aparece e mostra um link para um documento que descreve as melhorias no novo esquema.

## <a name="best-practices"></a>Melhores práticas

Aqui estão algumas boas práticas para migrar as suas aplicações lógicas para a versão mais recente do esquema:

* Copie o guião migrado para uma nova aplicação lógica. Não sobreponha a versão antiga até completar os seus testes e confirmar que a sua aplicação emmigrada funciona como esperado.

* Teste a sua aplicação lógica **antes** de produzir.

* Depois de terminar a migração, comece a atualizar as suas aplicações lógicas para utilizar as [APIs geridas](../connectors/apis-list.md) sempre que possível. Por exemplo, comece a utilizar o Dropbox v2 em todos os lugares onde utilizar o DropBox v1.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [migrar manualmente as suas aplicações Lógica](../logic-apps/logic-apps-schema-2015-08-01.md)
