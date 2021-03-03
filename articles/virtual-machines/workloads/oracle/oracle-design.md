---
title: Conceça e implemente uma base de dados da Oracle em Azure | Microsoft Docs
description: Desenhe e implemente uma base de dados Oracle no seu ambiente Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669988"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceça e implemente uma base de dados oracle em Azure

## <a name="assumptions"></a>Pressupostos

- Estás a planear migrar uma base de dados do Oráculo do local para o Azure.
- Você tem o [Pacote de Diagnósticos](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) ou o [Repositório de Carga automática](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) para a Base de Dados Oráculo que pretende migrar
- Tem uma compreensão das várias métricas em Oráculo.
- Tem uma compreensão de base do desempenho da aplicação e da utilização da plataforma.

## <a name="goals"></a>Objetivos

- Compreenda como otimizar a sua implantação oráculo em Azure.
- Explore as opções de afinação de desempenho para uma base de dados Oracle num ambiente Azure.
- Ter expectativas claras entre os limites da sintonização física através da arquitetura e vantagens ou a sintonização lógica do código de base de dados(SQL) e o design geral da base de dados.

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

- Determinar o uso real do CPU, uma vez que a Oracle é licenciada pelo núcleo, dimensionar as necessidades do vCPU pode ser um exercício essencial para poupar custos. 
- Determine o tamanho da base de dados, armazenamento de backup e taxa de crescimento.
- Determine os requisitos de IO, que pode estimar com base nos relatórios Oracle Statspack e AWR ou em ferramentas de monitorização de armazenamento de nível de SISTEMA.

## <a name="configuration-options"></a>Opções de configuração

Existem quatro áreas potenciais que pode sintonizar para melhorar o desempenho num ambiente Azure:

- Tamanho da máquina virtual
- Produção de rede
- Tipos e configurações de discos
- Definições de cache de disco

### <a name="generate-an-awr-report"></a>Gerar um relatório da AWR

Se tem uma base de dados Oracle Enterprise Edition existente e planeia migrar para Azure, tem várias opções. Se tiver o [Pacote de Diagnóstico para](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) as suas instâncias Oráculos, pode executar o relatório da Oracle AWR para obter as métricas (IOPS, Mbps, GiBs, e assim por diante). Para essas bases de dados sem a licença Diagnostics Pack ou para uma base de dados Standard Edition, as mesmas métricas importantes podem ser recolhidas com um relatório Statspack após a recolha de instantâneos manuais.  A principal diferença entre estes dois métodos de reporte é que a AWR é recolhida automaticamente e fornece mais informações sobre a base de dados do que a sua opção de reporte antecessora do Statspack.

Você pode considerar executar o seu relatório de AWR durante cargas de trabalho regulares e picos, para que você possa comparar. Para recolher a carga de trabalho mais precisa, considere um relatório de janela alargado de uma semana, vs. uma corrida de 24 horas, e perceba que a AWR fornece médias como parte dos seus cálculos no relatório.  Para uma migração do datacenter, recomendamos a recolha de relatórios para dimensionamento dos sistemas de produção e estimar as restantes cópias de base de dados utilizadas para testes, testes, desenvolvimento, etc., por percentagens (UAT igual a produção, teste e desenvolvimento 50% do tamanho da produção, etc.)

Por padrão, o repositório AWR retém 8 dias de dados e tira fotos em intervalos de hora.  Para executar um relatório AWR a partir da linha de comando, o seguinte pode ser realizado a partir de um terminal:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Principais métricas

O relatório solicitará as seguintes informações:
- Tipo de relatório: HTML ou TEXT(HTML em 12.1 e fornece informações adicionais que não o formato TEXT.)
- O número de dias de instantâneos para exibir(para intervalos de uma hora, um relatório de uma semana seria um 168 diferente em IDs instantâneos)
- O instantâneo inicial para a janela do relatório.
- O snapshot final para a janela do relatório.
- O nome do relatório a ser criado pelo script AWR.

Se executar o AWR num Cluster de Aplicações Reais, (RAC) o relatório da linha de comando é o awrgrpt.sql em vez de awrrpt.sql.  O relatório "g" criará um relatório para todos os nós na base de dados RAC num único relatório vs. ter de executar um em cada nó RAC.

Seguem-se as métricas que pode obter a partir do relatório da AWR:

