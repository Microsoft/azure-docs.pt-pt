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
ms.date: 10/21/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bcd27378039d539e36c72cf6e8fec7e8a1425e54
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750347"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento da máquina virtual do Azure do SAP HANA

O Azure fornece diferentes tipos de armazenamento que são adequados para VMs do Azure que estão executando o SAP HANA. Os **SAP Hana tipos de armazenamento do Azure certificados** que podem ser considerados para a lista de implantações SAP Hana como: 

- SSD Premium do Azure  
- [Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber mais sobre esses tipos de disco, consulte o artigo [selecionar um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs no armazenamento Standard e Premium do Azure. Se o cenário geral permitir, aproveite as implantações de [disco gerenciado do Azure](https://azure.microsoft.com/services/managed-disks/) . 

Para obter uma lista de tipos de armazenamento e seus SLAs em IOPS e taxa de transferência de armazenamento, examine a [documentação do Azure para discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).

As condições mínimas de SAP HANA certificadas para os diferentes tipos de armazenamento são: 

- O Azure SSD Premium-/Hana/log deve ser armazenado em cache com o [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)do Azure. O volume/Hana/data pode ser colocado em SSD Premium sem o Acelerador de Gravação do Azure ou no ultra Disk
- Ultra Disk do Azure pelo menos para o volume/Hana/log. O volume/Hana/data pode ser colocado em qualquer SSD Premium sem Acelerador de Gravação do Azure ou para obter tempos de reinicialização mais rápidos
- Volumes **NFS v 4.1** sobre Azure NetApp Files para/Hana/log **e** /Hana/data

Alguns dos tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar/Hana/data no armazenamento Premium e/Hana/log pode ser colocado em armazenamento de ultra disco para obter a baixa latência necessária. No entanto, não é recomendável misturar volumes NFS para, por exemplo,/Hana/data e usar um dos outros tipos de armazenamento certificados para/Hana/log

No mundo local, você raramente teve de se preocupar com os subsistemas de e/s e seus recursos. O motivo era que o fornecedor do dispositivo precisava garantir que os requisitos mínimos de armazenamento sejam atendidos por SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você deve estar ciente de alguns desses requisitos. Algumas das características de taxa de transferência mínimas que são solicitadas resultam na necessidade de:

- Habilitar leitura/gravação em **/Hana/log** de 250 MB/s com tamanhos de e/s de 1 MB
- Habilite a atividade de leitura de pelo menos 400 MB/s para **/Hana/data** para os tamanhos de e/s de 16 mb e 64 MB
- Habilitar a atividade de gravação de pelo menos 250 MB/s para **/Hana/data** com tamanhos de e/s de 16 mb e 64 MB

Considerando que baixa latência de armazenamento é essencial para sistemas DBMS, mesmo como DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento geralmente é voltado para as gravações do log de transações dos sistemas DBMS. Mas também operações como escrever pontos de salvamento ou carregar dados na memória após a recuperação de falhas podem ser críticas. Portanto, é **obrigatório** aproveitar os discos Premium do Azure para volumes **/Hana/data** e **/Hana/log** . Para obter a taxa de transferência mínima de **/Hana/log** e **/Hana/data** conforme desejado pelo SAP, você precisa criar um RAID 0 usando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E use os volumes RAID como volumes **/Hana/data** e **/Hana/log** . 

**Recomendação: como tamanhos de distribuição para o RAID 0, a recomendação é usar:**

- 64 KB ou 128 KB para **/Hana/data**
- 32 KB para **/Hana/log**

> [!NOTE]
> Você não precisa configurar nenhum nível de redundância usando volumes RAID, uma vez que o armazenamento Premium e Standard do Azure mantêm três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem taxa de transferência de e/s suficiente.

A acumulação de vários VHDs do Azure abaixo de um RAID é acumulada de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em 3 x p30 discos de armazenamento Premium do Azure, ele deverá fornecer três vezes o IOPS e três vezes a taxa de transferência de armazenamento de um único disco p30 de armazenamento Premium do Azure.

Além disso, mantenha a taxa de transferência de e/s geral da VM em mente ao dimensionar ou decidir por uma VM. A taxa de transferência geral do armazenamento de VM está documentada no artigo [tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo de Agendador de e/s do Linux
O Linux tem vários modos de agendamento de e/s diferentes. A recomendação comum por meio de fornecedores do Linux e do SAP é reconfigurar o modo do Agendador de e/s para volumes de disco do modo **CFQ** para o modo **NOOP** . Os detalhes são referenciados no [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com armazenamento Premium e Acelerador de Gravação do Azure para máquinas virtuais da série M do Azure
O Azure Acelerador de Gravação é uma funcionalidade disponível para VMs da série M do Azure exclusivamente. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de gravações no armazenamento Premium do Azure. Por SAP HANA, o Acelerador de Gravação deve ser usado somente no volume **/Hana/log** . Portanto, **/Hana/data** e **/Hana/log** são volumes separados com o Azure acelerador de gravação que dão suporte apenas ao volume **/Hana/log** . 

> [!IMPORTANT]
> Ao usar o armazenamento Premium do Azure, o uso do [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) do Azure ou o volume/Hana/log é obrigatório. Acelerador de Gravação está disponível apenas para VMs de armazenamento Premium e série M e série Mv2.

As recomendações de cache abaixo estão supondo as características de e/s para SAP HANA essa lista, como:

- Dificilmente há qualquer carga de trabalho de leitura em relação aos arquivos de dados do HANA. As exceções são e/SS de tamanho grande após a reinicialização da instância do HANA ou quando os dados são carregados no HANA. Outro caso de e/SS de leitura maiores em relação aos arquivos de dados pode ser backups de banco de dado do HANA. Como um cache de leitura de resultado, na maioria das vezes, não faz sentido, pois todos os volumes de arquivos de dados precisam ser lidos completamente.
- A gravação nos arquivos de dados é experimentada em intermitências com base nos pontos de salvamento do HANA e na recuperação de falhas do HANA. A gravação de pontos de salvamento é assíncrona e não mantém nenhuma transação de usuário. A gravação de dados durante a recuperação de falhas é um desempenho crítico para que o sistema responda rapidamente novamente. No entanto, a recuperação de falhas deve ser uma situação bastante excepcional
- Não há nenhuma leitura de arquivos do HANA Redo. As exceções são grandes e/SS ao executar backups de log de transações, recuperação de falha ou na fase de reinicialização de uma instância do HANA.  
- A carga principal em relação ao arquivo de log de refazer SAP HANA é gravações. Dependendo da natureza da carga de trabalho, você pode ter e/SS tão pequena quanto 4 KB ou em outros casos, os tamanhos de e/s de 1 MB ou mais. A latência de gravação em relação ao SAP HANA log de refazer é crítico para o desempenho.
- Todas as gravações precisam ser mantidas em disco de maneira confiável

**Recomendação: como resultado desses padrões de e/s observados por SAP HANA, o cache para os diferentes volumes usando o armazenamento Premium do Azure deve ser definido como:**

- **/Hana/data** -sem cache
- **/Hana/log** -sem cache-exceção para a série M e Mv2 em que acelerador de gravação deve ser habilitada sem cache de leitura. 
- **/Hana/Shared** -cache de leitura

### <a name="production-recommended-storage-solution"></a>Solução de armazenamento recomendada para produção

> [!IMPORTANT]
> A certificação SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de Gravação do Azure para o volume **/Hana/log** . Como resultado, o cenário de produção SAP HANA implantações nas máquinas virtuais da série M do Azure devem ser configuradas com o Acelerador de Gravação do Azure para o volume **/Hana/log** .  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte para SAP HANA pela SAP na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



**Recomendação: as configurações recomendadas para cenários de produção são semelhantes a:**

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | /Hana/data | /Hana/log | /Hana/Shared | /root volume | /usr/SAP | Hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x p30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x p30 | 1 x P6 | 1 x P6 |2 x p30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x p30 | 2 x P20 | 1 x p30 | 1 x P6 | 1 x P6 | 3 x p30 |
| M128s | 2000 GiB | 2000 MB/s |3 x p30 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x p30 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x p30 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x p30 | 1 x P10 | 1 x P6 | 4 x P50 |

Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure.

O Azure Acelerador de Gravação só funciona em conjunto com o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o volume **/Hana/log** precisam ser implantados como discos gerenciados.

Há limites de VHDs de armazenamento Premium do Azure por VM que podem ser suportados pelo Azure Acelerador de Gravação. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Azure Acelerador de Gravação podem ser encontradas no artigo [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Azure Acelerador de Gravação podem ser encontrados na mesma documentação.

**Recomendação: você precisa usar Acelerador de Gravação para discos que formam o volume/Hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuração de armazenamento do Azure com consciência de custo
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também usam para hospedar SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que podem não atender a todos os critérios mínimos de armazenamento para SAP HANA ou que não são oficialmente suportados com SAP HANA pela SAP. Mas, até agora, essas VMs pareciam funcionar bem para cenários de não produção. O **/Hana/data** e o **/Hana/log** são combinados em um volume. Como resultado, o uso do Azure Acelerador de Gravação pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários com suporte do SAP, verifique se um determinado tipo de VM tem suporte para SAP HANA pela SAP na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma mudança das recomendações anteriores para uma solução econômica, é mudar do [azure HDD Standard disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para melhor desempenho e mais confiável [discos de SSD standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | /Hana/data e/Hana/log<br /> distribuído com LVM ou MDADM | /Hana/Shared | /root volume | /usr/SAP | Hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1\.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1\.000 GiB | 1\.000 MB/s | 2 x p30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 3 x p30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2\.000 GiB | 2\.000 MB/s |3 x p30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x p30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x p30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Os discos recomendados para os tipos de VM menores com 3 x P20 sobredimensionam os volumes em relação às recomendações de espaço de acordo com o [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto, a opção conforme exibido na tabela foi feita para fornecer taxa de transferência de disco suficiente para SAP HANA. Se você precisar de alterações no volume **/Hana/backup** , que foi dimensionado para manter os backups que representam o dobro do volume de memória, sinta-se à vontade para se ajustar.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes sugeridos atende à carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **/Hana/data** e **/Hana/log**, você precisará aumentar o número de VHDs de armazenamento Premium do Azure. O dimensionamento de um volume com mais VHDs do que o listado aumenta a taxa de transferência de IOPS e e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima não se beneficiarão do [SLA de VM única da máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , pois ela usa uma combinação do armazenamento Premium do Azure e do armazenamento standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. Você precisaria escolher armazenamento Premium para todos os discos acima listados como armazenamento standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA de VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos SAP expressos em direção aos seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, foram encontradas situações em que essas recomendações ainda não forneceram recursos suficientes. Elas podem ser situações em que um cliente exigiu uma recarga mais rápida dos dados após uma reinicialização do HANA ou onde as configurações de backup exigiram maior largura de banda para o armazenamento. Outros casos incluíam **/Hana/log** em que 5000 IOPS não eram suficientes para a carga de trabalho específica. Então, use essas recomendações como um ponto de partida e se adapte com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento do Azure ultra Disk para SAP HANA
A Microsoft está no processo de distribuir um novo tipo de armazenamento do Azure chamado [ultra Disk do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). A diferença significativa entre o armazenamento do Azure oferecido até o momento e o ultra Disk é que os recursos de disco não estão mais vinculados ao tamanho do disco. Como um cliente, você pode definir esses recursos para o ultra Disk:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- O IOPS varia de 100 IOPS a IOPS de 160K (o máximo também depende dos tipos de VM)
- Taxa de transferência de armazenamento de 300 MB/seg a 2.000 MB/s

O ultra Disk oferece a possibilidade de definir um único disco que atenda a seu tamanho, IOPS e intervalo de taxa de transferência de disco. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM sobre o armazenamento Premium do Azure para construir volumes que atendam aos requisitos de taxa de transferência de armazenamento e IOPS. Você pode executar uma combinação de configuração entre o ultra Disk e o armazenamento Premium. Como resultado, você pode limitar o uso de ultra Disk para os volumes críticos de desempenho/Hana/data e/Hana/log e abranger os outros volumes com o armazenamento Premium do Azure

Outras vantagens do ultra Disk podem ser a melhor latência de leitura em comparação com o armazenamento Premium. A latência de leitura mais rápida pode ter vantagens quando você deseja reduzir os tempos de inicialização do HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento de ultra disco também podem ser sentidas quando o HANA está escrevendo pontos de salvamento. Como os discos de armazenamento Premium para/Hana/data geralmente não são Acelerador de Gravação armazenados em cache, a latência de gravação para/Hana/data no armazenamento Premium em comparação com o ultra Disk é maior. Pode ser esperado que o salvamento de pontos com o ultra Disk esteja com um desempenho melhor no ultra Disk.

> [!IMPORTANT]
> O ultra Disk ainda não está presente em todas as regiões do Azure e também ainda não está dando suporte a todos os tipos de VM listados abaixo. Para obter informações detalhadas em que o ultra Disk está disponível e quais famílias de VM têm suporte, consulte o artigo [quais tipos de disco estão disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de ultra Disk pura
Nessa configuração, você mantém os volumes/Hana/data e/Hana/log separadamente. Os valores sugeridos são derivados dos KPIs que o SAP tem para certificar tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | volume/Hana/data | taxa de transferência de e/s de/Hana/data | IOPS de/Hana/data | volume/Hana/log | taxa de transferência de e/s de/Hana/log | IOPS de/Hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2\.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M64ls | 512 GiB | 1\.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBps |9\.000 | 512 GB | 800 MBps  | 3000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 1200 MBps |9\.000 | 512 GB | 800 MBps  | 3000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3000 |   

Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o ultra Disk do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de ultra disco não estão disponíveis. Isso bloqueia o uso de instantâneos de VM com os serviços de backup do Azure

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de ultra Disk pura
Nessa configuração, você os volumes/Hana/data e/Hana/log no mesmo disco. Os valores sugeridos são derivados dos KPIs que o SAP tem para certificar tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no [whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| SKU DA VM | RAM | Um máximo de E/S DE VM<br /> Débito | Volume para/Hana/data e/log | /Hana/data e taxa de transferência de e/s de log | IOPS de/Hana/data e log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9\.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9\.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9\.500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10,000 | 
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10,000 | 
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10,000 | 
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 MBps |12.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 MBps |12.000 |  
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.700 GB | 1, 800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 15.000 GB | 1\.800 MBps | 12.000 |    

Os tipos de VM M416xx_v2 ainda não foram disponibilizados pela Microsoft para o público. Os valores listados se destinam a ser um ponto de partida e precisam ser avaliados em relação às demandas reais. A vantagem com o ultra Disk do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou de interromper a carga de trabalho aplicada ao sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v 4.1 no Azure NetApp Files
O Azure NetApp Files fornece compartilhamentos NFS nativos que podem ser usados para volumes/Hana/Shared,/Hana/data e/Hana/log. O uso de compartilhamentos NFS baseados em seja para esses volumes requer o uso do protocolo v 4.1 NFS. Não há suporte para o protocolo de NFS v3 para o uso de volumes relacionados ao HANA ao basear os compartilhamentos em seja. 

> [!IMPORTANT]
> o protocolo NFS v3 implementado em Azure NetApp Files não tem suporte para ser usado para/Hana/Shared,/Hana/data e/Hana/log

### <a name="important-considerations"></a>Considerações importantes
Ao considerar Azure NetApp Files para o SAP NetWeaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB.  
- O tamanho mínimo do volume é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, nas quais Azure NetApp Files volumes serão montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files.
- A taxa de transferência de um volume do Azure NetApp é uma função da cota de volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes da NetApp do Azure do HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.  
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (leitura & gravação, somente leitura, etc.). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente Azure NetApp Files recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atento às possíveis implicações de latência em algumas regiões do Azure.  
- É importante ter as máquinas virtuais implantadas de perto para o armazenamento do Azure NetApp para baixa latência. Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto.  
- A ID de usuário para <b>Sid</b>ADM e a ID de grupo para `sapsys` nas máquinas virtuais devem corresponder à configuração no Azure NetApp files. 

> [!IMPORTANT]
> Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre a ID de usuário para <b>Sid</b>ADM e a ID de grupo para `sapsys` entre a máquina virtual e a configuração do Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp, montadas em máquinas virtuais, serão exibidas como `nobody`. Certifique-se de especificar a ID de usuário correta para <b>Sid</b>ADM e a ID de grupo para `sapsys`, quando [um novo sistema for integrado](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ao Azure NetApp files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para o banco de dados HANA no Azure NetApp Files

A taxa de transferência de um volume do Azure NetApp é uma função do tamanho do volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao projetar a infraestrutura para SAP no Azure, você deve estar ciente de alguns requisitos mínimos de armazenamento pelo SAP, que se convertem em características de taxa de transferência mínima:

- Habilitar leitura/gravação em/Hana/log de 250 MB/s com tamanhos de e/s de 1 MB  
- Habilite a atividade de leitura de pelo menos 400 MB/s para/Hana/data para os tamanhos de e/s de 16 MB e 64 MB  
- Habilitar a atividade de gravação de pelo menos 250 MB/s para/Hana/data com tamanhos de e/s de 16 MB e 64 MB  

Os [limites de taxa de transferência de Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 Tib de cota de volume são:
- Camada de armazenamento Premium-64 MiB/s  
- Camada de armazenamento ultra-128 MiB/s  

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log, e de acordo com as diretrizes para `/hana/shared`, os tamanhos recomendados seriam semelhantes a:

| Volume | Tamanho<br /> Camada de armazenamento Premium | Tamanho<br /> Camada de armazenamento ultra |
| --- | --- | --- |
| /Hana/log | 4 TiB | 2 TiB |
| /Hana/data | 6,3 TiB | 3,2 TiB |
| /Hana/Shared | Max (512 GB, 1xRAM) por 4 nós de trabalho | Max (512 GB, 1xRAM) por 4 nós de trabalho |

A configuração de SAP HANA para o layout apresentado neste artigo, usando Azure NetApp Files camada de armazenamento ultra seria semelhante a:

| Volume | Tamanho<br /> Camada de armazenamento ultra |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB |
| /hana/log/mnt00002 | 2 TiB |
| /hana/data/mnt00001 | 3,2 TiB |
| /hana/data/mnt00002 | 3,2 TiB |
| /Hana/Shared | 2 TiB |

> [!NOTE]
> As recomendações de dimensionamento Azure NetApp Files declaradas aqui estão direcionadas para atender aos requisitos mínimos do SAP expressos em direção aos seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, isso pode não ser suficiente. Use essas recomendações como um ponto de partida e adapte-se com base nos requisitos de sua carga de trabalho específica.  

> [!TIP]
> Você pode redimensionar Azure NetApp Files volumes dinamicamente, sem a necessidade de `unmount` os volumes, parar as máquinas virtuais ou parar SAP HANA. Isso permite flexibilidade para atender ao seu aplicativo demandas de produtividade esperadas e imprevistas.

A documentação sobre como implantar uma configuração de expansão SAP HANA com o nó em espera usando volumes NFS v 4.1 hospedados em seja é publicada em [SAP Hana expansão com o nó em espera em VMs do Azure com Azure NetApp files em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, veja:

- [SAP Hana guia de alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
