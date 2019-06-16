---
title: Configurações de armazenamento de máquina virtual do Azure do SAP HANA | Documentos da Microsoft
description: Recomendações de armazenamento para a VM com o SAP HANA implementado nos mesmos.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735515"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurações de armazenamento da máquina virtual do Azure do SAP HANA

O Azure fornece diferentes tipos de armazenamento que são adequados para as VMs do Azure que estejam a executar o SAP HANA. Os tipos de armazenamento do Azure que podem ser considerados para a lista de implementações do SAP HANA, como: 

- Unidades de disco SSD Standard (SSD)
- Unidades de estado sólido (SSD) do Premium
- SSD Ultra em pré-visualização pública e ainda não é suportado com as aplicações de SAP de produção

Para saber mais sobre estes tipos de discos, consulte o artigo [selecione um tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

O Azure oferece dois métodos de implantação para VHDs no padrão do Azure e o armazenamento Premium. Se o cenário geral permite, aproveitar [disco gerido do Azure](https://azure.microsoft.com/services/managed-disks/) implementações. 

Para obter uma lista de tipos de armazenamento e os respetivos SLAs no débito IOPS e o armazenamento, reveja os [documentação do Azure para discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

**Recomendação: Utilizar o armazenamento Premium do Azure em conjunto com acelerador de escrita do Azure e utilizar Managed Disks do Azure para a implementação**

No mundo no local, raramente tinham de se preocupa com os subsistemas de e/s e as respetivas capacidades. Motivo era que o fornecedor da aplicação necessária para se certificar de que são cumpridos os requisitos de armazenamento mínima para o SAP HANA. À medida que cria a infraestrutura do Azure por conta própria, deve estar ciente de alguns dos requisitos. Algumas das características de débito mínimos que são solicitadas são resultando na necessidade de:

- Ativar a leitura/escrita no **/hana/do registo** de 250 MB/seg com tamanhos de e/s de 1 MB
- Ativar ler a atividade de, pelo menos, de 400 MB/seg para **dados/hana/** para os tamanhos de e/s de 16 MB e de 64 MB
- Ativar a atividade de escrita de, pelo menos, 250 MB/seg para **dados/hana/** com tamanhos de e/s de 16 MB e de 64 MB

Dado que o armazenamento baixo latência é fundamental para sistemas DBMS, mesmo quando o DBMS, como o SAP HANA, manter os dados na memória. O caminho crítico no armazenamento é normalmente em torno as gravações de log de transação dos sistemas DBMS. Mas também gostam de operações de escrita de pontos de reposição ou carregamento de dados na memória depois de recuperação contra panes pode ser considerado crítica. Portanto, é **obrigatório** tirar partido dos discos Premium do Azure **/hana/data** e **/hana/do registo** volumes. Para alcançar o débito mínimo de **/hana/do registo** e **/hana/dados** como pretendido, SAP, tem de criar um RAID 0 usando MDADM ou LVM sobre vários discos de armazenamento Premium do Azure. E utilizar os volumes RAID como **/hana/data** e **/hana/do registo** volumes. 

**Recomendação: Como o stripe tamanhos para o RAID 0, a recomendação é usar:**

- 64 KB ou 128 KB para   **/hana/dados**
- 32 KB para   **/hana/do registo**

> [!NOTE]
> Não precisa de configurar qualquer nível de redundância através de volumes de RAID, uma vez que o armazenamento do Azure Premium e Standard mantêm três imagens de um VHD. A utilização de um volume RAID é puramente configurar volumes que fornecem o débito de e/s suficiente.

Acumular um número de VHDs do Azure por baixo de uma RAID, é accumulative a partir de um lado de débito do armazenamento e de IOPS. Então, se colocar um RAID 0 através de discos de Premium Storage do Azure P30 x 3, ele deverá dar-lhe três vezes o IOPS e três vezes o débito de armazenamento de um único disco P30 de armazenamento do Azure Premium.

As recomendações de colocação em cache abaixo são supondo que as características de e/s para o SAP HANA essa lista, como:

- Há quase qualquer carga de trabalho de leitura nos arquivos de dados HANA. As exceções são grandes e/SS de tamanho após o reinício da instância do HANA ou quando dados são carregados em HANA. Outro caso de maior de leitura que e/s nos arquivos de dados pode ser backups de banco de dados do HANA. Como resultado principalmente a cache de leitura não faz sentido desde na maioria dos casos, todos os volumes de dados de ficheiros tem de ser totalmente lidas.
- Escrever contra os ficheiros de dados se verificarem em picos com base em pontos de reposição do HANA e recuperação de falhas do HANA. Pontos de reposição de escrita é assíncrono e não estão obstruindo de quaisquer transações de utilizador. Escrita de dados durante a recuperação de falhas é crítico de desempenho para o sistema a responder rapidamente novamente. No entanto, a recuperação de falhas deve ser situações excecionais em vez disso
- Há um mínimo de leituras dos ficheiros de Refazer do HANA. As exceções são grandes e/ss ao realizar backups de log de transação, recuperação de falha, ou na fase de reinício de uma instância do HANA.  
- Principal de carga com o arquivo de log de retrabalho SAP HANA é escritas. Depende da natureza da carga de trabalho, pode ter e/ss tão pequena como 4 KB ou em outros casos de tamanhos de e/s de 1 MB ou mais. Escreva a latência contra o log de retrabalho de SAP HANA é o desempenho crítico.
- Todas as gravações precisam ser mantidos no disco de forma fiável

**Recomendação: Como resultado desses padrões de e/s observados pelo SAP HANA, deve ser definida a colocação em cache para os diferentes volumes com o armazenamento Premium do Azure, como:**

- **dados/hana/** -sem colocação em cache
- **/ hana/do registo** - sem colocação em cache - exceção para a série M (ver mais tarde neste documento)
- **/ hana/partilhado** - leitura a colocação em cache


Tenha também o débito de e/s VM global em mente ao dimensionamento ou decidir para uma VM. Em geral o débito do armazenamento VM está documentado no artigo [tamanhos de máquinas virtuais com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo de agendador de e/s do Linux
Linux tem vários modos de agendamento de e/s diferentes. Recomendação comum através de fornecedores de Linux e SAP é reconfigurar o modo de agendador de e/s para volumes de disco a partir do **cfq** modo para o **noop** modo. Detalhes são referenciados numa [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento de produção com acelerador de escrita do Azure para máquinas de virtuais de série M do Azure
Acelerador de escrita do Azure é uma funcionalidade que é obter implementada para VMs de série M Azure exclusivamente. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de escrita contra o armazenamento Premium do Azure. Para o SAP HANA, acelerador de escrita deve ser utilizado em relação a **/hana/do registo** apenas o volume. Por conseguinte, o **/hana/data** e **/hana/do registo** são volumes separados com acelerador de escrita do Azure que suporta o **/hana/do registo** apenas o volume. 

> [!IMPORTANT]
> A certificação do SAP HANA para máquinas de virtuais de série M do Azure é exclusivamente com acelerador de escrita do Azure para o **/hana/do registo** volume. Como resultado, implementações de SAP HANA de cenário de produção em máquinas de virtuais de série M Azure devem ser configurados com acelerador de escrita do Azure para o **/hana/do registo** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recomendação: As configurações recomendadas para cenários de produção ter o seguinte aspeto:**

| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | /hana/data | / hana/do registo | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
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

Tipos de M416xx_v2 VM não são ainda disponibilizados pela Microsoft para o público. Verifique se o débito de armazenamento para os diferentes volumes sugeridos atende a carga de trabalho que pretende executar. Se a carga de trabalho requer mais altos volumes para **/hana/data** e **/hana/do registo**, terá de aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listadas aumenta o IOPS e débito de e/s dentro dos limites do tipo de máquina virtual do Azure.

Acelerador de escrita do Azure só funciona em conjunto com [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/). Então, pelo menos, os discos de armazenamento Premium do Azure que formam o **/hana/do registo** volume tenha de ser implementado como discos geridos.

Existem limites de VHDs de armazenamento Premium do Azure por VM, que pode ser suportado pelo Azure acelerador de escrita. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para um M32xx VM

Instruções mais detalhadas sobre como ativar o acelerador de escrita do Azure podem ser encontradas no artigo [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o acelerador de escrita do Azure podem ser encontradas na documentação do mesmo.

**Recomendação: Tem de utilizar o acelerador de escrita para discos que formam o volume de /hana/log**


## <a name="cost-conscious-azure-storage-configuration"></a>Custo consciente configuração de armazenamento do Azure
A tabela seguinte mostra uma configuração de tipos VM que os clientes também utilizar para alojar o SAP HANA em VMs do Azure. Pode haver alguns tipos VM que poderão não cumprir todos os critérios de armazenamento mínima para o SAP HANA ou não serem suportados oficialmente com o SAP HANA, SAP. Mas até agora essas VMs pareciam executar bem para cenários de não produção. O **/hana/data** e **/hana/do registo** são combinados para um volume. Como resultado a utilização de acelerador de escrita do Azure pode se tornar uma limitação em termos de IOPS.

> [!NOTE]
> Para cenários SAP suportado, verifique se um determinado tipo VM é suportado para o SAP HANA pela SAP no [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Uma alteração do primeira recomendações para uma solução de consciente de custo, é mover da [discos do Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) desempenhar melhor e mais fiável [discos SSD Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | / hana/dados e/hana/do registo<br /> repartidos com LVM ou MDADM | /hana/shared | volume de /Root | /usr/sap | cópia de segurança/Hana |
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


Tipos de M416xx_v2 VM não são ainda disponibilizados pela Microsoft para o público. Os discos recomendados para tipos de VM mais pequeno com 3 x P20 oversize os volumes sobre as recomendações de espaço de acordo com o [White Paper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto, a escolha, tal como apresentado na tabela foi efetuada a fornecer o débito de disco suficiente para o SAP HANA. Se precisar de alterações para o **/hana/cópia de segurança** volume, o que foi dimensionado para manter as cópias de segurança que representam as duas vezes o volume de memória, fique à vontade ajustar.   
Verifique se o débito de armazenamento para os diferentes volumes sugeridos atende a carga de trabalho que pretende executar. Se a carga de trabalho requer mais altos volumes para **/hana/data** e **/hana/do registo**, terá de aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listadas aumenta o IOPS e débito de e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima não se beneficiaria partir [máquina virtual do Azure única SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , uma vez que o que ele usa uma combinação de armazenamento Premium do Azure e o armazenamento padrão do Azure. No entanto, a seleção foi escolhida para otimizar os custos. Terá de escolher o armazenamento Premium para todos os discos em cima listado como o armazenamento padrão do Azure (Sxx) para fazer a configuração da VM em conformidade com o SLA de VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas visam os requisitos mínimos de que SAP expressa para seus provedores de infra-estrutura. Em cenários de carga de trabalho e de implementações de clientes reais, situações foram encontradas onde estas recomendações ainda não forneceu recursos suficientes. Estes podem ser situações em que um cliente necessária uma recarga mais rápida dos dados após um reinício do HANA ou onde configurações maior largura de banda necessária para o armazenamento de cópia de segurança. Noutros casos incluídos **/hana/do registo** onde 5000 IOPS não eram suficientes para a carga de trabalho específica. Então, fazer estas recomendações como um partir do ponto e adaptar-se com base nos requisitos da carga de trabalho.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Configuração de armazenamento Ultra SSD do Azure para o SAP HANA
A Microsoft está no processo de introdução de um novo tipo de armazenamento do Azure apresentou [SSD de Ultra Azure](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). A grande diferença entre o armazenamento do Azure, oferecido até agora e Ultra SSD é que os recursos de disco não estão vinculados para o tamanho do disco já. Como um cliente pode definir estas capacidades para Ultra SSD:

- Tamanho de um disco que vão desde 4 GiB para 65.536 GiB
- Intervalo IOPS de 100 IOPS a 160K IOPS (máximo depende também os tipos de VM)
- Débito do armazenamento de 300 MB/seg para 2.000 MB/seg

Para detalhes, veja o artigo da [anunciando o Ultra SSD – a próxima geração de tecnologia de discos do Azure (pré-visualização)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD dá-lhe a possibilidade de definir um único disco que atenda a seu tamanho, o IOPS e o intervalo de débito de disco. Em vez de usar os gerentes de volume lógico como LVM ou MDADM com base no armazenamento Premium do Azure para construir os volumes que satisfazem os requisitos de débito do armazenamento e de IOPS. Pode executar uma mistura de configuração entre UltraSSD e o armazenamento Premium. Como resultado, pode limitar a utilização de UltraSSD para os /hana/data críticos de desempenho e os volumes de /hana/log e abordar os outros volumes de armazenamento Premium do Azure

| SKU DE VM | RAM | Um máximo de E/S DE VM<br /> Débito | /hana/data volume | / hana/débito de e/s de dados | /hana/data IOPS | /hana/log volume | / hana/débito de e/s de registo | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2\.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2\.000 MB/s | 7200 GB | 1500 M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Tipos de M416xx_v2 VM não são ainda disponibilizados pela Microsoft para o público. Os valores apresentados destinam-se para ser um ponto de partida e tem de ser avaliada em comparação com as necessidades reais. A vantagem com SSD Ultra do Azure é que os valores de IOPS e débito podem ser adaptados sem a necessidade de encerrar a VM ou interrompendo a carga de trabalho aplicado ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com o armazenamento de UltraSSD não está disponível. Isto impede a utilização de instantâneos VM com os serviços de cópia de segurança do Azure

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte:

- [Guia de SAP HANA de elevada disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).