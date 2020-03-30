---
title: Modelo de solução de aplicações Azure oferece guia de publicação / Mercado Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Mercado Azure.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288738"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicações Azure: Guia de publicação de oferta de modelo de solução

Os modelos de solução são uma das principais formas de publicar uma solução no Mercado. Utilize este guia para compreender os requisitos desta oferta. 

Utilize a aplicação Azure: modelo de oferta de solução quando a sua solução requer uma implementação adicional e automatização de configuração para além de um único VM. Pode automatizar o fornecimento de um ou mais VMs utilizando aplicações Azure: modelos de solução. Também pode fornecer recursos de networking e armazenamento. Aplicações Azure: modelos de solução oferecem tipo de oferta fornece benefícios de automação para VMs individuais e soluções inteiras baseadas em IaaS.

Estes modelos de solução não são ofertas de transações, mas podem ser usados para implementar ofertas vm pagas faturadas através do mercado comercial da Microsoft. A chamada à ação que um utilizador vê é "Get It Now".


## <a name="requirements-for-solution-templates"></a>Requisitos para modelos de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Faturação e medição    |  Os recursos serão aprovisionados na subscrição do Azure do cliente. As máquinas virtuais pay-as-you-go (PAYGO) serão transacionadas com o cliente através da Microsoft, faturadas através da subscrição azure do cliente (PAYGO).  <br/> No caso de trazer a sua própria licença (BYOL), enquanto a Microsoft irá cobrar os custos de infraestrutura incorridos na subscrição do cliente, irá transacionar as suas taxas de licenciamento de software diretamente para o cliente.   |
|Disco rígido virtual compatível com azure (VHD)  |   Os VMs devem ser construídos em Windows ou Linux.  Para mais informações, [consulte Criar um VHD compatível com Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de utilização do cliente | Permitir a atribuição de utilização do cliente é necessária em todos os modelos de solução publicados no Mercado Azure. Para obter mais informações sobre a atribuição de utilização do cliente e como o permitir, consulte a atribuição de utilização do cliente parceiro [Azure](./azure-partner-customer-usage-attribution.md).  |
| Utilizar o Managed Disks | [Os Discos Geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) são a opção padrão para discos persificados de VMs IaaS em Azure. Deve utilizar discos geridos em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [amostras](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para a utilização de discos geridos nos modelos Azure ARM para atualizar os seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente dos Discos Geridos para uma conta de armazenamento para publicar o VHD como imagem no Mercado: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez, [registe-se](https://azuremarketplace.microsoft.com/sell) no mercado.

Se estiver registado e estiver a criar uma nova oferta ou a trabalhar numa já existente, inscreva-se no [Portal cloud partner](https://cloudpartner.azure.com) para criar ou completar a sua oferta.
