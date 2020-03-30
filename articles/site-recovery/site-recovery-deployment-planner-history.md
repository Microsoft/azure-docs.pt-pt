---
title: Histórico de versão de implementação de implementação de site azure
description: Diferentes correções de implementação de implementação de site e limitações conhecidas juntamente com as datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433423"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico de versão de implementação de implementação de site azure

Este artigo fornece histórico de todas as versões do Planificador de Implantação de Implementação de Sítios Azure juntamente com as correções, limitações conhecidas em cada uma e suas datas de lançamento.

## <a name="version-251"></a>Versão 2.51

**Data de Lançamento: 22 de agosto de 2019**

**Correções:**

- Fixou o problema da recomendação de custos com a versão 2.5 do Planificador de Implantação

## <a name="version-25"></a>Versão 2.5

**Data de Lançamento: 29 de julho de 2019**

**Correções:**

- Para máquinas virtuais VMware e máquinas físicas, a recomendação é atualizada para ser baseada na replicação para Discos Geridos.
- Suporte adicional para Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 ou mais tarde

## <a name="version-24"></a>Versão 2.4

**Data de Lançamento: 17 de abril de 2019**

**Correções:**

- Melhoria da compatibilidade do sistema operativo, especificamente ao lidar com erros baseados na localização.
- VMs adicionados com até 20 Mbps de taxa de alteração de dados (churn) à lista de verificação de compatibilidade.
- Mensagens de erro melhoradas
- Suporte adicional para vCenter 6.7.
- Suporte adicional para o Windows Server 2019 e red hat enterprise linux (RHEL).



## <a name="version-23"></a>Versão 2.3

**Data de Lançamento: 3 de dezembro de 2018**

**Correções:**

- Corrigiu um problema que impedia o Planificador de Implantação de gerar um relatório com a localização e subscrição do alvo fornecidos.

## <a name="version-22"></a>Versão 2.2 

**Data de Lançamento: 25 de abril de 2018**

**Correções:**

- Operações GetVMList:
  - Corrigiu um problema que fez com que o GetVMList falhasse se a pasta especificada não existisse. Agora, ou cria o diretório predefinido, ou cria o diretório especificado no parâmetro do ficheiro de saída.
  - Acrescentou razões de falha mais detalhadas para o GetVMList.
- Adicionou informações do tipo VM como uma coluna na ficha vms compatível do relatório do Planificador de Implantação.
- Recuperação de desastres hiper-V para Azure:
  - VMs excluídos com VHDs partilhados e discos PassThrough de perfis. A operação Startprofiling mostra a lista de VMs excluídos na consola.
  - Os VMs adicionados com mais de 64 discos à lista de VMs incompatíveis.
  - Atualizou o fator de compressão de replicação inicial (IR) e delta (DR).
  - Suporte limitado adicionado para armazenamento SMB.

## <a name="version-21"></a>Versão 2.1

**Data de Lançamento: 3 de janeiro de 2018**

**Correções:**

- Atualizou o relatório do Excel.
- Bugs fixos na operação GetThroughput.
- Opção acrescida de limitar o número de VMs a perfilar ou gerar o relatório. O limite de padrão é de 1.000 VMs.
- VMware para recuperação de desastres de Azure:
  - Corrigiu um problema de VM Do Windows Server 2016 que vai para a tabela incompatível. 
  - Mensagens de compatibilidade atualizadas para VMs do Windows Interface de Firmware Extensible (EFI).
- Atualizado o VMware para Azure e Hyper-V para Azure, limite de churn de dados VM por VM. 
- Melhoria da fiabilidade da análise de ficheiros da lista VM.

## <a name="version-201"></a>Versão 2.0.1

**Data de Lançamento: 7 de dezembro de 2017**

**Correções:**

- Recomendação adicional para otimizar a largura de banda da rede.

## <a name="version-20"></a>Versão 2.0

**Data de Lançamento: 28 de novembro de 2017**

**Correções:**

- Apoio adicional para a recuperação de desastres do Hyper-V para o desastre de Azure.
- Calculadora de custos adicionado.
- A versão osa adicionada verifica a vMware à recuperação de desastres do Azure para determinar se o VM é compatível ou incompatível com a proteção. A ferramenta utiliza a cadeia de versão OS que é devolvida pelo servidor vCenter para esse VM. É a versão do sistema operativo convidado que o utilizador selecionou ao criar o VM em VMware.

**Limitações conhecidas:**

- Para a recuperação de desastres hyper-V a Azure, `[` `]`VM ``` ` ``` com nome contendo os caracteres como: `,`, `"`, , e não são suportados. Se perfilado, a geração do relatório falhará ou terá um resultado incorreto.
- Para a VMware para a recuperação de desastres de Azure, vM com nome contendo vírina não é suportado. Se perfilado, a geração de relatórios falha ou terá um resultado incorreto.

## <a name="version-131"></a>Versão 1.3.1

**Data de Lançamento: 19 de julho de 2017** 

**Correções:**

- Suporte adicional para grandes discos (> 1 TB) na geração de relatórios. Agora pode usar o Deployment Planner para planear a replicação de máquinas virtuais com tamanhos de disco superiores a 1 TB (até 4095 GB).
Leia mais sobre o [Suporte de discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1.3

**Data de Lançamento: 9 de maio de 2017**

**Correções:**

- Apoio adicional ao disco gerido na geração de relatórios. O número de VMs que podem ser colocados numa única conta de armazenamento é calculado com base se o disco gerido for selecionado para Failover/Test Failover.

## <a name="version-12"></a>Versão 1.2

**Data de Lançamento: 7 de abril de 2017**

**Correções:**

- O tipo de boot adicionado (BIOS ou EFI) verifica cada VM para determinar se o VM é compatível ou incompatível com a proteção.
- Adicione informações do tipo OS para cada máquina virtual nas VMs compatíveis e folhas de cálculo vMs incompatíveis.
- Apoio adicional à operação GetThroughput para as regiões do Governo dos EUA e da China Microsoft Azure.
- Foram adicionadas mais verificações de pré-requisitos para o vCenter e o Servidor ESXi.
- Corrigiu uma questão de relatório incorreto ser gerado quando as definições locais são definidas para não inglês.

## <a name="version-11"></a>Versão 1.1

**Data de Lançamento: 9 de março de 2017**

**Correções:**

- Corrigiu um problema que impedia o perfil de VMs quando existem dois ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões do VCenter ESXi.
- Corrigiu um problema que fez com que a cópia e a procura fossem desativadas para as VMs compatíveis e folhas de cálculo vMs incompatíveis.

## <a name="version-10"></a>Versão 1.0

**Data de Lançamento: 23 de fevereiro de 2017**

**Limitações conhecidas:**

- Suporta apenas o VMware para cenários de recuperação de desastres do Azure. Para cenários de recuperação de desastres Hyper-V a Azure, utilize a ferramenta de planejador de [capacidade hiper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Não apoia a operação GetThroughput para as regiões do Governo dos EUA e da China Microsoft Azure.
- A ferramenta não pode perfilar VMs se o servidor vCenter tiver dois ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi.
Nesta versão, a ferramenta ignora a criação de perfis para nomes ou endereços IP de VMs duplicados em VMListFile. A solução é criar perfis para as VMs com um anfitrião ESXi em vez do vCenter Server. Certifique-se de executar uma instância para cada hospedeiro ESXi.
