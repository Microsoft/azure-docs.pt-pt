---
title: Guia de publicação da oferta do modelo de solução de aplicações do Azure | O Azure Marketplace
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: c2393b6ea9f1a2c2b35be63272743e081f4ae240
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937752"
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

## <a name="next-steps"></a>Passos Seguintes
Se ainda não fez isso, [registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e está a criar uma nova oferta ou trabalhando num já existente, inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
