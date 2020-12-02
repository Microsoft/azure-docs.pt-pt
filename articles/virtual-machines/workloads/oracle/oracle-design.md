---
title: Conceber e implementar uma base de dados da Oracle no Azure Microsoft Docs
description: Desenhe e implemente uma base de dados Oracle no seu ambiente Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 5e9ddecd694a9051e746d07cbc1bee4d98bf5829
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484435"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceça e implemente uma base de dados oracle em Azure

## <a name="assumptions"></a>Pressupostos

- Estás a planear migrar uma base de dados do Oráculo do local para o Azure.
- Tem o [Pacote de Diagnósticos](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) para a Base de Dados do Oráculo que pretende migrar
- Tem uma compreensão das várias métricas nos relatórios da Oracle AWR.
- Tem uma compreensão de base do desempenho da aplicação e da utilização da plataforma.

## <a name="goals"></a>Objetivos

- Compreenda como otimizar a sua implantação oráculo em Azure.
- Explore as opções de afinação de desempenho para uma base de dados Oracle num ambiente Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre uma implementação no local e a implementação do Azure 

Seguem-se algumas coisas importantes a ter em mente quando se está a migrar para o Azure. 

Uma diferença importante é que numa implementação do Azure, recursos como VMs, discos e redes virtuais são partilhados entre outros clientes. Além disso, os recursos podem ser estrangulados com base nos requisitos. Em vez de se concentrar em evitar falhar (MTBF), o Azure está mais focado em sobreviver à falha (MTTR).

O quadro que se segue enumera algumas das diferenças entre uma implementação no local e uma implementação Azure de uma base de dados oracle.