- Nome da base de dados, nome de instância e nome do anfitrião
- Versão de base de dados (suportabilidade da Oracle)
- CPU/Núcleos
- SGA/PGA, (e assessores para informá-lo se subdimensionado)
- Memória total em GB
- CPU % Ocupado
- DB CPUs
- IOPs (ler/escrever)
- MBPs (ler/escrever)
- Produção de rede
- Taxa de latência da rede (baixa/alta)
- Principais eventos de espera 
- Definições de parâmetros para base de dados
- É base de dados RAC, Exadata, usando funcionalidades avançadas ou configurações

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
- As páginas enormes não são ativadas no linux por defeito. Considere ativar páginas enormes e definir `use_large_pages = ONLY` no Oráculo DB. Isto pode ajudar a aumentar o desempenho. Pode encontrar mais informações [aqui](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Tipos e configurações de discos

- *Discos de OS predefinidos*: Estes tipos de disco oferecem dados persistentes e cacheing. São otimizados para acesso ao SISTEMA no arranque, e não são projetados para cargas de trabalho transacionais ou de data warehouse (analítico).

- *Discos geridos*: O Azure gere as contas de armazenamento que utiliza para os discos VM. Especifica o tipo de disco (mais frequentemente premium SSD para cargas de trabalho da Oracle) e o tamanho do disco de que necessita. O Azure cria e gere o disco para si.  O disco gerido premium SSD só está disponível para séries VM otimizadas para memória e especificamente concebidas. Depois de escolher um determinado tamanho VM, o menu mostra apenas os SKUs de armazenamento premium disponíveis que são baseados nesse tamanho VM.

![Screenshot da página de disco gerido](./media/oracle-design/premium_disk01.png)

Depois de configurar o seu armazenamento num VM, é melhor carregar os discos antes de criar uma base de dados. Conhecer a taxa de E/S em termos de latência e produção pode ajudá-lo a determinar se os VMs suportam a produção esperada com alvos de latência.

Existem várias ferramentas para testes de carga de aplicação, tais como Oracle Orion, Sysbench, SLOB e Fio.

Faça o teste de carga de novo depois de ter implantado uma base de dados oracle. Inicie as suas cargas de trabalho regulares e máximas, e os resultados mostram-lhe a linha de base do seu ambiente.  Seja realista no teste de carga de trabalho, não faz sentido executar uma carga de trabalho que não é nada como o que vai correr no VM na realidade.

Como a Oracle é uma base de dados intensiva para muitos, é muito importante dimensionar o armazenamento com base na taxa IOPS e não no tamanho do armazenamento. Por exemplo, se o IOPS necessário for de 5.000, mas só precisa de 200 GB, ainda poderá obter o disco premium classe P30, mesmo que venha com mais de 200 GB de armazenamento.

A taxa IOPS pode ser obtida a partir do relatório da AWR. É determinado pelo registo de redo, leituras físicas e taxa de escrita.  Verifique sempre que a série VM escolhida também tem a capacidade de lidar com a procura de IO da carga de trabalho.  Se o VM tiver um limite de IO inferior ao do armazenamento, o limite máximo será definido pelo VM.

![Screenshot da página de relatório da AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho do redo é de 12.200.000 bytes por segundo, o que equivale a 11,63 MBPs.
O IOPS é de 12.200.000 / 2.358 = 5.174.

Depois de ter uma imagem clara dos requisitos de E/S, pode escolher uma combinação de unidades que sejam mais adequadas para satisfazer esses requisitos.

**Recomendações**

- Para o espaço de tabela de dados, espalhe a carga de trabalho de E/S através de vários discos utilizando armazenamento gerido ou Oracle ASM.
- Utilize a compressão avançada da Oracle para reduzir a E/S (tanto para dados como para índices).
- Separe os registos de redo, os espaços de mesa Temp e U doumos em discos de dados separados.
- Não coloque ficheiros de aplicação em discos de OS predefinidos (/dev/sda). Estes discos não estão otimizados para tempos de arranque rápidos em VM, e podem não proporcionar um bom desempenho para a sua aplicação.
- Ao utilizar VMs da Série M no armazenamento Premium, ative [o Acelerador de Escrita](../../how-to-enable-write-accelerator.md) no disco de registos de redo.
- Considere mover registos de redo com alta latência para ultra disco.

### <a name="disk-cache-settings"></a>Definições de cache de disco

Existem três opções para o caching do anfitrião, mas para uma base de dados Oracle, apenas o caching ReadOnly é recomendado para uma carga de trabalho de base de dados.  O ReadWrite pode introduzir vulnerabilidades significativas num ficheiro de dados, onde o objetivo de uma base de dados escrever é registrá-lo no ficheiro de dados, não cache a informação.

Ao contrário de um sistema de ficheiros ou aplicação, para uma base de dados, a recomendação para o cache do anfitrião é *ReadOnly*: Todos os pedidos estão em cache para futuras leituras. Todos os escritos continuam a ser escritos para o disco.

**Recomendações**

Para maximizar a produção, recomendamos que comece com **ReadOnly** para o caching do anfitrião sempre que possível. Para o Armazenamento Premium, tenha em mente que deve desativar as "barreiras" quando monta o sistema de ficheiros com as opções **ReadOnly.** Atualize o ficheiro /etc/fstab com o UUID para os discos.

![Screenshot da página de disco gerida que mostra as opções ReadOnly e None.](./media/oracle-design/premium_disk02.png)

- Para discos OS, utilize o cache padrão **de Leitura/Escrita** e utilize SSD premium para VMs de carga de trabalho da Oracle.  Certifique-se também de que o volume utilizado para a troca também está no SSD premium.
- Para todos os DATAFILES, utilize **ReadOnly** para caching. ReadOnly caching só está disponível para disco gerido premium, P30 e acima.  Existe um limite de um volume 4095GiB que pode ser usado com o caching ReadOnly.  Qualquer alocação maior irá desativar o caching do hospedeiro por padrão.

Se as cargas de trabalho variarem muito entre o dia e a noite e a carga de trabalho do IO puder apoiá-la, o P1-P20 Premium SSD com rebentamento pode fornecer o desempenho necessário durante as cargas de lote noturno ou as exigências limitadas de IO.  

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
- [Explore amostras de CLI de implantação VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
