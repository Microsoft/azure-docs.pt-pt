---
title: Histórico de versão do Planejador de Implantações do Azure Site Recovery
description: Conhecidos diferentes Site Recovery versões Planejador de Implantações correções e limitações conhecidas junto com suas datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433423"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico de versão do Planejador de Implantações do Azure Site Recovery

Este artigo fornece o histórico de todas as versões do Planejador de Implantações do Azure Site Recovery junto com as correções, limitações conhecidas em cada uma das datas de lançamento.

## <a name="version-251"></a>Versão 2,51

**Data de lançamento: 22 de agosto de 2019**

**Contida**

- Correção do problema de recomendação de custo com Planejador de Implantações versão 2,5

## <a name="version-25"></a>Versão 2,5

**Data de lançamento: 29 de julho de 2019**

**Contida**

- Para máquinas virtuais VMware e máquinas físicas, a recomendação é atualizada para ser baseada na replicação para Managed Disks.
- Adicionado suporte para Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 ou posterior

## <a name="version-24"></a>Versão 2,4

**Data de lançamento: 17 de abril de 2019**

**Contida**

- Compatibilidade aprimorada do sistema operacional, especificamente ao lidar com erros baseados na localização.
- VMs adicionadas com até 20 Mbps de taxa de alteração de dados (rotatividade) para a lista de verificação de compatibilidade.
- Mensagens de erro aprimoradas
- Adicionado suporte para o vCenter 6,7.
- Suporte adicionado para estação de trabalho do Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versão 2,3

**Data de lançamento: 3 de dezembro de 2018**

**Contida**

- Correção de um problema que impedia a Planejador de Implantações de gerar um relatório com o local de destino e a assinatura fornecidos.

## <a name="version-22"></a>Versão 2,2 

**Data de lançamento: 25 de abril de 2018**

**Contida**

- Operações de GetVMList:
  - Corrigido um problema que fazia com que o GetVMList falhasse se a pasta especificada não existir. Agora, ele cria o diretório padrão ou cria o diretório especificado no parâmetro arquivo_de_saída.
  - Foram adicionadas razões de falha mais detalhadas para o GetVMList.
- Adicionadas informações de tipo de VM como uma coluna na planilha de VMs compatíveis do relatório de Planejador de Implantações.
- Recuperação de desastre do Hyper-V para o Azure:
  - VMs excluídas com VHDs compartilhados e discos de passagem da criação de perfil. A operação Startprofiling mostra a lista de VMs excluídas no console do.
  - Adicionadas VMs com mais de 64 discos à lista de VMs incompatíveis.
  - Atualizado o fator de compactação de replicação inicial (IR) e replicação delta (DR).
  - Adição de suporte limitado para armazenamento SMB.

## <a name="version-21"></a>Versão 2,1

**Data de lançamento: 3 de janeiro de 2018**

**Contida**

- O relatório do Excel foi atualizado.
- Correção de bugs na operação getthroughput.
- Opção adicionada para limitar o número de VMs para o perfil ou gerar o relatório. O limite padrão é de 1.000 VMs.
- Recuperação de desastre do VMware para o Azure:
  - Corrigido um problema da VM do Windows Server 2016 entrando na tabela incompatível. 
  - Mensagens de compatibilidade atualizadas para VMs do Windows EFI (Extensible Firmware Interface).
- Atualizado o VMware para o Azure e o Hyper-V para o Azure, limite de variação de dados de VM por VM. 
- Confiabilidade aprimorada da análise de arquivos da lista de VMs.

## <a name="version-201"></a>Versão 2.0.1

**Data de lançamento: 7 de dezembro de 2017**

**Contida**

- Adição de recomendação para otimizar a largura de banda da rede.

## <a name="version-20"></a>Versão 2,0

**Data de lançamento: 28 de novembro de 2017**

**Contida**

- Suporte adicionado para o Hyper-V para a recuperação de desastre do Azure.
- Calculadora de custo adicionada.
- Adicionada a verificação de versão do so para a recuperação de desastre do VMware para o Azure para determinar se a VM é compatível ou incompatível com a proteção. A ferramenta usa a cadeia de caracteres da versão do sistema operacional que é retornada pelo servidor do vCenter para essa VM. É a versão do sistema operacional convidado que o usuário selecionou ao criar a VM no VMware.

**Limitações conhecidas:**

- Para a recuperação de desastre do Hyper-V para o Azure, a VM com o nome que contém os caracteres como: `,`, `"`, `[`, `]` e ``` ` ``` não tem suporte. Se o perfil for criado, a geração de relatórios falhará ou terá um resultado incorreto.
- Para a recuperação de desastre do VMware para o Azure, não há suporte para VM com nome contendo vírgula. Se for criado um perfil, a geração de relatórios falhará ou terá um resultado incorreto.

## <a name="version-131"></a>1\.3.1 da versão

**Data de lançamento: 19 de julho de 2017** 

**Contida**

- Adicionado suporte para discos grandes (> 1 TB) na geração de relatórios. Agora você pode usar Planejador de Implantações para planejar a replicação para máquinas virtuais com tamanhos de disco maiores que 1 TB (até 4095 GB).
Leia mais sobre o [Suporte de discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1,3

**Data de lançamento: 9 de maio de 2017**

**Contida**

- Adicionado suporte para disco gerenciado na geração de relatórios. O número de VMs que podem ser colocadas em uma única conta de armazenamento é calculado com base em se o disco gerenciado for selecionado para failover/failover de teste.

## <a name="version-12"></a>Versão 1,2

**Data de lançamento: 7 de abril de 2017**

**Contida**

- O tipo de inicialização (BIOS ou EFI) adicionado verifica cada VM para determinar se a VM é compatível ou incompatível com a proteção.
- Adicionadas informações de tipo de sistema operacional para cada máquina virtual nas planilhas de VMs compatíveis e VMs incompatíveis.
- Suporte adicionado para a operação getthroughput para as regiões do governo dos EUA e da China Microsoft Azure.
- Foram adicionadas mais verificações de pré-requisitos para o vCenter e o Servidor ESXi.
- Correção de um problema de relatório incorreto ao ser gerado quando as configurações de localidade são definidas como não inglês.

## <a name="version-11"></a>Versão 1,1

**Data de lançamento: 9 de março de 2017**

**Contida**

- Correção de um problema que impedia a criação de perfil de VMs quando há duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi do vCenter.
- Corrigido um problema que causou a desabilitação da cópia e da pesquisa para as planilhas de VMs compatíveis e VMs incompatíveis.

## <a name="version-10"></a>Versão 1,0

**Data de lançamento: 23 de fevereiro de 2017**

**Limitações conhecidas:**

- Dá suporte apenas para cenários de recuperação de desastre do VMware para o Azure. Para cenários de recuperação de desastre do Hyper-V para o Azure, use a [ferramenta planejador de capacidade do Hyper-v](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Não dá suporte à operação getthroughput para as regiões do governo dos EUA e da China Microsoft Azure.
- A ferramenta cann't o perfil de VMs se o servidor vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi.
Nesta versão, a ferramenta ignora a criação de perfis para nomes ou endereços IP de VMs duplicados em VMListFile. A solução é criar perfis para as VMs com um anfitrião ESXi em vez do vCenter Server. Certifique-se de executar uma instância para cada host ESXi.
