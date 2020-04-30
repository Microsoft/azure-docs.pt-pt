---
title: Modelo de solução de aplicações Azure oferece guia de publicação / Mercado Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084860"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplicações Azure: Modelo de solução oferece requisitos de publicação

Este artigo explica os requisitos para o tipo de oferta de modelo de solução, que é uma forma de publicar uma oferta de aplicação Azure no Azure Marketplace. O modelo de oferta de modelo de solução requer um modelo de Gestor de [Recursos Azure (modelo ARM)](../azure-resource-manager/templates/overview.md) para implementar automaticamente a sua infraestrutura de solução.

Utilize o modelo de oferta de solução de aplicação Azure quando forem necessárias as seguintes condições:

- A sua solução requer uma automatização adicional de implementação e configuração para além de um único VM, como uma combinação de VMs, networking e recursos de armazenamento.
- O seu cliente vai gerir a solução por si mesmo.

A chamada à ação que um utilizador vê para este tipo de oferta é "Get It Now".

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Faturação e medição    |  As ofertas de modelos de solução não são ofertas transatas, mas podem ser usadas para implementar ofertas vm pagas faturadas através do mercado comercial da Microsoft. Os recursos que o modelo ARM da solução implanta será aprovisionado na subscrição azure do cliente. As máquinas virtuais pay-as-you-go (PAYGO) serão transacionadas com o cliente através da Microsoft, faturadas através da subscrição do Cliente Azure.<br/> No caso de trazer a sua própria licença (BYOL), enquanto a Microsoft irá cobrar os custos de infraestrutura incorridos na subscrição do cliente, irá transacionar as suas taxas de licenciamento de software diretamente para o cliente.   |
|Disco rígido virtual compatível com azure (VHD)  |   Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>Criar uma oferta de [aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md)(para VHDs Windows)</li><li>[Distribuições linux endossadas em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (para VHDs Linux).</li></ul> |
| Atribuição de utilização do cliente | Permitir a atribuição de utilização do cliente é necessária em todos os modelos de solução publicados no Mercado Azure. Para obter mais informações sobre a atribuição de utilização do cliente e como o permitir, consulte a atribuição de utilização do cliente parceiro [Azure](./azure-partner-customer-usage-attribution.md).  |
| Utilizar o Managed Disks | [Os Discos Geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) são a opção padrão para discos persificados de VMs IaaS em Azure. Deve utilizar discos geridos em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [amostras](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para a utilização de discos geridos nos modelos Azure ARM para atualizar os seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente dos Discos Geridos para uma conta de armazenamento para publicar o VHD como imagem no Mercado: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passos seguintes

- Se ainda não o fez, [saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o Mercado Azure.
- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- [Crie uma oferta de aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) para mais informações.
