---
title: Migrar aplicativos para o esquema mais recente
description: Como migrar definições JSON de fluxo de trabalho do aplicativo lógico para a versão mais recente do esquema da linguagem de definição de fluxo de trabalho
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666793"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrar aplicativos lógicos para a versão mais recente do esquema

Para mover seus aplicativos lógicos existentes para o esquema mais recente, siga estas etapas: 

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

2. No menu do aplicativo lógico, escolha **visão geral**. Na barra de ferramentas, escolha **Atualizar esquema**.

   > [!NOTE]
   > Quando você escolhe **Atualizar esquema**, os aplicativos lógicos do Azure executam automaticamente as etapas de migração e fornecem a saída de código para você. Você pode usar essa saída para atualizar a definição do aplicativo lógico. No entanto, certifique-se de seguir as práticas recomendadas, conforme descrito na seção **práticas recomendadas** a seguir.

   ![Atualizar esquema](./media/connectors-schema-migration/update-schema.png)

   A página Atualizar esquema é exibida e mostra um link para um documento que descreve as melhorias no novo esquema.

## <a name="best-practices"></a>Melhores práticas

Aqui estão algumas práticas recomendadas para migrar seus aplicativos lógicos para a versão mais recente do esquema:

* Copie o script migrado para um novo aplicativo lógico. Não substitua a versão antiga até concluir o teste e confirme se o aplicativo migrado funciona conforme o esperado.

* Teste seu aplicativo lógico **antes** de colocá-lo em produção.

* Depois de concluir a migração, comece a atualizar seus aplicativos lógicos para usar as [APIs gerenciadas](../connectors/apis-list.md) sempre que possível. Por exemplo, comece a usar o Dropbox V2 em todos os lugares em que você usa o DropBox v1.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [migrar manualmente seus aplicativos lógicos](../logic-apps/logic-apps-schema-2015-08-01.md)
