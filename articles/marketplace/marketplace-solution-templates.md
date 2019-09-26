---
title: Guia de publicação da oferta de modelo de solução de aplicativos do Azure | Azure Marketplace
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 725be2ee239a879be8200d33acaf566b1d42d446
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300343"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicativos do Azure: Guia de publicação da oferta de modelo de solução

Os modelos de solução são uma das principais maneiras de publicar uma solução no Marketplace. Use este guia para entender os requisitos para esta oferta. 

Use o tipo de oferta aplicativo do Azure: modelo de solução quando sua solução exigir implantação adicional e automação de configuração além de uma única VM. Você pode automatizar o provisionamento de uma ou mais VMs usando aplicativos do Azure: modelos de solução. Você também pode provisionar recursos de rede e armazenamento. Aplicativos do Azure: o tipo de oferta de modelos de solução fornece benefícios de automação para VMs únicas e soluções baseadas em IaaS inteiras.

Esses modelos de solução não são ofertas de transações, mas podem ser usados para implantar ofertas de VM pagas cobradas por meio do Marketplace comercial da Microsoft. O plano de ação que um usuário vê é "obter agora".


## <a name="requirements-for-solution-templates"></a>Requisitos para modelos de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais PAYGO (pré-pagas) serão transacionadas com o cliente pela Microsoft, cobradas por meio da assinatura do Azure do cliente (PAYGO).  <br/> No caso de BYOL (traga sua própria licença), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você receberá suas tarifas de licenciamento de software diretamente para o cliente.   |
|VHD (disco rígido virtual) compatível com o Azure  |   As VMs devem ser criadas no Windows ou no Linux.  Para obter mais informações, [consulte criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de uso do cliente | É necessário habilitar a atribuição de uso do cliente em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de uso do cliente e como habilitá-la, consulte [atribuição de uso do cliente do parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Utilizar o Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) é a opção padrão para discos persistentes de VMs de IaaS no Azure. Você deve usar Managed Disks em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [exemplos](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para usar Managed DISKS nos modelos de ARM do Azure para atualizar seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente do Managed Disks para uma conta de armazenamento para publicar o VHD como uma imagem no Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Passos seguintes
Se você ainda não fez isso, [Registre-](https://azuremarketplace.microsoft.com/sell) se no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente, entre no [portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
