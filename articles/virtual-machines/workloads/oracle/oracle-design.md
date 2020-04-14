---
title: Conceber e implementar uma base de dados da Oracle no Azure Microsoft Docs
description: Desenhe e implemente uma base de dados Oracle no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 41e1720dfeaa98a9d0bc2227c58083ce769b06e0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263408"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceber e implementar uma base de dados da Oracle em Azure

## <a name="assumptions"></a>Pressupostos

- Estás a planear migrar uma base de dados da Oracle do local para o Azure.
- Tem o Pacote de [Diagnósticos](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) para a Base de Dados Oracle que pretende migrar
- Tem uma compreensão das várias métricas nos relatórios da Oracle AWR.
- Tem uma compreensão base do desempenho da aplicação e da utilização da plataforma.

## <a name="goals"></a>Objetivos

- Entenda como otimizar a sua implantação oracle em Azure.
- Explore as opções de afinação de desempenho para uma base de dados Daoracle num ambiente Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre uma implementação no local e a implementação do Azure 

Seguem-se algumas coisas importantes a ter em mente quando se está a migrar para o Local aplicações para o Azure. 

Uma diferença importante é que, numa implementação do Azure, recursos como VMs, discos e redes virtuais são partilhados entre outros clientes. Além disso, os recursos podem ser acelerados com base nos requisitos. Em vez de se concentrar em evitar falhas (MTBF), Azure está mais focado em sobreviver à falha (MTTR).

O quadro seguinte enumera algumas das diferenças entre uma implementação no local e uma implementação do Azure de uma base de dados da Oracle.

