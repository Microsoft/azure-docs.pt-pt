---
title: Histórico da versão do planejador de implementação de recuperação do site Azure
description: Conhecidas diferentes versões de planeamento de implementação de implementação de locais de recuperação de locais, corrigem e limitações conhecidas, juntamente com as datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133798"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico da versão do planejador de implementação de recuperação do site Azure

Este artigo fornece o histórico de todas as versões do Azure Site Recovery Deployment Planner juntamente com as correções, limitações conhecidas em cada uma e as suas datas de lançamento.

## <a name="version-252"></a>Versão 2.52

**Data de Lançamento: 4 de junho de 2020**

**Correções:**

- Suporte adicional para vCenter 7.0
- Suporte adicional para os seguintes sistemas operativos:

    - SUSE Linux Enterprise 15 (com todas as versões menores)
    - Red Hat Enterprise Linux 8 (com todas as versões menores)


## <a name="version-251"></a>Versão 2.51

**Data de Lançamento: 22 de agosto de 2019**

**Correções:**

- Fixo o problema da recomendação de custos com a versão 2.5 do Deployment Planner

## <a name="version-25"></a>Versão 2.5

**Data de Lançamento: 29 de julho de 2019**

**Correções:**

- Para máquinas virtuais VMware e máquinas físicas, a recomendação é atualizada para ser baseada na replicação de Discos Geridos.
- Suporte adicional para Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 ou mais tarde

## <a name="version-24"></a>Versão 2.4

**Data de Lançamento: 17 de abril de 2019**

**Correções:**

- Melhor compatibilidade do sistema operativo, especificamente ao lidar com erros baseados na localização.
- Adicionou VMs com até 20 Mbps de taxa de alteração de dados (churn) à lista de verificação de compatibilidade.
- Mensagens de erro melhoradas
- Suporte adicional para vCenter 6.7.
- Suporte adicional para o Windows Server 2019 e red hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versão 2.3

**Data de Lançamento: 3 de dezembro de 2018**

**Correções:**

- Corrigiu um problema que impedia o Planejador de Implantação de gerar um relatório com a localização e subscrição do alvo fornecidos.

## <a name="version-22"></a>Versão 2.2 

**Data de Lançamento: 25 de abril de 2018**

**Correções:**

- Operações GetVMList:
  - Corrigiu um problema que fez com que o GetVMList falhasse se a pasta especificada não existisse. Agora cria o diretório predefinido ou cria o diretório especificado no parâmetro do ficheiro de saída.
  - Adicionou razões de falha mais detalhadas para a GetVMList.
- Adicionou informação do tipo VM como uma coluna na folha de VMs compatível do relatório do Planejador de Implantação.
- Hiper-V para recuperação de desastres de Azure:
  - VMs excluídos com VHDs partilhados e discos PassThrough de perfis. A operação Startprofiling mostra a lista de VMs excluídos na consola.
  - Adicionou VMs com mais de 64 discos à lista de VMs incompatíveis.
  - Atualizou o fator de replicação inicial (IR) e de replicação delta (DR).
  - Apoio limitado adicionado ao armazenamento de SMB.

## <a name="version-21"></a>Versão 2.1

**Data de Lançamento: 3 de janeiro de 2018**

**Correções:**

- Atualize o relatório excel.
- Bugs fixos na operação GetThroughput.
- Opção acrescida de limitar o número de VMs ao perfil ou gerar o relatório. O limite por defeito é de 1.000 VMs.
- VMware para recuperação de desastres de Azure:
  - Corrigiu um problema do Windows Server 2016 VM indo para a tabela incompatível. 
  - Mensagens de compatibilidade atualizadas para VMs do Windows Interface de Firmware Extensível (EFI).
- Atualizou o VMware para Azure e Hyper-V para Azure, limite de churn de dados VM por VM. 
- Melhor fiabilidade da análise de ficheiros da lista VM.

