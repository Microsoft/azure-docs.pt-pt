---
title: Cargas de trabalho do Chapéu Vermelho na visão geral do Azure Microsoft Docs
description: Conheça as ofertas de produtos Red Hat disponíveis no Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78970177"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de trabalho do Chapéu Vermelho em Azure

As cargas de trabalho do Chapéu Vermelho são suportadas através de uma variedade de ofertas no Azure. As imagens red hat enterprise Linux (RHEL) estão no centro das cargas de trabalho da RHEL, assim como a Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Imagens de Linux da Empresa de Chapéu Vermelho

O Azure oferece uma ampla oferta de imagens RHEL no Azure. Estas imagens são disponibilizadas através de dois modelos de licenciamento diferentes: pay-as-you-go e bring-your-your-own-subscription (BYOS). Novas imagens RHEL no Azure são publicadas quando novas versões RHEL são lançadas e atualizadas ao longo dos seus ciclos de vida, se necessário.

### <a name="pay-as-you-go-images"></a>Imagens pay-as-you-go

O Azure oferece uma variedade de imagens pagas rHEL. Estas imagens têm direito ao RHEL e estão ligadas a uma fonte de atualizações (Red Hat Update Infrastructure). Estas imagens cobram uma taxa premium pelo direito rhel e atualizações. As variantes de imagem pay-as-you-go da RHEL incluem:

* RHEL padrão.
* RHEL para SAP.
* RHEL para SAP com Serviços de Alta Disponibilidade e Atualização.

Você pode querer usar as imagens pay-as-you-go se você não quiser se preocupar em pagar separadamente pelo número apropriado de subscrições.

### <a name="red-hat-gold-images"></a>Imagens de ouro do chapéu vermelho

Azure também oferece Red`rhel-byos`Hat Gold Images (). Estas imagens podem ser úteis para os clientes que têm subscrições de Red Hat existentes e querem usá-las em Azure. É necessário ativar as subscrições do Red Hat existentes para o Red Hat Cloud Access antes de as poder utilizar em Azure. O acesso a estas imagens é concedido automaticamente quando as suas subscrições de Chapéu Vermelho estão ativadas para o Cloud Access e cumprem os requisitos de elegibilidade. A utilização destas imagens permite que um cliente evite faturação dupla que possa ser incorrida com a utilização das imagens pay-as-you-go.
* Saiba como [ativar as suas subscrições de Chapéu Vermelho para Cloud Access com Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Saiba como [localizar Imagens De Ouro do Chapéu Vermelho no portal Azure, no Azure CLI ou no Cmdlet PowerShell](./byos.md).

> [!NOTE]
> A faturação dupla é incorrida quando um utilizador paga duas vezes por subscrições RHEL. Este cenário geralmente acontece quando um cliente usa Red Hat Subscription-Manager para anexar um direito num VM de pagamento RHEL. Por exemplo, um cliente que utiliza o Subscription-Manager para anexar um direito para pacotes SAP numa imagem de pagamento RHEL é indiretamente cobrado duas vezes porque paga duas vezes pela RHEL. Pagam uma vez através da taxa de prémio pay-as-you-go e uma vez através da sua subscrição SAP. Este cenário não acontece aos utilizadores de imagem BYOS.

### <a name="generation-2-images"></a>Imagens da Geração 2

As máquinas virtuais da Geração 2 (VMs) fornecem algumas funcionalidades mais recentes em comparação com os VMs da Geração 1. Para mais informações, consulte a documentação da [Geração 2.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) A diferença fundamental do ponto de vista da imagem RHEL é que os VMs da Geração 2 usam uma UEFI em vez da interface de firmware BIOS. Também usam uma tabela de partição GUID (GPT) em vez de um master boot record (MBR) no tempo de arranque. A utilização de um GPT permite, entre outras coisas, tamanhos de disco osso maiores do que 2 TB. Além disso, os [VMs da série Mv2](../../mv2-series.md) funcionam apenas nas imagens da Geração 2.

As imagens da Geração 2 estão disponíveis no Azure Marketplace. Procure "gen2" na imagem SKU na lista de todas as imagens que aparecem quando utiliza o Azure CLI. Vá ao separador **Avançado** no processo de implementação vm para implementar um VM de Geração 2.

## <a name="red-hat-update-infrastructure"></a>Infraestrutura de Atualização do Red Hat

O Azure fornece infraestruturas de atualização do chapéu vermelho apenas para VMs RHEL pay-as-you-go. RHUI é efetivamente um espelho dos CDNs do Chapéu Vermelho, mas só é acessível aos VMs RHEL do Azure. Tem acesso aos pacotes apropriados, dependendo da imagem RHEL que implementou. Por exemplo, um RHEL para imagem SAP tem acesso aos pacotes SAP para além dos pacotes Base RHEL.

### <a name="rhui-update-behavior"></a>Comportamento de atualização rHUI

Imagens RHEL ligadas à atualização RHUI por padrão `yum update` à versão menor mais recente do RHEL quando a é executada. Este comportamento significa que um RHEL 7.4 VM pode ser atualizado `yum update` para RHEL 7.7 se uma operação for executada nele. Este comportamento é por design para RHUI. Para mitigar este comportamento de atualização, mude de repositórios RHEL regulares para [repositórios de suporte](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)de atualização estendida .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as imagens RHEL no Azure.](./redhat-images.md)
* Saiba mais sobre a Infraestrutura de Atualização do [Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [imagem`rhel-byos`](./byos.md)de ouro do chapéu vermelho .
