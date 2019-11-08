---
title: Oferta de máquina virtual no Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de VM no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 0313c4f2581615e1d1a3a57fc191db7d7cc6e825
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808565"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Esta seção explica como publicar uma nova oferta de máquina virtual no [Azure Marketplace](https://azuremarketplace.microsoft.com). O suporte é fornecido para máquinas virtuais baseadas em Windows e Linux, contendo um disco rígido virtual do sistema operacional (VHD) e zero ou mais VHDs de dados. | ![ícone de máquina virtual](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Visão geral da publicação

O vídeo a seguir, [otimizar sua oferta do Azure Marketplace](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), apresenta uma ampla visão geral do Azure Marketplace, incluindo como publicar neste Marketplace (usando uma solução de máquina virtual), como otimizar a experiência do usuário com a página do seu produto e a experiência de Test Drive opcional, como os leads de usuário são gerados e como você pode consumi-los e otimizar o envolvimento do cliente.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Fluxo do processo de publicação da VM

O diagrama a seguir ilustra as etapas de alto nível na publicação de uma oferta de VM. 

![Processo de publicação da VM](./media/publishvm_001.png)

1. Crie a oferta-todos os detalhes e informações sobre a oferta estão configurados, incluindo a descrição da oferta, materiais de marketing, informações legais, de suporte e especificações de ativos.

2. Crie os ativos comerciais e técnicos – crie os ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (aqui, as VMs e os discos anexados). 

3. Criar a SKU – crie as SKUs associadas associadas à oferta e envie-as.  Um SKU exclusivo é necessário para cada imagem que você planeja publicar. 
 
4. Certificar e publicar a oferta-depois que a oferta e os ativos técnicos forem concluídos, você poderá enviar a oferta. Esse envio iniciará o processo de publicação, no qual a solução é testada, validada, certificada e, em seguida, "fica ativa" no Marketplace.  

## <a name="next-steps"></a>Passos seguintes

Antes de considerar essas etapas, você deve atender aos [requisitos técnicos e de negócios](./cpp-prerequisites.md) para publicar uma VM no Microsoft Azure Marketplace. 