## <a name="version-201"></a>Versão 2.0.1

**Data de Lançamento: 7 de dezembro de 2017**

**Correções:**

- Recomendação adicional para otimizar a largura de banda da rede.

## <a name="version-20"></a>Versão 2.0

**Data de Lançamento: 28 de novembro de 2017**

**Correções:**

- Apoio adicional para a recuperação de desastres de Hiper-V a Azure.
- Calculadora de custos adicionada.
- Verificação da versão OS adicionada para vMware para recuperação de desastres Azure para determinar se o VM é compatível ou incompatível com a proteção. A ferramenta utiliza a cadeia de versão OS que é devolvida pelo servidor vCenter para esse VM. É a versão do sistema operativo de hóspedes que o utilizador selecionou enquanto cria o VM em VMware.

**Limitações conhecidas:**

- Para a recuperação de desastres Hyper-V a Azure, vM com nome contendo os caracteres como: `,` , , , e não são `"` `[` `]` ``` ` ``` suportados. Se for perfilado, a geração de relatórios falhará ou terá um resultado incorreto.
- Para a recuperação de desastres VMware to Azure, vM com nome contendo vírgula não é suportado. Se for perfilado, a geração de relatórios falha ou terá um resultado incorreto.

## <a name="version-131"></a>Versão 1.3.1

**Data de Lançamento: 19 de julho de 2017** 

**Correções:**

- Apoio adicional para discos grandes (> 1 TB) na geração de relatórios. Agora pode utilizar o Deployment Planner para planear a replicação de máquinas virtuais com tamanhos de disco superiores a 1 TB (até 4095 GB).
Leia mais sobre o [Suporte de discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1.3

**Data de Lançamento: 9 de maio de 2017**

**Correções:**

- Apoio adicional ao disco gerido na geração de relatórios. O número de VMs que podem ser colocados numa única conta de armazenamento é calculado com base no facto de o disco gerido ser selecionado para Failover/Test Failover.

## <a name="version-12"></a>Versão 1.2

**Data de Lançamento: 7 de abril de 2017**

**Correções:**

- O tipo de arranque adicionado (BIOS ou EFI) verifica cada VM para determinar se o VM é compatível ou incompatível com a proteção.
- Adicionou informações do tipo DE para cada máquina virtual nos VMs compatíveis e folhas de cálculo VMs incompatíveis.
- Apoio adicional à operação GetThroughput para as regiões do Governo dos EUA e da China Microsoft Azure.
- Foram adicionadas mais verificações de pré-requisitos para o vCenter e o Servidor ESXi.
- Corrigiu um problema de relatório incorreto gerado quando as definições do local são definidas como não-inglesas.

## <a name="version-11"></a>Versão 1.1

**Data de Lançamento: 9 de março de 2017**

**Correções:**

- Corrigiu um problema que impedia o perfil de VMs quando existem dois ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões vCenter ESXi.
- Corrigiu um problema que fez com que a cópia e a procura fossem desativadas para os VMs compatíveis e folhas de cálculo VMs incompatíveis.

## <a name="version-10"></a>Versão 1.0

**Data de Lançamento: 23 de fevereiro de 2017**

**Limitações conhecidas:**

- Suporta apenas para VMware para cenários de recuperação de desastres Azure. Para cenários de recuperação de desastres Hyper-V a Azure, utilize a [ferramenta de planificador de capacidade Hiper-V](./hyper-v-deployment-planner-overview.md).
- Não apoia a operação GetThroughput para as regiões do Governo dos EUA e da China Microsoft Azure.
- A ferramenta não pode perfilar VMs se o servidor vCenter tiver dois ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi.
Nesta versão, a ferramenta ignora a criação de perfis para nomes ou endereços IP de VMs duplicados em VMListFile. A solução é criar perfis para as VMs com um anfitrião ESXi em vez do vCenter Server. Certifique-se de executar um exemplo para cada anfitrião ESXi.
