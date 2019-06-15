---
title: Conceber e implementar uma base de dados do Oracle no Azure | Documentos da Microsoft
description: Conceber e implementar uma base de dados do Oracle no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: c5a76b9cee8fd6eb09ee4d24c1380202fd17cc6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836353"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceba e implemente uma base de dados Oracle no Azure

## <a name="assumptions"></a>Suposições

- Estiver a planear migrar uma base de dados do Oracle no local para o Azure.
- Tem uma compreensão das métricas de vários relatórios Oracle AWR.
- Tem uma compreensão de linha de base de desempenho da aplicação e a utilização de plataforma.

## <a name="goals"></a>Objetivos

- Aprenda a otimizar a sua implementação do Oracle no Azure.
- Explore as opções para uma base de dados do Oracle no ambiente do Azure de ajuste de desempenho.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre no local e a implementação do Azure 

Seguem-se alguns aspetos importantes a ter em mente quando estiver a migrar aplicações no local para o Azure. 

Uma diferença importante é que uma implementação do Azure, os recursos, tais como VMs, discos e redes virtuais são partilhados entre os outros clientes. Além disso, os recursos podem ser otimizados com base nos requisitos. Em vez de concentrar-se em evitar com falhas (MTBF), o Azure está mais focalizado em sobreviverem à falha (MTTR).

A tabela seguinte lista algumas das diferenças entre uma implementação no local e uma implementação do Azure de uma base de dados Oracle.

