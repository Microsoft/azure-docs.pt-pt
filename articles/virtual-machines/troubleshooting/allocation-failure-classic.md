---
title: Solucionando problemas de falhas de alocação de VM do Azure no modelo de implantação clássico | Microsoft Docs
description: Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM clássica no Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d43176e04337c2faf7be0bea682428056bc4ab46
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059186"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Etapas de solução de problemas específicas para cenários de falha de alocação no modelo de implantação clássico

A seguir estão os cenários de alocação comuns que fazem com que uma solicitação de alocação seja fixada. Vamos nos aprofundar em cada cenário mais adiante neste artigo.

- Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
- Reiniciar VMs parcialmente paradas (desalocadas)
- Reiniciar VMs completamente paradas (desalocadas)
- Implantações de preparo e produção (somente plataforma como serviço)
- Grupo de afinidade (proximidade de serviço ou VM)
- Afinidade – rede virtual baseada em grupo

Quando você receber um erro de alocação, verifique se algum dos cenários listados se aplica ao seu erro. Use o erro de alocação retornado pela plataforma Azure para identificar o cenário correspondente. Se sua solicitação for fixada, remova algumas das restrições de fixação para abrir a solicitação para mais clusters, aumentando assim a chance de êxito de alocação.
Em geral, se o erro não declarar que "o tamanho de VM solicitado não tem suporte", você sempre poderá tentar novamente mais tarde. Isso ocorre porque recursos suficientes podem ter sido liberados no cluster para acomodar sua solicitação. Se o problema for que o tamanho de VM solicitado não tem suporte, tente um tamanho de VM diferente. Caso contrário, a única opção é remover a restrição de fixação.

Dois cenários comuns de falha estão relacionados aos grupos de afinidade. No passado, um grupo de afinidade foi usado para fornecer proximidade às VMs e instâncias de serviço, ou foi usado para habilitar a criação de uma rede virtual. Com a introdução de redes virtuais regionais, os grupos de afinidades não são mais necessários para criar uma rede virtual. Com a redução da latência de rede na infraestrutura do Azure, a recomendação para usar grupos de afinidade para VMs ou proximidade de serviço mudou.

O diagrama a seguir apresenta a taxonomia dos cenários de alocação (fixados). 

![Taxonomia de alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
**Ao**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa da fixação do cluster**

Uma solicitação para redimensionar uma VM ou adicionar uma VM ou uma instância de função a um serviço de nuvem existente deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um novo serviço de nuvem permite que a plataforma Azure encontre outro cluster com recursos gratuitos ou que ofereça suporte ao tamanho da VM que você solicitou.

**Solução**

Se o erro for Upgrade_VMSizeNotSupported *, tente um tamanho de VM diferente. Se o uso de um tamanho de VM diferente não for uma opção, mas se for aceitável usar um endereço IP virtual (VIP) diferente, crie um novo serviço de nuvem para hospedar a nova VM e adicione o novo serviço de nuvem à rede virtual regional em que as VMs existentes estão em execução. Se o serviço de nuvem existente não usar uma rede virtual regional, você ainda poderá criar uma nova rede virtual para o novo serviço de nuvem e, em seguida, conectar sua [rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro for GeneralError *, é provável que o tipo de recurso (como um tamanho de VM específico) seja suportado pelo cluster, mas o cluster não tem recursos livres no momento. Semelhante ao cenário acima, adicione o recurso de computação desejado por meio da criação de um novo serviço de nuvem (Observe que o novo serviço de nuvem precisa usar um VIP diferente) e usar uma rede virtual regional para conectar seus serviços de nuvem.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)
**Ao**

GeneralError

**Causa da fixação do cluster**

A desalocação parcial significa que você parou (desalocada) uma ou mais, mas não todas, VMs em um serviço de nuvem. Quando você parar (desalocar) uma VM, os recursos associados serão liberados. A reinicialização dessa VM parada (desalocada) é, portanto, uma nova solicitação de alocação. Reiniciar VMs em um serviço de nuvem parcialmente desalocado é equivalente a adicionar VMs a um serviço de nuvem existente. A solicitação de alocação deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um serviço de nuvem diferente permite que a plataforma Azure encontre outro cluster com recursos gratuitos ou que ofereça suporte ao tamanho da VM que você solicitou.

**Solução**

Se for aceitável usar um VIP diferente, exclua as VMs interrompidas (desalocadas) (mas mantenha os discos associados) e adicione as VMs novamente por meio de um serviço de nuvem diferente. Use uma rede virtual regional para conectar seus serviços de nuvem:

* Se o serviço de nuvem existente usar uma rede virtual regional, basta adicionar o novo serviço de nuvem à mesma rede virtual.
* Se o serviço de nuvem existente não usar uma rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)
**Ao**

