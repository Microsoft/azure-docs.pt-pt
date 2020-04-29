---
title: Pré-requisitos da máquina virtual para o Microsoft Azure Mercado Azure
description: Lista de pré-requisitos necessários para publicar uma oferta VM ao Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80277621"
---
# <a name="virtual-machine-prerequisites"></a>Pré-requisitos da máquina virtual

Este artigo lista os requisitos técnicos e empresariais que deve cumprir antes de poder publicar uma oferta VM no [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/)  Se ainda não o fez, reveja o Guia de Publicação da [Oferta Virtual de Máquinas.](../../marketplace-virtual-machines.md)


## <a name="technical-requirements"></a>Requisitos técnicos

Os pré-requisitos técnicos para a publicação de uma solução de máquina virtual (VM) são simples:

- Deve ter uma conta Azure ativa. Se não tiver um, pode inscrever-se no site do [Microsoft Azure](https://azure.microsoft.com).  
- Deve ter um ambiente configurado para suportar o desenvolvimento de VM Windows ou Linux.  Para mais informações, consulte o site de documentação VM associado:
    - [Documentação sobre as VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentação sobre as VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Requisitos empresariais

Os requisitos comerciais incluem obrigações processuais, contratuais e legais: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Deve ser um Editor de Cloud Marketplace registado.  Se ainda não estiver registado, siga os passos do artigo [Torne-se um Editor do Mercado de Nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Deve utilizar a mesma conta de registo do Microsoft Developer Center para iniciar sessão no [Portal do Parceiro cloud](https://cloudpartner.azure.com).
    > Você deve ter apenas uma conta Microsoft para as suas ofertas do Azure Marketplace. Não deve ser específico para serviços ou ofertas individuais.
    
- A sua empresa (ou sua subsidiária) deve estar localizada numa região de venda/região apoiada pelo Azure Marketplace.  Para obter uma lista atual destes países/regiões, consulte as Políticas de [Participação no Mercado do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- O seu produto deve ser licenciado de forma compatível com modelos de faturação suportados pelo Azure Marketplace.  Para mais informações, consulte [as opções de Faturação no Mercado Azure.](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace) 
- É responsável por disponibilizar apoio técnico aos clientes de forma comercialmente razoável. Este apoio pode ser gratuito, pago ou através de abordagens comunitárias.
- É responsável pelo licenciamento do seu software e de quaisquer dependências de software de terceiros.
- Deve fornecer conteúdo que satisfaça os critérios para a sua oferta ser listada no Azure Marketplace e no portal Azure. <!-- TD: Meaning/links? -->
- Deve concordar com os termos das Políticas de Participação no [Mercado do Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) e do Acordo de Editor.
- Deve cumprir os [Termos de Utilização do Website Microsoft Azure,](https://azure.microsoft.com/support/legal/website-terms-of-use/) [a Declaração](https://privacy.microsoft.com/privacystatement)de Privacidade da Microsoft e o Acordo de Programa Certificado [Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Passos seguintes

Depois de ter cumprido estes pré-requisitos, pode [criar a sua oferta vm](./cpp-create-offer.md).
