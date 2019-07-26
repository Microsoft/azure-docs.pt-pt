---
title: Projetar e implementar um banco de dados Oracle no Azure | Microsoft Docs
description: Projete e implemente um banco de dados Oracle em seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: ebe6f27818df8407504e4254f16d952aa298b6cc
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348314"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceba e implemente uma base de dados Oracle no Azure

## <a name="assumptions"></a>Suposições

- Você está planejando migrar um banco de dados Oracle do local para o Azure.
- Você tem o [pacote de diagnóstico](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) para o Oracle Database que pretende migrar
- Você tem uma compreensão das várias métricas nos relatórios AWR da Oracle.
- Você tem uma compreensão da linha de base do desempenho do aplicativo e da utilização da plataforma.

## <a name="goals"></a>Objetivos

- Entenda como otimizar sua implantação do Oracle no Azure.
- Explore as opções de ajuste de desempenho para um banco de dados Oracle em um ambiente do Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre uma implementação local e o Azure 

A seguir estão algumas coisas importantes para ter em mente quando você está migrando aplicativos locais para o Azure. 

Uma diferença importante é que, em uma implementação do Azure, recursos como VMs, discos e redes virtuais são compartilhados entre outros clientes. Além disso, os recursos podem ser restringidos com base nos requisitos. Em vez de se concentrar em evitar falhas (MTBF), o Azure é mais focado em sobreviver à falha (MTTR).

A tabela a seguir lista algumas das diferenças entre uma implementação local e uma implementação do Azure de um banco de dados Oracle.

