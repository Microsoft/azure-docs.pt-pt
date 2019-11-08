---
title: Configurações da oferta para uma oferta de aplicativo Power BI | Azure Marketplace
description: Defina as configurações de oferta para uma oferta de aplicativo Power BI para o Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822669"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Guia de configurações da oferta de aplicativos Power BI

Ao abrir a nova página de **oferta** para aplicativos de serviço, você verá primeiro a guia **configurações da oferta** . Você fornece os identificadores primários e o nome da sua oferta nessa guia. Um asterisco (*) indica um campo obrigatório.

![Separador Definições de Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de configurações da oferta 

Na guia **configurações da oferta** , você precisa inserir informações nos campos obrigatórios a seguir. Os campos obrigatórios são indicted por um asterisco (*).

|  Campo        |  Descrição                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta\***  | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador será visível em URLs de produto, modelos de Azure Resource Manager e relatórios de cobrança. O comprimento máximo é de 50 caracteres. Ele pode conter apenas caracteres alfanuméricos minúsculos e traços (-). Ele não pode terminar com um traço. Esse identificador não pode ser alterado depois que uma oferta é ativada. Se a contoso publicar uma oferta com a ID da oferta `sample-SvcApp`, a oferta será atribuída à URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **\* do Publicador** | O identificador exclusivo da sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas à sua ID do editor. Esse valor não pode ser alterado depois que a oferta é salva.                         |
| **Nome\***      | Um nome de exibição para sua oferta. Esse nome aparecerá em AppSource e na Portal do Cloud Partner. O comprimento máximo é de 50 caracteres. Use um nome de marca que seja reconhecível para seu produto. Não inclua o nome da sua organização aqui, a menos que o aplicativo esteja comercializado com esse nome. Se você estiver fornecendo essa oferta em outros sites e publicações, use o mesmo nome em todas as publicações.    <br/>Se você liberar uma oferta durante o período de visualização para aplicativos Power BI, adicione a cadeia de caracteres `(Preview)` no final do nome do aplicativo, desta forma: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passos seguintes

Na próxima guia, você especificará [informações técnicas](./cpp-technical-info-tab.md) para sua oferta.
