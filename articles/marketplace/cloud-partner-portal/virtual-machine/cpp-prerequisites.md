---
title: Pré-requisitos de máquina virtual para o Microsoft Azure | Documentos da Microsoft
description: Lista de pré-requisitos necessários para publicar uma oferta VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010857"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos de máquina virtual

Este artigo lista os dois o técnico e requisitos empresariais que tem de cumprir antes de podem publicar uma oferta VM para o [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Se ainda não o fez, reveja os [guia de publicação da oferta de Máquina Virtual](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Requisitos técnicos

As pré-requisitos técnicos para a publicação de uma solução de máquina virtual (VM) são simples:

- Tem de ter uma conta do Azure Active Directory. Se não tiver um, pode inscrever-se com o [site do Microsoft Azure](https://azure.microsoft.com).  
- Tem de ter um ambiente configurado para suportar o desenvolvimento do Windows ou VM do Linux.  Para obter mais informações, consulte o site de documentação de VM associado:
    - [Documentação sobre as VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação sobre as VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos comerciais

Os requisitos comerciais incluem obrigações de procedimentos, contratuais e legais: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Tem de ser um publicador de Marketplace na nuvem registados.  Se não estiver registrado ainda, siga os passos no artigo [se tornar um publicador de Marketplace da Cloud](../../become-publisher.md).

    > [!NOTE]
    > Deve utilizar a mesma conta de registo do Microsoft Developer Center para iniciar sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com).
    > Deve ter apenas uma conta Microsoft para as ofertas do Azure Marketplace. Não deve ser específica de ofertas ou serviços individuais.
    
- A sua empresa (ou respetiva subsidiária) tem de estar localizada num destino de venda--país suportado pelo Azure Marketplace.  Para obter uma lista atual nestes países, consulte [políticas de participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Seu produto têm de estar licenciado de forma que seja compatível com modelos de faturação suportados pelo Azure Marketplace.  Para obter mais informações, consulte [opções de faturação no Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- É responsável por disponibilizar suporte técnico aos clientes de forma que sejam comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de Comunidade.
- É responsável por licenciamento seu software e as dependências de software de terceiros.
- Tem de fornecer conteúdo que cumpre os critérios para a sua oferta seja incluída no Azure Marketplace e no portal do Azure. <!-- TD: Meaning/links? -->
- Tem de concordar com os termos do [políticas de participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e contrato de publicador.
- Deve estar em conformidade com o [Microsoft Azure site termos de utilização](https://azure.microsoft.com/support/legal/website-terms-of-use/), [declaração de privacidade do Microsoft](https://privacy.microsoft.com/privacystatement), e [contrato do Microsoft Azure Certified programa](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Passos Seguintes

Depois de ter cumprido estes pré-requisitos, poderá [criar a sua oferta VM](./cpp-create-offer.md).