> 
> |  | **Implementação local** | **Implementação do Azure** |
> | --- | --- | --- |
> | **Redes** |LAN/WAN  |SDN (rede definida pelo software)|
> | **Grupo de segurança** |Ferramentas de restrição de IP/porta |[Grupo de segurança de rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (tempo médio de recuperação)|
> | **Manutenção planeada** |Aplicação de patch/atualizações|[Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (aplicação de patch/atualizações gerenciadas pelo Azure) |
> | **Recurso** |Dedicado  |Compartilhado com outros clientes|
> | **Regiões** |Datacenters |[Pares de regiões](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Armazenamento** |SAN/discos físicos |[Armazenamento gerenciado pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Dimensionamento** |Escala vertical |Dimensionamento horizontal|


### <a name="requirements"></a>Requisitos

- Determine o tamanho do banco de dados e a taxa de crescimento.
- Determine os requisitos de IOPS, que você pode estimar com base em relatórios AWR da Oracle ou outras ferramentas de monitoramento de rede.

## <a name="configuration-options"></a>Opções de configuração

Há quatro áreas potenciais que você pode ajustar para melhorar o desempenho em um ambiente do Azure:

- Tamanho da máquina virtual
- Taxa de transferência de rede
- Tipos de disco e configurações
- Configurações de cache de disco

### <a name="generate-an-awr-report"></a>Gerar um relatório AWR

Se você tiver um banco de dados Oracle existente e estiver planejando migrar para o Azure, terá várias opções. Se você tiver o [pacote de diagnóstico](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) para suas instâncias do Oracle, poderá executar o relatório AWR da Oracle para obter as métricas (IOPS, Mbps, GiBs e assim por diante). Em seguida, escolha a VM com base nas métricas que você coletou. Ou você pode entrar em contato com sua equipe de infraestrutura para obter informações semelhantes.

Você pode considerar a execução de seu relatório AWR durante cargas de trabalho regulares e de pico, para que você possa comparar. Com base nesses relatórios, você pode dimensionar as VMs com base na carga de trabalho média ou na carga de trabalho máxima.

Veja a seguir um exemplo de como gerar um relatório AWR (gerar seus relatórios AWR usando o Oracle Enterprise Manager, se a instalação atual tiver um):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Métricas-chave

A seguir estão as métricas que você pode obter do relatório AWR:

- Número total de núcleos
- Velocidade do relógio da CPU
- Memória total em GB
- Utilização da CPU
- Taxa de transferência de dados de pico
- Taxa de alterações de e/s (leitura/gravação)
- Taxa de log de restauração (MBPs)
- Taxa de transferência de rede
- Taxa de latência de rede (baixa/alta)
- Tamanho do banco de dados em GB
- Bytes recebidos via SQL * net de/para o cliente

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Estimar o tamanho da VM com base na CPU, memória e uso de e/s do relatório AWR

Uma coisa que você pode examinar são os cinco principais eventos de primeiro plano cronometrados que indicam onde estão os gargalos do sistema.

Por exemplo, no diagrama a seguir, a sincronização do arquivo de log está na parte superior. Indica o número de esperas que são necessárias antes que o LGWR grave o buffer de log no arquivo de log de restauração. Esses resultados indicam que é necessário o armazenamento de melhor desempenho ou discos. Além disso, o diagrama também mostra o número de CPU (núcleos) e a quantidade de memória.

![Captura de tela da página de relatório AWR](./media/oracle-design/cpu_memory_info.png)

O diagrama a seguir mostra a e/s total de leitura e gravação. Havia 59 GB de leitura e 247,3 GB gravados durante o tempo do relatório.

![Captura de tela da página de relatório AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Escolher uma VM

Com base nas informações coletadas do relatório AWR, a próxima etapa é escolher uma VM de um tamanho semelhante que atenda às suas necessidades. Você pode encontrar uma lista de VMs disponíveis no artigo [memória otimizada](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ajuste o dimensionamento da VM com uma série de VMs semelhante com base no ACU

Depois de escolher a VM, preste atenção ao ACU para a VM. Você pode escolher uma VM diferente com base no valor ACU que atenda melhor às suas necessidades. Para obter mais informações, consulte [unidade de computação do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Captura de tela da página unidades ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Taxa de transferência de rede

O diagrama a seguir mostra a relação entre a taxa de transferência e o IOPS:

![Captura de tela da taxa de transferência](./media/oracle-design/throughput.png)

A taxa de transferência de rede total é estimada com base nas seguintes informações:
- Tráfego do SQL * net
- MBps x número de servidores (fluxo de saída, como o Oracle Data Guard)
- Outros fatores, como a replicação de aplicativos

![Captura de tela da taxa de transferência do SQL * net](./media/oracle-design/sqlnet_info.png)

Com base em seus requisitos de largura de banda de rede, há vários tipos de gateway para você escolher. Eles incluem Basic, VpnGw e Azure ExpressRoute. Para obter mais informações, consulte a [página de preços do gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recommendations (Recomendações)**

- A latência de rede é maior em comparação com uma implantação local. A redução de viagens de ida e volta da rede pode melhorar muito o desempenho.
- Para reduzir viagens de ida e volta, consolide aplicativos com transações altas ou aplicativos "informais" na mesma máquina virtual.
- Use máquinas virtuais com [rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para melhorar o desempenho da rede.
- Para determinados distrubutions do Linux, considere habilitar o [suporte a corte/desmapeamento](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Instale o [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) em uma máquina virtual separada.
- As páginas enormes não são habilitadas no Linux por padrão. Considere habilitar páginas enormes e definir `use_large_pages = ONLY` no Oracle DB. Isso pode ajudar a aumentar o desempenho. Mais informações podem ser encontradas [aqui](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Tipos de disco e configurações

- *Discos do sistema operacional padrão*: Esses tipos de disco oferecem dados persistentes e Caching. Eles são otimizados para o acesso do so na inicialização e não são projetados para cargas de trabalho transacionais ou data warehouses (analíticas).

- *Discos não gerenciados*: Com esses tipos de disco, você gerencia as contas de armazenamento que armazenam os arquivos de disco rígido virtual (VHD) que correspondem aos seus discos de VM. Os arquivos VHD são armazenados como BLOBs de páginas em contas de armazenamento do Azure.

- *Discos gerenciados*: O Azure gerencia as contas de armazenamento que você usa para seus discos de VM. Especifique o tipo de disco (Premium ou Standard) e o tamanho do disco que você precisa. O Azure cria e gerencia o disco para você.

- *Discos de armazenamento Premium*: Esses tipos de disco são mais adequados para cargas de trabalho de produção. O armazenamento Premium dá suporte a discos de VM que podem ser anexados a VMs de série de tamanho específicas, como VMs DS, DSv2, GS e F Series. O disco Premium vem com tamanhos diferentes e você pode escolher entre discos de 32 GB a 4.096 GB. Cada tamanho de disco tem suas próprias especificações de desempenho. Dependendo dos requisitos do aplicativo, você pode anexar um ou mais discos à sua VM.

Ao criar um novo disco gerenciado no portal, você pode escolher o tipo de **conta** para o tipo de disco que deseja usar. Tenha em mente que nem todos os discos disponíveis são mostrados no menu suspenso. Depois de escolher um tamanho de VM específico, o menu mostrará somente as SKUs de armazenamento Premium disponíveis que se baseiam nesse tamanho de VM.

![Captura de tela da página de disco gerenciado](./media/oracle-design/premium_disk01.png)

Depois de configurar o armazenamento em uma VM, talvez você queira testar os discos antes de criar um banco de dados. Conhecer a taxa de e/s em termos de latência e taxa de transferência pode ajudá-lo a determinar se as VMs dão suporte à taxa de transferência esperada com metas de latência.

Há várias ferramentas para testes de carga de aplicativo, como Oracle Orion, Sysbench e fio.

Execute o teste de carga novamente depois de implantar um banco de dados Oracle. Inicie suas cargas de trabalho regulares e de pico e os resultados mostrarão a você a linha de base do seu ambiente.

Pode ser mais importante dimensionar o armazenamento com base na taxa de IOPS em vez do tamanho do armazenamento. Por exemplo, se o IOPS necessário for 5.000, mas você precisar de apenas 200 GB, você ainda poderá obter o disco Premium da classe p30, mesmo que ele venha com mais de 200 GB de armazenamento.

A taxa de IOPS pode ser obtida no relatório AWR. Ele é determinado pelo log de refazer, pelas leituras físicas e pela taxa de gravações.

![Captura de tela da página de relatório AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho de refazer é 12,2 milhões bytes por segundo, que é igual a 11,63 MBPs.
O IOPS é 12,2 milhões/2.358 = 5.174.

Depois de ter uma visão clara dos requisitos de e/s, você pode escolher uma combinação de unidades que são mais adequadas para atender a esses requisitos.

**Recommendations (Recomendações)**

- Para o espaço de tabela de dados, espalhe a carga de trabalho de e/s em vários discos usando o armazenamento gerenciado ou o Oracle ASM.
- À medida que o tamanho do bloco de e/s aumenta para operações de leitura e uso intensivo de gravação, adicione mais discos de dados.
- Aumente o tamanho do bloco para processos sequenciais grandes.
- Use a compactação de dados para reduzir a e/s (para dados e índices).
- Separar logs de refazer, sistema e Temps, e desfazer TS em discos de dados separados.
- Não coloque nenhum arquivo de aplicativo em discos do sistema operacional padrão (/dev/sda). Esses discos não são otimizados para tempos de inicialização rápidos da VM e podem não fornecer um bom desempenho para seu aplicativo.
- Ao usar VMs da série M no armazenamento Premium, habilite [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) no disco de logs de restauração.

### <a name="disk-cache-settings"></a>Configurações de cache de disco

Há três opções para o cache de host:

- *ReadOnly*: Todas as solicitações são armazenadas em cache para futuras leituras. Todas as gravações são persistidas diretamente no armazenamento de BLOBs do Azure.

- *ReadWrite*: Esse é um algoritmo "Read-Ahead". As leituras e gravações são armazenadas em cache para futuras leituras. As gravações que não são de write-through são persistidas no cache local primeiro. Ele também fornece a menor latência de disco para cargas de trabalho leves. O uso do cache ReadWrite com um aplicativo que não lida com a persistência dos dados necessários pode levar à perda de dados, se a VM falhar.

- *Nenhum* (desabilitado): Usando essa opção, você pode ignorar o cache. Todos os dados são transferidos para o disco e persistidos no armazenamento do Azure. Esse método oferece a taxa de e/s mais alta para cargas de trabalho com uso intensivo de e/s. Você também precisa tomar "o custo da transação" em consideração.

**Recommendations (Recomendações)**

Para maximizar a taxa de transferência, recomendamos que você inicie com **nenhum** para o cache de host. Para o armazenamento Premium, tenha em mente que você deve desabilitar as "barreiras" ao montar o sistema de arquivos com as opções **ReadOnly** ou **None** . Atualize o arquivo/etc/fstab com o UUID para os discos.

![Captura de tela da página de disco gerenciado](./media/oracle-design/premium_disk02.png)

- Para discos do sistema operacional, use o cache de **leitura/gravação** padrão.
- Para SYSTEM, TEMP e UNDO, use **None** para Caching.
- Para dados, use **nenhum** para armazenar em cache. Mas se seu banco de dados for somente leitura ou de leitura intensiva, use cache **somente leitura** .

Depois que a configuração do disco de dados for salva, você não poderá alterar a configuração de cache do host, a menos que desmonte a unidade no nível do sistema operacional e, em seguida, monte-a novamente depois de fazer a alteração.

## <a name="security"></a>Segurança

Depois de ter configurado e configurado seu ambiente do Azure, a próxima etapa é proteger sua rede. Aqui estão algumas recomendações:

- *Política de NSG*: NSG pode ser definido por uma sub-rede ou NIC. É mais simples controlar o acesso no nível de sub-rede, tanto para segurança quanto para roteamento de força para coisas como firewalls de aplicativo.

- *Jumpbox*: Para um acesso mais seguro, os administradores não devem se conectar diretamente ao serviço de aplicativo ou ao banco de dados. Um Jumpbox é usado como uma mídia entre o computador do administrador e os recursos do Azure.
![Captura de tela da página de topologia do Jumpbox](./media/oracle-design/jumpbox.png)

    O computador administrador deve oferecer acesso restrito por IP somente ao Jumpbox. O Jumpbox deve ter acesso ao aplicativo e ao banco de dados.

- *Rede privada* (sub-redes): É recomendável que você tenha o serviço de aplicativo e o banco de dados em sub-redes separadas; portanto, um controle melhor pode ser definido pela política de NSG.


## <a name="additional-reading"></a>Leitura adicional

- [Configurar Oracle ASM](configure-oracle-asm.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurar o Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação do Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explorar exemplos de implantação de VM CLI do Azure](../../linux/cli-samples.md)
