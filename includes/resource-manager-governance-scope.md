---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: dc4281c17b92e1720625764a52a34a94d6f296ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184190"
---
Antes de criar quaisquer itens, vamos rever o conceito de âmbito. O Azure oferece quatro níveis de gestão: grupos de gestão, subscrição, grupo de recursos e recurso. Os [Grupos de gestão](../articles/billing/billing-enterprise-mgmt-group-overview.md) estão numa versão de pré-visualização. A imagem seguinte mostra um exemplo destas camadas.

![Âmbito](./media/resource-manager-governance-scope/scope-levels.png)

Pode aplicar as definições de gestão em qualquer um destes níveis de âmbito. O nível que selecionar determina o quanto a definição é aplicada. Os níveis inferiores herdam as definições de níveis mais altos. Quando aplica uma definição à subscrição, essa definição é aplicada a todos os grupos de recursos e recursos na sua subscrição. Quando aplica uma definição ao grupo de recursos, essa definição é aplicada ao grupo de recursos e todos os respetivos recursos. No entanto, outro grupo de recursos não tem essa definição.

Normalmente, faz sentido aplicar definições críticas em níveis mais altos e requisitos específicos do projeto em níveis inferiores. Por exemplo, deve certificar-se de que todos os recursos da sua organização são implementados em determinadas regiões. Para cumprir este requisito, aplique uma política à subscrição que especifica as localizações permitidas. Como os outros utilizadores na sua organização adicionam novos grupos de recursos e recursos, as localizações permitidas são impostas automaticamente. 
