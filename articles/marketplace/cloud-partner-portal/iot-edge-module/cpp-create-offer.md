---
title: Criar uma oferta de módulo do Azure IoT Edge | Documentos da Microsoft
description: Como publicar um novo módulo do IoT Edge para o Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ee0c0add2895b30a60db577cc11c83a0e00bb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854777"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Criar uma nova oferta de módulo do IoT Edge com o Portal de parceiro de Cloud

Este artigo descreve como criar e publicar uma entrada de oferta de módulo do IoT Edge para o Azure Marketplace. Cada oferta é apresentada como uma entidade própria no Azure Marketplace e está associada um ou mais SKUs.  Uma oferta de módulo do IoT Edge é composta pelos seguintes agrupamentos de recursos e serviços de suporte:

|  **Grupo de recursos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKUs            |  A menor unidade implementável de uma oferta. Uma única oferta (a classe product) pode ter várias SKUs associadas com a oferta. Pode utilizar o SKU para diferenciar entre funcionalidades suportadas e modelos de faturação. |
|  Marketplace       | Contém o marketing, legais e provocar ativos de gestão e as especificações.  <ul><li> Recursos de marketing incluem o nome da oferta, descrição e logotipos</li> <li> Recursos legais incluem uma política de privacidade, termos de utilização e outra documentação legal</li>  <li> Política de gestão levar permite que especifique como lidar com leva a partir do portal de utilizador final do Azure Marketplace.</li> </ul> |
| Suporte            | Contém informações de contacto e política de suporte |


## <a name="new-offer-form"></a>Novo formulário de oferta 

Inicie sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com/)e, em seguida, selecione **+ nova oferta** na barra de menu à esquerda. No menu da nova oferta, selecione **módulos do IoT Edge** para apresentar o **nova oferta** formam e iniciar o processo de definição de ativos para uma nova oferta de módulo Edge ioT. 

![Novo módulo do IoT Edge oferecer as seleção de interface do usuário](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Passos Seguintes

O **nova oferta** página para o tipo de oferta de módulo do IoT Edge fornece um conjunto de guias e os campos de formulário que irá utilizar para criar uma nova oferta. Cada um dos seguintes artigos explica como utilizar o separador para definir os grupos de recursos e serviços de suporte para a sua nova oferta de módulo do IoT Edge.

- [Separador Definições da oferta](./cpp-offer-settings-tab.md)
- [Separador de SKUs](./cpp-skus-tab.md)
- [Separador Marketplace](./cpp-marketplace-tab.md)
- [Separador Suporte](./cpp-support-tab.md)
