---
title: Guia de publicação para ofertas de modelo de solução de aplicações Azure - Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de modelos de solução no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: f62b3478c5c711423913b5918886b43b79ac691d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858320"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guia de publicação para ofertas de modelo de solução de aplicações Azure

Este artigo explica os requisitos para a publicação de ofertas de modelos de solução, que é uma forma de publicar ofertas de aplicações Azure no Azure Marketplace. O modelo de oferta de modelo de solução requer um modelo de Gestor de [Recursos Azure (modelo ARM)](../azure-resource-manager/templates/overview.md) para implementar automaticamente a sua infraestrutura de solução.

Utilize o modelo de oferta de oferta de *solução* de aplicação Azure nas seguintes condições:

- A sua solução requer uma automatização adicional de implementação e configuração para além de uma única máquina virtual (VM), como uma combinação de VMs, networking e recursos de armazenamento.
- Os seus clientes vão gerir a solução por si mesmos.

A chamada à ação que um cliente vê para este tipo de oferta é *Get It Now*.

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Faturação e medição    |  As ofertas de modelos de solução não são ofertas de transações, mas podem ser usadas para implementar ofertas vm pagas que são faturadas através do mercado comercial da Microsoft. Os recursos que o modelo ARM da solução implementa são configurados na subscrição azure do cliente. As máquinas virtuais pay-as-you-go são transacionadas com o cliente através da Microsoft e faturadas através da subscrição do Cliente Azure.<br/> Para a faturação de bring-your-your-license (BYOL), embora a Microsoft fatura os custos de infraestrutura incorridos na subscrição do cliente, você transaciona as suas taxas de licenciamento de software com o cliente diretamente.   |
|Disco rígido virtual compatível com azure (VHD)  |   Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>[Crie uma oferta de aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) (para VHDs Windows).</li><li>[Distribuições linux endossadas em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (para VHDs Linux).</li></ul> |
| Atribuição de utilização dos clientes | Permitir a atribuição de utilização do cliente é necessário em todos os modelos de solução que são publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de utilização do cliente e como o permitir, consulte a atribuição de utilização do cliente parceiro [Azure](./azure-partner-customer-usage-attribution.md).  |
| Utilize discos geridos | [Os discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) são a opção padrão para discos de infraestrutura persistentes como vMs de serviço (IaaS) em Azure. Deve utilizar discos geridos em modelos de solução. <ul><li>Para atualizar os seus modelos de solução, siga a orientação em [Utilizar discos geridos em modelos de Gestor de Recursos Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)e use as [amostras](https://github.com/Azure/azure-quickstart-templates)fornecidas .<br><br> </li><li>Para publicar o VHD como imagem no Azure Marketplace, importe o VHD subjacente dos discos geridos para uma conta de armazenamento utilizando qualquer um dos seguintes métodos:<ul><li>[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [O Azure CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul></ul> |

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a cultivar o seu negócio de cloud com o [Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte Criar uma oferta de [aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) para mais informações.
