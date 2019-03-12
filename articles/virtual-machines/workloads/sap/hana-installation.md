---
title: Instalar o SAP HANA no SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Como instalar o SAP HANA num SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bdf23d1a2142e5c83ceeb72a79ca4fbea65d09c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534281"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar o SAP HANA (instâncias grandes) no Azure

Antes de ler este artigo, familiarize-se com [termos comuns de instâncias grandes do HANA](hana-know-terms.md) e o [SKUs de instâncias grandes do HANA](hana-available-skus.md).

A instalação do SAP HANA é sua responsabilidade. Pode começar a instalar um novo SAP HANA no servidor do Azure (instâncias grandes), depois de estabelecer a conectividade entre redes virtuais do Azure e a instância grande do HANA unidade (s). 

> [!Note]
> Por diretiva SAP, a instalação do SAP HANA deve ser executada por uma pessoa que tiver passado o exame de certificação associar de tecnologia de SAP, o exame de certificação de instalação do SAP HANA ou, quem é um certificado integrador de sistemas (SI).

Quando estiver planejando instalar HANA 2.0, consulte [suporte a nota SAP #2235581 - SAP HANA: Sistemas operativos suportados](https://launchpad.support.sap.com/#/notes/2235581/E) para se certificar de que o sistema operacional é suportado com o SAP HANA de versão que está a instalar. O sistema operativo suportado para HANA 2.0 é mais restritivo que o sistema operativo suportado para HANA 1.0. 

> [!IMPORTANT] 
> Tipo II unidades, atualmente que apenas a versão de SO do SLES 12 SP2 é suportada. 

É necessário validar o seguinte antes de iniciar a instalação do HANA:
- [Unidade (s) HLI](#validate-the-hana-large-instance-units)
- [Configuração do sistema operativo](#operating-system)
- [Configuração da rede](#networking)
- [Configuração do armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar a instância grande do HANA unidade (s)

Depois de receber a unidade de instância grande do HANA da Microsoft, Valide as seguintes definições e ajustar conforme necessário.

O **primeiro passo** depois de receber a instância grande do HANA e estabeleça o acesso e conectividade para as instâncias, é registrar o sistema operacional da instância do seu fornecedor do sistema operacional. Este passo inclui a registar o seu SO do SUSE Linux numa instância do SUSE SMT que é implementada numa VM no Azure. 

A unidade de instância grande do HANA, pode ligar a esta instância SMT. (Para obter mais informações, consulte [como configurar o servidor SMT para SUSE Linux](hana-setup-smt.md)). Em alternativa, o SO do Red Hat tem de ser registado com o Red Hat Gestor de subscrições que precisam de ligar a. Para obter mais informações, consulte as observações em [o que é o SAP HANA no Azure (instâncias grandes)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Este passo é necessário para a aplicação de patches do SO, o que é da responsabilidade do cliente. Para o SUSE, localize a documentação de instalação e configuração SMT nesta página sobre [instalação SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

O **segunda etapa** é verificar a existência de novos patches e correções da versão/versão de SO específica. Certifique-se de que o nível de correção da instância grande do HANA está no estado mais recente. Poderá haver casos em que os patches mais recentes não estão incluídos. Depois de a demorar mais de uma unidade de instância grande do HANA, é obrigatório para verificar se os patches, precisam ser aplicados.

O **terceira etapa** é verificar as notas SAP relevantes para instalar e configurar o SAP HANA em lançamento/versão de SO específica. Devido à alteração recomendações ou alterações para SAP notes ou configurações que são dependentes de cenários de instalação individuais, Microsoft não sempre será possível configurar uma unidade de instância grande do HANA perfeitamente. 

Por conseguinte, é obrigatório para, como um cliente para ler as notas SAP associadas ao SAP HANA para a sua versão exata do Linux. Além disso, verifique as configurações da versão/versão do SO e aplicar as definições de configuração, se ainda não o fez.

Especificamente, verifique os seguintes parâmetros e, eventualmente, ajustar-se:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partir do SLES12 SP1 e RHEL 7.2, esses parâmetros devem ser definidos num ficheiro de configuração no diretório /etc/sysctl.d. Por exemplo, um ficheiro de configuração com o nome 91-NetApp-HANA.conf tem de ser criado. Para versões mais antigas de SLES e RHEL, esses parâmetros têm de ser in/etc/sysctl.conf conjunto.

Para todas as versões do RHEL a partir do RHEL 6.3, tenha em atenção o seguinte: 
- O sunrpc.tcp_slot_table_entries = 128 parâmetro tem de ser definido in/etc/modprobe.d/sunrpc-local.conf. Se o ficheiro não existir, terá de criá-la pela primeira vez, adicionando a seguinte entrada: 
    - options sunrpc tcp_max_slot_table_entries=128

O **quarto passo** é verificar a hora do sistema de sua unidade de instância grande do HANA. As instâncias são implementadas com um fuso horário do sistema. Este fuso horário representa a localização da região do Azure em que se encontra o carimbo de data / instância grande do HANA. Pode alterar a hora do sistema ou o fuso horário das instâncias que é proprietário. 

Se o solicitar mais instâncias no seu inquilino, terá de se adaptar o fuso horário das instâncias recentemente entregues. A Microsoft não tem nenhuma ideia o fuso horário do sistema que configurou com as instâncias depois de entregar o direito de permanência. Assim, as instâncias recentemente implementadas não podem ser definidas no mesmo fuso horário que aquela alterada para. Ele tem cabe a como cliente para adaptar o fuso horário de instância (s) que foram passada, se necessário. 

O **quinto passo** é verificar etc/anfitriões. Como os painéis obterem passados, eles têm diferentes endereços IP que são atribuídos para diferentes fins. Verifique o ficheiro de anfitriões/etc. Quando as unidades são adicionadas para um inquilino existente, não espere que ter etc/anfitriões de novos sistemas implantados mantidos corretamente com os endereços IP dos sistemas que foram entregues anteriormente. É da responsabilidade do cliente como o cliente torna-se de que uma instância recentemente implementada pode interagir e resolver os nomes das unidades de que implementou anteriormente no seu inquilino. 


## <a name="operating-system"></a>Sistema operativo

> [!IMPORTANT] 
> Para unidades de tipo II, apenas a versão de SO do SLES 12 SP2 é atualmente suportada. 

O espaço de comutação da imagem do SO entregue está definido como 2 GB de acordo com o [suporte a nota SAP #1999997 - FAQ: Memória do SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Como um cliente, se pretender que uma configuração diferente, tem de defini-lo por conta própria.

[SUSE Linux Enterprise Server 12 SP1 para aplicações SAP](https://www.suse.com/products/sles-for-sap/download/) é a distribuição de Linux que está instalado para SAP HANA no Azure (instâncias grandes). Essa distribuição particular oferece recursos específicos da SAP "Out of box" (incluindo parâmetros predefinidos para executar o SAP no SLES efetivamente).

Ver [biblioteca/white papers do recurso](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na rede de Comunidade do SAP (SCN) para vários recursos úteis relacionadas com a implementação de SAP HANA no SLES (incluindo a configuração de alta disponibilidade, segurança sistema de proteção que é específica para operações de SAP e muito mais).

Segue-se SAP adicional e útil em ligações de SUSE:

- [SAP HANA no site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Melhores práticas para SAP: Colocar em fila replicação – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – proteção contra vírus SLES para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo 12 do SLES para SAP applications)

Seguem-se notas de suporte do SAP que são aplicáveis a implementação de SAP HANA no SLES 12:

- [SAP suporte Nota #1944799 – diretrizes de SAP HANA para a instalação de sistema operativo do SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Suporte a nota SAP #2205917 – SAP HANA DB as definições de sistema operacional para o SLES 12 para aplicações SAP recomendada](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Suporte a nota SAP #1984787 – SUSE Linux Enterprise Server 12: observações de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP suporte Nota #171356 – software SAP no Linux:  Informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Suporte a nota SAP #1391070 – soluções do UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para o SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para a execução do SAP HANA nas instâncias grandes do HANA. Versões do RHEL 6.7 e 7.2 estão disponíveis. Tenha em atenção que diferentes dos padrões convencionais nativo VMs do Azure em que são suportadas apenas RHEL 7.2 e versões mais recentes, HANA nas instâncias grandes suporta RHEL 6.7 também. No entanto, recomendamos a utilização de uma versão de 7.x RHEL.

Seguem-se SAP úteis adicional nos links relacionados do Red Hat:
- [SAP HANA no site do Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Seguem-se notas de suporte do SAP que são aplicáveis à implementação de SAP HANA em Red Hat:

- [SAP suporte Nota #2009879 - diretrizes de SAP HANA para o sistema de operativo Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Suporte a nota SAP #2292690 - SAP HANA DB: Definições de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota SAP de suporte #2247020 - SAP HANA DB: Definições de sistema operacional recomendadas para RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020)
- [Suporte a nota SAP #1391070 – soluções do UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Suporte a nota SAP #2228351 - Linux: Revisão de 11 de SPS de base de dados do SAP HANA 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Suporte a nota SAP #2397039 - FAQ: SAP no RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Suporte a nota SAP #1496410 - Red Hat Enterprise Linux 6.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/1496410)
- [Suporte a nota SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Sincronização de hora

Aplicações de SAP baseiam-se na arquitetura do SAP NetWeaver são sensíveis às diferenças de tempo para os vários componentes que compõem o sistema SAP. Informações do Estado da SAP ABAP curto com o título de erro do ZDATE\_grande\_tempo\_DIFF já devem estar familiarizados. Isso acontece porque esses despejos curtos apresentado quando a hora do sistema de diferentes servidores ou VMs é também muito-drifting.

Para o SAP HANA no Azure (instâncias grandes), sincronização de hora que fez no Azure não se aplica a unidades de computação no carimbos de data / instância grande. Esta sincronização não é aplicável para executar aplicações SAP em VMs do Azure nativo, porque o Azure garante que o tempo de um sistema corretamente está sincronizado. 

Como resultado, tem de configurar um servidor de time separadas que pode ser utilizado por servidores de aplicações SAP em execução em VMs do Azure e as instâncias de base de dados do SAP HANA em execução nas instâncias grandes do HANA. A infraestrutura de armazenamento em carimbos de data / instância grande é a hora sincronizada com os servidores NTP.


## <a name="networking"></a>Funcionamento em Rede
Partimos do princípio de que seguiu as recomendações na criação de redes virtuais do Azure e na conexão essas redes virtuais com o HANA nas instâncias grandes, conforme descrito nos seguintes documentos:

- [Descrição geral do SAP HANA (instância grande) e a arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Existem alguns detalhes que vale a pena mencionar sobre o funcionamento em rede das unidades de únicos. Cada unidade de instância grande do HANA vem com dois ou três endereços IP que estão atribuídos a dois ou três portas NIC. Três endereços IP são utilizados nas configurações de escalamento horizontal do HANA e o cenário de replicação de sistema do HANA. Um dos endereços IP que é atribuído ao NIC da unidade está fora do servidor do conjunto de IP que está descrito em [descrição geral do SAP HANA (instâncias grandes) e a arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Para obter mais informações sobre os detalhes de Ethernet para a sua arquitetura, consulte a [cenários suportados de HLI](hana-supported-scenario.md).

## <a name="storage"></a>Armazenamento

O esquema de armazenamento para o SAP HANA no Azure (instâncias grandes) é configurado pelo SAP HANA na gestão de serviço do Azure através de SAP diretrizes recomendada. Essas diretrizes estão documentadas no [requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper. 

Os tamanhos aproximados dos volumes diferentes com SKUs diferentes instâncias grandes de HANA está documentado no [descrição geral do SAP HANA (instâncias grandes) e a arquitetura no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomenclatura dos volumes de armazenamento estão listadas na tabela a seguir:

| Utilização do armazenamento | Nome de montagem | Nome do volume | 
| --- | --- | ---|
| Dados do HANA | /Hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| Registo do HANA | /Hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Cópia de segurança de registo HANA | /hana/log/backups | Armazenamento de IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA partilhado | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* é a instância do HANA ID do sistema. 

*Inquilino* é uma enumeração interna de operações, ao implementar um inquilino.

Partilha de usr/sap HANA mesmo volume. A nomenclatura do mountpoints inclui o ID de sistema das instâncias do HANA, bem como o número de montagem. Nas implementações de aumentar verticalmente, há apenas uma montagem, tais como mnt00001. Nas implementações de escalamento horizontal, por outro lado, verá monta tantos que tenha nós de trabalho e o mestre. 

Para ambientes de escalamento horizontal, dados, registos e log volumes de cópia de segurança são partilhados e anexados a cada nó na configuração de escalamento horizontal. Para configurações que são várias instâncias do SAP, um conjunto diferente de volumes é criado e ligado à unidade de instância grande do HANA. Para detalhes de esquema de armazenamento para o seu cenário, consulte [cenários suportados de HLI](hana-supported-scenario.md).

Quando examinar uma unidade de instância grande do HANA, percebe que as unidades são fornecidos com o volume de disco generoso para HANA/dados e que existe um volume HANA/registo/cópia de segurança. O motivo que fizemos os HANA/dados tão grandes é que os instantâneos de armazenamento que oferecemos como um cliente estiver a utilizar o mesmo volume de disco. Os instantâneos de armazenamento mais efetuar, mais espaço é consumido pelo instantâneos nos volumes de armazenamento atribuída. 

O volume HANA/registo/cópia de segurança não deve para ser o volume para cópias de segurança da base de dados. Ele é ajustado para ser utilizado como o volume de cópia de segurança para os backups de log de transação do HANA. Para obter mais informações, consulte [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Para além do armazenamento que é fornecido, pode comprar capacidade de armazenamento adicional em incrementos de 1 TB. Este armazenamento adicional pode ser adicionado como novos volumes para uma instância grande do HANA.

Durante a integração com o SAP HANA na gestão de serviço do Azure, o cliente especifica um utilizador ID (UID) e o grupo ID (GID) para o grupo de utilizador e sapsys sidadm (por exemplo: 1000,500). Durante a instalação do sistema SAP HANA, tem de utilizar estes mesmos valores. Como deseja implementar várias instâncias HANA numa unidade, tem vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implementação terá de definir o seguinte:

- O SID das diferentes instâncias HANA (sidadm é derivado do mesmo).
- Os tamanhos de memória das diferentes instâncias do HANA. O tamanho de memória por instância define o tamanho dos volumes em cada conjunto de individual volume.

Com base nas recomendações do fornecedor de armazenamento, as seguintes opções de montagem estão configuradas para todos os volumes montados (exclui arranque LUN):

- rw de NFS, vers=3.0,username = 4, disco rígido, timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, bloquear 0 0

Estes montagem pontos estão configurados em /etc/fstab., conforme mostrado nos gráficos seguintes:

![fstab de volumes montados numa unidade de instância grande do HANA](./media/hana-installation/image1_fstab.PNG)

O resultado do comando df -h numa unidade de instância grande do HANA S72m é semelhante a:

![fstab de volumes montados numa unidade de instância grande do HANA](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e nós nos carimbos de data / instância grande são sincronizadas com os servidores NTP. Quando sincroniza o SAP HANA nas unidades do Azure (instâncias grandes) e as VMs do Azure num servidor NTP, não deverá haver nenhum descompassos de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou de instância grande carimbos de data /.

Para otimizar o SAP HANA para o armazenamento utilizado por baixo, defina os seguintes parâmetros de configuração de SAP HANA:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Para versões de SAP HANA 1.0 até SPS12, esses parâmetros podem ser definidos durante a instalação da base de dados SAP HANA, conforme descrito em [#2267798 - configuração da base de dados SAP HANA a nota SAP](https://launchpad.support.sap.com/#/notes/2267798).

Também pode configurar os parâmetros após a instalação de banco de dados do SAP HANA, utilizando a estrutura de hdbparam. 

O armazenamento utilizado nas instâncias grandes do HANA tem uma limitação de tamanho de ficheiro. O [limitação de tamanho é 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro em sistemas de ficheiros EXT3, HANA není implicitamente atento a limitação de armazenamento imposta pelo armazenamento de instâncias grandes do HANA. como resultado HANA não criará automaticamente um novo ficheiro de dados quando é atingido o limite de tamanho de ficheiro de 16TB. Como HANA tenta aumentar o arquivo para além dos 16 TB, o HANA relatório de erros e o servidor de índices falhará no final.

> [!IMPORTANT]
> Para impedir que o HANA tentando aumentar os arquivos de dados além do limite de tamanho de ficheiro de 16 TB de armazenamento de instâncias grandes do HANA, tem de definir os seguintes parâmetros no ficheiro de configuração global.ini SAP HANA
> 
- datavolume_striping=true
- datavolume_striping_size_gb = 15000
- Consulte também a SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
- Tenha em atenção a nota SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Com o SAP HANA 2.0, a estrutura de hdbparam foi preterida. Como resultado, tem de definir os parâmetros com comandos SQL. Para obter mais informações, consulte [a nota SAP #2399079: Eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte a [cenários suportados de HLI](hana-supported-scenario.md) para saber mais sobre o esquema de armazenamento para a sua arquitetura.


**Passos seguintes?**

- Consulte [instalação de HANA HLI](hana-example-installation.md)










































 







 




