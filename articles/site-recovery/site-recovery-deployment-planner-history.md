---
title: Histórico de versões do Azure Site Recovery Deployment Planner
description: Conhecido diferentes de versões do Site Recovery Deployment Planner de correções e limitações conhecidas, juntamente com as respetivas datas de lançamento.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927376"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Histórico de versões do Azure Site Recovery Deployment Planner

Este artigo fornece o histórico de todas as versões do Azure Site Recovery Deployment Planner, juntamente com as correções, conhecido limitações em cada e respetivas datas de lançamento.

## <a name="version-24"></a>Versão 2.4

**Data de lançamento: 17 de Abril de 2019**

**Correções:**

- Melhorada a compatibilidade do sistema operacional, especificamente ao tratamento de erros com base na localização.
- Foi adicionadas VMs com até 20 Mbps de dados altera a taxa (renovação) para a lista de verificação de compatibilidade.
- Mensagens de erro melhoradas
  - Foi adicionado suporte para o vCenter 6.7.
  - Foi adicionado suporte para a estação de trabalho do Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versão 2.3

**Data de lançamento: 3 de Dezembro de 2018**

**Correções:**

- Foi corrigido um problema que impediu o planeador de implementações de gerar um relatório com a subscrição e localização de destino fornecido.

## <a name="version-22"></a>Versão 2.2 

**Data de lançamento: 25 de Abril de 2018**

**Correções:**

- Operações de GetVMList:
  - Foi corrigido um problema que causou GetVMList falha se a pasta especificada não existe. Ele agora ou cria o diretório predefinido ou cria o diretório especificado no parâmetro outputfile.
  - Adicionar que mais detalhadas os motivos das falhas de GetVMList.
- Foram adicionadas informações de tipo VM como uma coluna na folha de VMs compatível do relatório do Planeador de implementações.
- Hyper-V para recuperação após desastre do Azure:
  - Discos excluídas VMs com VHDs partilhado e o pass-through a criação de perfis. A operação de Startprofiling mostra a lista de VMs excluídas na consola do.
  - Foi adicionadas VMs com mais de 64 discos para a lista de VMs incompatíveis.
  - Atualizados os replicação inicial (IR) e o fator de compressão de replicação (DR) de delta.
  - Foi adicionado suporte limitado para armazenamento de SMB.

## <a name="version-21"></a>Versão 2.1

**Data de lançamento: 3 de Janeiro de 2018**

**Correções:**

- Atualizado o relatório de Excel.
- Foram corrigidos os erros na operação GetThroughput.
- Opção adicional para limitar o número de VMs para o perfil ou gerar o relatório. O limite predefinido é de 1000 VMs.
- VMware para recuperação após desastre do Azure:
  - Foi corrigido um problema de passar para a tabela incompatível de VM do Windows Server 2016. 
  - Mensagens de atualizadas de compatibilidade para as VMs do Windows de Interface de Firmware extensível (EFI).
- Atualizado o VMware para Azure e Hyper-V para o Azure, limite por VM de alterações a dados da VM. 
- Fiabilidade melhorada do ficheiro de lista VM de análise.

## <a name="version-201"></a>Versão 2.0.1

**Data de lançamento: 7 de Dezembro de 2017**

**Correções:**

- Recomendação foi adicionada para otimizar a largura de banda de rede.

## <a name="version-20"></a>Versão 2.0

**Data de lançamento: 28 de Novembro de 2017**

**Correções:**

- Foi adicionado suporte para o Hyper-V para recuperação após desastre do Azure.
- Calculadora de custo adicional.
- Foram adicionada OS verificação da versão do VMware para recuperação após desastre do Azure para determinar se a VM é compatível ou incompatível para a proteção. A ferramenta utiliza a cadeia de versão do SO devolvido pelo servidor vCenter para essa VM. É a versão de sistema operativo convidado que o utilizador selecionou ao criar a VM no VMware.

**Limitações conhecidas:**

- Para Hyper-V para recuperação após desastre do Azure, a VM com o nome que contém os carateres, como: `,`, `"`, `[`, `]`, e ``` ` ``` não são suportados. Se as quais criar perfis, geração de relatórios irá falhar ou terá um resultado incorreto.
- Para VMware para recuperação após desastre do Azure, não é suportada a VM com o nome que contenha por vírgulas. Se as quais criar perfis, falha de geração de relatórios ou terá um resultado incorreto.

## <a name="version-131"></a>Versão 1.3.1

**Data de lançamento: 19 de Julho de 2017** 

**Correções:**

- Foi adicionado suporte para discos grandes (> 1 TB) na geração de relatórios. Agora, pode utilizar o planeador de implementações para planear a replicação para máquinas virtuais com tamanhos de discos superiores a 1 TB (até 4095 GB).
Leia mais sobre o [Suporte de discos grandes no Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versão 1.3

**Data de lançamento: 9 de Maio de 2017**

**Correções:**

- Foi adicionado suporte para o disco gerido na geração de relatórios. O número de VMs que podem ser colocados para uma conta de armazenamento única é calculado com base no se o disco gerido está selecionado para a ativação pós-falha/ativação pós-falha.

## <a name="version-12"></a>versão 1.2

**Data de lançamento: 7 de Abril de 2017**

**Correções:**

- Foi adicionado de arranque (BIOS ou EFI) verificações do tipo para cada VM determinar se a VM é compatível ou incompatível para a proteção.
- Tipo do SO adicionadas informações para cada máquina virtual nas VMs compatíveis e folhas de cálculo de VMs incompatíveis.
- Foi adicionado suporte para a operação GetThroughput para as regiões US Government e China do Microsoft Azure.
- Foram adicionadas mais verificações de pré-requisitos para o vCenter e o Servidor ESXi.
- Foi corrigido um problema de relatório incorreto, ser gerado quando as configurações de localidade estão definidas para inglês.

## <a name="version-11"></a>Versão 1.1

**Data de lançamento: 9 de Março de 2017**

**Correções:**

- Foi corrigido um problema que impediu a criação de perfis de VMs quando existem duas ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi de vCenter.
- Foi corrigido um problema que causou a cópia e a pesquisa para ser desativado para as VMs compatíveis e folhas de cálculo de VMs incompatíveis.

## <a name="version-10"></a>Versão 1.0

**Data de lançamento: 23 de Fevereiro de 2017**

**Limitações conhecidas:**

- Suporta apenas para VMware para cenários de recuperação após desastre do Azure. Para o Hyper-V para cenários de recuperação após desastre do Azure, utilize o [ferramenta capacity planner do Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Não suporta a operação GetThroughput para as regiões US Government e China do Microsoft Azure.
- O perfil de cann't ferramenta VMs se o servidor do vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários anfitriões ESXi.
Nesta versão, a ferramenta ignora a criação de perfis para nomes ou endereços IP de VMs duplicados em VMListFile. A solução é criar perfis para as VMs com um anfitrião ESXi em vez do vCenter Server. Certifique-se para executar uma instância para cada anfitrião ESXi.
