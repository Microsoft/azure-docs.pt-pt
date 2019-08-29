---
title: SAP HANA configurações de armazenamento de máquina virtual do Azure | Microsoft Docs
description: Recomendações de armazenamento para VM que têm SAP HANA implantadas neles.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45d8844a34c5b7d9f36099304c1619e09d39305c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099620"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento da máquina virtual do Azure do SAP HANA

O Azure fornece diferentes tipos de armazenamento que são adequados para VMs do Azure que estão executando o SAP HANA. Os tipos de armazenamento do Azure que podem ser considerados para SAP HANA lista de implantações como: 

- SSD (unidades de disco SSD Standard)
- SSD (unidades de estado sólido) Premium
- [Ultra Disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Para saber mais sobre esses tipos de disco, consulte o artigo [selecionar um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs no armazenamento Standard e Premium do Azure. Se o cenário geral permitir, aproveite as implantações de [disco gerenciado do Azure](https://azure.microsoft.com/services/managed-disks/) . 

Para obter uma lista de tipos de armazenamento e seus SLAs em IOPS e taxa de transferência de armazenamento, examine a [documentação do Azure para discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).

**Recomendação Usar o armazenamento Premium do Azure em conjunto com o Azure Acelerador de Gravação e usar o Managed Disks do Azure para implantação**

No mundo local, você raramente teve de se preocupar com os subsistemas de e/s e seus recursos. O motivo era que o fornecedor do dispositivo precisava garantir que os requisitos mínimos de armazenamento sejam atendidos por SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você deve estar ciente de alguns desses requisitos. Algumas das características de taxa de transferência mínimas que são solicitadas resultam na necessidade de:

- Habilitar leitura/gravação em **/Hana/log** de 250 MB/s com tamanhos de e/s de 1 MB
- Habilite a atividade de leitura de pelo menos 400 MB/s para **/Hana/data** para os tamanhos de e/s de 16 mb e 64 MB
- Habilitar a atividade de gravação de pelo menos 250 MB/s para **/Hana/data** com tamanhos de e/s de 16 mb e 64 MB

Considerando que baixa latência de armazenamento é essencial para sistemas DBMS, mesmo como DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento geralmente é voltado para as gravações do log de transações dos sistemas DBMS. Mas também operações como escrever pontos de salvamento ou carregar dados na memória após a recuperação de falhas podem ser críticas. Portanto, é **obrigatório** aproveitar os discos Premium do Azure para volumes **/Hana/data** e **/Hana/log** . Para obter a taxa de transferência mínima de **/Hana/log** e **/Hana/data** conforme desejado pelo SAP, você precisa criar um RAID 0 usando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E use os volumes RAID como volumes **/Hana/data** e **/Hana/log** . 

**Recomendação Como tamanhos de distribuição para o RAID 0, a recomendação é usar:**

- 64 KB ou 128 KB para **/Hana/data**
- 32 KB para **/Hana/log**

> [!NOTE]
> Você não precisa configurar nenhum nível de redundância usando volumes RAID, uma vez que o armazenamento Premium e Standard do Azure mantêm três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem taxa de transferência de e/s suficiente.

A acumulação de vários VHDs do Azure abaixo de um RAID é acumulada de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em 3 x p30 discos de armazenamento Premium do Azure, ele deverá fornecer três vezes o IOPS e três vezes a taxa de transferência de armazenamento de um único disco p30 de armazenamento Premium do Azure.

As recomendações de cache abaixo estão supondo as características de e/s para SAP HANA essa lista, como:

- Dificilmente há qualquer carga de trabalho de leitura em relação aos arquivos de dados do HANA. As exceções são e/SS de tamanho grande após a reinicialização da instância do HANA ou quando os dados são carregados no HANA. Outro caso de e/SS de leitura maiores em relação aos arquivos de dados pode ser backups de banco de dado do HANA. Como um cache de leitura de resultado, na maioria das vezes, não faz sentido, pois todos os volumes de arquivos de dados precisam ser lidos completamente.
- A gravação nos arquivos de dados é experimentada em intermitências com base nos pontos de salvamento do HANA e na recuperação de falhas do HANA. A gravação de pontos de salvamento é assíncrona e não mantém nenhuma transação de usuário. A gravação de dados durante a recuperação de falhas é um desempenho crítico para que o sistema responda rapidamente novamente. No entanto, a recuperação de falhas deve ser uma situação bastante excepcional
- Não há nenhuma leitura de arquivos do HANA Redo. As exceções são grandes e/SS ao executar backups de log de transações, recuperação de falha ou na fase de reinicialização de uma instância do HANA.  
- A carga principal em relação ao arquivo de log de refazer SAP HANA é gravações. Dependendo da natureza da carga de trabalho, você pode ter e/SS tão pequena quanto 4 KB ou em outros casos, os tamanhos de e/s de 1 MB ou mais. A latência de gravação em relação ao SAP HANA log de refazer é crítico para o desempenho.
- Todas as gravações precisam ser mantidas em disco de maneira confiável

**Recomendação Como resultado desses padrões de e/s observados por SAP HANA, o cache para os diferentes volumes usando o armazenamento Premium do Azure deve ser definido como:**

- **/Hana/data** -sem cache
- **/Hana/log** -sem cache-exceção para a série M (consulte mais adiante neste documento)
- **/Hana/Shared** -cache de leitura


Além disso, mantenha a taxa de transferência de e/s geral da VM em mente ao dimensionar ou decidir por uma VM. A taxa de transferência geral do armazenamento de VM está documentada no artigo [tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo de Agendador de e/s do Linux
O Linux tem vários modos de agendamento de e/s diferentes. A recomendação comum por meio de fornecedores do Linux e do SAP é reconfigurar o modo do Agendador de e/s para volumes de disco do modo **CFQ** para o modo **NOOP** . Os detalhes são referenciados no [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento de produção com o Azure Acelerador de Gravação para máquinas virtuais da série M do Azure
O Azure Acelerador de Gravação é uma funcionalidade que está sendo distribuída exclusivamente para VMs da série M do Azure. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de gravações no armazenamento Premium do Azure. Por SAP HANA, o Acelerador de Gravação deve ser usado somente no volume **/Hana/log** . Portanto, **/Hana/data** e **/Hana/log** são volumes separados com o Azure acelerador de gravação que dão suporte apenas ao volume **/Hana/log** . 

> [!IMPORTANT]
> A certificação SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de Gravação do Azure para o volume **/Hana/log** . Como resultado, o cenário de produção SAP HANA implantações nas máquinas virtuais da série M do Azure devem ser configuradas com o Acelerador de Gravação do Azure para o volume **/Hana/log** .  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte para SAP HANA pela SAP na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recomendação As configurações recomendadas para cenários de produção são semelhantes a:**

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | /Hana/data | /Hana/log | /Hana/Shared | /root volume | /usr/sap | Hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure.

O Azure Acelerador de Gravação só funciona em conjunto com o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o volume **/Hana/log** precisam ser implantados como discos gerenciados.

Há limites de VHDs de armazenamento Premium do Azure por VM que podem ser suportados pelo Azure Acelerador de Gravação. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Azure Acelerador de Gravação podem ser encontradas no artigo [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Azure Acelerador de Gravação podem ser encontrados na mesma documentação.

**Recomendação Você precisa usar Acelerador de Gravação para discos que formam o volume/Hana/log**


## <a name="cost-conscious-azure-storage-configuration"></a>Configuração de armazenamento do Azure com consciência de custo
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também usam para hospedar SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que podem não atender a todos os critérios mínimos de armazenamento para SAP HANA ou que não são oficialmente suportados com SAP HANA pela SAP. Mas, até agora, essas VMs pareciam funcionar bem para cenários de não produção. O **/Hana/data** e o **/Hana/log** são combinados em um volume. Como resultado, o uso do Azure Acelerador de Gravação pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários com suporte do SAP, verifique se um determinado tipo de VM tem suporte para SAP HANA pela SAP na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma mudança das recomendações anteriores para uma solução econômica, é mudar do [azure HDD Standard disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para melhor desempenho e mais confiável [discos de SSD standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | /Hana/data e/Hana/log<br /> distribuído com LVM ou MDADM | /Hana/Shared | /root volume | /usr/sap | Hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Os discos recomendados para os tipos de VM menores com 3 x P20 sobredimensionam os volumes em relação às recomendações de espaço de acordo com o [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto, a opção conforme exibido na tabela foi feita para fornecer taxa de transferência de disco suficiente para SAP HANA. Se você precisar de alterações no volume **/Hana/backup** , que foi dimensionado para manter os backups que representam o dobro do volume de memória, sinta-se à vontade para se ajustar.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima não se beneficiarão do [SLA de VM única da máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , pois ela usa uma combinação do armazenamento Premium do Azure e do armazenamento standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. Você precisaria escolher armazenamento Premium para todos os discos acima listados como armazenamento standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA de VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos SAP expressos em direção aos seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, foram encontradas situações em que essas recomendações ainda não forneceram recursos suficientes. Elas podem ser situações em que um cliente exigiu uma recarga mais rápida dos dados após uma reinicialização do HANA ou onde as configurações de backup exigiram maior largura de banda para o armazenamento. Outros casos incluíam **/Hana/log** em que 5000 IOPS não eram suficientes para a carga de trabalho específica. Então, use essas recomendações como um ponto de partida e se adapte com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento do Azure ultra Disk para SAP HANA
A Microsoft está no processo de distribuir um novo tipo de armazenamento do Azure chamado [ultra Disk do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk). A grande diferença entre o armazenamento do Azure oferecido até o momento e o ultra Disk é que os recursos de disco não estão mais vinculados ao tamanho do disco. Como um cliente, você pode definir esses recursos para o ultra Disk:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a IOPS de 160K (o máximo também depende dos tipos de VM)
- Taxa de transferência de armazenamento de 300 MB/seg a 2.000 MB/s

O ultra Disk oferece a possibilidade de definir um único disco que atenda a seu tamanho, IOPS e intervalo de taxa de transferência de disco. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM sobre o armazenamento Premium do Azure para construir volumes que atendam aos requisitos de taxa de transferência de armazenamento e IOPS. Você pode executar uma combinação de configuração entre o ultra Disk e o armazenamento Premium. Como resultado, você pode limitar o uso de ultra Disk para os volumes críticos de desempenho/Hana/data e/Hana/log e abranger os outros volumes com o armazenamento Premium do Azure

> [!IMPORTANT]
> O ultra Disk ainda não está presente em todas as regiões do Azure e também ainda não dá suporte a todos os tipos de VM. Para obter informações detalhadas em que o ultra Disk está disponível e quais famílias de VM têm suporte, consulte o artigo [quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk).

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | volume/Hana/data | taxa de transferência de e/s de/Hana/data | /hana/data IOPS | volume/Hana/log | taxa de transferência de e/s de/Hana/log | IOPS de/Hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1\.000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1\.000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2\.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1\.000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1\.000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2\.000 MB/s | 7200 GB | 1500m bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o ultra Disk do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de ultra disco não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os serviços de backup do Azure

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte:

- [SAP Hana guia de alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).