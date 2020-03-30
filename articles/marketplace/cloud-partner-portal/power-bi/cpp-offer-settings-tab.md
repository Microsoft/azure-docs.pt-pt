---
title: Oferta de configurações para uma oferta de Aplicação Power BI [ Mercado Azure
description: Configure as definições de oferta para uma oferta de Aplicação Power BI para o mercado microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286358"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI Apps Oferecem separador de configurações

Ao abrir a página **New Offer** para aplicações de serviço, vê primeiro o separador Definições de **Oferta.** Fornece os principais identificadores e o nome da sua oferta neste separador. Um asterisco (*) indica um campo necessário.

![Separador Definições de Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Oferecer campos de Definições 

No separador Definições de **Oferta,** tem de introduzir informações nos seguintes campos necessários. Os campos necessários são indiciados por um asterisco (*).

|  Campo        |  Descrição                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta\***  | Um identificador único (dentro de um perfil de editor) para a oferta. Este identificador será visível em URLs de produto, modelos de Gestor de Recursos Azure e relatórios de faturação. O comprimento máximo é de 50 caracteres. Só pode conter caracteres alfanuméricos minúsculos e traços (-). Não pode terminar com um traço. Este identificador não pode ser mudado depois de uma oferta ir ao vivo. Se A Contoso publicar uma `sample-SvcApp`oferta com ID de oferta, a oferta é atribuída ao URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource .      |
| **Editora\*** | Identificador único da sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem estar associadas à sua identificação do editor. Este valor não pode ser alterado depois de a oferta ser guardada.                         |
| **Nome\***      | Um nome de exibição para a sua oferta. Este nome aparecerá no AppSource e no Portal do Parceiro cloud. O comprimento máximo é de 50 caracteres. Use uma marca que seja reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que a aplicação seja comercializada com esse nome. Se estiver a oferecer esta oferta noutros websites e publicações, use o mesmo nome em todas as publicações.    <br/>Se lançar uma oferta durante o período de pré-visualização das Aplicações Power BI, adicione a corda `(Preview)` no final do nome da sua aplicação, assim: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passos seguintes

No separador seguinte, irá especificar [informações técnicas](./cpp-technical-info-tab.md) para a sua oferta.
