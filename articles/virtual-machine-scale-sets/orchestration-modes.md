---
title: Saiba mais sobre modos de orquestração para conjuntos de escala de máquinas virtuais em Azure
description: Saiba mais sobre os modos de orquestração para conjuntos de escala de máquinas virtuais em Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: cbe080b4c2b6e73ae15fd186589bd43535bfc13d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198393"
---
# <a name="orchestration-mode-preview"></a>Modo de orquestração (pré-visualização)

Os conjuntos de escala de máquinas virtuais proporcionam um agrupamento lógico de máquinas virtuais geridas por plataformas. Com conjuntos de escala, cria-se um modelo de configuração de máquina virtual, adiciona-se ou remove automaticamente instâncias adicionais com base no CPU ou na carga de memória e atualiza-se automaticamente para a versão mais recente do SISTEMA. Tradicionalmente, os conjuntos de escala permitem criar máquinas virtuais utilizando um modelo de configuração VM fornecido no momento da criação do conjunto de escala, e o conjunto de escala só pode gerir máquinas virtuais que são criadas implicitamente com base no modelo de configuração.

Com o modo de orquestração definido em escala (pré-visualização), pode agora escolher se o conjunto de escala deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de escala, ou casos de máquinas virtuais criados implicitamente com base no modelo de configuração. O modo de orquestração de conjuntos de escala também o ajuda a projetar a sua infraestrutura VM para uma elevada disponibilidade, implantando estes VMs em domínios de falhas e Zonas de Disponibilidade.


Os conjuntos de escala de máquinavirtual suportam 2 modos de orquestração distintos:

- ScaleSetVM – As instâncias de máquinavirtual adicionadas ao conjunto de escala baseiam-se no modelo de configuração do conjunto de escala. O ciclo de vida da instância de máquina virtual - criação, atualização, eliminação - é gerido pelo conjunto de escala.
- VM (máquinas virtuais) – As máquinas virtuais criadas fora do conjunto de escala podem ser explicitamente adicionadas ao conjunto de escala. 
 

> [!IMPORTANT]
> O modo de orquestração é definido quando cria o conjunto de escala e não pode ser alterado ou atualizado mais tarde. 
> 
> Esta funcionalidade de conjuntos de escala de máquinas virtuais está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


## <a name="orchestration-modes"></a>Modos de orquestração

|                             | "OrchestrationMode": "VM" (VirtualMachine) | "modo de orquestração": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modelo de configuração VM      | Nenhum                                       | Necessário |
| Adicionar novo VM ao conjunto de escala  | Os VMs são explicitamente adicionados à escala definida quando o VM é criado. | Os VMs são implicitamente criados e adicionados à escala definida com base no modelo de configuração VM, contagem de instâncias e regras de autoscalcificação | |
| Eliminar VM                   | Os VMs têm de ser eliminados individualmente, o conjunto de escala não será eliminado se tiver quaisquer VMs nele. | Os VMs podem ser eliminados individualmente, eliminando o conjunto de escala eliminará todas as instâncias vM.  |
| VMs anexados/desapegos           | Não suportado                              | Não suportado |
| Ciclo de Vida da Instância (Criação através da Eliminação) | Os VMs e os seus artefactos (como discos e NICs) podem ser geridos de forma independente. | As instâncias e os seus artefactos (como discos e NICs) estão implícitos nas instâncias de conjunto de escala que os criam. Não podem ser separadas ou geridas separadamente fora do conjunto de escala |
| Domínios de falha               | Pode definir domínios de falha. 2 ou 3 com base no apoio regional e 5 na zona de disponibilidade. | Pode definir domínios de falha que vão de 1 a 5 |
| Domínios de atualização              | Os domínios de atualização são automaticamente mapeados para domínios de falha | Os domínios de atualização são automaticamente mapeados para domínios de falha |
| Zonas de Disponibilidade          | Apoia a implantação regional ou VMs numa zona de disponibilidade | Apoia a implantação regional ou múltiplas Zonas de Disponibilidade; Pode definir a estratégia de equilíbrio da zona |
| Escala Automática                   | Não suportado                              | Suportado |
| Atualização do OS                  | Não suportado                              | Suportado |
| Atualizações de modelos               | Não suportado                              | Suportado |
| Controlo de instâncias            | Controlo VM completo. Os VMs têm URI totalmente qualificado que suporta mInuários full leque de capacidades de gestão De VM Azure (como Política Azure, Backup Azure e Recuperação de Site Azure) | Os VMs são recursos dependentes da escala definida. Os casos só podem ser acedidos para gestão através do conjunto de escala. |
| Modelo de instância              | Definição do modelo Microsoft.Compute/VirtualMachines. | Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines definição do modelo. |
| Capacidade                    | Pode ser criado um conjunto de escala vazia; até 200 VMs podem ser adicionados ao conjunto de escala | Conjuntos de escala podem ser definidos com uma contagem de exemplo0 - 1000 |
| Mover                        | Suportado                                  | Suportado |
| Grupo de colocação única == falso | Não suportado                          | Suportado |


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a [visão geral das opções de disponibilidade.](availability.md)
