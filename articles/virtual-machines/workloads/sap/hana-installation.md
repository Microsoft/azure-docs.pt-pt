---
title: Instale sAP HANA no SAP HANA no Azure (Grandes Instâncias) [ Microsoft Docs
description: Como instalar o SAP HANA num SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4db072cf881c936db6721845e7823082388515b0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117126"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar o SAP HANA (Grandes Instâncias) no Azure

Antes de ler este artigo, conheça os [termos comuns da HANA Large Instances](hana-know-terms.md) e as [SKUs de Grandes Instâncias HANA.](hana-available-skus.md)

A instalação do SAP HANA é da sua responsabilidade. Pode começar a instalar um novo servidor SAP HANA no Azure (Grandes Instâncias) depois de estabelecer a conectividade entre as suas redes virtuais Azure e as unidades HANA Large Instance. 

> [!Note]
> De acordo com a política SAP, a instalação do SAP HANA deve ser realizada por uma pessoa que tenha passado no exame Certificado SAP Technology Associate, no exame de certificação de instalação SAP HANA ou que seja um integrador de sistema certificado pela SAP (SI).

Quando estiver a planear instalar o HANA 2.0, consulte a nota de [suporte SAP #2235581 - SAP HANA: Sistemas operativos suportados](https://launchpad.support.sap.com/#/notes/2235581/E) para garantir que o OS é suportado com o SAP HANA que o está a instalar. O Sistema operativo apoiado para HANA 2.0 é mais restritivo do que o Sistema operativo apoiado para hana 1.0. Também precisa de verificar se a libertação de SO em que está interessado está listada como suportada para a unidade HLI específica nesta [lista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)publicada . Clique na unidade para obter todos os detalhes com a lista de SO suportada dessa unidade. 

Valide o seguinte antes de iniciar a instalação HANA:
- [Unidade(s) HLI(s)](#validate-the-hana-large-instance-units)
- [Configuração do sistema operativo](#operating-system)
- [Configuração da rede](#networking)
- [Configuração de armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar as unidades de grandes instâncias HANA

Depois de receber a unidade HANA Large Instance da Microsoft, valide as seguintes definições e ajuste-a se necessário.

O **primeiro passo** depois de receber a Grande Instância HANA e estabelecer acesso e conectividade às instâncias, é verificar no portal Azure se a(s) instância(s) está a aparecer com as SKUs e os SISTEMA corretos. Leia o controlo de [grandes instâncias azure HANA através do portal Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) para obter os passos necessários para efetuar as verificações.

O **segundo passo** após receber a Grande Instância HANA e estabelecer acesso e conectividade às instâncias, é registar o Sistema operativo da ocorrência com o seu fornecedor de SO. Este passo inclui o registo do seu SUSE Linux OS num caso de SUSE SMT que está implantado num VM em Azure. 

A unidade HANA Large Instance pode ligar-se a esta instância SMT. (Para obter mais informações, consulte [como configurar o servidor SMT para o SUSE Linux](hana-setup-smt.md)). Em alternativa, o seu Red Hat OS precisa de ser registado no Red Hat Subscription Manager a que precisa de se ligar. Para mais informações, consulte as observações no [What is SAP HANA on Azure (Grandes Instâncias)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Este passo é necessário para remendar o Sistema operativo, que é da responsabilidade do cliente. Para suse, encontre a documentação para instalar e configurar SMT nesta página sobre a [instalação de SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

O **terceiro passo** é verificar se há novos patches e correções da versão/versão específica do SISTEMA. Verifique se o nível de patch da Grande Instância HANA está no estado mais recente. Pode haver casos em que os últimos patches não estão incluídos. Depois de assumir uma unidade HANA Large Instance, é obrigatório verificar se os patches precisam de ser aplicados.

O **quarto passo** é verificar as notas SAP relevantes para a instalação e configuração do SAP HANA na versão/versão específica do OS. Devido à alteração de recomendações ou alterações nas notas ou configurações do SAP que dependem de cenários de instalação individuais, a Microsoft nem sempre será capaz de configurar uma unidade HANA Large Instance na perfeição. 

Portanto, é obrigatório para si, como cliente, ler as notas SAP relacionadas com o SAP HANA para a sua versão exata do Linux. Verifique também as configurações do lançamento/versão DO See e aplique as definições de configuração se ainda não o fez.

Especificamente, verifique os seguintes parâmetros e eventualmente ajuste-se a:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 1677216 16777216
- net.ipv4.tcp_wmem = 65536 1677216 16777216

A partir do SLES12 SP1 e do RHEL 7.2, estes parâmetros devem ser definidos num ficheiro de configuração no diretório /etc/sysctl.d. Por exemplo, deve ser criado um ficheiro de configuração com o nome 91-NetApp-HANA.conf. Para as libertações mais antigas de SLES e RHEL, estes parâmetros devem ser definidos em/etc/sysctl.conf.

Para todos os lançamentos rHEL a partir de RHEL 6.3, lembre-se: 
- O sunrpc.tcp_slot_table_entries = 128 parâmetros deve ser colocado em/etc/modprobe.d/sunrpc-local.conf. Se o ficheiro não existir, tem de o criar primeiro adicionando a entrada: 
    - opções sunrpc tcp_max_slot_table_entries=128

O **quinto passo** é verificar o tempo do sistema da sua unidade HANA Large Instance. As ocorrências são implantadas com um fuso horário do sistema. Este fuso horário representa a localização da região de Azure na qual se encontra o carimbo HANA Large Instance. Pode alterar o tempo ou o fuso horário do sistema das instâncias que possui. 

Se encomendar mais instâncias ao seu inquilino, precisa de adaptar o fuso horário dos casos recém-entregues. A Microsoft não tem informações sobre o fuso horário do sistema que configura com as instâncias após a transferência. Assim, as instâncias recém-implantadas podem não ser definidas no mesmo fuso horário que aquele para o qual mudou. É da sua responsabilidade como cliente adaptar o fuso horário da(s) instância que foram entregues, se necessário. 

O **sexto passo** é verificar etc/anfitriões. À medida que as lâminas são entregues, têm diferentes endereços IP que são atribuídos para diferentes fins. Verifique o ficheiro etc/anfitriões. Quando as unidades são adicionadas a um inquilino existente, não espere ter etc/anfitriões dos sistemas recém-implantados mantidos corretamente com os endereços IP dos sistemas que foram entregues anteriormente. É da sua responsabilidade como cliente garantir que uma instância recém-implantada possa interagir e resolver os nomes das unidades que implantou anteriormente no seu inquilino. 


## <a name="operating-system"></a>Sistema operativo

O espaço de troca da imagem de OS entregue está definido para 2 GB de acordo com a nota de [suporte SAP #1999997 - FAQ: Memória SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Como cliente, se quer um ambiente diferente, deve defini-lo por si mesmo.

[SUSE Linux Enterprise Server 12 SP1 para aplicações SAP](https://www.suse.com/products/sles-for-sap/download/) é a distribuição do Linux que está instalado para SAP HANA em Azure (Grandes Instâncias). Esta distribuição específica fornece capacidades específicas do SAP "fora da caixa" (incluindo parâmetros pré-definidos para executar o SAP em SLES de forma eficaz).

Consulte a [biblioteca de recursos/documentos brancos](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site da SUSE e [sAP sobre SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na Rede Comunitária SAP (SCN) para obter vários recursos úteis relacionados com a implantação do SAP HANA no SLES (incluindo a configuração de alta disponibilidade, endurecimento de segurança específico das operações sap, e muito mais).

Segue-se um SAP adicional e útil em ligações relacionadas com o SUSE:

- [SAP HANA no site SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Boas práticas para SAP: Replicação em fila – SAP NetWeaver na SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – Proteção contra vírus SLES para SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicações SAP)

Seguem-se as notas de suporte SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Nota de suporte SAP #1944799 – Diretrizes SAP HANA para instalação do sistema operativo SLES](http://service.sap.com/sap/support/notes/1944799)
- [Nota de suporte SAP #2205917 – SAP HANA DB recomendadefinições de OS para aplicações SLES 12 para aplicações SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota de suporte SAP #1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte SAP #171356 – Software SAP no Linux: Informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte SAP #1391070 – Soluções Linux UUID](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para executar SAP HANA em HANA Grandes Instâncias. Os lançamentos de RHEL 7.2 e 7.3 estão disponíveis e suportados. 

Seguem-se sAP útil adicional em ligações relacionadas com o Chapéu Vermelho:
- [SAP HANA no site red hat linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Seguem-se as notas de suporte SAP aplicáveis à implementação do SAP HANA na Cartola Vermelha:

- [Nota de suporte SAP #2009879 - Diretrizes SAP HANA para o sistema operativo Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota de suporte SAP #1391070 – Soluções Linux UUID](https://launchpad.support.sap.com/#/notes/1391070)
- [Nota de suporte SAP #2228351 - Linux: SAP HANA Database SPS 11 revisão 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Nota de suporte SAP #2397039 - FAQ: SAP no RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Sincronização de hora

As aplicações SAP que são construídas sobre a arquitetura SAP NetWeaver são sensíveis às diferenças de tempo para os vários componentes que compõem o sistema SAP. As lixeiras curtas SAP ABAP com o título de erro de ZDATE \_ LARGE TIME DIFF são \_ \_ provavelmente familiares. Isso porque estas pequenas lixeiras aparecem quando o tempo do sistema de diferentes servidores ou VMs está a afastar-se demasiado.

Para o SAP HANA on Azure (Grandes Instâncias), a sincronização temporal que é feita em Azure não se aplica às unidades computadas nos selos de Grande Instância. Esta sincronização não é aplicável para executar aplicações SAP em VMs azure nativos, porque Azure garante que o tempo de um sistema é corretamente sincronizado. 

Como resultado, deve configurar um servidor de tempo separado que pode ser utilizado por servidores de aplicações SAP que estão em execução em VMs Azure e pelas instâncias de base de dados SAP HANA que estão em execução em casos de grandes instâncias HANA. A infraestrutura de armazenamento em selos de Grande Instância é sincronizada com servidores NTP.


## <a name="networking"></a>Redes
Assumimos que seguiu as recomendações na conceção das suas redes virtuais Azure e na ligação dessas redes virtuais às Grandes Instâncias HANA, conforme descrito nos seguintes documentos:

- [Visão geral e arquitetura SAP HANA (Grande Instância) em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura e conectividade SAP HANA (Grandes Instâncias) em Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que vale a pena mencionar sobre o networking das unidades individuais. Cada unidade HANA Large Instance vem com dois ou três endereços IP que são atribuídos a duas ou três portas NIC. Três endereços IP são usados em configurações de escala HANA e no cenário de replicação do sistema HANA. Um dos endereços IP atribuídos ao NIC da unidade está fora do conjunto IP do servidor que é descrito na [visão geral do SAP HANA (Grandes Instâncias) e arquitetura em Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Para obter mais informações sobre os detalhes da Ethernet para a sua arquitetura, consulte os [cenários suportados pelo HLI.](hana-supported-scenario.md)

## <a name="storage"></a>Armazenamento

O layout de armazenamento para SAP HANA on Azure (Grandes Instâncias) é configurado pela SAP HANA on Azure através de `service management` diretrizes recomendadas pela SAP. Estas diretrizes estão documentadas no livro branco de [armazenamento SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

Os tamanhos ásperos dos diferentes volumes com as diferentes SKUs de grandes instâncias HANA estão documentados na [visão geral e arquitetura sap HANA (Grandes Instâncias) em Azure.](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

As convenções de nomeação dos volumes de armazenagem estão enumeradas no quadro seguinte:

| Utilização de armazenamento | Nome do monte | Nome de volume | 
| --- | --- | ---|
| Dados hana | /hana/data/SID/mnt0000 \< m> | IP de armazenamento:/hana_data_SID_mnt00001_tenant_vol |
| Diário hana | /hana/log/SID/mnt0000 \< m> | IP de armazenamento:/hana_log_SID_mnt00001_tenant_vol |
| Backup de log HANA | /hana/log/backups | IP de armazenamento:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhado | /hana/shared/SID | IP de armazenamento:/hana_shared_SID_mnt00001_tenant_vol/partilhado |
| usr/seiva | /usr/sap/SID | IP de armazenamento:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* é o ID do sistema de instância HANA. 

*O inquilino* é uma enumeração interna de operações ao implantar um inquilino.

Hana usr/seiva partilham o mesmo volume. A nomenclatura dos pontos de montagem inclui a identificação do sistema das instâncias HANA, bem como o número de montagem. Em destacamentos de escala, há apenas um suporte, como mnt00001. Em destacamentos de escala, por outro lado, vê-se tantos montes como se tem nós operários e mestres. 

Para ambientes de escala, os volumes de dados, registoe e cópia de segurança de registo são partilhados e ligados a cada nó na configuração de escala-out. Para configurações que são múltiplas instâncias SAP, um conjunto diferente de volumes é criado e ligado à unidade HANA Large Instance. Para obter detalhes do layout de armazenamento para o seu cenário, consulte [cenários suportados pelo HLI](hana-supported-scenario.md).

Quando se olha para uma unidade HANA Large Instance, percebe-se que as unidades vêm com um volume de disco generoso para HANA/dados, e que existe um volume HANA/log/backup. A razão pela qual fizemos o HANA/dados tão grandes é que as fotos de armazenamento que lhe oferecemos como cliente estão usando o mesmo volume de disco. Quanto mais instantâneos de armazenamento você executa, mais espaço é consumido por instantâneos nos volumes de armazenamento atribuídos. 

O volume HANA/log/backup não deve ser o volume para cópias de dados. É dimensionado para ser usado como o volume de reserva para as cópias de segurança do registo de transações HANA. Para mais informações, consulte [AF HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Além do armazenamento que é fornecido, pode adquirir capacidade de armazenamento adicional em incrementos de 1-TB. Este armazenamento adicional pode ser adicionado como novos volumes a uma grande instância HANA.

Durante o embarque com o SAP HANA no `service management` Azure, o cliente especifica um ID de utilizador (UID) e id de grupo (GID) para o utilizador sidadm e grupo sapsys (por exemplo: 1000.500). Durante a instalação do sistema SAP HANA, deve utilizar os mesmos valores. Como pretende implementar várias instâncias HANA numa unidade, obtém vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento de implantação é necessário definir:

- O SID das diferentes instâncias HANA (sidadm é derivado dele).
- Os tamanhos de memória dos diferentes casos hana. O tamanho da memória por exemplo define o tamanho dos volumes em cada conjunto de volume individual.

Com base nas recomendações do fornecedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui a bota LUN):

- nfs rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Estes pontos de montagem estão configurados em /etc/fstab, como mostrado nos seguintes gráficos:

![fstab de volumes montados na unidade HANA Large Instance](./media/hana-installation/image1_fstab.PNG)

A saída do comando df-h numa unidade de grande instância S72m HANA parece:

![fstab de volumes montados na unidade HANA Large Instance](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos selos de Grande Instância são sincronizados com servidores NTP. Quando sincronizar as unidades SAP HANA em Azure (Grandes Instâncias) e VMs Azure contra um servidor NTP, não deve haver uma deriva de tempo significativa entre a infraestrutura e as unidades computacionais em selos Azure ou Large Instance.

Para otimizar o SAP HANA ao armazenamento utilizado por baixo, defina os seguintes parâmetros de configuração SAP HANA:

- max_parallel_io_requests 128
- async_read_submit
- async_write_submit_ative
- async_write_submit_blocks todos
 
Para versões SAP HANA 1.0 até SPS12, estes parâmetros podem ser definidos durante a instalação da base de dados SAP HANA, conforme descrito na [nota SAP #2267798 - Configuração da base de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Também pode configurar os parâmetros após a instalação da base de dados SAP HANA utilizando a estrutura hdbparam. 

O armazenamento utilizado em HANA Grandes Instâncias tem uma limitação de tamanho de ficheiro. A limitação do [tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro nos sistemas de ficheiros EXT3, a HANA não está ciente implicitamente da limitação de armazenamento imposta pelo armazenamento HANA Large Instances. Como resultado, a HANA não criará automaticamente um novo ficheiro de dados quando o limite de tamanho de ficheiro de 16TB for atingido. À medida que a HANA tenta aumentar o ficheiro para além de 16 TB, a HANA reportará erros e o servidor de índice sairá no final.

> [!IMPORTANT]
> Para evitar que a HANA tente cultivar ficheiros de dados para além do limite de tamanho de ficheiro de 16 TB do armazenamento HANA Large Instance, precisa de definir os seguintes parâmetros no ficheiro de configuração global SAP HANA.ini
> 
> - datavolume_striping=verdade
> - datavolume_striping_size_gb = 15000
> - Consulte também a nota SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Esteja atento à nota da SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Com o SAP HANA 2.0, a estrutura do hdbparam foi depreciada. Como resultado, os parâmetros devem ser definidos utilizando comandos SQL. Para mais informações, consulte [a nota SAP #2399079: Eliminação do hdbparam em HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para saber mais sobre o layout de armazenamento para a sua arquitetura.


**Passos seguintes**

- Consulte a [instalação HANA no HLI](hana-example-installation.md)










































 







 




