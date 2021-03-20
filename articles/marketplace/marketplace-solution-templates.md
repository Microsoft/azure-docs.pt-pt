---
title: Guia de publicação para ofertas de modelo de solução de aplicações Azure - Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de modelos de solução no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879348"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guia de publicação para ofertas de modelo de solução de aplicações Azure

Este artigo explica os requisitos para a publicação de ofertas de modelos de solução, que é uma forma de publicar ofertas de aplicações Azure no Azure Marketplace. O tipo de oferta de modelo de solução requer um [modelo de Gestor de Recursos Azure (modelo ARM)](../azure-resource-manager/templates/overview.md) para implantar automaticamente a sua infraestrutura de solução.

Utilize o modelo de oferta *de solução* de aplicação Azure nas seguintes condições:

- A sua solução requer uma automatização adicional de implantação e configuração para além de uma única máquina virtual (VM), como uma combinação de VMs, networking e recursos de armazenamento.
- Os seus clientes vão gerir a solução por si mesmos.

A opção de listagem que um cliente vê para este tipo de oferta é *Get It Now*.

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Faturação e medição    |  As ofertas de modelos de solução não são ofertas de transações, mas podem ser usadas para implementar ofertas de VM pagas que são faturadas através do mercado comercial da Microsoft. Os recursos que o modelo ARM da solução implementa são configurados na subscrição Azure do cliente. As máquinas virtuais pay-as-you-go são transacionadas com o cliente através da Microsoft e faturadas através da subscrição Azure do cliente.<br/> Para a faturação da sua própria licença (BYOL), embora a Microsoft faça contas de custos de infraestrutura que são incorridos na subscrição do cliente, transaciona diretamente as suas taxas de licenciamento de software com o cliente.   |
|Disco rígido virtual compatível com Azure (VHD)  |   Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>[Crie uma oferta de aplicação Azure](./create-new-azure-apps-offer.md) (para VHDs windows).</li><li>[Distribuição linux endossada em Azure](../virtual-machines/linux/endorsed-distros.md) (para Linux VHDs).</li></ul> |
| Atribuição de utilização dos clientes | É necessária a atribuição de utilização do cliente em todos os modelos de solução que são publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de utilização do cliente e como a capacitar, consulte a [atribuição de utilização do cliente do parceiro Azure](./azure-partner-customer-usage-attribution.md).  |
| Utilizar discos geridos | [Os discos geridos](../virtual-machines/managed-disks-overview.md) são a opção padrão para discos de infraestrutura persistidos como um VMs de serviço (IaaS) em Azure. Deve utilizar discos geridos em modelos de solução. <ul><li>Para atualizar os seus modelos de solução, siga as orientações em [Utilizar discos geridos nos modelos Azure Resource Manager](../virtual-machines/using-managed-disks-template-deployments.md), e use as [amostras fornecidas](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Para publicar o VHD como uma imagem no Azure Marketplace, importe o VHD subjacente dos discos geridos para uma conta de armazenamento utilizando qualquer um dos seguintes métodos:<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [A CLI do Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, aprenda a [fazer crescer o seu negócio na nuvem com o Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)

Para se inscrever e começar a trabalhar no Partner Center:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [Criar uma oferta de aplicação Azure](./create-new-azure-apps-offer.md) para mais informações.