> 
> |  | **Implementação no local** | **Implementação do Azure** |
> | --- | --- | --- |
> | **Redes** |LAN/WAN  |SDN (networking definido por software)|
> | **Grupo de segurança** |Ferramentas de restrição IP/porta |[Grupo de Segurança da Rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (tempo médio para recuperação)|
> | **Manutenção planeada** |Remendar/atualizar|[Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (patching/upgrades geridos pelo Azure) |
> | **Recurso** |Dedicada  |Partilhado com outros clientes|
> | **Regiões** |Datacenters |[Pares de região](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Armazenamento** |SAN/discos físicos |[Armazenamento gerido pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Escala** |Escala vertical |Dimensionamento horizontal|


### <a name="requirements"></a>Requisitos

- Determina o tamanho e a taxa de crescimento da base de dados.
- Determine os requisitos do IOPS, que pode estimar com base em relatórios da Oracle AWR ou outras ferramentas de monitorização da rede.

## <a name="configuration-options"></a>Opções de configuração

Existem quatro áreas potenciais que pode sintonizar para melhorar o desempenho num ambiente Azure:

- Tamanho da máquina virtual
- Entrada de rede
- Tipos e configurações de disco
- Definições de cache do disco

### <a name="generate-an-awr-report"></a>Gerar um relatório aWR

Se tiver uma base de dados oráculo existente e estiver a planear migrar para O Azure, tem várias opções. Se tiver o Pacote de Diagnóstico sinuoso para os seus [casos](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) de Oráculo, pode executar o relatório Oracle AWR para obter as métricas (IOPS, Mbps, GiBs, e assim por diante). Em seguida, escolha o VM com base nas métricas que recolheu. Ou pode contactar a sua equipa de infraestruturas para obter informações semelhantes.

Pode considerar executar o seu relatório de AWR durante as cargas de trabalho regulares e máximas, para que possa comparar. Com base nestes relatórios, pode dimensionar os VMs com base na carga de trabalho média ou na carga de trabalho máxima.

Segue-se um exemplo de como gerar um relatório AWR (Gere os seus relatórios DeWR utilizando o seu Oracle Enterprise Manager, se a sua instalação atual tiver um):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Métricas-chave

Seguem-se as métricas que pode obter do relatório AWR:

- Número total de núcleos
- Velocidade do relógio CPU
- Memória total em GB
- Utilização da CPU
- Taxa máxima de transferência de dados
- Taxa de alterações de I/O (ler/escrever)
- Taxa de registo de redo (MBPs)
- Entrada de rede
- Taxa de latência da rede (baixa/alta)
- Tamanho da base de dados em GB
- Bytes recebidos via SQL*Net de/para cliente

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Estimar o tamanho do VM com base na CPU, na memória e na utilização de I/S do relatório AWR

Uma coisa que se pode ver são os cinco primeiros eventos de primeiro plano cronometrados que indicam onde estão os estrangulamentos do sistema.

Por exemplo, no diagrama seguinte, a sincronização do ficheiro de registo está na parte superior. Indica o número de esperas necessárias antes de o LGWR escrever o tampão de registo para o ficheiro de registo de refazer. Estes resultados indicam que são necessários armazenamentos ou discos com melhor desempenho. Além disso, o diagrama também mostra o número de CPU (núcleos) e a quantidade de memória.

![Screenshot da página de relatório da AWR](./media/oracle-design/cpu_memory_info.png)

O diagrama seguinte mostra o total de I/O de ler e escrever. Foram lidas 59 GB e 247,3 GB escritas durante o tempo do relatório.

![Screenshot da página de relatório da AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Escolha um VM

Com base nas informações recolhidas no relatório da AWR, o próximo passo é escolher um VM de tamanho semelhante que cumpra os seus requisitos. Pode encontrar uma lista de VMs disponíveis no artigo [Memória otimizada](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Afinar o tamanho vm com uma série VM semelhante baseada na ACU

Depois de escolher o VM, preste atenção à ACU para o VM. Você pode escolher um VM diferente com base no valor da ACU que melhor se adequa às suas necessidades. Para mais informações, consulte a [unidade de cálculo Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Screenshot da página unidades da ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Entrada de rede

O diagrama seguinte mostra a relação entre a entrada e o IOPS:

![Screenshot da entrada](./media/oracle-design/throughput.png)

A produção total da rede é estimada com base nas seguintes informações:
- Tráfego sQL*Net
- MBps x número de servidores (fluxo de saída, como Oráculo Data Guard)
- Outros fatores, como a replicação da aplicação

![Screenshot da entrada SQL*Net](./media/oracle-design/sqlnet_info.png)

Com base nos requisitos de largura de banda da rede, existem vários tipos de gateway para você escolher. Estes incluem básico, VpnGw, e Azure ExpressRoute. Para mais informações, consulte a página de preços do [gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recomendações**

- A latência da rede é maior em comparação com uma implantação no local. A redução das viagens de ida e volta em rede pode melhorar consideravelmente o desempenho.
- Para reduzir as viagens de ida e volta, consolide aplicações que tenham transações elevadas ou aplicações "tagarela" na mesma máquina virtual.
- Utilize máquinas virtuais com [networking acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para um melhor desempenho da rede.
- Para certas destrubutions linux, considere permitir o [apoio TRIM/UNMAP](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Instale o [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) numa máquina virtual separada.
- Páginas enormes não são ativadas em linux por padrão. Considere permitir páginas enormes `use_large_pages = ONLY` e colocar no Oracle DB. Isto pode ajudar a aumentar o desempenho. Mais informações podem ser encontradas [aqui.](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)

### <a name="disk-types-and-configurations"></a>Tipos e configurações de disco

- *Discos osso padrão*: Estes tipos de disco oferecem dados persistentes e cache. Estão otimizados para acesso a OS no arranque, e não são projetados para cargas de trabalho de armazém de dados ou de dados (analíticas).

- *Discos não geridos*: Com estes tipos de discos, gere as contas de armazenamento que armazenam os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Os ficheiros VHD são armazenados como bolhas de página nas contas de armazenamento do Azure.

- *Discos geridos*: O Azure gere as contas de armazenamento que utiliza para os seus discos VM. Especifica o tipo de disco (premium ou standard) e o tamanho do disco de que necessita. O Azure cria e gere o disco para si.

- Discos de *armazenamento premium:* Estes tipos de disco são mais adequados para cargas de trabalho de produção. O armazenamento premium suporta discos VM que podem ser anexados a VMs de séries de tamanhoespecífico, tais como DS, DSv2, GS e VMs da série F. O disco premium vem com diferentes tamanhos, e pode escolher entre discos que variam entre 32 GB e 4.096 GB. Cada tamanho do disco tem as suas próprias especificações de desempenho. Dependendo dos seus requisitos de candidatura, pode anexar um ou mais discos ao seu VM.

Quando cria um novo disco gerido a partir do portal, pode escolher o **tipo conta** para o tipo de disco que pretende utilizar. Tenha em mente que nem todos os discos disponíveis são mostrados no menu suspenso. Depois de escolher um tamanho VM particular, o menu mostra apenas o armazenamento premium disponível SKUs que são baseados nesse tamanho vM.

![Screenshot da página de disco gerido](./media/oracle-design/premium_disk01.png)

Depois de configurar o seu armazenamento num VM, é melhor carregar os discos antes de criar uma base de dados. Conhecer a taxa de I/S em termos de latência e de entrada pode ajudá-lo a determinar se os VMs suportam o resultado esperado com alvos de latência.

Existem várias ferramentas para testes de carga de aplicações, tais como Oracle Orion, Sysbench e Fio.

Faça o teste de carga novamente depois de ter implantado uma base de dados da Oracle. Inicie as suas cargas de trabalho regulares e máximas, e os resultados mostram-lhe a linha de base do seu ambiente.

Pode ser mais importante dimensionar o armazenamento com base na taxa IOPS em vez do tamanho do armazenamento. Por exemplo, se o IOPS necessário for de 5.000, mas só precisa de 200 GB, ainda pode obter o disco premium classe P30, mesmo que venha com mais de 200 GB de armazenamento.

A taxa iops pode ser obtida a partir do relatório AWR. É determinado pelo registo de redo, leituras físicas e taxa de escrita.

![Screenshot da página de relatório da AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho do redo é de 12.200.000 bytes por segundo, o que equivale a 11,63 MBPs.
O IOPS é 12.200.000 / 2.358 = 5.174.

Depois de ter uma imagem clara dos requisitos de I/S, pode escolher uma combinação de unidades que são mais adequadas para satisfazer esses requisitos.

**Recomendações**

- Para o espaço de tabela de dados, espalhe a carga de trabalho em I/S através de vários discos utilizando armazenamento gerido ou Oracle ASM.
- À medida que o tamanho do bloco de I/S aumenta para operações intensivas de leitura e de write-intensivo, adicione mais discos de dados.
- Aumente o tamanho do bloco para grandes processos sequenciais.
- Utilize a compressão de dados para reduzir o I/S (tanto para dados como para índices).
- Separe os registos de refazer, sistema e temperaturas, e desfaça tS em discos de dados separados.
- Não coloque ficheiros de aplicação em discos os devidos (/dev/sda). Estes discos não estão otimizados para tempos de arranque vm rápidos, e podem não fornecer um bom desempenho para a sua aplicação.
- Quando utilizar VMs da Série M no armazenamento Premium, ative o [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) no disco de registos de redo.

### <a name="disk-cache-settings"></a>Definições de cache do disco

Existem três opções para o caching anfitrião:

- *Leia Também:* Todos os pedidos estão em cache para leituras futuras. Todas as escritas são percecionadas diretamente para o armazenamento de Azure Blob.

- *Leia:* Este é um algoritmo "read-ahead". As leituras e os escritos estão em cache para leituras futuras. As escritas não escritas são persistidas primeiro na cache local. Também fornece a latência de disco mais baixa para cargas de trabalho leves. Utilizar a cache ReadWrite com uma aplicação que não lida com a persistência dos dados necessários pode levar à perda de dados, se o VM falhar.

- *Nenhum* (desativado): Ao utilizar esta opção, pode contornar a cache. Todos os dados são transferidos para o disco e persistem para o Armazenamento Azure. Este método dá-lhe a maior taxa de I/S para cargas de trabalho intensivas. Também precisa de ter em consideração o "custo de transação".

**Recomendações**

Para maximizar a entrada, recomendamos que comece com **nenhum** para o caching hospedeiro. Para o Armazenamento Premium, lembre-se de que deve desativar as "barreiras" quando montar o sistema de ficheiros com as opções **ReadOnly** ou **None.** Atualize o ficheiro /etc/fstab com o UUID para os discos.

![Screenshot da página de disco gerido](./media/oracle-design/premium_disk02.png)

- Para os discos OS, utilize o predefinido **Ler/Escrever** cache.
- Para sistema, TEMP e UNDO usam **nenhum** para cache.
- Para DATA, use **Nenhum** para cache. Mas se a sua base de dados for apenas para leitura ou para leitura intensiva, use o caching **apenas de leitura.**

Depois de a definição do disco de dados ser guardada, não pode alterar a definição de cache do hospedeiro a menos que desmonte a unidade ao nível de OS e, em seguida, remonte-a depois de ter feito a alteração.

## <a name="security"></a>Segurança

Depois de configurar e configurar o seu ambiente Azure, o próximo passo é proteger a sua rede. Aqui ficam algumas recomendações:

- *Política NSG*: NSG pode ser definido por uma sub-rede ou NIC. É mais simples controlar o acesso ao nível da sub-rede, tanto para segurança como para forçar o encaminhamento para coisas como firewalls de aplicações.

- *Jumpbox*: Para um acesso mais seguro, os administradores não devem ligar-se diretamente ao serviço de aplicação ou à base de dados. Uma caixa de salto é usada como um meio de comunicação entre a máquina de administrador e os recursos Azure.
![Screenshot da página de topologia Jumpbox](./media/oracle-design/jumpbox.png)

    A máquina de administrador deve oferecer apenas acesso restrito a IP à caixa de salto. A caixa de salto deve ter acesso à aplicação e base de dados.

- *Rede privada* (subnets): Recomendamos que tenha o serviço de aplicação e base de dados em subredes separadas, para que um melhor controlo possa ser definido pela política NSG.


## <a name="additional-reading"></a>Leitura adicional

- [Configurar Oracle ASM](configure-oracle-asm.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configure Oráculo Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação da Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explore as amostras azure CLI de implantação vm](../../linux/cli-samples.md)
