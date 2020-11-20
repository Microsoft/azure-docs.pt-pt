---
title: Cargas de trabalho do Chapéu Vermelho na visão geral do Azure Microsoft Docs
description: Conheça as ofertas de produtos Red Hat disponíveis no Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 3e4a770a33f5cdb3c3b2de09cbcb8431b2fc3fbb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968660"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de carga de chapéu vermelho em Azure

As cargas de trabalho da Red Hat são suportadas através de uma variedade de ofertas em Azure. As imagens Red Hat Enterprise Linux (RHEL) estão no centro das cargas de trabalho da RHEL, assim como a Infraestrutura de Atualização de Chapéus Vermelhos (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Imagens red hat enterprise Linux

A Azure oferece uma ampla oferta de imagens RHEL em Azure. Estas imagens são disponibilizadas através de dois modelos de licenciamento diferentes: pay-as-you-go e bring-your-your-own-subscription (BYOS). Novas imagens RHEL no Azure são publicadas quando novas versões RHEL são lançadas e atualizadas ao longo dos seus ciclos de vida, se necessário.

### <a name="pay-as-you-go-images"></a>Imagens pay-as-you-go

O Azure oferece uma variedade de imagens de pay-as-you-go rhel. Estas imagens têm direito a RHEL e estão anexadas a uma fonte de atualizações (Infraestrutura de Atualização de Chapéus Vermelhos). Estas imagens cobram uma taxa premium pelo direito e atualizações da RHEL. As variantes de imagem pay-as-you-go da RHEL incluem:

* RHEL padrão.
* RHEL para SAP.
* RHEL para SAP com Serviços de Alta Disponibilidade e Atualização.

É possível que queira utilizar as imagens pay-as-you-go se não quiser preocupar-se em pagar separadamente pelo número adequado de subscrições.

### <a name="red-hat-gold-images"></a>Imagens de ouro do chapéu vermelho

Azure também oferece Imagens de Ouro de Chapéu Vermelho `rhel-byos` (). Estas imagens podem ser úteis para clientes que tenham subscrições de Red Hat existentes e queiram usá-las em Azure. É-lhe exigido que ative as subscrições do Red Hat existente para o Red Hat Cloud Access antes de as poder utilizar no Azure. O acesso a estas imagens é concedido automaticamente quando as subscrições do Red Hat estão ativadas para o Cloud Access e cumprem os requisitos de elegibilidade. A utilização destas imagens permite que um cliente evite faturações duplas que possam ser incorridas com a utilização das imagens pay-as-you-go.
* Saiba como [ativar as subscrições do Red Hat para o Cloud Access com Azure.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* Saiba como [localizar imagens de ouro do chapéu vermelho no portal Azure, no Azure CLI ou no Cmdlet PowerShell](./byos.md).

> [!NOTE]
> A faturação dupla é incorrida quando um utilizador paga duas vezes por subscrições RHEL. Este cenário geralmente acontece quando um cliente usa o Red Hat Subscription-Manager para anexar um direito num VM pay-as-you-go rhel. Por exemplo, um cliente que usa Subscription-Manager para anexar um direito de pacotes SAP numa imagem de pay-as-you-go rhel é faturado indiretamente duas vezes porque pagam duas vezes pela RHEL. Pagam uma vez através da taxa de prémio pay-as-you-go e uma vez através da sua assinatura SAP. Este cenário não acontece com os utilizadores de imagem BYOS.

### <a name="generation-2-images"></a>Imagens da geração 2

As máquinas virtuais de geração 2 (VMs) fornecem algumas funcionalidades mais recentes em comparação com os VMs da Geração 1. Para mais informações, consulte a [documentação da Geração 2.](../../generation-2.md) A principal diferença de uma perspetiva de imagem RHEL é que os VMs da Geração 2 usam um UEFI em vez de interface de firmware BIOS. Também usam uma Tabela de Partição GUID (GPT) em vez de um recorde de arranque principal (MBR) no tempo de arranque. A utilização de um GPT permite, entre outras coisas, tamanhos de disco de SO superiores a 2 TB. Além disso, os [VMs da série Mv2](../../mv2-series.md) funcionam apenas em imagens da Geração 2.

As imagens RHEL Generation 2 estão disponíveis no Mercado Azure. Procure "gen2" na imagem SKU na lista de todas as imagens que aparecem quando utiliza o Azure CLI. Vá ao separador **Avançado** no processo de implementação de VM para implementar uma Geração 2 VM.

## <a name="red-hat-update-infrastructure"></a>Infraestrutura de Atualização do Red Hat

O Azure fornece infraestruturas de atualização de chapéus vermelhos apenas para VMs RHEL pay-as-you-go. RHUI é efetivamente um espelho dos CDNs do chapéu vermelho, mas só é acessível aos VMs RHEL pay-as-you-go do Azure. Tem acesso aos pacotes apropriados dependendo da imagem RHEL que implementou. Por exemplo, uma imagem RHEL para SAP tem acesso aos pacotes SAP para além dos pacotes RHEL base.

### <a name="rhui-update-behavior"></a>Comportamento de atualização de RHUI

Imagens RHEL ligadas à atualização RHUI por padrão à versão menor mais recente do RHEL quando a `yum update` é executada. Este comportamento significa que um RHEL 7.4 VM pode ser atualizado para RHEL 7.7 se uma `yum update` operação for executada nele. Este comportamento é por design para RHUI. Para mitigar este comportamento de atualização, mude de repositórios rhel regulares para [repositórios de suporte de atualização alargado .](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as imagens RHEL no Azure](./redhat-images.md).
* Saiba mais sobre [a Infraestrutura de Atualização de Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [oferta da Red Hat Gold Image `rhel-byos` ()](./byos.md)