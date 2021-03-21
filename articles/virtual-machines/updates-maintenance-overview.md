---
title: Atualizações e visão geral da manutenção
description: Conheça as atualizações e opções de manutenção disponíveis com máquinas virtuais em Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022298"
---
# <a name="updates-and-maintenance-overview"></a>Atualizações e visão geral da manutenção
Este artigo fornece uma visão geral das várias opções de atualização e manutenção para máquinas virtuais Azure (VMs).

## <a name="automatic-os-image-upgrade"></a>Atualização automática de imagem do SO

Permitir [atualizações automáticas de imagens de SO](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) no seu conjunto de escala ajuda a facilitar a gestão da atualização, atualizando de forma segura e automática o disco DE para todos os casos no conjunto de escala.

[A atualização automática do SO](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) tem as seguintes características:

- Uma vez configurada, a mais recente imagem de SO publicada pelos editores de imagem é automaticamente aplicada à escala definida sem a intervenção do utilizador.
- Atualiza os lotes de casos de forma rolante cada vez que uma nova imagem é publicada pela editora.
- Integra-se com sondas de saúde de aplicação e [extensão de Saúde de Aplicação.](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context)
- Funciona para todos os tamanhos VM, e para imagens Windows e Linux.
- Pode optar por não fazer upgrades automáticos a qualquer momento (as atualizações de OS também podem ser iniciadas manualmente).
- O disco OS de um VM é substituído pelo novo DISCO OS criado com a versão de imagem mais recente. Extensões configuradas e scripts de dados personalizados são executados, enquanto os discos de dados persistidos são mantidos.
- [A sequência de extensão](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) é suportada.
- A atualização automática de imagens de SO pode ser ativada num conjunto de escala de qualquer tamanho.


## <a name="automatic-vm-guest-patching-preview"></a>Patching automático de hóspedes VM (Pré-visualização)

Permitir o [patch de hóspedes VM automático](automatic-vm-guest-patching.md) para os seus VMs Azure ajuda a facilitar a gestão da atualização, remendando de forma segura e automática máquinas virtuais para manter a conformidade com a segurança.

[O patching automático de hóspedes VM](automatic-vm-guest-patching.md) tem as seguintes características:
- Patches classificados como *Critical* ou *Security* são automaticamente descarregados e aplicados no VM.
- As manchas são aplicadas durante as horas de ponta no fuso horário do VM.
- A orquestração de remendos é gerida pelo Azure e os patches são aplicados seguindo [os princípios de disponibilidade primeiro.](automatic-vm-guest-patching.md#availability-first-patching)
- A saúde da máquina virtual, tal como determinada através de sinais de saúde da plataforma, é monitorizada para detetar falhas de correção.
- Funciona para todos os tamanhos VM.


## <a name="automatic-extension-upgrade-preview"></a>Atualização automática de extensão (Pré-visualização)

[A Atualização automática de extensão](automatic-extension-upgrade.md) está disponível em pré-visualização para VMs Azure e conjuntos de balança de máquina virtual Azure. Quando a atualização automática da extensão é ativada num conjunto de VM ou escala, a extensão é atualizada automaticamente sempre que o editor de extensão lança uma nova versão para essa extensão.

 A atualização automática da extensão tem as seguintes funcionalidades:
- Suportado para VMs Azure e conjuntos de balança de máquina virtual Azure. Os conjuntos de balança de máquina virtual do tecido de serviço não estão atualmente suportados.
- As atualizações são aplicadas num modelo de implantação de primeira disponibilidade.
- Para um conjunto de balança de máquina virtual, não mais de 20% das máquinas virtuais definidas em escala serão atualizadas num único lote. O tamanho mínimo do lote é uma máquina virtual.
- Funciona para todos os tamanhos VM, e para extensões Windows e Linux.
- Pode optar por não fazer upgrades automáticos a qualquer momento.
- A atualização automática da extensão pode ser ativada num conjunto de escala de máquina virtual de qualquer tamanho.
- Cada extensão suportada é inscrita individualmente, e pode escolher quais extensões para atualizar automaticamente.
- Apoiado em todas as regiões de nuvem pública.

## <a name="hotpatch"></a>Hotpatch 

[Hotpatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) é uma nova forma de instalar atualizações em novas máquinas virtuais do Windows Server Azure Edition (VMs) que não requerem um reboot após a instalação. Hotpatch para VMs da Edição Azure do Windows Server, tem os seguintes benefícios:

- Menor impacto da carga de trabalho com menos reboots
- Implementação mais rápida de atualizações à medida que os pacotes são menores, instalam-se mais rapidamente e têm uma orquestração de patch mais fácil com o Azure Update Manager
- Melhor proteção, uma vez que os pacotes de atualização hotpatch são abrangidos por atualizações de segurança do Windows que instalam mais rapidamente sem reiniciar


## <a name="azure-update-management"></a>Gestão de atualização Azure

Pode utilizar [a Gestão de Atualização na Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) para gerir as atualizações do sistema operativo para as suas máquinas virtuais Windows e Linux em Azure, em ambientes no local e em outros ambientes em nuvem. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas de agente e gerir o processo de instalação das atualizações necessárias para os servidores.

## <a name="maintenance-control"></a>Controlo da manutenção

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando [o controlo de manutenção](maintenance-control.md). O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de aguardar as atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados ao Azure.

Com [controlo de manutenção,](maintenance-control.md)pode:
- Atualizações de lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatizar as atualizações da plataforma configurando um calendário de manutenção ou utilizando [funções Azure](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 


## <a name="scheduled-events"></a>Eventos agendados

Eventos Agendados é um Serviço de Metadados Azure que dá tempo à sua aplicação para se preparar para a manutenção da máquina virtual (VM). Fornece informações sobre os próximos eventos de manutenção (por exemplo, reiniciar) para que a sua aplicação possa preparar-se para eles e limitar a interrupção. Está disponível para todos os tipos de Máquinas Virtuais Azure, incluindo PaaS e IaaS tanto no Windows como no Linux. 

Para obter informações sobre eventos agendados, consulte [Eventos Agendados para VMs](./windows/scheduled-events.md) windows e [eventos agendados para Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação [de Disponibilidade e escala](availability.md) para mais formas de aumentar o tempo de ação das suas aplicações e serviços. 