---
title: 'Recolher linha de base: Boas práticas de desempenho & diretrizes'
description: Fornece passos para recolher uma linha de base de desempenho como diretrizes para otimizar o desempenho do seu SqL Server na Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572450"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Recolher linha de base: Melhores práticas de desempenho para SQL Server em Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece informações para recolher uma linha de base de desempenho como uma série de boas práticas e diretrizes para otimizar o desempenho do seu SQL Server em Azure Virtual Machines (VMs).

Existe tipicamente uma compensação entre otimizar os custos e otimizar o desempenho. Esta série de boas práticas de desempenho está focada em obter o *melhor* desempenho para SQL Server em Azure Virtual Machines. Se a sua carga de trabalho for menos exigente, poderá não necessitar de todas as otimizações recomendadas. Considere as suas necessidades de desempenho, custos e padrões de carga de trabalho à medida que avalia estas recomendações.

## <a name="overview"></a>Descrição Geral

Para uma abordagem prescritiva, recolha contadores de desempenho usando PerfMon/LogMan e capture estatísticas de espera do SQL Server para entender melhor as pressões gerais e potenciais estrangulamentos do ambiente de origem. 

Comece por recolher o CPU, a memória, o [IOPS,](../../../virtual-machines/premium-storage-performance.md#iops) [o rendimento](../../../virtual-machines/premium-storage-performance.md#throughput)e a [latência](../../../virtual-machines/premium-storage-performance.md#latency) da carga de trabalho de origem em momentos de pico após a [lista de verificação](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)de desempenho da aplicação . 

Recolha dados durante horas de ponta, tais como cargas de trabalho durante o seu típico dia útil, mas também outros processos de alta carga, como o processamento de fim de dia e cargas de trabalho ETL de fim de semana. Considere aumentar os seus recursos para cargas de trabalho atipicamente pesadas, como o processamento no final do trimestre, e, em seguida, escalar feito uma vez que a carga de trabalho esteja concluída. 

Utilize a análise de desempenho para selecionar o [Tamanho VM](../../../virtual-machines/sizes-memory.md) que pode escalar para os requisitos de desempenho da sua carga de trabalho.


## <a name="storage"></a>Armazenamento

O desempenho do SQL Server depende fortemente do subsistema de E/S e o desempenho do armazenamento é medido pelo IOPS e pela produção. A menos que a sua base de dados se encaixe na memória física, o SQL Server traz constantemente páginas de base de dados dentro e fora da piscina tampão. Os ficheiros de dados do SQL Server devem ser tratados de forma diferente. O acesso aos ficheiros de registo é sequencial, exceto quando uma transação precisa de ser revertida onde os ficheiros de dados, incluindo o temporário, são acedidos aleatoriamente. Se tiver um subsistema de E/S lento, os seus utilizadores poderão experimentar problemas de desempenho, tais como tempos de resposta lentos e tarefas que não são concluídas devido a intervalos de tempo. 

As máquinas virtuais do Azure Marketplace têm ficheiros de registo num disco físico que é separado dos ficheiros de dados por padrão. Os ficheiros de dados temporários contam e o tamanho cumprem as melhores práticas e são direcionados para a `D:\` unidade efémera. 

Os seguintes contadores PerfMon podem ajudar a validar a produção de IO exigida pelo seu SqL Server: 
* **\LogicalDisk\Disk Reads/Sec** (ler IOPS)
* **\LogicalDisk\Disk Writes/Sec** (escrever IOPS) 
* **\LogicalDisk\Disk Read Bytes/Sec** (ler os requisitos de produção para os ficheiros dados, log e tempdb)
* **\LogicalDisk\Disk Write Bytes/Sec** (escreva os requisitos de produção para os ficheiros dados, log e tempdb)

Utilizando requisitos de IOPS e de produção nos níveis máximos, avalie os tamanhos de VM que correspondam à capacidade das suas medições. 

Se a sua carga de trabalho necessitar de 20K de IOPS e IOPS de escrita de 10K, pode escolher E16s_v3 (com até 32K em cache e 25600 IOPS não colados) ou M16_s (com até 20K em cache e 10K IOPS sem pressão) com 2 discos P30 sem tiras utilizando espaços de armazenamento. 

Certifique-se de compreender tanto os requisitos de produção como o IOPS da carga de trabalho, uma vez que os VMs têm limites de escala diferentes para IOPS e produção.

## <a name="memory"></a>Memória

Rastreia tanto a memória externa utilizada pelo SO como a memória utilizada internamente pelo SQL Server. Identificar a pressão para qualquer um dos componentes ajudará a dimensionar as máquinas virtuais e a identificar oportunidades de afinação. 

Os seguintes contadores PerfMon podem ajudar a validar a saúde da memória de uma máquina virtual SQL Server: 
* [\Memory\MBytes disponíveis](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Memória do Servidor (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Preguiçosas escreve/seg](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page esperança de vida](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Computação

O cálculo em Azure é gerido de forma diferente das instalações. Os servidores no local são construídos para durar vários anos sem uma atualização devido à sobrecarga de gestão e ao custo de aquisição de novo hardware. A virtualização atenua alguns destes problemas, mas as aplicações são otimizadas para tirar o máximo partido do hardware subjacente, o que significa que qualquer alteração significativa no consumo de recursos requer reequilíbrio de todo o ambiente físico. 

Este não é um desafio em Azure onde uma nova máquina virtual numa série diferente de hardware, e mesmo numa região diferente, é fácil de alcançar. 

Em Azure, pretende aproveitar o máximo possível de recursos de máquinas virtuais, pelo que as máquinas virtuais Azure devem ser configuradas para manter o CPU médio o mais alto possível sem afetar a carga de trabalho. 

Os seguintes contadores PerfMon podem ajudar a validar a saúde computacional de uma máquina virtual SQL Server:
* **\Tempo do processador informação (_Total) \%**
* **\Tempo do processador (sqlservr) \%**

> [!NOTE] 
> Idealmente, tente usar 80% do seu cálculo, com picos acima de 90% mas não atingindo 100% durante qualquer período de tempo sustentado. Fundamentalmente, você só quer providenciar o cálculo que a aplicação precisa e, em seguida, planeja escalar para cima ou para baixo como o negócio exige. 


## <a name="next-steps"></a>Passos seguintes

Para saber mais, veja os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Tamanho VM](performance-guidelines-best-practices-vm-size.md)
- [Armazenamento](performance-guidelines-best-practices-storage.md)


Para obter as melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](security-considerations-best-practices.md).

Reveja outros artigos da Máquina Virtual do SqL Server [no SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).
