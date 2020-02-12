---
title: Cargas de trabalho do Chapéu Vermelho na visão geral do Azure Microsoft Docs
description: Conheça as ofertas de produtos Red Hat disponíveis no Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: daba49e6861eb67fd07c6fcf618b2b2d6cdd8c89
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133808"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de trabalho do Chapéu Vermelho em Azure
As cargas de trabalho do Chapéu Vermelho são suportadas através de uma variedade de ofertas no Azure. As imagens red hat enterprise Linux (RHEL) estão no centro das cargas de trabalho da RHEL, assim como a Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Imagens Red Hat Enterprise Linux (RHEL)
O Azure oferece uma ampla oferta de imagens RHEL no Azure. Estas imagens são disponibilizadas através de dois modelos de licenciamento diferentes: pay-as-you-go (PAYG) e bring-your-your-own-subscription (BYOS). Novas imagens RHEL no Azure são publicadas quando novas versões RHEL são lançadas e atualizadas ao longo dos seus ciclos de vida, se necessário.

### <a name="pay-as-you-go-payg-images"></a>Imagens pay-as-you-go (PAYG)
O Azure oferece uma variedade de imagens RHEL PAYG. Estas imagens têm direito ao RHEL e estão ligadas a uma fonte de atualizações (Red Hat Update Infrastructure). Estas imagens cobrarão uma taxa premium pelo direito rhel e atualizações. As variantes de imagem RHEL PAYG incluem:
* RHEL padrão
* RHEL para SAP
* RHEL para SAP com Serviços de Alta Disponibilidade e Atualização.

Pode querer utilizar as imagens PAYG se não quiser preocupar-se em pagar separadamente pelo número adequado de subscrições.

### <a name="red-hat-gold-images-rhel-byos"></a>Imagens de Ouro do Chapéu Vermelho (`rhel-byos`)
O Azure também oferece imagens do Red Hat Gold. Estas imagens podem ser úteis para os clientes que têm subscrições de Red Hat existentes e querem usá-las em Azure. Você precisa habilitar suas assinaturas do Red Hat existentes para o acesso à nuvem Red Hat antes de poder usá-las no Azure. O acesso a essas imagens é concedido automaticamente quando suas assinaturas do Red Hat estão habilitadas para acesso à nuvem e atendem aos requisitos de qualificação. A utilização destas imagens permite que um cliente evite a faturação dupla que possa ser incorrida com a utilização das imagens PAYG.
* [Saiba como ativar as suas subscrições de Chapéu Vermelho para Cloud Access com O Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Saiba como localizar Imagens De Ouro do Chapéu Vermelho no portal Azure, CLI ou PowerShell Cmdlet](./byos.md)

> [!NOTE]
> Nota sobre a faturação dupla: A faturação dupla é incorrida quando um utilizador paga duas vezes por subscrições RHEL. Isto geralmente acontece quando um cliente usa o gestor de subscrição para anexar um direito num VM RHEL PAYG. Por exemplo, um cliente que utilize o gestor de subscrição para anexar um direito para pacotes SAP numa imagem RHEL PAYG será indiretamente faturado duas vezes porque pagará duas vezes pela RHEL - uma vez através da taxa de prémio PAYG e uma vez através da sua subscrição SAP. Isto não acontecerá aos utilizadores de imagem BYOS.

### <a name="generation-2-images"></a>Imagens da Geração 2
Os VMs da Geração 2 fornecem algumas funcionalidades mais recentes em comparação com os VMs da Geração 1. Os detalhes estão estabelecidos na documentação da [Geração 2.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) A diferença fundamental do ponto de vista da imagem RHEL é que os VMs da Geração 2 usam uma UEFI em vez de interface de firmware BIOS, e usam uma tabela de partição GUID (GPT) em vez de um master boot record (MBR) no tempo de arranque. Isto permite, entre outras coisas, tamanhos de disco osso maiores do que 2TB. Além disso, os [VMs da série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series) funcionam apenas nas imagens da Geração 2.

As imagens da Geração 2 da RHEL estão disponíveis no mercado. Procure "gen2" na imagem SKU ao listar todas as imagens usando o Azure CLI, e vá ao separador "Advanced" no processo de implementação vm para implementar um VM geração 2.

## <a name="red-hat-update-infrastructure-rhui"></a>Infraestrutura de Atualização de Chapéu Vermelho (RHUI)
O Azure fornece infraestruturas de atualização do chapéu vermelho apenas para máquinas virtuais PAYG RHEL (VMs). RHUI é efetivamente um espelho dos CDNs do Chapéu Vermelho, mas só é acessível aos VMs Azure PAYG RHEL. Terá acesso aos pacotes apropriados, dependendo da imagem rHEL que implementou. Por exemplo, um RHEL para imagem SAP terá acesso aos pacotes SAP para além dos pacotes Base RHEL.

### <a name="rhui-update-behavior"></a>Comportamento de atualização rHUI
As imagens RHEL ligadas à RHUI irão, por padrão, atualizar para a versão menor mais recente do RHEL quando um `yum update` é executado. Este comportamento significa que um RHEL 7.4 VM pode ser atualizado para RHEL 7.7 se for executada uma operação `yum update`. Isto é por design de RHUI. No entanto, este comportamento de atualização pode ser atenuado mudando de repositórios RHEL regulares para os [repositórios de Suporte de Atualização Estendida (EUS).](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [imagens RHEL no Azure](./redhat-images.md)
* Saiba mais sobre a Infraestrutura de [Atualização](./redhat-rhui.md) do Chapéu Vermelho
* Saiba mais sobre a oferta de [Gold Image (`rhel-byos`)](./byos.md) do Chapéu Vermelho
