---
title: Oferta de configurações para uma oferta de Aplicação Power BI [ Mercado Azure
description: Configure as definições de oferta para uma oferta de Aplicação Power BI para o mercado microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 1bf8abb2d8ac6c1c9d2e03ef5e4f1edce530cbdb
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985767"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI Apps Oferecem separador de configurações

>[!Important]
>A partir de 13 de abril de 2020, começaremos a movimentar a gestão das suas ofertas de aplicações Power BI para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na criação de [aplicações Power BI](https://aka.ms/AzureCreatePBIServiceApp) para gerir as suas ofertas migratórias.

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
