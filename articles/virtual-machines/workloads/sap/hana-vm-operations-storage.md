---
title: Configurações de armazenamento de máquinas virtuais SAP HANA Azure Microsoft Docs
description: Recomendações de armazenamento para VM que têm SAP HANA implantado neles.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, Disco Ultra de Armazenamento, Armazenamento Premium
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bbaa9d33d3a31b682a66b2a3254fc2265b6f8d7b
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357082"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento da máquina virtual do Azure do SAP HANA

O Azure fornece diferentes tipos de armazenamento adequados para VMs Azure que estão executando SAP HANA. Os **tipos de armazenamento certificados Azure certificados DA SAP HANA** que podem ser considerados para a lista de implementações SAP HANA como: 

- SSD premium azul ou armazenamento premium 
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Para saber destes tipos de disco, consulte os [tipos de armazenamento Azure do](./planning-guide-storage.md) artigo para a carga de trabalho SAP e [selecione um tipo de disco](../../disks-types.md)

A Azure oferece dois métodos de implementação para VHDs em Azure Standard e armazenamento premium. Esperamos que aproveite o disco gerido pela [Azure](https://azure.microsoft.com/services/managed-disks/) para implementações de armazenamento de blocos Azure. 

Para obter uma lista de tipos de armazenamento e seus SLAs em IOPS e produção de armazenamento, reveja a [documentação Azure para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Independentemente do tipo de armazenamento Azure escolhido, o sistema de ficheiros utilizado nesse armazenamento tem de ser suportado pela SAP para o sistema operativo específico e o DBMS. [Nota de suporte SAP #2972496](https://launchpad.support.sap.com/#/notes/2972496) lista os sistemas de ficheiros suportados para diferentes sistemas operativos e bases de dados, incluindo SAP HANA. Isto aplica-se a todos os volumes que o SAP HANA poderá ter acesso para leitura e escrita para qualquer tarefa. Especificamente utilizando NFS on Azure para SAP HANA, restrições adicionais de versões NFS aplicam-se como indicado mais tarde neste artigo 


As condições mínimas certificadas SAP HANA para os diferentes tipos de armazenamento são: 

- Armazenamento premium Azure - **/hana/log** é necessário para ser suportado pelo Acelerador de [Escrita](../../how-to-enable-write-accelerator.md)Azure . O volume **/hana/dados** poderia ser colocado em armazenamento premium sem acelerador de escrita Azure ou em disco ultra
- Disco Azure Ultra pelo menos para o volume **/hana/log.** O volume **/hana/dados** pode ser colocado em armazenamento premium sem acelerador de escrita Azure ou para obter tempos de reinício mais rápidos Disco ultra
- **Volumes NFS v4.1** em cima de Ficheiros Azure NetApp para **/hana/log e /hana/data**. O volume de /hana/partilhado pode utilizar o protocolo NFS v3 ou NFS v4.1

Alguns dos tipos de armazenamento podem ser combinados. Por exemplo, é possível colocar **/hana/dados** no armazenamento premium e **/hana/log** pode ser colocado no armazenamento de disco ultra para obter a latência baixa necessária. Se utilizar um volume baseado em ANF para **/hana/dados,** o volume  **/hana/log** também tem de ser baseado em NFS em cima da ANF. A utilização de NFS em cima da ANF para um dos volumes (como /hana/dados) e o armazenamento premium Azure ou disco Ultra para o outro volume (como **/hana/log)** não é **suportado**.

No mundo dos locais, raramente se preocupava com os subsistemas de E/S e as suas capacidades. A razão foi que o vendedor de aparelhos precisava de se certificar de que os requisitos mínimos de armazenamento são cumpridos para o SAP HANA. Ao construir a infraestrutura Azure, deve estar ciente de alguns destes requisitos emitidos pela SAP. Algumas das características mínimas de produção que a SAP recomenda são:

- Ler/escrever em **/hana/log** de 250 MB/seg com 1 MB Tamanhos de E/S
- Ler atividade de pelo menos 400 MB/seg para **/hana/dados** para 16 tamanhos de MB e 64 MB I/O
- Atividade de escrita de pelo menos 250 MB/seg para **/hana/dados** com tamanhos de 16 MB e 64 MB I/O

Dado que a baixa latência de armazenamento é fundamental para os sistemas DBMS, mesmo que o DBMS, como o SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de transações escritas dos sistemas DBMS. Mas também operações como escrever pontos de salvamento ou carregar dados na memória após a recuperação do acidente podem ser críticas. Por isso, é **obrigatório** alavancar o armazenamento premium Azure, disco ultra ou ANF para **/hana/data** e **/hana/log** volumes. 


Alguns princípios orientadores na seleção da sua configuração de armazenamento para HANA podem ser listados como:

- Decida o tipo de armazenamento com base nos [tipos de armazenamento Azure para a carga de trabalho SAP](./planning-guide-storage.md) e [selecione um tipo de disco](../../disks-types.md)
- A produção global de VM I/O e os limites de IOPS em mente ao dimensionar ou decidir para um VM. A produção geral de armazenamento de VM está documentada no artigo [Tamanhos de máquina virtual otimizados memória](../../sizes-memory.md)
- Ao decidir a configuração de armazenamento, tente manter-se abaixo da produção geral do VM com a sua configuração **/hana/volume de dados.** Escrevendo pontos de salvamento, SAP HANA pode ser agressivo emitindo I/Os. É facilmente possível empurrar até aos limites de produção do seu volume **/hana/dados** ao escrever um ponto de poupança. Se o seu (s) disco(s) que constrói o volume **/hana/dados** tiver uma produção superior à que o seu VM permite, poderá encontrar situações em que a produção utilizada pela escrita de pontos de salvamento esteja a interferir com as exigências de produção do registo de redo. Uma situação que pode afetar a produção de aplicações
- Se estiver a utilizar o armazenamento premium Azure, a configuração menos dispendiosa é usar gestores de volume lógicos para construir conjuntos de riscas para construir os volumes **/hana/dados** e **/hana/log**

> [!IMPORTANT]
> As sugestões para as configurações de armazenamento destinam-se a ser feitas como direções para começar. Executando a carga de trabalho e analisando padrões de utilização de armazenamento, você pode perceber que você não está usando toda a largura de banda de armazenamento ou IOPS fornecido. Então, pode considerar reduzir o tamanho no armazém. Ou, ao contrário, a sua carga de trabalho pode precisar de mais produção de armazenamento do que sugerida com estas configurações. Como resultado, poderá ter de implantar mais capacidade, IOPS ou produção. No campo da tensão entre a capacidade de armazenamento necessária, a latência de armazenamento necessária, a produção de armazenamento e a configuração iops necessária e menos dispendiosa, o Azure oferece diferentes tipos de armazenamento com diferentes capacidades e diferentes pontos de preço para encontrar e ajustar-se ao compromisso certo para si e para a sua carga de trabalho HANA.

## <a name="linux-io-scheduler-mode"></a>Modo de Programador Linux I/O
Linux tem vários modos de agendamento de I/O diferentes. Recomendação comum através dos fornecedores Linux e SAP é reconfigurar o modo de programador de E/S para volumes de discos desde o **mq-deadline** ou modo **kyber** até ao modo **noop** (não multiqueue) ou **nenhum** para o modo (multiqueue). Os detalhes são referenciados na [#1984787 da Nota SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluções com armazenamento premium e Acelerador de Escrita Azure para máquinas virtuais Azure M-Series
O Azure Write Accelerator é uma funcionalidade disponível exclusivamente para VMs da Série M-Série Azure. Como o nome afirma, o objetivo da funcionalidade é melhorar a latência de I/O de escritas contra o armazenamento premium Azure. Para o SAP HANA, o Acelerador de Escrita deve ser utilizado apenas contra o volume **/hana/log.** Portanto, o **/hana/data** e **/hana/log** são volumes separados com acelerador de escrita Azure suportando apenas o volume **/hana/log.** 

> [!IMPORTANT]
> Ao utilizar o armazenamento premium Azure, a utilização do Acelerador de [Escrita](../../how-to-enable-write-accelerator.md) Azure para o volume **/hana/log** é obrigatória. O Write Accelerator está disponível apenas para armazenamento premium e M-Series e VMs Mv2-Series. O Write Accelerator não está a funcionar em combinação com outras famílias Azure VM, como o Esv3 ou o Edsv4.

As recomendações de cache para discos premium Azure abaixo assumem as características de E/S para SAP HANA que lista como:

- Quase não há carga de trabalho lida contra os ficheiros de dados da HANA. As exceções são de grande tamanho I/Os após o reinício da instância HANA ou quando os dados são carregados em HANA. Outro caso de maior leitura de I/Os contra ficheiros de dados pode ser a cópia de segurança da base de dados HANA. Como resultado, a leitura geral não faz sentido, uma vez que na maioria dos casos, todos os volumes de ficheiros de dados precisam de ser lidos completamente. 
- Escrever contra os ficheiros de dados é experimentado em rajadas baseadas em pontos de salvamento HANA e recuperação de acidentes HANA. Escrever pontos de poupança é assíncronos e não está a atrasar nenhuma transação do utilizador. Escrever dados durante a recuperação de acidentes é um desempenho crítico para que o sistema responda rapidamente novamente. No entanto, a recuperação de acidentes deve ser situações bastante excecionais
- Quase não há leituras dos ficheiros de redo da HANA. As exceções são grandes I/Os quando efetos cópias de segurança de registo de transações, recuperação de acidentes ou na fase de reinício de um caso HANA.  
- A carga principal contra o ficheiro de registo de redo SAP HANA é escrita. Dependendo da natureza da carga de trabalho, pode ter I/Os tão pequeno como 4 KB ou em outros casos tamanhos de E/S de 1 MB ou mais. Escrever latência contra o registo de redo SAP HANA é crítico de desempenho.
- Todos os escritos precisam ser persistidos no disco de uma forma confiável

**Recomendação: Em resultado destes padrões de E/S observados pela SAP HANA, o caching para os diferentes volumes que utilizam o armazenamento premium Azure deve ser definido como:**

- **/hana/data** - sem caching ou leitura de caching
- **/hana/log** - sem caching - exceção para VMs M e Mv2-Series onde o Acelerador de Escrita Azure deve ser ativado 
- **/hana/shared** - ler caching
- **Disco oss** - não altere o cache padrão que é definido por Azure no tempo de criação do VM


Se estiver a utilizar LVM ou mdadm para construir conjuntos de listras em vários discos premium Azure, precisa de definir tamanhos de listras. Estes tamanhos diferem entre **/hana/data** e **/hana/log**. **Recomendação: Como tamanhos de listras, a recomendação é usar:**

- 256 KB para **/hana/dados**
- 64 KB para **/hana/log**

> [!NOTE]
> O tamanho das listras para **/hana/dados** foi alterado de recomendações anteriores que pediam 64 KB ou 128 KB para 256 KB com base em experiências de clientes com versões Linux mais recentes. O tamanho de 256 KB está a proporcionar um desempenho ligeiramente melhor. Também alteramos a recomendação para tamanhos de listras de **/hana/log** de 32 KB para 64 KB, de modo a obter rendimento suficiente com tamanhos de E/S maiores.

> [!NOTE]
> Não é necessário configurar qualquer nível de redundância utilizando volumes RAID, uma vez que o armazenamento do bloco Azure mantém três imagens de um VHD. A utilização de uma risca com discos premium Azure é puramente para configurar volumes que fornecem produção de IOPS e/ou I/O suficiente.

A acumulação de vários VHDs Azure por baixo de um conjunto de listras, é acumulado a partir de um IOPS e lado de produção de armazenamento. Assim, se colocar uma risca em mais de 3 x P30 Azure discos de armazenamento premium, deve dar-lhe três vezes o IOPS e três vezes a produção de armazenamento de um único disco Azure premium Storage P30.

> [!IMPORTANT]
> Caso esteja a utilizar o LVM ou o mdadm como gestor de volume para criar conjuntos de riscas em vários discos premium Azure, os três SAP HANA FileSystems /data, /log e /shared não devem ser colocados num grupo de volume padrão ou raiz. É altamente recomendado seguir a orientação dos Fornecedores Linux que é tipicamente para criar grupos de volume individuais para /dados, /log e /compartilhados.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funcionalidade de explosão Azure para armazenamento premium
Para discos de armazenamento premium Azure menores ou iguais a 512 GiB na capacidade, é oferecida a funcionalidade de explosão. A forma exata como funciona a explosão do disco é descrita no artigo [Disco rebentando](../../linux/disk-bursting.md). Ao ler o artigo, compreende o conceito de acumulação de IOPS e produção nos tempos em que a sua carga de trabalho de I/S está abaixo do IOPS nominal e da produção dos discos (para mais detalhes sobre a produção nominal ver [preços do Disco Gerido).](https://azure.microsoft.com/pricing/details/managed-disks/) Você vai acumular o delta do IOPS e a produção entre o seu uso atual e os valores nominais do disco. As rajadas estão limitadas a um máximo de 30 minutos.

Os casos ideais em que esta funcionalidade de explosão pode ser planeada serão provavelmente os volumes ou discos que contêm ficheiros de dados para os diferentes DBMS. Espera-se que a carga de trabalho de E/S contra esses volumes, especialmente com sistemas de pequena a média gama, se pareça com:

- Carga de trabalho de leitura baixa a moderada, uma vez que os dados idealmente estão em cache na memória, ou como no caso da HANA deve estar completamente na memória
- Explosões de escrita desencadeadas por pontos de verificação de bases de dados ou pontos de poupança que são emitidos regularmente
- Carga de trabalho de backup que lê num fluxo contínuo em casos em que cópias de segurança não são executadas através de instantâneos de armazenamento
- Para SAP HANA, carregue os dados na memória após um reinício de um caso

Especialmente em sistemas DBMS mais pequenos, onde a sua carga de trabalho está a lidar com algumas centenas de transações por segundo apenas, tal funcionalidade de explosão também pode fazer sentido para os discos ou volumes que armazenam a transação ou redo log. A carga de trabalho esperada contra tal disco ou volumes parece:

- Escreve regularmente para o disco que depende da carga de trabalho e da natureza da carga de trabalho, uma vez que cada compromisso emitido pela aplicação é suscetível de desencadear uma operação de E/S
- Maior carga de trabalho em produção para casos de tarefas operacionais, como a criação ou reconstrução de índices
- Leia as rajadas ao realizar registos de transações ou refazer backups de registos


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Solução de armazenamento recomendada de produção com base no armazenamento premium Azure

> [!IMPORTANT]
> A certificação SAP HANA para máquinas virtuais Azure M-Series é exclusivamente com O Acelerador de Escrita Azure para o volume **/hana/log.** Como resultado, espera-se que as implementações do cenário de produção SAP HANA em máquinas virtuais Azure M-Series sejam configuradas com acelerador de escrita Azure para o volume **/hana/log.**  

> [!NOTE]
> Em cenários que envolvem armazenamento premium Azure, estamos a implementar capacidades de explosão na configuração. Como está a utilizar ferramentas de teste de armazenamento de qualquer forma ou forma, mantenha em mente a forma como o [disco premium Azure rebenta.](../../linux/disk-bursting.md) Executando os testes de armazenamento entregues através da ferramenta SAP HWCCT ou HCMT, não esperamos que todos os testes passem os critérios, uma vez que alguns dos testes excederão os créditos de rebentamento que pode acumular. Especialmente quando todos os testes correm sequencialmente sem pausa.


> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para SAP HANA pela SAP na [documentação SAP para a IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recomendação: As configurações recomendadas com armazenamento premium Azure para cenários de produção parecem:**

Configuração para volume SAP **/hana/dados:**

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/dados | Débito Aprovisionado | Produção máxima de rajada | IOPS | IOPS de explosão |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | GiB de 192 | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M64ls | 512 GiB | 1.000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2.000 | 14,000 |
| M64s | 1.000 GiB | 1.000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4,400 | 14,000 |
| M64ms | 1.750 GiB | 1.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200 | 14,000 |  
| M128s | 2.000 GiB | 2.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200| 14,000 | 
| M128ms | 3.800 GiB | 2.000 MBps | 4 x P30 | 800 MBps | sem estourar | 20 000 | sem estourar | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 4 x P30 | 800 MBps | sem estourar | 20 000| sem estourar | 
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 4 x P40 | 1.000 MBps | sem estourar | 30,000 | sem estourar |
| M416s_v2 | 5.700 GiB | 2.000 MBps | 4 x P40 | 1.000 MBps | sem estourar | 30,000 | sem estourar |
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 4 x P50 | 2.000 MBps | sem estourar | 30,000 | sem estourar |


Para o volume **/hana/log.** a configuração seria como:

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | **/volume de hana/log** | Débito Aprovisionado | Produção máxima de rajada | IOPS | IOPS de explosão |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | GiB de 192 | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10,500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10,500 | 
| M64ls | 512 GiB | 1.000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10,500 | 
| M64s | 1.000 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M64ms | 1.750 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M128s | 2.000 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500|  
| M128ms | 3.800 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416s_v2 | 5.700 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 


Para os outros volumes, a configuração seria como:

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/compartilhado | /volume de raiz | /usr/seiva |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | GiB de 192 | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1.000 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1.750 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2.000 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3.800 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2.850 GiB | 1.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5.700 GiB | 2.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Verifique se o rendimento de armazenamento dos diferentes volumes sugeridos corresponde à carga de trabalho que pretende executar. Se a carga de trabalho requer volumes mais elevados para **/hana/dados** e **/hana/log,** é necessário aumentar o número de VHDs de armazenamento premium Azure. Dimensionar um volume com mais VHDs do que listado aumenta a produção de IOPS e I/O dentro dos limites do tipo de máquina virtual Azure.

O Acelerador Azure Write só funciona em conjunto com [discos geridos pela Azure.](https://azure.microsoft.com/services/managed-disks/) Assim, pelo menos os discos de armazenamento premium Azure que formam o volume **/hana/log** precisam de ser implantados como discos geridos. Instruções e restrições mais detalhadas do Acelerador de Escrita Azure podem ser encontradas no artigo [Write Accelerator](../../how-to-enable-write-accelerator.md).

Para os VMs certificados HANA da família Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) e do [Edsv4,](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)é necessário fazer anF para o volume **/hana/data** e **/hana/log.** Ou precisa aproveitar o armazenamento do disco Azure Ultra em vez do armazenamento premium Azure apenas para o volume **/hana/log.** Como resultado, as configurações para o volume **/hana/dados** no armazenamento premium Azure podem parecer:

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/dados | Débito Aprovisionado | Produção máxima de rajada | IOPS | IOPS de explosão |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10,500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1500 | 10,500|
| E48ds_v4 | 384 GiB | 1.152 MBps | 3 x P15 |  375 MBps |510 MBps | 3,300  | 10,500 | 
| E64ds_v4 | 504 GiB | 1.200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 
| E64s_v3 | 432 GiB | 1.200 MB/s | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 

Para os outros volumes, incluindo **/hana/log** no disco Ultra, a configuração pode parecer:

| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /volume de hana/log | /hana/log I/O produção | /hana/log IOPS | /hana/compartilhado | /volume de raiz | /usr/seiva |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1.152 MBps | 192 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1.200 MBps | 256 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1.200 MBps | 220 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuração de armazenamento de disco Azure Ultra para SAP HANA
Outro tipo de armazenamento Azure chama-se [disco Azure Ultra](../../disks-types.md#ultra-disk). A diferença significativa entre o armazenamento Azure oferecido até agora e o disco Ultra é que as capacidades do disco já não estão ligadas ao tamanho do disco. Como cliente, pode definir estas capacidades para disco Ultra:

- Tamanho de um disco que vai de 4 GiB a 65.536 GiB
- Os IOPS variam entre 100 IOPS e 160K IOPS (o máximo depende também dos tipos VM)
- Produção de armazenamento de 300 MB/seg para 2.000 MB/seg

O disco ultra dá-lhe a possibilidade de definir um único disco que cumpra o seu tamanho, IOPS e gama de saída de disco. Em vez de utilizar gestores de volume lógicos como LVM ou MDADM em cima do armazenamento premium Azure para construir volumes que satisfaçam os requisitos de produção de IOPS e armazenamento. Pode executar uma mistura de configuração entre o disco Ultra e o armazenamento premium. Como resultado, pode limitar a utilização do disco Ultra ao desempenho crítico **/hana/volumes** de dados e **/hana/log** e cobrir os outros volumes com armazenamento premium Azure

Outras vantagens do disco Ultra podem ser a latência mais lida em comparação com o armazenamento premium. A latência de leitura mais rápida pode ter vantagens quando se pretende reduzir os tempos de arranque HANA e a carga subsequente dos dados na memória. As vantagens do armazenamento ultra-disco também podem ser sentidas quando hana está escrevendo pontos de salvamento. 

> [!NOTE]
> O disco ultra ainda não está presente em todas as regiões do Azure e também não está ainda a apoiar todos os tipos de VM listados abaixo. Para obter informações detalhadas sobre onde o disco Ultra está disponível e quais as famílias VM que são apoiadas, verifique o artigo [Que tipos de disco estão disponíveis no Azure?](../../disks-types.md#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Solução de armazenamento recomendada de produção com configuração de disco Ultra puro
Nesta configuração, mantenha os **volumes /hana/data** e **/hana/log** separadamente. Os valores sugeridos são derivados dos KPI's que o SAP tem de certificar os tipos de VM para as configurações DE SAP HANA e armazenamento, conforme recomendado no [papel branco de armazenamento SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As recomendações excedem frequentemente os requisitos mínimos do SAP, tal como indicado anteriormente neste artigo. As recomendações enumeradas são um compromisso entre as recomendações de tamanho por SAP e a produção máxima de armazenamento que os diferentes tipos de VM fornecem.

> [!NOTE]
> O disco Azure Ultra está a impor um mínimo de 2 IOPS por gigabyte de capacidade de um disco


| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /volume de dados /hana/volume de dados | /hana/data I/O produção | /hana/data IOPS | /volume de hana/log | /hana/log I/O produção | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MBps | 2.500 | 80 GB | 250 MB | 1.800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2.500 | 128 GB | 250 MBps | 1.800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBps | 3.000 | 192 GB | 250 MBps | 1.800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBps | 3500 |  256 GB | 250 MBps | 1.800 |
| E64s_v3 | 432 GiB | 1.200 MB/s | 610 GB | 400 MBps | 3500 | 220 GB | 250 MB | 1.800 |
| M32ts | GiB de 192 | 500 MB/s | 250 GB | 400 MBps | 2.500 | 96 GB | 250 MBps  | 1.800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2.500 | 256 GB | 250 MBps  | 1.800 |
| M64ls | 512 GiB | 1.000 MB/s | 620 GB | 400 MBps | 3500 | 256 GB | 250 MBps  | 1.800 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 5000 | 512 GB | 250 MBps  | 2.500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 5000 | 512 GB | 250 MBps  | 2.500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2.500 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 750 MBps |9600 | 512 GB | 250 MBps  | 2.500 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2.500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 750 MBps | 14,400 | 512 GB | 250 MBps  | 2.500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.000 MBps | 14,400 | 512 GB | 400 MBps  | 4000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 28,800 | 512 GB | 400 MBps  | 4000 |   

**Os valores enumerados destinam-se a ser um ponto de partida e precisam de ser avaliados em face das exigências reais.** A vantagem com o disco Azure Ultra é que os valores para IOPS e produção podem ser adaptados sem a necessidade de desligar o VM ou parar a carga de trabalho aplicada ao sistema.   

> [!NOTE]
> Até agora, não estão disponíveis instantâneos de armazenamento com ultra disco. Isto bloqueia o uso de instantâneos VM com serviços de backup Azure


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumes NFS v4.1 em ficheiros Azure NetApp
Para obter detalhes sobre a ANF para HANA, leia o documento [NFS v4.1 volumes em Azure NetApp Files for SAP HANA](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Solução consciente de custos com armazenamento premium Azure
Até agora, a solução de armazenamento premium Azure descrita neste documento na secção [Soluções com armazenamento premium e acelerador de escrita Azure para máquinas virtuais Azure M-Series destinavam-se](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) a cenários apoiados pela produção SAP HANA. Uma das características das configurações de produção suportais é a separação dos volumes para dados SAP HANA e refazer o registo em dois volumes diferentes. A razão para tal separação é que as características da carga de trabalho nos volumes são diferentes. E que, com as configurações de produção sugeridas, poderiam ser necessários diferentes tipos de caching ou mesmo diferentes tipos de armazenamento de blocos Azure. As configurações suportadas pela produção utilizando o alvo de armazenamento do bloco Azure também estão em conformidade com o [SLA VM único para máquinas virtuais Azure.](https://azure.microsoft.com/support/legal/sla/virtual-machines/)  No caso de cenários não produtivos, algumas das considerações tomadas para os sistemas de produção podem não se aplicar a sistemas de não produção mais baixos. Como resultado, os dados HANA e o volume de registo podem ser combinados. Embora eventualmente com alguns culpados, como eventualmente não cumprir certos KPI's de produção ou latência que são necessários para os sistemas de produção. Outro aspeto para reduzir os custos nestes ambientes pode ser a utilização do [armazenamento SSD Standard Azure.](./planning-guide-storage.md#azure-standard-ssd-storage) Embora uma escolha que invalida o [único VM SLA para máquinas virtuais Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Uma alternativa menos dispendiosa para tais configurações poderia parecer:


| SKU da VM | RAM | Um máximo de VM I/O<br /> Débito | /hana/dados e /hana/log<br /> listrado com LVM ou MDADM | /hana/compartilhado | /volume de raiz | /usr/seiva | comentários |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM tipo não certificado HANA <br /> Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| M32ts | GiB de 192 | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 5.000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM tipo não certificado HANA <br /> Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 5.000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1.152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| E64v3 | 432 GiB | 1.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Não conseguirá menos de 1 ms de latência de armazenamento<sup>1</sup> |
| M64ls | 512 GiB | 1.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M64s | 1.000 GiB | 1.000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M64ms | 1.750 GiB | 1.000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M128s | 2.000 GiB | 2.000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 10.000<sup>2</sup> |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 20.000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | A utilização do Acelerador de Escrita para dados combinados e volume de registo limitará a taxa de IOPS a 20.000<sup>2</sup> |


<sup>1</sup> [O Acelerador de Escrita Azure](../../how-to-enable-write-accelerator.md) não pode ser utilizado com as famílias Ev4 e Ev4 VM. Como resultado da utilização do armazenamento premium Azure, a latência de E/S não será inferior a 1 ms

<sup>2</sup> A família VM suporta [o Acelerador de Escrita Azure,](../../how-to-enable-write-accelerator.md)mas existe um potencial para que o limite IOPS do acelerador Write possa limitar as configurações de disco capacidades IOPS

No caso de combinar os dados e o volume de registo para o SAP HANA, os discos que construem o volume listrado não devem ter lido cache ou leitura/gravação de cache ativada.

Existem tipos de VM listados que não são certificados com SAP e, como tal, não estão listados no chamado [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). O feedback dos clientes foi o de que esses tipos de VM não listados foram utilizados com sucesso para algumas tarefas não-produção.


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte:

- [Guia de alta disponibilidade SAP HANA para máquinas virtuais Azure](./sap-hana-availability-overview.md).