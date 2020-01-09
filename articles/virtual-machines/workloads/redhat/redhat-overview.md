---
title: Cargas de trabalho do Red Hat na visão geral do Azure | Microsoft Docs
description: Saiba mais sobre as ofertas de produtos da Red Hat disponíveis no Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473165"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de trabalho do Red Hat no Azure
As cargas de trabalho do Red Hat têm suporte por meio de uma variedade de ofertas no Azure. As imagens Red Hat Enterprise Linux (RHEL) estão no núcleo das cargas de trabalho do RHEL, assim como a RHUI (Red Hat Update Infrastructure).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Imagens Red Hat Enterprise Linux (RHEL)
O Azure oferece uma ampla oferta de imagens RHEL no Azure. Essas imagens são disponibilizadas por meio de dois modelos de licenciamento diferentes: pré-pago (PAYG) e traga sua própria assinatura (BYOS). Novas imagens RHEL no Azure são publicadas quando novas versões de RHEL são lançadas e atualizadas em todo o ciclo de vida conforme necessário.

### <a name="pay-as-you-go-payg-images"></a>Imagens PAYG (pré-pago)
O Azure oferece uma variedade de imagens PAYG do RHEL. Essas imagens vêm adequadamente qualificadas para o RHEL e são anexadas a uma fonte de atualizações (infraestrutura de atualização do Red Hat). Essas imagens cobrarão uma taxa Premium para o direito e as atualizações do RHEL. As variantes de imagem PAYG do RHEL incluem:
* RHEL Standard
* RHEL for SAP
* RHEL for SAP com serviços de alta disponibilidade e atualização.

Talvez você queira usar as imagens PAYG se não quiser se preocupar com o pagamento separado para o número apropriado de assinaturas.

### <a name="bring-your-own-subscription-byos-images"></a>Imagens BYOS (traga sua própria assinatura)
O Azure também oferece imagens BYOS do RHEL. Essas imagens podem ser úteis para clientes que têm assinaturas Red Hat existentes e desejam usá-las no Azure. Você precisa converter suas assinaturas existentes em assinaturas de acesso à nuvem antes de poder usá-las no Azure. O acesso a essas imagens é concedido somente quando a Red Hat verifica se você tem assinaturas de acesso à nuvem suficientes. O uso dessas imagens permite que um cliente evite a cobrança dupla que pode ser cobrada usando as imagens PAYG. Você pode solicitar acesso às imagens BYOS [aqui](https://aka.ms/rhel-byos).

> [!NOTE]
> Observação na cobrança dupla: a cobrança dupla é incorrida quando um usuário paga duas vezes para assinaturas do RHEL. Isso geralmente acontece quando um cliente usa o Gerenciador de assinaturas para anexar um direito em uma VM RHEL PAYG. Por exemplo, um cliente que usa o Gerenciador de assinaturas para anexar um direito para pacotes do SAP em uma imagem RHEL PAYG será cobrado de forma indireta, pois ele pagará duas vezes pelo RHEL-Once por meio da taxa do PAYG Premium e uma vez por meio de sua assinatura do SAP. Isso não ocorrerá para BYOS os usuários de imagem.

## <a name="red-hat-update-infrastructure-rhui"></a>RHUI (Red Hat Update Infrastructure)
O Azure fornece a infraestrutura de atualização do Red Hat somente para VMs (máquinas virtuais) PAYG RHEL. O RHUI é efetivamente um espelho do Red Hat CDNs, mas só é acessível para as VMs do Azure PAYG RHEL. Você terá acesso aos pacotes apropriados dependendo da imagem de RHEL que você implantou. Por exemplo, um RHEL for SAP Image terá acesso aos pacotes SAP, além dos pacotes base RHEL.

### <a name="rhui-update-behavior"></a>Comportamento de atualização do RHUI
As imagens RHEL conectadas ao RHUI serão, por padrão, atualizadas para a versão secundária mais recente do RHEL quando um `yum update` for executado. Esse comportamento significa que uma VM RHEL 7,4 pode ser atualizada para RHEL 7,7 se uma operação de `yum update` for executada nela. Isso é por design de RHUI. No entanto, esse comportamento de atualização pode ser mitigado alternando-se de repositórios RHEL regulares para os [repositórios de suporte de atualização estendida (eus)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens RHEL no Azure](./redhat-images.md)
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md)
* Saiba mais sobre a [oferta BYOS do RHEL](./redhat-byos.md)