> 
> |  | **Implementação no local** | **Implementação do Azure** |
> | --- | --- | --- |
> | **Redes** |LAN/WAN  |SDN (redes definidas por software)|
> | **Grupo de segurança** |Ferramentas de restrição de IP/porta |[Grupo de segurança de rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (tempo médio de recuperação)|
> | **Manutenção planeada** |Aplicação de patches/atualização|[Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (aplicação de patches/atualizações geridas pelo Azure) |
> | **Recurso** |Dedicado  |Partilhado com outros clientes|
> | **Regiões** |Datacenters |[Pares de região](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Armazenamento** |Discos de SAN/físico |[Armazenamento geridos pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Dimensionamento** |Dimensionamento vertical |Dimensionamento horizontal|


### <a name="requirements"></a>Requisitos

- Determine a taxa de tamanho e crescimento da base de dados.
- Determine os requisitos de IOPS, o que pode fazer uma estimativa com base no Oracle AWR relatórios ou outra ferramentas de monitorização de rede.

## <a name="configuration-options"></a>Opções de configuração

Existem quatro áreas potenciais que pode ajustar para melhorar o desempenho num ambiente do Azure:

- Tamanho da máquina virtual
- Débito de rede
- Tipos de disco e configurações
- Definições de cache do disco

### <a name="generate-an-awr-report"></a>Gerar um relatório AWR

Se tiver uma base de dados Oracle existente e estiver a planear migrar para o Azure, tem várias opções. Pode executar o relatório de Oracle AWR para obter as métricas (IOPS, Mbps, GiBs e assim por diante). Em seguida, selecione a VM com base nas métricas que recolheu. Ou pode contactar a equipa de infraestrutura para obter informações semelhantes.

Pode optar por executar o relatório AWR durante cargas de trabalho regulares e horas de pico, para que pode comparar. Com base nestes relatórios, pode dimensionar as VMs com base na carga de trabalho média ou a carga de trabalho máxima.

Segue-se um exemplo de como gerar um relatório AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Métricas-chave

Seguem-se as métricas que pode obter a partir do relatório AWR:

- Número total de núcleos
- Velocidade de clock de CPU
- Total de memória em GB
- Utilização da CPU
- Taxa de transferência de dados de pico
- Taxa de alterações de e/s (leitura/escrita)
- Refazer a taxa de registo (MBPs)
- Débito de rede
- Taxa de latência de rede (alta/baixa)
- Tamanho da base de dados em GB
- Bytes recebidos por meio de SQL * líquido de/para o cliente

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Tamanho VM de estimativa com base na utilização da CPU, memória e e/s do relatório AWR

Uma coisa que pode observar é que os eventos de primeiro plano temporizados de cinco principais que indicam onde estão os congestionamentos de sistema.

Por exemplo, no diagrama seguinte, a sincronização de ficheiros de registo está na parte superior. Ele indica o número de esperas que sejam necessários antes do LGWR escreve o buffer de registo para o ficheiro de registo de Refazer. Esses resultados indicam que o melhor desempenho de armazenamento ou discos são necessários. Além disso, o diagrama também mostra o número de CPU (núcleos) e a quantidade de memória.

![Captura de ecrã da página do relatório AWR](./media/oracle-design/cpu_memory_info.png)

O diagrama seguinte mostra o total e/s de leitura e escrita. Havia 59 GB, ler e GB 247.3 escrito durante o período do relatório.

![Captura de ecrã da página do relatório AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Escolha uma VM

Com base nas informações que recolheu do relatório AWR, a próxima etapa é escolher uma VM de um tamanho semelhante que cumpre os seus requisitos. Pode encontrar uma lista de VMs disponíveis no artigo [otimizada de memória](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ajustar o dimensionamento de VM com uma série VM semelhante, com base no ACU

Depois de escolher a VM, preste atenção à ACU para a VM. Pode escolher uma VM diferente com base no valor ACU que melhor se adeque às suas necessidades. Para obter mais informações, consulte [unidade de computação do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Captura de ecrã da página de unidades ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Débito de rede

O diagrama seguinte mostra a relação entre o débito e IOPS:

![Captura de ecrã do débito](./media/oracle-design/throughput.png)

O débito de rede total é estimado com base nas seguintes informações:
- SQL * tráfego de rede
- MBps x número de servidores (fluxo de saída, como o Oracle Data Guard)
- Outros fatores, como replicação de aplicação

![Captura de ecrã do SQL * o débito de rede](./media/oracle-design/sqlnet_info.png)

Com base nos seus requisitos de largura de banda de rede, existem vários tipos de gateway para a sua escolha. Estes incluem basic, VpnGw e o Azure ExpressRoute. Para obter mais informações, consulte a [página de preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recommendations (Recomendações)**

- Latência de rede é superior em comparação com uma implementação no local. Reduzindo rede arredondar viagens pode significativamente melhorar o desempenho.
- Para reduzir a ida e volta, consolide os aplicativos que têm transações elevadas ou aplicativos "conversadoras" na mesma máquina virtual.

### <a name="disk-types-and-configurations"></a>Tipos de disco e configurações

- *Padrão de discos de SO*: Esses tipos de disco oferecem dados persistentes e a colocação em cache. Eles estão otimizados para acesso do sistema operacional na inicialização e não são concebidos para qualquer um transacional ou cargas de trabalho (análise) do armazém de dados.

- *Discos não geridos*: Com esses tipos de disco, gerir as contas de armazenamento que armazenam os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Ficheiros VHD são armazenados como blobs de páginas em contas de armazenamento do Azure.

- *Discos geridos*: O Azure gere as contas de armazenamento que utilizar para os discos da VM. Especifique o tipo de disco (premium ou standard) e o tamanho do disco que precisa. O Azure cria e gere o disco por si.

- *Discos de armazenamento Premium*: Esses tipos de disco são mais adequados para cargas de trabalho de produção. O armazenamento Premium suporta discos VM que podem ser anexados a VMs de série de tamanho específicas, como série DS, DSv2, GS e F VMs. O disco premium vem com diferentes tamanhos e pode escolher entre discos variam entre 32 GB e 4,096 GB. Cada tamanho de disco tem seus próprio especificações de desempenho. Dependendo dos requisitos de aplicação, pode anexar um ou mais discos à sua VM.

Quando criar um novo disco gerido a partir do portal, pode escolher o **tipo de conta** para o tipo de disco que pretende utilizar. Tenha em mente que nem todos os discos disponíveis são apresentados no menu pendente. Depois de escolher um determinado tamanho VM, o menu mostra apenas o armazenamento premium disponível SKUs que se baseiam nesse tamanho VM.

![Captura de ecrã da página de disco gerido](./media/oracle-design/premium_disk01.png)

Depois de configurar o armazenamento numa VM, poderá pretender carregar os discos de teste antes de criar uma base de dados. Saber a taxa de e/s em termos de débito e latência pode ajudar a determinar se as VMs suportam o débito esperado com destinos de latência.

Existem várias ferramentas para o aplicativo teste de carga, como Oracle Orion, Sysbench e Fio.

Execute novamente o teste de carga, depois de implementar uma base de dados Oracle. Iniciar as suas cargas de trabalho regulares e horas de pico e os resultados mostram-lhe a linha de base do seu ambiente.

Poderá ser mais importante para o tamanho de armazenamento com base na taxa de IOPS, em vez do tamanho de armazenamento. Por exemplo, se o IOPS necessário é 5.000, mas precisa apenas 200 GB, poderá obter o disco de premium de classe P30, mesmo que ele vem com mais de 200 GB de armazenamento.

A taxa IOPS pode ser obtida a partir do relatório AWR. Este será determinado pelo log de retrabalho, leituras físicas e taxa de gravações.

![Captura de ecrã da página do relatório AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho de Refazer é 12,200,000 bytes por segundo, o que é igual a 11.63 MBPs.
O IOPS é 12,200,000 / 2,358 = 5,174.

Depois de ter uma visão clara dos requisitos de e/s, pode escolher uma combinação de unidades que melhor se adequam para atender a esses requisitos.

**Recommendations (Recomendações)**

- Para o espaço de tabelas de dados, distribuir a carga de trabalho de e/s por um número de discos com o armazenamento gerido ou Oracle ASM.
- À medida que aumenta o tamanho do bloco de e/s para operações de leitura intensiva e de escrita intensiva, adicione mais discos de dados.
- Aumente o tamanho de bloco para grandes processos seqüenciais.
- Utilize compressão de dados para reduzir e/s (para dados e índices).
- Separar os registos de Refazer, sistema e termos e anular TS em discos de dados separado.
- Não coloque nenhum ficheiro de aplicação em discos de SO predefinido (/ desenvolvimento/sda). Estes discos não estão otimizados para a VM rápida tempos de inicialização e eles podem não oferecer um bom desempenho para a sua aplicação.

### <a name="disk-cache-settings"></a>Definições de cache do disco

Existem três opções para a colocação em cache do anfitrião:

- *Só de leitura*: Todos os pedidos são colocados em cache para leituras futuras. Todas as escritas são mantidas diretamente para o armazenamento de Blobs do Azure.

- *Leitura e escrita*: Este é um algoritmo "read-ahead". As leituras e gravações são colocadas em cache para leituras futuras. Escritas simultânea não são mantidas pela primeira vez para a cache local. Para o SQL Server, as escritas são mantidas ao armazenamento do Azure porque utiliza através de escrita. Ele também fornece a menor latência de disco para cargas de trabalho leves.

- *Nenhum* (desativada): Ao utilizar esta opção, pode ignorar a cache. Todos os dados são transferidos para o disco e mantidos no armazenamento do Azure. Este método permite-lhe a mais alta taxa de e/s para cargas de trabalho intensivas de e/s. Também terá de levar "custos de transação" em consideração.

**Recommendations (Recomendações)**

Para maximizar o débito, recomendamos que comece com **None** para colocação em cache do anfitrião. Para armazenamento Premium, tenha em atenção que tem de desativar as "barreiras" ao montar o sistema de ficheiros com o **só de leitura** ou **nenhum** opções. Atualize o ficheiro de /etc/fstab. com o UUID aos discos.

![Captura de ecrã da página de disco gerido](./media/oracle-design/premium_disk02.png)

- Discos de SO, utilizar predefinição **leitura/escrita** colocação em cache.
- Para o sistema, TEMP e DESFAZER usar **None** para colocar em cache.
- Para os dados, utilize **None** para colocar em cache. Mas se a sua base de dados é só de leitura ou leitura intensiva, utilize **só de leitura** colocação em cache.

Depois da definição de disco de dados é guardada, não é possível alterar a definição de cache do anfitrião, a menos que desmonte a unidade ao nível do SO e, em seguida, voltar a montá-lo Depois que fizer a alteração.


## <a name="security"></a>Segurança

Depois de configurar e configurado o seu ambiente do Azure, a próxima etapa é proteger a sua rede. Seguem-se algumas recomendações:

- *Política NSG*: NSG pode ser definido por uma sub-rede ou NIC. É mais simples para controlar o acesso ao nível da sub-rede, tanto para segurança e a força de encaminhamento para coisas como firewalls de aplicações.

- *Jumpbox*: Para obter acesso mais seguro, os administradores devem não ligar-se diretamente para o serviço de aplicações ou a base de dados. Uma jumpbox é utilizado como um suporte de dados entre a máquina de administrador e os recursos do Azure.
![Captura de ecrã da página de topologia Jumpbox](./media/oracle-design/jumpbox.png)

    A máquina de administrador deve oferecer acesso restrito de IP para a jumpbox apenas. A jumpbox deve ter acesso à aplicação e base de dados.

- *Rede privada* (sub-redes): Recomendamos que tenha o serviço de aplicações e a base de dados em sub-redes separadas, para que um melhor controle pode ser definido pela política NSG.


## <a name="additional-reading"></a>Leitura adicional

- [Configurar Oracle ASM](configure-oracle-asm.md)
- [Configurar a proteção de dados Oracle](configure-oracle-dataguard.md)
- [Configurar Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle backup e recuperação](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar VMs de elevada disponibilidade](../../linux/create-cli-complete.md)
- [Explore exemplos de CLI do Azure de implementação de VM](../../linux/cli-samples.md)
