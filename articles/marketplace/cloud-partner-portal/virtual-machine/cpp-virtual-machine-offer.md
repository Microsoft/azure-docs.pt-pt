---
title: Oferta virtual de máquinas no Mercado Azure
description: Visão geral do processo de publicação de uma oferta VM no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: 939a5f6a4c70a8a1229507e0357cb588c17152fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288772"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Esta secção explica como publicar uma nova oferta de máquinavirtual ao [Azure Marketplace.](https://azuremarketplace.microsoft.com) O suporte é fornecido tanto para máquinas virtuais baseadas no Windows como para as máquinas virtuais baseadas em Linux, contendo um disco rígido virtual do sistema operativo (VHD) e zero ou mais VHDs de dados. | ![ícone de máquina virtual](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Visão geral de publicação

O seguinte vídeo, [Otimize Your Azure Marketplace Offer,](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player)apresenta uma visão geral ampla do Mercado Azure, incluindo como publicar neste mercado (utilizando uma solução de máquina virtual), como otimizar a experiência do utilizador com a sua página de produto e experiência opcional de Test Drive, como os suportes do utilizador são gerados e como pode consumi-los, e otimizar o envolvimento do cliente.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Fluxo de processo de publicação VM

O diagrama que se segue ilustra os passos de alto nível na publicação de uma oferta vm. 

![Processo de publicação VM](./media/publishvm_001.png)

1. Criar a oferta - Todos os detalhes e informações sobre a oferta estão configurados, incluindo a descrição da oferta, materiais de marketing, informações legais, de suporte e especificações de ativos.

2. Criar os ativos empresariais e técnicos - Criar os ativos empresariais (documentos legais e materiais de marketing) e os ativos técnicos para a solução associada (aqui, os VMs e discos anexos). 

3. Crie o SKU - Crie os SKU(s) associados associados à oferta e submeta-os.  É necessário um SKU único para cada imagem que está a planear publicar. 
 
4. Certifique e publique a oferta - Assim que a oferta e os ativos técnicos estiverem concluídos, pode submeter a oferta. Esta submissão iniciará o processo de publicação, no qual a solução é testada, validada, certificada e depois "vai ao vivo" no mercado.  

## <a name="next-steps"></a>Passos seguintes

Antes de considerar estes passos, deve cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para a publicação de um VM no Microsoft Azure Marketplace. 
