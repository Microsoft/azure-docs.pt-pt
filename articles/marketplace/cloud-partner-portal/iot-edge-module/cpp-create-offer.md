---
title: Crie uma oferta de módulo Sem Borda Azure IoT Mercado Azure
description: Como publicar um novo Módulo IoT Edge para o Mercado.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148072"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Crie uma nova oferta de móduloS IoT Edge com o Portal do Parceiro cloud

>[!Important]
>A partir de 13 de abril de 2020, começaremos a movimentar a gestão do seu módulo IoT Edge oferece ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) para gerir as suas ofertas migratórias.

Este artigo descreve como criar e publicar um módulo IoT Edge oferece entrada para o Mercado Azure. Cada oferta aparece como sua própria entidade no Azure Marketplace e está associada a uma ou mais SKUs.  Uma oferta de módulo IoT Edge é composta pelos seguintes agrupamentos de ativos e serviços de apoio:

|  **Grupo de ativos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKU            |  A menor unidade implantável de uma oferta. Uma única oferta (classe de produto) pode ter várias UsC associadas à oferta. Pode utilizar SKUs para diferenciar entre funcionalidades suportadas e modelos de faturação. |
|  Marketplace       | Contém ativos e especificações de marketing, gestão legal e de chumbo.  <ul><li> Os ativos de marketing incluem nome, descrição e logotipos de oferta</li> <li> Os ativos legais incluem uma política de privacidade, termos de uso e outra documentação legal</li>  <li> A política de gestão de chumbo permite especificar como lidar com os cabos do portal de utilizador final do Mercado Azure.</li> </ul> |
| Suporte            | Contém informações de contacto de suporte e políticas |


## <a name="new-offer-form"></a>Formulário New Offer 

Inscreva-se no Portal do [Parceiro cloud](https://cloudpartner.azure.com/)e, em seguida, selecione + **Nova Oferta** na barra de menu salimos à esquerda. No menu New Offer, selecione **Módulos IoT Edge** para exibir o formulário **New Offer** e iniciar o processo de definição de ativos para uma nova oferta do Módulo IoT Edge. 

![Novo módulo IoT Edge oferece seleção de interface de utilizador](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Passos seguintes

A página **New Offer** para o tipo de oferta do módulo IoT Edge fornece um conjunto de separadores e campos de formulário que você vai usar para criar uma nova oferta. Cada um dos seguintes artigos explica como usar o separador para definir os grupos de ativos e serviços de apoio para a sua nova oferta de módulos IoT Edge.

- [Separador Definições de Oferta](./cpp-offer-settings-tab.md)
- [Separador de SKUs](./cpp-skus-tab.md)
- [Separador Marketplace](./cpp-marketplace-tab.md)
- [Separador Suporte](./cpp-support-tab.md)