GeneralError

**Causa da fixação do cluster**

A desalocação completa significa que você parou (desalocou) todas as VMs de um serviço de nuvem. As solicitações de alocação para reiniciar essas VMs precisam ser tentadas no cluster original que hospeda o serviço de nuvem. A criação de um novo serviço de nuvem permite que a plataforma Azure encontre outro cluster com recursos gratuitos ou que ofereça suporte ao tamanho da VM que você solicitou.

**Solução**

Se for aceitável usar um VIP diferente, exclua as VMs (desalocadas) originais interrompidas (mas mantenha os discos associados) e exclua o serviço de nuvem correspondente (os recursos de computação associados já foram liberados quando você parou (desalocaram) as VMs). Crie um novo serviço de nuvem para adicionar as VMs de volta.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implantações de preparo/produção (somente plataforma como serviço)
**Ao**

New_General * ou New_VMSizeNotSupported *

**Causa da fixação do cluster**

A implantação de preparo e a implantação de produção de um serviço de nuvem são hospedadas no mesmo cluster. Quando você adiciona a segunda implantação, a solicitação de alocação correspondente será tentada no mesmo cluster que hospeda a primeira implantação.

**Solução**

Exclua a primeira implantação e o serviço de nuvem original e reimplante o serviço de nuvem. Essa ação pode chegar à primeira implantação em um cluster que tem recursos livres suficientes para se ajustar às implantações ou em um cluster que dá suporte aos tamanhos de VM que você solicitou.

## <a name="affinity-group-vmservice-proximity"></a>Grupo de afinidade (proximidade de serviço/VM)
**Ao**

New_General * ou New_VMSizeNotSupported *

**Causa da fixação do cluster**

Qualquer recurso de computação atribuído a um grupo de afinidade é vinculado a um cluster. Novas solicitações de recursos de computação nesse grupo de afinidades são tentadas no mesmo cluster em que os recursos existentes estão hospedados. Isso é verdadeiro se os novos recursos são criados por meio de um novo serviço de nuvem ou por meio de um serviço de nuvem existente.

**Solução**

Se um grupo de afinidade não for necessário, não use um grupo de afinidade ou agrupe seus recursos de computação em vários grupos de afinidade.

## <a name="affinity-group-based-virtual-network"></a>Afinidade-rede virtual baseada em grupo
**Ao**

New_General * ou New_VMSizeNotSupported *

**Causa da fixação do cluster**

Antes de as redes virtuais regionais serem introduzidas, era necessário associar uma rede virtual a um grupo de afinidade. Como resultado, os recursos de computação colocados em um grupo de afinidades são vinculados pelas mesmas restrições, conforme descrito no "cenário de alocação: Seção de grupo de afinidade (proximidade de serviço/VM) "acima. Os recursos de computação estão vinculados a um cluster.

**Solução**

Se você não precisar de um grupo de afinidade, crie uma nova rede virtual regional para os novos recursos que você está adicionando e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Como alternativa, você pode [migrar sua rede virtual baseada em grupo de afinidades para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, adicionar os recursos desejados novamente.


