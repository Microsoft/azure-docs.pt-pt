---
title: Saiba mais sobre modos de orquestração para conjuntos de escala de máquina virtual em Azure
description: Saiba mais sobre os modos de orquestração para conjuntos de escala de máquinas virtuais em Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: eb7d4d8a6f1c1ee55601cdd839e330147e60bcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011078"
---
# <a name="orchestration-modes-preview"></a>Modos de orquestração (pré-visualização)

> [!CAUTION]
> Obrigado a todos os que participaram nesta pré-estreia pública. Conseguimos recolher feedback valioso da nossa comunidade. Esta pré-visualização está agora **fechada** para quaisquer novos participantes, de forma a integrar feedback. Atualizaremos este espaço com qualquer nova informação.

Os conjuntos de escala de máquinas virtuais fornecem um agrupamento lógico de máquinas virtuais geridas pela plataforma. Com os conjuntos de escala, cria-se um modelo de configuração de máquina virtual, adiciona automaticamente ou remove casos adicionais com base na cpu ou na carga de memória e atualiza-se automaticamente para a versão mais recente do SO. Tradicionalmente, os conjuntos de escala permitem criar máquinas virtuais utilizando um modelo de configuração VM fornecido no momento da criação do conjunto de escala, e o conjunto de escala só pode gerir máquinas virtuais que são criadas implicitamente com base no modelo de configuração.

Com o modo de orquestração definido em escala (pré-visualização), pode agora escolher se o conjunto de escala deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de escala, ou casos de máquina virtual criados implicitamente com base no modelo de configuração. O modo de orquestração de conjunto de escala também o ajuda a projetar a sua infraestrutura VM para uma elevada disponibilidade, implantando estes VMs em domínios de falhas e Zonas de Disponibilidade.


Conjuntos de escala de máquina virtual suportarão 2 modos de orquestração distintos:

- ScaleSetVM – As instâncias de máquinas virtuais adicionadas ao conjunto de escala baseiam-se no modelo de configuração do conjunto de escala. O ciclo de vida da máquina virtual - criação, atualização, eliminação - é gerido pelo conjunto de escalas.
- VM (máquinas virtuais) – As máquinas virtuais criadas fora do conjunto de escala podem ser explicitamente adicionadas ao conjunto de escalas. 
 

> [!IMPORTANT]
> O modo de orquestração é definido quando cria o conjunto de escala e não pode ser alterado ou atualizado mais tarde. 
> 
> Esta característica dos conjuntos de escala de máquinas virtuais encontra-se atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modos de orquestração

| Funcionalidade                     | "OrchestrationMode": "VM" (VirtualMachine) | "OrchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modelo de configuração VM      | Nenhum                                       | Necessário |
| Adicionar novo VM ao conjunto de escala  | Os VMs são explicitamente adicionados à escala definida quando o VM é criado. | Os VMs são implicitamente criados e adicionados à escala definida com base no modelo de configuração VM, contagem de instâncias e regras de AutoScaling | |
| Eliminar VM                   | Os VMs têm de ser eliminados individualmente, o conjunto de escala não será eliminado se tiver VMs nele. | Os VMs podem ser eliminados individualmente, eliminando o conjunto de escalas eliminará todas as instâncias VM.  |
| Anexar/desprender VMs           | Não suportado                              | Não suportado |
| Exemplo Ciclo de vida (Criação através da Eliminação) | Os VMs e os seus artefactos (como discos e NICs) podem ser geridos de forma independente. | Os casos e os seus artefactos (como discos e NICs) estão implícitos nas instâncias definidas na escala que os criam. Não podem ser separados ou geridos separadamente fora do conjunto de escala |
| Domínios de falha               | Pode definir domínios de falhas. 2 ou 3 com base no apoio regional e 5 para zona de Disponibilidade. | Pode definir domínios de falha saindo de 1 a 5 |
| Domínios de atualização              | Os domínios de atualização são automaticamente mapeados para domínios de avaria | Os domínios de atualização são automaticamente mapeados para domínios de avaria |
| Zonas de Disponibilidade          | Suporta implantação regional ou VMs numa zona de disponibilidade | Suporta a implantação regional ou múltiplas Zonas de Disponibilidade; Pode definir a estratégia de equilíbrio da zona |
| Escala Automática                   | Não suportado                              | Suportado |
| Atualização do SO                  | Não suportado                              | Suportado |
| Atualizações de modelos               | Não suportado                              | Suportado |
| Controlo de instâncias            | Controlo VM completo. Os VMs têm URI totalmente qualificado que suportam toda a gama de capacidades de gestão Azure VM (como Azure Policy, Azure Backup e Azure Site Recovery) | Os VMs são recursos dependentes da escala definida. Os casos só podem ser acedidos para gestão através do conjunto de escalas. |
| Modelo de exemplo              | Definição de modelo Microsoft.Compute/VirtualMachines. | Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines definição de modelo. |
| Capacidade                    | Pode ser criado um conjunto de escala vazia; até 200 VMs podem ser adicionados ao conjunto de escala | Conjuntos de escala podem ser definidos com uma contagem de exemplos 0 - 1000 |
| Mover                        | Suportado                                  | Suportado |
| Grupo de colocação única == falso | Não suportado                          | Suportado |


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a [visão geral das opções de disponibilidade.](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machine-scale-sets%2ftoc.json)
