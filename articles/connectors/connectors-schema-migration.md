---
title: Migrar aplicativos para o esquema mais recente
description: Como migrar seus aplicativos lógicos para a versão mais recente do esquema
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: dc967c90f28b29a63a1f26d0aae86b6d769c1ccf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786933"
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
