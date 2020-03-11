---
title: Configurações de armazenamento de máquinas virtuais SAP HANA Azure / Microsoft Docs
description: Recomendações de armazenamento para VM que têm SAP HANA implantado neles.
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080313"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento da máquina virtual do Azure do SAP HANA

O Azure fornece diferentes tipos de armazenamento que são adequados para VMs Azure que estão a executar SAP HANA. Os tipos de **armazenamento Azure certificados SAP HANA** que podem ser considerados para a lista de implementações sap HANA como: 

- Azure Premium SSD  
- [Disco ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber sobre estes tipos de disco, consulte o artigo [Selecione um tipo](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) de disco

O Azure oferece dois métodos de implementação para VHDs em Acabamentos Standard Azure e Armazenamento Premium. Se o cenário geral permitir, aproveite as implementações de [discos geridos pelo Azure.](https://azure.microsoft.com/services/managed-disks/) 

Para obter uma lista de tipos de armazenamento e os seus SLAs em IOPS e entrada de armazenamento, reveja a [documentação azure para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independentemente do tipo de armazenamento Azure escolhido, o sistema de ficheiros utilizado nesse armazenamento precisa de ser suportado pela SAP para o sistema operativo específico e dbmS. [A nota](https://launchpad.support.sap.com/#/notes/405827) de suporte sAP #405827 lista os sistemas de ficheiros suportados para diferentes sistemas operativos e bases de dados, incluindo o SAP HANA. Isto aplica-se a todos os volumes Que o SAP HANA pode ter acesso para leitura e escrita para qualquer tarefa. Especificamente utilizando o NFS em Azure para sap HANA, as restrições adicionais das versões NFS aplicam-se, conforme indicado mais tarde neste artigo 


As condições mínimas certificadas sAP HANA para os diferentes tipos de armazenamento são: 

- Azure Premium SSD - /hana/log é necessário para ser cached com Acelerador de [Escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)Azure . O volume /hana/data pode ser colocado em SSD Premium sem acelerador de escrita Azure ou em disco Ultra
- Disco Azure Ultra pelo menos para o volume /hana/log. O volume /hana/data pode ser colocado em SSD Premium sem acelerador de escrita Azure ou para obter tempos de reinício mais rápidoS disco
- **Volumes NFS v4.1** em cima dos Ficheiros Azure NetApp para /hana/log **e** /hana/data. O volume de /hana/compartilhado pode utilizar o protocolo NFS v3 ou NFS v4.1. O protocolo NFS v4.1 é obrigatório para /hana/data//hana/log volumes.

Alguns dos tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar /hana/dados no Armazenamento Premium e /hana/log pode ser colocado no armazenamento de disco Ultra de forma a obter a baixa latência necessária. No entanto, se utilizar um volume NFS v4.1 baseado em ANF para /hana/dados, é obrigado a utilizar outro volume NFS v4.1 baseado no ANF para /hana/log. Não é **suportado**o uso de NFS em cima da ANF para um dos volumes (como /hana/data) e armazenamento Premium Azure ou disco Ultra para o outro volume (como /hana/log).

No mundo das instalações, raramente se preocupava com os subsistemas de I/S e as suas capacidades. A razão era que o vendedor de aparelhos precisava de se certificar de que os requisitos mínimos de armazenamento são cumpridos para o SAP HANA. Ao construir a infraestrutura Azure, deve estar ciente de alguns destes requisitos emitidos pela SAP. Algumas das características mínimas de entrada que são exigidas à SAP, estão a resultar na necessidade de:

- Ativar a leitura/escrever em **/hana/log** de um 250 MB/seg com tamanhos de 1 MB I/O
- Ativar a atividade de leitura de pelo menos 400 MB/seg para **/hana/dados** para tamanhos de 16 MB e 64 MB I/O
- Ativar a atividade de escrita de pelo menos 250 MB/seg para **/hana/dados** com tamanhos de 16 MB e 64 MB I/O

Dado que a baixa latência de armazenamento é fundamental para os sistemas DBMS, mesmo que o DBMS, como o SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno dos registos de transações dos sistemas DBMS. Mas também operações como escrever pontos de salvamento ou carregar dados na memória após a recuperação do acidente podem ser cruciais. Por isso, é **obrigatório** alavancar discos Premium Azure para **/hana/data** e **/hana/log** volumes. Para obter a entrada mínima de **/hana/log** e **/hana/data,** conforme desejado pelo SAP, é necessário construir um RAID 0 utilizando MDADM ou LVM sobre vários discos de Armazenamento Premium Azure. E utilize os volumes RAID como **/hana/data** e **volumes /hana/log.** 

**Recomendação: Como tamanhos de listras para o RAID 0, a recomendação é usar:**

- 256 KB para **/hana/dados**
- 32 KB para **/hana/log**

> [!IMPORTANT]
> O tamanho da risca para /hana/dados foi alterado de recomendações anteriores, pedindo 64 KB ou 128 KB para 256 KB com base nas experiências do cliente com versões linux mais recentes. O tamanho de 256 KB está proporcionando um desempenho ligeiramente melhor

> [!NOTE]
> Não é necessário configurar qualquer nível de redundância utilizando volumes RAID uma vez que o armazenamento Azure Premium e Standard mantém três imagens de um VHD. A utilização de um volume RAID é puramente para configurar volumes que fornecem uma entrada suficiente de I/S.

Acumular uma série de VHDs Azure por baixo de um RAID, é acumulado a partir de um iOPS e do lado de entrada de armazenamento. Assim, se colocar um RAID 0 sobre discos de armazenamento Premium 3 x P30 Azure, deve dar-lhe três vezes o IOPS e três vezes a entrada de armazenamento de um único disco Azure Premium Storage P30.

Tenha também em mente a entrada geral de I/O em vm ao dimensionar ou decidir um VM. A entrada global de armazenamento vm está documentada no artigo Tamanhos de [máquina virtual otimizados](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)pela memória.

## <a name="linux-io-scheduler-mode"></a>Modo Programador Linux I/O
O Linux tem vários modos de agendamento de I/S diferentes. A recomendação comum através dos fornecedores Linux e do SAP é reconfigurar o modo de programador de I/O para volumes de disco desde o modo **cfq** até ao **noop** (não-multifila) ou **nenhum** para o modo (multifila). Os detalhes são referenciados no [#1984787 de Nota SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com Armazenamento Premium e Acelerador de Escrita Azure para máquinas virtuais da Série M Azure
O Acelerador De Escrita Azure é uma funcionalidade que está disponível exclusivamente para VMs da Série M. Azure. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de I/O de escritas contra o Armazenamento Premium Azure. Para o SAP HANA, o Acelerador de Escrita deve ser utilizado apenas contra o volume **/hana/log.** Portanto, o **/hana/data** e **/hana/log** são volumes separados com o Acelerador de Escrita Azure suportando apenas o volume **/hana/log.** 

> [!IMPORTANT]
> Ao utilizar o Armazenamento Azure Premium, a utilização do Acelerador de [Escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) Azure ou o volume de /hana/log é obrigatória. Write Accelerator está disponível apenas para Armazenamento premium e Série M e VMs sérieM.

As recomendações de cache abaixo estão assumindo as características de I/O para SAP HANA que lista como:

- Quase não há nenhuma carga de trabalho lida contra os ficheiros de dados da HANA. As exceções são de grandes dimensões em I/Os após o reinício da instância HANA ou quando os dados são carregados em HANA. Outro caso de I/Os de leitura maior contra ficheiros de dados pode ser backups de base de dados HANA. Como resultado, a maior parte das vezes não faz sentido, uma vez que na maioria dos casos, todos os volumes de ficheiros de dados precisam de ser lidos completamente.
- Escrever contra os ficheiros de dados é experimentado em explosões baseadas em pontos de salvamento HANA e recuperação de acidentes HANA. Escrever pontos de poupança é assíncrono e não está a atrasar quaisquer transações de utilizadores. Escrever dados durante a recuperação do acidente é fundamental para que o sistema responda rapidamente. No entanto, a recuperação de acidentes deve ser situações bastante excecionais
- Quase não há leituras dos ficheiros hana refazer. As exceções são grandes I/Os ao efetuar backups de registo de transações, recuperação de acidentes ou na fase de reinício de uma instância HANA.  
- A carga principal contra o ficheiro de redo do SAP HANA é escrita. Dependendo da natureza da carga de trabalho, pode ter I/Os tão pequeno como 4 KB ou em outros casos tamanhos de I/O de 1 MB ou mais. Escrever latência contra o registo de redodo SAP HANA é crítico de desempenho.
- Todos os escritos precisam de ser persistidos no disco de uma forma fiável

**Recomendação: Como resultado destes padrões de I/S observados pela SAP HANA, o cache para os diferentes volumes utilizando o Armazenamento Premium Azure deve ser definido como:**

- **/hana/dados** - sem cache
- **/hana/log** - sem cache - exceção para m-e Mv2-Series onde o Acelerador de Escrita deve ser ativado sem o cache lido. 
- **/hana/shared** - ler cache

### <a name="production-recommended-storage-solution"></a>Solução de armazenamento recomendada para produção

> [!IMPORTANT]
> A certificação SAP HANA para máquinas virtuais Azure M-Series é exclusivamente com o Acelerador de Escrita Azure para o volume **/hana/log.** Como resultado, espera-se que as implementações do SAP HANA em máquinas virtuais da Série M Azure sejam configuradas com o Acelerador de Escrita Azure para o volume **/hana/log.**  

> [!NOTE]
> Para cenários de produção, verifique se um certo tipo de VM é suportado para SAP HANA por SAP na [documentação SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As recomendações excedem frequentemente os requisitos mínimos do SAP, tal como referido no início do presente artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o máximo de armazenamento que os diferentes tipos de VM fornecem.

**Recomendação: As configurações recomendadas para cenários de produção parecem:**

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/dados | /hana/log | /hana/compartilhado | /volume de raiz | /usr/sap | hana/backup |
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

Verifique se a entrada de armazenamento dos diferentes volumes sugeridos corresponde à carga de trabalho que pretende executar. Se a carga de trabalho necessitar de volumes mais elevados para **/hana/data** e **/hana/log,** precisa aumentar o número de VHDs de Armazenamento Premium Azure. Dimensionar um volume com mais VHDs do que listado aumenta a entrada de IOPS e I/S dentro dos limites do tipo de máquina virtual Azure.

O Acelerador De Escrita Azure só funciona em conjunto com [discos geridos pela Azure.](https://azure.microsoft.com/services/managed-disks/) Assim, pelo menos os discos de armazenamento Premium Azure que formam o volume **/hana/log** precisam de ser implantados como discos geridos.

Existem limites de VHDs de Armazenamento Premium Azure por VM que podem ser suportados pelo Acelerador de Escrita Azure. Os limites atuais são:

- 16 VHDs para um M128xx e M416xx VM
- 8 VHDs para um M64xx e M208xx VM
- 4 VHDs para um M32xx VM

Instruções mais detalhadas sobre como ativar o acelerador de escrita azure podem ser encontradas no artigo [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Escrita Azure podem ser encontrados na mesma documentação.

**Recomendação: Você precisa usar acelerador de escrita para discos que formem o volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configuração de armazenamento azure consciente do custo
A tabela seguinte mostra uma configuração de tipos VM que os clientes também usam para hospedar SAP HANA em VMs Azure. Pode haver alguns tipos de VM que podem não cumprir todos os critérios mínimos de armazenamento para o SAP HANA ou não são oficialmente suportados com o SAP HANA pela SAP. Mas até agora esses VMs pareciam ter um bom desempenho para cenários de não produção. Os **dados /hana/data** e **/hana/log** são combinados a um volume. Como resultado, o uso do Acelerador de Escrita Azure pode tornar-se uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários suportados pela SAP, verifique se um certo tipo de VM é suportado para SAP HANA por SAP na [documentação SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma mudança das recomendações anteriores para uma solução consciente dos custos, é passar dos [discos HDD Padrão Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para [discos SSD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) mais bem-sucedidos e mais fiáveis

As recomendações excedem frequentemente os requisitos mínimos do SAP, tal como referido no início do presente artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/data e /hana/log<br /> listrado com LVM ou MDADM | /hana/compartilhado | /volume de raiz | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1\.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1\.000 GiB | 1\.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2\.000 GiB | 2\.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os discos recomendados para os tipos de VM mais pequenos com 3 x P20 oversize os volumes relativos às recomendações espaciais de acordo com o [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto, a escolha, tal como apresentada na tabela, foi feita para fornecer uma entrada de disco suficiente para o SAP HANA. Se necessitar de alterações no volume **/hana/backup,** que foi dimensionado para manter cópias de segurança que representam o dobro do volume de memória, sinta-se à vontade para ajustar.   
Verifique se a entrada de armazenamento dos diferentes volumes sugeridos corresponde à carga de trabalho que pretende executar. Se a carga de trabalho necessitar de volumes mais elevados para **/hana/data** e **/hana/log,** precisa aumentar o número de VHDs de Armazenamento Premium Azure. Dimensionar um volume com mais VHDs do que listado aumenta a entrada de IOPS e I/S dentro dos limites do tipo de máquina virtual Azure. 

> [!NOTE]
> As configurações acima não beneficiariam da [máquina virtual Azure Única VM SLA,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) uma vez que utiliza uma mistura de Armazenamento Premium Azure e Armazenamento Padrão Azure. No entanto, a seleção foi escolhida para otimizar os custos. Você precisaria escolher O Armazenamento Premium para todos os discos acima que listados como Azure Standard Storage (Sxx) para tornar a configuração VM em conformidade com o VM SLA single Azure.


> [!NOTE]
> As recomendações de configuração do disco indicadas visam os requisitos mínimos que a SAP expressa para os seus fornecedores de infraestruturas. Em implementações reais de clientes e cenários de carga de trabalho, foram encontradas situações em que estas recomendações ainda não forneceram capacidades suficientes. Estas podem ser situações em que um cliente exigiu uma recarga mais rápida dos dados após um reinício hana ou onde as configurações de backup exigiam maior largura de banda para o armazenamento. Outros casos incluíam **/hana/log** onde 5000 IOPS não eram suficientes para a carga de trabalho específica. Por isso, tome estas recomendações como ponto de partida e adapte-se com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento de disco Azure Ultra para SAP HANA
A Microsoft está em processo de lançar um novo tipo de armazenamento Azure chamado [disco Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). A diferença significativa entre o armazenamento Azure oferecido até agora e o disco Ultra é que as capacidades do disco já não estão ligadas ao tamanho do disco. Como cliente pode definir estas capacidades para disco Ultra:

- Tamanho de um disco que varia de 4 GiB a 65.536 GiB
- Os IOPS variam entre 100 IOPS e 160K IOPS (o máximo depende também dos tipos de VM)
- Entrada de armazenamento de 300 MB/seg a 2.000 MB/seg

O disco ultra dá-lhe a possibilidade de definir um único disco que cumpra o seu tamanho, IOPS e intervalo de entrada de disco. Em vez de utilizar gestores de volumelógicos como o LVM ou o MDADM em cima do Armazenamento Premium Azure para construir volumes que satisfaçam os requisitos de iopS e de armazenamento. Pode executar uma mistura de configuração entre o disco Ultra e o Armazenamento Premium. Como resultado, pode limitar o uso do disco Ultra ao desempenho crítico /hana/data e /hana/log volumes e cobrir os outros volumes com Armazenamento Premium Azure

Outras vantagens do disco Ultra podem ser a latência mais lida em comparação com o Armazenamento Premium. A latência mais rápida pode ter vantagens quando pretende reduzir os tempos de arranque da HANA e a carga subsequente dos dados na memória. Vantagens do armazenamento de discos Ultra também podem ser sentidas quando a HANA está a escrever pontos de poupança. Uma vez que os discos de armazenamento premium para /hana/dados geralmente não são cachede Write Accelerator, a latência escrita para /hana/dados no Armazenamento Premium em comparação com o disco Ultra é maior. Pode esperar-se que a escrita de savepoint com o disco Ultra esteja a ter um melhor desempenho no disco Ultra.

> [!IMPORTANT]
> O disco ultra ainda não está presente em todas as regiões do Azure e também ainda não está a apoiar todos os tipos de VM listados abaixo. Para obter informações detalhadas onde o disco Ultra está disponível e quais as famílias vm que são apoiadas, verifique o artigo Quais os tipos de [discos disponíveis em Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada para produção com configuração de disco Ultra puro
Nesta configuração, mantenha separadamente os volumes /hana/data e /hana/log. Os valores sugeridos são derivados das KPIs que o SAP tem de certificar os tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no Livro Branco de [Armazenamento SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações excedem frequentemente os requisitos mínimos do SAP, tal como referido no início do presente artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/volume de dados | /hana/dados I/O | /hana/data IOPS | /hana/volume de registo | /hana/log I/O | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1\.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBps |9,000 | 512 GB | 800 MBps  | 3\.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3\.000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBps | 9,000 | 512 GB | 800 MBps  | 3\.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 9,000 | 512 GB | 800 MBps  | 3\.000 |   

Os valores enumerados destinam-se a ser um ponto de partida e precisam de ser avaliados em comparação com as reais exigências. A vantagem com o disco Azure Ultra é que os valores para iOPS e produção podem ser adaptados sem a necessidade de desligar o VM ou parar a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até ao momento, não estão disponíveis instantâneos de armazenamento com armazenamento de discos Ultra. Isto bloqueia o uso de instantâneos VM com serviços de backup Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento consciente de custo com configuração de disco Ultra puro
Nesta configuração, você os volumes /hana/data e /hana/log no mesmo disco. Os valores sugeridos são derivados das KPIs que o SAP tem de certificar os tipos de VM para SAP HANA e configurações de armazenamento, conforme recomendado no Livro Branco de [Armazenamento SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

As recomendações excedem frequentemente os requisitos mínimos do SAP, tal como referido no início do presente artigo. As recomendações listadas são um compromisso entre as recomendações de tamanho da SAP e o máximo de armazenamento que os diferentes tipos de VM fornecem.

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | Volume para /hana/data e /log | /hana/data e log I/O | /hana/data e iOPS de log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10,000 | 
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10,000 | 
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10,000 | 
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 MBps |12.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 MBps |12.000 |  
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.700 GB | 1\.800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 15.000 GB | 1\.800 MBps | 12.000 |    

Os valores enumerados destinam-se a ser um ponto de partida e precisam de ser avaliados em comparação com as reais exigências. A vantagem com o disco Azure Ultra é que os valores para iOPS e produção podem ser adaptados sem a necessidade de desligar o VM ou parar a carga de trabalho aplicada ao sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v4.1 em Ficheiros Azure NetApp
O Azure NetApp Files fornece ações nativas da NFS que podem ser usadas para /hana/partilhado, /hana/data, e /hana/log volumes. A utilização de ações nfs baseadas em ANF para os volumes /hana/data e /hana/log requer a utilização do protocolo V4.1 NFS. O protocolo NFS v3 não é suportado para a utilização de volumes /hana/data e /hana/log ao basear as ações na ANF. 

> [!IMPORTANT]
> O protocolo NFS v3 implementado nos Ficheiros Azure NetApp **não** é suportado para ser utilizado para /hana/data e /hana/log. A utilização do NFS 4.1 é obrigatória para /hana/data e /hana/log volumes de um ponto de vista funcional. Considerando que, para o volume /hana/partilhado, o NFS v3 ou o protocolo NFS v4.1 podem ser utilizados do ponto de vista funcional.

### <a name="important-considerations"></a>Considerações importantes
Ao considerar os Ficheiros Azure NetApp para o SAP Netweaver e SAP HANA, esteja ciente das seguintes considerações importantes:

- A piscina de capacidade mínima é 4 TiB.  
- O tamanho mínimo do volume é de 100 GiB
- Os Ficheiros Azure NetApp e todas as máquinas virtuais, onde serão montados volumes de Ficheiros Azure NetApp, devem estar na mesma Rede Virtual Azure ou em [redes virtuais na](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mesma região.  
- A rede virtual selecionada deve ter uma subnet, delegada nos Ficheiros Azure NetApp.
- A produção de um volume Azure NetApp é uma função do nível de quota de volume e de serviço, tal como documentado no nível de [Serviço para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes HANA Azure NetApp, certifique-se de que a entrada resultante satisfaz os requisitos do sistema HANA.  
- O Azure NetApp Files oferece política de [exportação:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)pode controlar os clientes permitidos, o tipo de acesso (Read&Write, Read Only, etc.). 
- A funcionalidade De Ficheiros Azure NetApp ainda não está ciente da zona. Atualmente, a funcionalidade Azure NetApp Files não está implementada em todas as zonas de disponibilidade de uma região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.  
- É importante que as máquinas virtuais se desloque nas proximidades do armazenamento Azure NetApp para baixa latência. 
- O ID do utilizador para <b>sid</b>adm e o ID do grupo para `sapsys` nas máquinas virtuais devem corresponder à configuração nos Ficheiros Azure NetApp. 

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é crítica. Trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Ficheiros Azure NetApp são implantados nas proximidades.  

> [!IMPORTANT]
> Se houver um desfasamento entre o ID do utilizador para <b>sid</b>adm e o ID do grupo para `sapsys` entre a máquina virtual e a configuração Azure NetApp, as permissões para ficheiros em volumes Azure NetApp, montados em máquinas virtuais, serão apresentadas como `nobody`. Certifique-se de especificar o ID de utilizador correto para <b>sid</b>adm e o ID do grupo para `sapsys`, quando [embarcar um novo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) para Ficheiros Azure NetApp.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para base de dados HANA em Ficheiros Azure NetApp

A entrada de um volume Azure NetApp é uma função do tamanho do volume e nível de serviço, conforme documentado no nível de [Serviço para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao conceber a infraestrutura para SAP em Azure, deve estar ciente de alguns requisitos mínimos de armazenamento por Parte Da SAP, que se traduzem em características mínimas de rendimento de:

- Ativar a leitura/escrever em /hana/log de um 250 MB/seg com tamanhos de 1 MB I/O  
- Ativar a atividade de leitura de pelo menos 400 MB/seg para /hana/dados para tamanhos de 16 MB e 64 MB I/O  
- Ativar a atividade de escrita de pelo menos 250 MB/seg para /hana/dados com tamanhos de 16 MB e 64 MB I/O  

Os limites de produção dos [Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 TiB de quota de volume são:
- Premium Storage Tier - 64 MiB/s  
- Ultra Storage Tier - 128 MiB/s  

> [!IMPORTANT]
> Independentemente da capacidade que desloque num único volume NFS, espera-se que o planalto atinja a largura de banda de 1,2-1,4 GB/seg alavancada por um consumidor numa máquina virtual. Isto tem a ver com a arquitetura subjacente da oferta da ANF e os limites de sessão linux relacionados em torno do NFS. Os números de desempenho e de desempenho, tal como documentados no artigo, foram realizados resultados de testes de [referência de desempenho para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) contra um volume NFS partilhado com vários VMs de clientes e como resultado com várias sessões. Este cenário é diferente do cenário que medimos no SAP. Quando medimos a entrada de um único VM contra um volume NFS. hospedado na ANF.

Para satisfazer os requisitos mínimos de entrada de dados e registo sapeianos e, de acordo com as diretrizes para `/hana/shared`, os tamanhos recomendados seriam:

| Volume | Tamanho<br /> Nível de armazenamento premium | Tamanho<br /> Nível ultra armazenamento | Protocolo NFS suportado |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/dados | 6.3 Tib | 3.2 Tib | v4.1 |
| /hana/compartilhado | Máx (512 GB, 1xRAM) por 4 nós de trabalhador | Máx (512 GB, 1xRAM) por 4 nós de trabalhador | v3 ou v4.1 |


> [!NOTE]
> As recomendações de dimensionamento do Azure NetApp, aqui indicadas, visam cumprir os requisitos mínimos que a SAP expressa para os seus fornecedores de infraestruturas. Em implementações reais de clientes e cenários de carga de trabalho, isso pode não ser suficiente. Utilize estas recomendações como ponto de partida e adapte-se, com base nos requisitos da sua carga de trabalho específica.  

Por isso, pode considerar a implementação de um misto semelhante para os volumes ANF listados para armazenamento de discos Ultra já. Considere também os tamanhos dos tamanhos listados para os volumes para as diferentes VM SKUs já feitas nas tabelas de discos Ultra.

> [!TIP]
> Pode redimensionar os volumes do Azure NetApp Files de forma dinâmica, sem a necessidade de `unmount` os volumes, parar as máquinas virtuais ou parar o SAP HANA. Isso permite flexibilidade para satisfazer a sua aplicação, tanto esperadas como imprevistas exigências de entrada.

A documentação sobre como implementar uma configuração de escala SAP HANA com nó de standby utilizando volumes NFS v4.1 que estão hospedados na ANF é publicada em [escala SAP HANA com nó de standby em VMs Azure com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte:

- [Guia de alta disponibilidade SAP HANA para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