|  | Implementação no local | Implementação do Azure |
| --- | --- | --- |
| **Rede** |LAN/WAN  |SDN (rede definida por software)|
| **Grupo de segurança** |Ferramentas de restrição IP/porta |[Grupo de Segurança de Rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (tempo médio para a recuperação)|
| **Manutenção planeada** |Remendos/upgrades|[Conjuntos de disponibilidade](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (patching/upgrades geridos pelo Azure) |
| **Recurso** |Dedicada  |Partilhado com outros clientes|
| **Regiões** |Datacenters |[Pares de região](../../regions.md#region-pairs)|
| **Armazenamento** |DISCOS SAN/Físicos |[Armazenamento gerido pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Dimensionamento** |Escala vertical |Dimensionamento horizontal|


### <a name="requirements"></a>Requisitos

- Determine o tamanho e a taxa de crescimento da base de dados.
- Determine os requisitos do IOPS, que pode estimar com base em relatórios oracle AWR ou outras ferramentas de monitorização da rede.

## <a name="configuration-options"></a>Opções de configuração

Existem quatro áreas potenciais que pode sintonizar para melhorar o desempenho num ambiente Azure:

- Tamanho da máquina virtual
- Produção de rede
- Tipos e configurações de discos
- Definições de cache de disco

### <a name="generate-an-awr-report"></a>Gerar um relatório da AWR

Se tem uma base de dados Oráculo existente e planeia migrar para Azure, tem várias opções. Se tiver o [Pacote de Diagnóstico para](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) as suas instâncias Oráculos, pode executar o relatório da Oracle AWR para obter as métricas (IOPS, Mbps, GiBs, e assim por diante). Em seguida, escolha o VM com base nas métricas que recolheu. Ou pode contactar a sua equipa de infraestruturas para obter informações semelhantes.

Você pode considerar executar o seu relatório de AWR durante cargas de trabalho regulares e picos, para que você possa comparar. Com base nestes relatórios, pode dimensionar os VMs com base na carga de trabalho média ou na carga de trabalho máxima.

Segue-se um exemplo de como gerar um relatório de AWR (Gere os seus relatórios de AWR utilizando o seu Oracle Enterprise Manager, se a sua instalação atual tiver um):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Principais métricas

Seguem-se as métricas que pode obter a partir do relatório da AWR:

- Número total de núcleos
- Velocidade do relógio CPU
- Memória total em GB
- Utilização da CPU
- Taxa máxima de transferência de dados
- Taxa de alterações de E/S (ler/escrever)
- Taxa de registo de redo (MBPs)
- Produção de rede
- Taxa de latência da rede (baixa/alta)
- Tamanho da base de dados em GB
- Bytes recebidos via SQL*Net de/para cliente

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Estimativa do tamanho dos VM com base no uso de CPU, memória e I/S do relatório AWR

Uma coisa que você pode olhar são os cinco melhores eventos de primeiro plano cronometrado que indicam onde estão os estrangulamentos do sistema.

Por exemplo, no diagrama seguinte, a sincronização do ficheiro de registo está na parte superior. Indica o número de esperas que são necessárias antes de o LGWR escrever o tampão de registo para o ficheiro de registo de redo. Estes resultados indicam que são necessários armazenamento ou discos de melhor desempenho. Além disso, o diagrama também mostra o número de CPU (núcleos) e a quantidade de memória.

![Screenshot que mostra a sincronização do ficheiro de registo na parte superior da tabela.](./media/oracle-design/cpu_memory_info.png)

O diagrama seguinte mostra o total de E/O de ler e escrever. Foram 59 GB lidos e 247,3 GB escritos durante o período do relatório.

![Screenshot que mostra o total de E/O de ler e escrever.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Escolha um VM

Com base nas informações que recolheu do relatório da AWR, o próximo passo é escolher um VM de tamanho semelhante que satisfaça os seus requisitos. Pode encontrar uma lista de VMs disponíveis no artigo [Memória otimizada.](../../sizes-memory.md)

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Afinar o tamanho VM com uma série de VM semelhante baseada na ACU

Depois de ter escolhido o VM, preste atenção à ACU para o VM. Você pode escolher um VM diferente com base no valor ACU que melhor se adequa aos seus requisitos. Para mais informações, consulte [a unidade de computação Azure](../../acu.md).

![Screenshot da página das unidades da ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Produção de rede

O diagrama a seguir mostra a relação entre a produção e o IOPS:

![Screenshot de produção](./media/oracle-design/throughput.png)

O rendimento total da rede é estimado com base nas seguintes informações:
- Tráfego SQL*Net
- MBps x número de servidores (fluxo de saída, como Oracle Data Guard)
- Outros fatores, como a replicação da aplicação

![Screenshot da produção SQL*Net](./media/oracle-design/sqlnet_info.png)

Com base nos requisitos de largura de banda da sua rede, existem vários tipos de gateway para escolher. Estes incluem básico, VpnGw, e Azure ExpressRoute. Para mais informações, consulte a [página de preços da porta de entrada VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recomendações**

- A latência da rede é maior em comparação com uma implantação no local. A redução das viagens de ida e volta na rede pode melhorar consideravelmente o desempenho.
- Para reduzir as viagens de ida e volta, consolidar aplicações que tenham transações elevadas ou aplicações "chatty" na mesma máquina virtual.
- Utilize máquinas virtuais com [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para um melhor desempenho da rede.
- Para certas distribuições do Linux, considere permitir o [suporte TRIM/UNMAP](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Instale [o Gestor da Empresa Oracle](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) numa máquina virtual separada.
- As páginas enormes não são ativadas no linux por defeito. Considere ativar páginas enormes e definir `use_large_pages = ONLY` no Oráculo DB. Isto pode ajudar a aumentar o desempenho. Mais informações podem ser [encontradas aqui.](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)

### <a name="disk-types-and-configurations"></a>Tipos e configurações de discos

- *Discos de OS predefinidos*: Estes tipos de disco oferecem dados persistentes e cacheing. São otimizados para acesso ao SISTEMA no arranque, e não são projetados para cargas de trabalho transacionais ou de data warehouse (analítico).

- *Discos não geridos*: Com estes tipos de discos, gere as contas de armazenamento que armazenam os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Os ficheiros VHD são armazenados como bolhas de página nas contas de armazenamento Azure.

- *Discos geridos*: O Azure gere as contas de armazenamento que utiliza para os discos VM. Especifica o tipo de disco (premium ou standard) e o tamanho do disco de que necessita. O Azure cria e gere o disco para si.

- *Discos de armazenamento premium*: Estes tipos de disco são os mais adequados para cargas de trabalho de produção. O armazenamento premium suporta discos VM que podem ser ligados a VMs específicos da série de tamanho, tais como DS, DSv2, GS e VMs sérieS F. O disco premium vem com diferentes tamanhos, e você pode escolher entre discos que variam de 32 GB a 4,096 GB. Cada tamanho do disco tem as suas próprias especificações de desempenho. Dependendo dos requisitos da sua aplicação, pode anexar um ou mais discos ao seu VM.

Quando criar um novo disco gerido a partir do portal, pode escolher o **tipo de Conta** para o tipo de disco que pretende utilizar. Tenha em mente que nem todos os discos disponíveis são mostrados no menu suspenso. Depois de escolher um determinado tamanho VM, o menu mostra apenas os SKUs de armazenamento premium disponíveis que são baseados nesse tamanho VM.

![Screenshot da página de disco gerido](./media/oracle-design/premium_disk01.png)

Depois de configurar o seu armazenamento num VM, é melhor carregar os discos antes de criar uma base de dados. Conhecer a taxa de E/S em termos de latência e produção pode ajudá-lo a determinar se os VMs suportam a produção esperada com alvos de latência.

Existem várias ferramentas para testes de carga de aplicações, tais como Oracle Orion, Sysbench e Fio.

Faça o teste de carga de novo depois de ter implantado uma base de dados oracle. Inicie as suas cargas de trabalho regulares e máximas, e os resultados mostram-lhe a linha de base do seu ambiente.

Pode ser mais importante dimensionar o armazenamento com base na taxa IOPS em vez do tamanho do armazenamento. Por exemplo, se o IOPS necessário for de 5.000, mas só precisa de 200 GB, ainda poderá obter o disco premium classe P30, mesmo que venha com mais de 200 GB de armazenamento.

A taxa IOPS pode ser obtida a partir do relatório da AWR. É determinado pelo registo de redo, leituras físicas e taxa de escrita.

![Screenshot da página de relatório da AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho do redo é de 12.200.000 bytes por segundo, o que equivale a 11,63 MBPs.
O IOPS é de 12.200.000 / 2.358 = 5.174.

Depois de ter uma imagem clara dos requisitos de E/S, pode escolher uma combinação de unidades que sejam mais adequadas para satisfazer esses requisitos.

**Recomendações**

- Para o espaço de tabela de dados, espalhe a carga de trabalho de E/S através de vários discos utilizando armazenamento gerido ou Oracle ASM.
- À medida que o tamanho do bloco de E/S aumenta para operações intensivas de leitura e de escrita intensiva, adicione mais discos de dados.
- Aumente o tamanho do bloco para grandes processos sequenciais.
- Utilize a compressão de dados para reduzir a E/S (tanto para dados como para índices).
- Separe os registos, o sistema e as temperaturas e desfaça o TS em discos de dados separados.
- Não coloque ficheiros de aplicação em discos de OS predefinidos (/dev/sda). Estes discos não estão otimizados para tempos de arranque rápidos em VM, e podem não proporcionar um bom desempenho para a sua aplicação.
- Ao utilizar VMs da Série M no armazenamento Premium, ative [o Acelerador de Escrita](../../how-to-enable-write-accelerator.md) no disco de registos de redo.

### <a name="disk-cache-settings"></a>Definições de cache de disco

Existem três opções para o caching hospedeiro:

- *Leia Também:* Todos os pedidos estão em cache para futuras leituras. Todas as escritas são persistiu diretamente para o armazenamento da Azure Blob.

- *Leia :* Este é um algoritmo de "ler-a-frente". As leituras e as escritas estão em cache para futuras leituras. As gravações não-escritas são persistiu primeiro para a cache local. Também fornece a latência mais baixa do disco para cargas de trabalho leves. A utilização da cache ReadWrite com uma aplicação que não lida com a persistência dos dados necessários pode levar à perda de dados, se o VM falhar.

- *Nenhum* (desativado): Ao utilizar esta opção, pode contornar a cache. Todos os dados são transferidos para o disco e persistiram no Azure Storage. Este método dá-lhe a maior taxa de E/S para cargas de trabalho intensivas de I/O. Também é necessário ter em consideração o "custo de transação".

**Recomendações**

Para maximizar a produção, recomendamos que comece com **Nenhum** para o caching hospedeiro. Para o Armazenamento Premium, tenha em mente que deve desativar as "barreiras" quando monta o sistema de ficheiros com as opções **ReadOnly** ou **None.** Atualize o ficheiro /etc/fstab com o UUID para os discos.

![Screenshot da página de disco gerida que mostra as opções ReadOnly e None.](./media/oracle-design/premium_disk02.png)

- Para discos OS, utilize o cache padrão **de leitura/escrita.**
- Para SISTEMA, TEMP e UNDO utilize **Nenhum** para caching.
- Para DATA, utilize **Nenhum** para caching. Mas se a sua base de dados for apenas de leitura ou de leitura intensiva, use o caching apenas de **Leitura.**

Depois de a definição do disco de dados ser guardada, não pode alterar a definição de cache do anfitrião a menos que desmonte a unidade ao nível de SO e, em seguida, remonte-a depois de ter feito a alteração.

## <a name="security"></a>Segurança

Depois de configurar e configurar o seu ambiente Azure, o próximo passo é proteger a sua rede. Aqui estão algumas recomendações:

- *Política NSG*: O NSG pode ser definido por uma sub-rede ou NIC. É mais simples controlar o acesso ao nível da sub-rede, tanto para segurança como para encaminhamento de forças para coisas como firewalls de aplicações.

- *Jumpbox*: Para um acesso mais seguro, os administradores não devem ligar-se diretamente ao serviço de aplicações ou à base de dados. Uma caixa de salto é usada como um meio de comunicação entre a máquina de administrador e os recursos Azure.
![Screenshot da página de topologia jumpbox](./media/oracle-design/jumpbox.png)

    A máquina de administrador deve oferecer acesso restrito a IP apenas à caixa de salto. A caixa de salto deve ter acesso à aplicação e à base de dados.

- *Rede privada* (sub-redes): Recomendamos que tenha o serviço de aplicação e base de dados em sub-redes separadas, para que um melhor controlo possa ser definido pela política NSG.


## <a name="additional-reading"></a>Leitura adicional

- [Configurar Oracle ASM](configure-oracle-asm.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configure Portão Dourado oráculo](configure-oracle-golden-gate.md)
- [Apoio e recuperação da Oráculo](./oracle-overview.md)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explore amostras de CLI de implantação VM](../../linux/cli-samples.md)