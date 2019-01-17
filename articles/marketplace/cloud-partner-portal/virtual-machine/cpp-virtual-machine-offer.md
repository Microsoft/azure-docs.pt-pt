---
title: Oferta de máquina virtual no Azure Marketplace | Documentos da Microsoft
description: Descrição geral do processo de publicação de uma oferta VM no Azure Marketplace.
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: bbe757ccd1d6a37cbcf04f3ecd6dd088ef1ff211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353027"
---
# <a name="virtual-machine-offer"></a>Oferta de máquina virtual

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Esta secção explica como publicar uma nova oferta de máquina virtual para o [do Azure Marketplace](https://azuremarketplace.microsoft.com). O suporte é fornecido para máquinas de virtuais baseados em Linux e Windows, que contém um sistema operativo disco virtual (VHD) e dados de zero ou mais VHDs. | ![ícone de máquina virtual](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Descrição geral de publicação

O vídeo seguinte, [otimizar Your Azure Marketplace oferece](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), apresenta uma ampla visão geral do Azure Marketplace, incluindo como publicar no marketplace (usando uma solução de máquina virtual), como otimizar a experiência do usuário com a sua página de produto e a experiência de teste de unidade opcional, como oportunidades potenciais do utilizador são gerados e como pode aceder aos mesmos e otimizar o envolvimento do cliente.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Fluxo do processo de publicação de VM

O diagrama seguinte ilustra as etapas de alto nível em publicar uma oferta VM. 

![Processo de publicação de VM](./media/publishvm_001.png)

1. Criar a oferta - todos os detalhes e informações sobre a oferta são configuradas, incluindo a descrição da oferta, materiais, legal, especificações de recursos e informações de suporte de marketing.

2. Criar os recursos técnicos e comerciais - criar os ativos empresariais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (aqui, as VMs e os discos ligados). 

3. Criar o SKU - criar o SKU associado de (s) associadas à oferta e enviá-los.  Um SKU exclusivo é necessário para cada imagem que pretende publicar. 
 
4. Certificar e publicar a oferta - assim que a oferta e os ativos técnicos são concluídos, pode enviar a oferta. Esta submissão será iniciado o processo de publicação, na qual a solução é testada, validado, certified, em seguida, "entrar no ar" no marketplace.  

## <a name="next-steps"></a>Passos Seguintes

Antes de considerar estes passos, tem de cumprir os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para publicar uma VM para o Microsoft Azure Marketplace. 
