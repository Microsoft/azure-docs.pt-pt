---
title: Saiba mais sobre os modos de orquestração para conjuntos de dimensionamento de máquinas virtuais no Azure
description: Saiba mais sobre modos de orquestração para conjuntos de dimensionamento de máquinas virtuais no Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279052"
---
# <a name="orchestration-mode-preview"></a>Modo de orquestração (versão prévia)

Os conjuntos de dimensionamento de máquinas virtuais fornecem um agrupamento lógico de máquinas virtuais gerenciadas por plataforma. Com os conjuntos de dimensionamento, você cria um modelo de configuração de máquina virtual, adiciona ou remove automaticamente instâncias adicionais com base na carga de CPU ou memória e atualiza automaticamente para a versão mais recente do sistema operacional. Tradicionalmente, os conjuntos de dimensionamento permitem que você crie máquinas virtuais usando um modelo de configuração de VM fornecido no momento da criação do conjunto de dimensionamento, e o conjunto de dimensionamento só pode gerenciar máquinas virtuais criadas implicitamente com base no modelo de configuração.

Com o modo de orquestração do conjunto de dimensionamento (versão prévia), agora você pode escolher se o conjunto de dimensionamento deve orquestrar máquinas virtuais que são criadas explicitamente fora de um modelo de configuração de conjunto de dimensionamento ou instâncias de máquina virtual criadas implicitamente com base no modelo de configuração. O modo de orquestração do conjunto de dimensionamento também ajuda a projetar sua infraestrutura de VM para alta disponibilidade implantando essas VMs em domínios de falha e Zonas de Disponibilidade.


Os conjuntos de dimensionamento de máquinas virtuais oferecerão suporte a dois modos de orquestração distintos:

- ScaleSetVM – as instâncias de máquina virtual adicionadas ao conjunto de dimensionamento são baseadas no modelo de configuração do conjunto de dimensionamento. O ciclo de vida da instância de máquina virtual – criação, atualização, exclusão-é gerenciado pelo conjunto de dimensionamento.
- VM (máquinas virtuais) – máquinas virtuais criadas fora do conjunto de dimensionamento podem ser adicionadas explicitamente ao ScaleMode. 
 

> [!IMPORTANT]
> O modo de orquestração é definido quando você cria o conjunto de dimensionamento e não pode ser alterado ou atualizado posteriormente. 
> 
> Esse recurso dos conjuntos de dimensionamento de máquinas virtuais está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modos de orquestração

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modelo de configuração de VM      | Nenhuma                                       | Obrigatório |
| Adicionando Nova VM ao conjunto de dimensionamento  | As VMs são adicionadas explicitamente ao conjunto de dimensionamento quando a VM é criada. | As VMs são criadas implicitamente e adicionadas ao conjunto de dimensionamento com base nas regras modelo de configuração de VM, contagem de instâncias e dimensionamento automático | |
| Excluir VM                   | As VMs precisam ser excluídas individualmente, o conjunto de dimensionamento não será excluído se tiver alguma VM nela. | As VMs podem ser excluídas individualmente, a exclusão do conjunto de dimensionamento excluirá todas as instâncias de VM.  |
| Anexar/desanexar VMs           | Não suportado                              | Não suportado |
| Ciclo de vida da instância (criação por meio de exclusão) | As VMs e seus artefatos (como discos e NICs) podem ser gerenciados de forma independente. | Instâncias e seus artefatos (como discos e NICs) são implícitos para as instâncias do conjunto de dimensionamento que os criam. Eles não podem ser desanexados ou gerenciados separadamente fora do conjunto de dimensionamento |
| Domínios de falha               | Pode definir domínios de falha. 2 ou 3 com base no suporte regional e 5 para a zona de disponibilidade. | Pode definir domínios de falha indo de 1 a 5 |
| Domínios de atualização              | Os domínios de atualização são mapeados automaticamente para domínios de falha | Os domínios de atualização são mapeados automaticamente para domínios de falha |
| Zonas de Disponibilidade          | Dá suporte à implantação regional ou a VMs em uma zona de disponibilidade | Dá suporte à implantação regional ou a vários Zonas de Disponibilidade; Pode definir a estratégia de balanceamento de zona |
| DimensionamentoAutomático                   | Não suportado                              | Suportadas |
| Atualização do so                  | Não suportado                              | Suportadas |
| Atualizações de modelo               | Não suportado                              | Suportadas |
| Controle de instância            | Controle de VM completo. As VMs têm um URI totalmente qualificado que oferece suporte a toda a gama de recursos de gerenciamento de VM do Azure (como Azure Policy, backup do Azure e Azure Site Recovery) | As VMs são recursos dependentes do conjunto de dimensionamento. As instâncias podem ser acessadas para gerenciamento somente por meio do conjunto de dimensionamento. |
| Modelo de instância              | Definição do modelo Microsoft. Compute/VirtualMachines. | Definição do modelo Microsoft. Compute/VirtualMachineScaleSets/VirtualMachines. |
| Capacidade                    | Um conjunto de dimensionamento vazio pode ser criado; até 200 VMs podem ser adicionadas ao conjunto de dimensionamento | Os conjuntos de dimensionamento podem ser definidos com uma contagem de instâncias 0-1000 |
| Mover                        | Suportadas                                  | Suportadas |
| Grupo de posicionamento único = = falso | Não suportado                          | Suportadas |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [visão geral das opções de disponibilidade](availability.md).
