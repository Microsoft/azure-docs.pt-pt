---
title: Capacidade de computação de planeamento para o Azure Stack | Documentos da Microsoft
description: Saiba mais sobre o planeamento para implementações do Azure Stack de capacidade de computação.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: 437e55b1a2907418fe47f418245431fa1c882b80
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915692"
---
# <a name="azure-stack-compute-capacity-planning"></a>Planeamento da capacidade de computação do Azure Stack
O [tamanhos VM suportados no Azure Stack](./user/azure-stack-vm-sizes.md) são um subconjunto desses suportado no Azure. Azure impõe limites de recursos ao longo de muitos vetores para evitar o consumo excessivo de recursos (servidor local e o nível de serviço). Sem gerar alguns limites no consumo de inquilino, as experiências de inquilino irão afetado quando outros inquilinos overconsume recursos. Para funcionamento em rede de saída da VM, existem limites de largura de banda no local no Azure Stack que correspondem a limitações do Azure. Para recursos de armazenamento, limites de IOPs de armazenamento foram implementados no Azure Stack para evitar básico consumo excessivo de recursos por inquilinos para acesso de armazenamento.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Colocação de VMS e núcleo virtual físico para aprovisionar em excesso
No Azure Stack, não é possível para um inquilino especificar um servidor específico para utilizar para a colocação de VM. A única preocupação quando a colocação de VMs é se existe memória suficiente no anfitrião para esse tipo VM. O Azure Stack não confirmar em excesso memória; No entanto, é permitido um overcommit do número de núcleos. Uma vez que os algoritmos de posicionamento não observar o existente para a taxa de excesso de núcleos físicos como um fator virtual, cada anfitrião pode ter um rácio diferente. 

No Azure, para assegurar elevada disponibilidade de um sistema de produção de várias VMS, as VMs são colocadas num conjunto de disponibilidade para serem distribuídos por vários domínios de falha. No Azure Stack, um domínio de falha num conjunto de disponibilidade é definido como um único nó a unidade de escala.

Enquanto a infraestrutura do Azure Stack é resiliente a falhas, a tecnologia subjacente (clustering de ativação pós-falha) ainda incorre num período de indisponibilidade para as VMs num servidor físico afetado em caso de falha de hardware. Atualmente, o Azure Stack suporta ter um conjunto de disponibilidade com um máximo de três domínios de falha para ser consistente com o Azure. Serão fisicamente isoladas umas das outras VMs colocadas num conjunto de disponibilidade ao propagá-los de forma mais uniforme possível através de vários domínios de falha (nós do Azure Stack). Se houver uma falha de hardware, VMs a partir do domínio de falha com falha irão ser reiniciadas nos outros nós, mas, se possível, mantidas em domínios de falha de outras VMs no mesmo conjunto de disponibilidade. Quando o hardware estiver online novamente, as VMs vão ser reequilibradas para manter uma elevada disponibilidade.

Outro conceito que é utilizado pelo Azure para proporcionar elevada disponibilidade está sob a forma de domínios de atualização nos conjuntos de disponibilidade. Um domínio de atualização é um grupo lógico de hardware subjacente que pode entrar em manutenção ou ser reiniciado ao mesmo tempo. No Azure Stack, as VMs estão no ar migrados entre os outros anfitriões no cluster online antes do anfitrião subjacente é atualizado. Uma vez que não existe nenhum tempo de inatividade de inquilino durante uma atualização de anfitrião, a funcionalidade do domínio de atualização no Azure Stack só existe para compatibilidade com o modelo com o Azure.

## <a name="azure-stack-resiliency-resources"></a>Recursos de resiliência do Azure Stack
Para permitir a atualização de um Azure Stack e patches de sistema integrado e para ser resiliente a falhas de hardware físico, uma parte da memória total do servidor é reservado e indisponível para colocação de máquina virtual (VM) do inquilino.

Se um servidor falhar, as VMs alojadas no servidor com falha serão reiniciadas em servidores restantes, disponíveis para fornecer disponibilidade da VM. Da mesma forma, durante o processo de patches e atualizações, todas as VMs em execução num servidor irá ser live migrado para outro servidor disponível. Esta gestão da VM ou o movimento só pode ser realizado se existir capacidade de reserva para permitir o reinício ou a migração para ocorrer.

O cálculo seguinte resulta na memória total e disponível que pode ser utilizada para colocação de VM do inquilino. Esta capacidade de memória é de todo a unidade de escala do Azure Stack.

  Memória disponível para colocação de VM = memória de memória Total do servidor – reserva da resiliência – utilizada ao executar VMs - sobrecarga de infraestrutura do Azure Stack <sup>1</sup>

  Reserva de resiliência = H + R * ((N-1) * H) + V * (N-2)

> Em que:
> - H = tamanho da memória de servidor único
> - N = tamanho de unidade de escala (número de servidores)
> - R = a reserva do sistema operativo para a sobrecarga de SO<sup>2</sup>
> - V = maior VM in a unidade de escala

  <sup>1</sup> infraestrutura do azure Stack sobrecarga = 230 GB

  <sup>2</sup> a reserva do sistema operativo para sobrecarga = 15% de memória do nó. O valor de reserva do sistema operativo é uma estimativa e varia com base na capacidade de memória física do servidor e a sobrecarga de sistema de operativo geral.

O valor V, maior VM na unidade de escala, baseia-se dinamicamente no inquilino do maior tamanho de memória da VM. Por exemplo, o maior valor para a VM pode ser 7 GB ou 112 GB ou qualquer outro VM memória tamanho suportado na solução do Azure Stack.

O cálculo acima é uma estimativa e sujeitos a alteração com base na versão atual do Azure Stack. Capacidade para implementar serviços e VMs inquilinas baseia-se com as especificidades da solução implantada. Este cálculo de exemplo é apenas um guia e não a resposta absoluta da capacidade de implementar VMs.



## <a name="next-steps"></a>Passos Seguintes
[Planeamento de capacidade de armazenamento](capacity-planning-storage.md)
