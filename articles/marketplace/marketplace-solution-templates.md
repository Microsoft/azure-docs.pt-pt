---
title: Oferta de modelo de solução de aplicações do Azure guia de publicação
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: d955f5c11121e8d42bc4b02b75427ab07298e74b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264187"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicações do Azure: Oferta de modelo de solução guia de publicação

Modelos de soluções são uma das principais formas que publicar uma solução no mercado. Utilize este guia para compreender os requisitos para esta oferta. 

Utilizar a aplicação do Azure: tipo de oferta para modelo de solução quando a sua solução requer automação de implantação e configuração adicional para além de uma única VM. Pode automatizar o aprovisionamento de uma ou mais VMs a utilizar aplicações do Azure: modelos de soluções. Também pode aprovisionar os recursos de rede e armazenamento. As aplicações do Azure: modelos de solução oferecem tipo fornece benefícios de automatização para VMs únicas e todas soluções baseadas em IaaS.

Estes modelos de solução são ofertas de transação, o que são implementadas e faturadas através do Marketplace. A chamada a ação que um utilizador vê é a "Obter agora."


## <a name="requirements-for-solution-templates"></a>Requisitos para modelos de soluções

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Faturação e medição    |  Os recursos serão aprovisionados na subscrição do Azure do cliente. Máquinas de virtuais de pay as you go (em PAYGO) será transacionadas com o cliente através da Microsoft, cobrado por meio da subscrição do cliente do Azure (em PAYGO).  <br/> No caso de bring-your-própria licença (BYOL), embora a Microsoft irá cobrar os custos de infraestrutura da subscrição de cliente, será transact seu software diretamente de taxas para o cliente de licenciamento.   |
|Compatível com o Azure de disco rígido virtual (VHD)  |   As VMs devem ser criadas no Windows ou Linux.  Para obter mais informações, [consulte Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de utilização do cliente | Ativar a atribuição de utilização do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de utilização do cliente e como ativá-lo, consulte [atribuição de utilização do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
|  |  |


## <a name="next-steps"></a>Próximos Passos
Se ainda não fez isso, [registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e está a criar uma nova oferta ou trabalhando num já existente, inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
