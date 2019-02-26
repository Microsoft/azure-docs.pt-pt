---
title: Definições para uma aplicação do Power BI oferece - Azure Marketplace da oferta | Documentos da Microsoft
description: Configure definições de oferta para uma oferta de aplicação do Power BI para o mercado do Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819604"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Separador de definições de oferta de aplicações de BI de energia

Quando abre o **nova oferta** página para o serviço de aplicações, primeiro veja o **oferecer definições** separador. Fornecer os identificadores de principal e o nome da sua oferta neste separador. Um asterisco (*) indica um campo obrigatório.

![Separador de definições da oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de definições da oferta 

Sobre o **oferecer definições** guia, tem de introduzir informações nos seguintes campos obrigatórios:

|  Campo        |  Descrição                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID de oferta**  | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador estará visível no produto URLs, modelos do Azure Resource Manager, e relatórios de faturação. O comprimento máximo é de 50 carateres. Pode conter apenas carateres alfanuméricos em minúsculas e hífenes (-). Não pode terminar com um traço. Este identificador não pode ser alterado depois de uma oferta entra no ar. Se a Contoso publica uma oferta com o ID da oferta `sample-SvcApp`, a oferta é atribuída o URL de AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publicador** | Identificador exclusivo da sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser alterado depois da oferta é guardada.                         |
| **Nome**      | Nome a apresentar para a sua oferta. Este nome será apresentado no AppSource e no Portal de parceiros de nuvem. O comprimento máximo é de 50 carateres. Utilize um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que a aplicação é comercializada com esse nome. Se estiver fornecendo esta oferta em outros Web sites e publicações, utilize o mesmo nome em todas as publicações.    <br/>Se libera uma oferta durante o período de pré-visualização para aplicações do Power BI, adicione a cadeia de caracteres `(Preview)` no final do nome da sua aplicação, como este: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passos Seguintes

No separador seguinte, vai especificar [informações técnicas](./cpp-technical-info-tab.md) para a sua oferta.
