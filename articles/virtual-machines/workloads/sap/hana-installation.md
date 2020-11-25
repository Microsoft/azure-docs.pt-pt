---
title: Instalar SAP HANA em SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Como instalar o SAP HANA num SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1430b32c0e74be7a0e50fa4c5c183018b2b55e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006307"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar o SAP HANA (Grandes Instâncias) em Azure

Antes de ler este artigo, conheça os [termos comuns da HANA Large Instances](hana-know-terms.md) e os [SKUs de Grandes Instâncias HANA.](hana-available-skus.md)

A instalação da SAP HANA é da sua responsabilidade. Pode começar a instalar um novo servidor SAP HANA no servidor Azure (Grandes Instâncias) depois de estabelecer a conectividade entre as suas redes virtuais Azure e a unidade HANA Large Instance. 

> [!Note]
> De acordo com a política SAP, a instalação do SAP HANA deve ser realizada por uma pessoa que tenha passado no exame Certificado SAP Technology Associate, no exame de certificação sap HANA Installation, ou que seja um integrador de sistema certificado pela SAP (SI).

Quando estiver a planear instalar o HANA 2.0, consulte a [nota de suporte do SAP #2235581 - SAP HANA: Sistemas operativos suportados](https://launchpad.support.sap.com/#/notes/2235581/E) para garantir que o SISTEMA operativo é suportado com o desbloqueio SAP HANA que está a instalar. O SISTEMA apoiado para HANA 2.0 é mais restritivo do que o SO apoiado para HANA 1.0. Também precisa verificar se a versão osa em que está interessado está listada como suportada pela unidade HLI específica nesta [lista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)publicada . Clique na unidade para obter todos os detalhes com a lista de SO suportada dessa unidade. 

Valide o seguinte antes de iniciar a instalação HANA:
- [Unidades HLI](#validate-the-hana-large-instance-units)
- [Configuração do sistema operativo](#operating-system)
- [Configuração de rede](#networking)
- [Configuração do armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar a unidade HANA Large Instance(s)

Depois de receber a unidade HANA Large Instance da Microsoft, valide as seguintes definições e ajuste-as conforme necessário.

O **primeiro passo** depois de receber a HANA Large Instance e estabelecer acesso e conectividade às instâncias, é verificar no portal Azure se as instâncias(s) estão a aparecer com os SKUs e os SO corretos. Leia [O controlo de Azure HANA Large Instances através do portal Azure](./hana-li-portal.md) para os passos necessários para realizar as verificações.

O **segundo passo** depois de receber a HANA Large Instance e estabelecer acesso e conectividade às instâncias, é registar o SO da instância com o seu fornecedor de SO. Este passo inclui o registo do seu SUSE Linux OS num caso de SUSE SMT que está implantado num VM em Azure. 

A unidade HANA Large Instance pode ligar-se a esta instância SMT. (Para obter mais informações, consulte [como configurar o servidor SMT para SUSE Linux](hana-setup-smt.md)). Em alternativa, o seu Sistema operativo Red Hat precisa de ser registado com o Gestor de Assinaturas red Hat a que precisa de se ligar. Para mais informações, consulte as observações em [What is SAP HANA on Azure (Grandes Instâncias)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json) 

Este passo é necessário para remendar o SO, que é da responsabilidade do cliente. Para a SUSE, encontre a documentação para instalar e configurar sMT nesta página sobre [a instalação SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

O **terceiro passo** é verificar se existem novos patches e correções da versão/versão específica do SISTEMA. Verifique se o nível de correção da HANA Large Instance está no estado mais recente. Pode haver casos em que os últimos patches não estejam incluídos. Depois de assumir uma unidade HANA Large Instance, é obrigatório verificar se os patches precisam de ser aplicados.

O **quarto passo** é verificar as notas SAP relevantes para a instalação e configuração do SAP HANA na versão/versão específica do SISTEMA. Devido à alteração de recomendações ou alterações nas notas ou configurações SAP que dependem de cenários de instalação individuais, a Microsoft nem sempre será capaz de configurar uma unidade HANA Large Instance na perfeição. 

Por isso, é obrigatório que, como cliente, leia as notas SAP relacionadas com a SAP HANA para o seu lançamento exato do Linux. Verifique também as configurações do release/versão SO e aplique as definições de configuração se ainda não o fez.

Especificamente, verifique os seguintes parâmetros e, eventualmente, ajuste-se a:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Começando pelo SLES12 SP1 e PELO RHEL 7.2, estes parâmetros devem ser definidos num ficheiro de configuração no diretório /etc/sysctl.d. Por exemplo, deve ser criado um ficheiro de configuração com o nome 91-NetApp-HANA.conf. Para as versões SLES e RHEL mais antigas, estes parâmetros devem ser definidos em/etc/sysctl.conf.

Para todos os lançamentos RHEL a partir de RHEL 6.3, tenha em mente: 
- O sunrpc.tcp_slot_table_entries = parâmetro 128 deve ser definido em/etc//modprobe.d/sunrpc-local.conf. Se o ficheiro não existir, é necessário criá-lo primeiro adicionando a entrada: 
    - opções sunrpc tcp_max_slot_table_entries=128

O **quinto passo** é verificar o tempo do sistema da sua unidade HANA Large Instance. As ocorrências são implantadas com um fuso horário do sistema. Este fuso horário representa a localização da região de Azure em que está localizado o carimbo HANA Large Instance. Pode alterar o tempo do sistema ou o fuso horário das instâncias que possui. 

Se encomendar mais instâncias para o seu inquilino, precisa de adaptar o fuso horário dos casos recém-entregues. A Microsoft não tem nenhuma visão sobre o fuso horário do sistema que configura com as instâncias após a transferência. Assim, os casos recentemente implementados podem não ser definidos no mesmo fuso horário que aquele para o qual mudou. É sua responsabilidade como cliente adaptar o fuso horário do(s) instância(s) que foram entregues, se necessário. 

O **sexto passo** é verificar etc/anfitriões. À medida que as lâminas são entregues, eles têm diferentes endereços IP que são atribuídos para diferentes propósitos. Verifique o ficheiro etc/anfitriões. Quando as unidades são adicionadas a um inquilino existente, não espere ter etc/anfitriões dos sistemas recém-implantados mantidos corretamente com os endereços IP dos sistemas que foram entregues anteriormente. É sua responsabilidade como cliente garantir que uma instância recém-implantada possa interagir e resolver os nomes das unidades que implementou anteriormente no seu inquilino. 


## <a name="operating-system"></a>Sistema operativo

O espaço de troca da imagem de SO entregue é definido para 2 GB de acordo com a [nota de suporte SAP #1999997 - FAQ: Memória SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Como cliente, se quiser uma definição diferente, deve defini-la por si mesma.

[O SUSE Linux Enterprise Server 12 SP1 para aplicações SAP](https://www.suse.com/products/sles-for-sap/download/) é a distribuição do Linux que está instalada para SAP HANA em Azure (Grandes Instâncias). Esta distribuição específica fornece capacidades específicas do SAP "fora da caixa" (incluindo parâmetros pré-definidos para executar SAP em SLES eficazmente).

Consulte [a biblioteca de recursos/livros brancos](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site da SUSE e SAP na [SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na Rede Comunitária SAP (SCN) para vários recursos úteis relacionados com a implantação do SAP HANA no SLES (incluindo a instalação de alta disponibilidade, endurecimento de segurança específico das operações SAP, e muito mais).

Segue-se o SAP adicional e útil em ligações relacionadas com a SUSE:

- [SAP HANA no site SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Melhores práticas para SAP: Replicação enqueue – SAP NetWeaver na SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – Proteção contra vírus SLES para SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicações SAP)

Seguem-se as notas de suporte SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Nota de suporte SAP #1944799 – Orientações SAP HANA para instalação do sistema operativo SLES](http://service.sap.com/sap/support/notes/1944799)
- [Nota de suporte SAP #2205917 – SAP HANA DB recomendou definições de OS para SLES 12 para aplicações SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota de suporte SAP #1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte SAP #171356 – software SAP em Linux: Informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte SAP #1391070 – Soluções Linux UUID](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para executar SAP HANA em HANA Large Instances. As versões rhel 7.2 e 7.3 estão disponíveis e suportadas. 

Seguem-se um SAP útil adicional em ligações relacionadas com o Chapéu Vermelho:
- [SAP HANA no site Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Seguem-se as notas de suporte SAP aplicáveis à implementação do SAP HANA no Chapéu Vermelho:

- [Nota de suporte SAP #2009879 - Diretrizes SAP HANA para o sistema operativo Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota de suporte SAP #1391070 – Soluções Linux UUID](https://launchpad.support.sap.com/#/notes/1391070)
- [Nota de suporte SAP #2228351 - Linux: SAP HANA Database SPS 11 revisão 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Nota de suporte SAP #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Sincronização de hora

As aplicações SAP que são construídas sobre a arquitetura SAP NetWeaver são sensíveis às diferenças de tempo para os vários componentes que compõem o sistema SAP. As lixeiras curtas SAP ABAP com o título de erro do ZDATE \_ LARGE \_ TIME \_ DIFF são provavelmente familiares. Isto porque estas pequenas lixeiras aparecem quando o tempo do sistema de diferentes servidores ou VMs está a afastar-se demasiado.

Para o SAP HANA on Azure (Grandes Instâncias), a sincronização temporal que é feita em Azure não se aplica às unidades de cálculo nos selos de Grande Instância. Esta sincronização não é aplicável para executar aplicações SAP em VMs nativos Azure, porque Azure garante que o tempo de um sistema é corretamente sincronizado. 

Como resultado, deve configurar um servidor de tempo separado que possa ser utilizado por servidores de aplicações SAP que estão a ser executadas em VMs Azure e pelas instâncias da base de dados SAP HANA que estão a ser executadas em HANA Large Instances. A infraestrutura de armazenamento em selos de Grande Instância é sincronizada com servidores NTP.


## <a name="networking"></a>Rede
Assumimos que seguiu as recomendações na conceção das suas redes virtuais Azure e na ligação dessas redes virtuais às Grandes Instâncias HANA, conforme descrito nos seguintes documentos:

- [Visão geral e arquitetura em Azure](./hana-overview-architecture.md)
- [Infraestrutura e conectividade SAP HANA (Grandes Instâncias) em Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que merecem ser mencionados sobre a ligação em rede das unidades individuais. Cada unidade HANA Large Instance vem com dois ou três endereços IP que são atribuídos a duas ou três portas NIC. Três endereços IP são usados em configurações de escala HANA e no cenário de replicação do sistema HANA. Um dos endereços IP atribuídos ao NIC da unidade está fora do conjunto IP do servidor que é descrito na [visão geral do SAP HANA (Grandes Instâncias) e arquitetura em Azure](./hana-overview-architecture.md).

Para mais informações sobre os detalhes da Ethernet para a sua arquitetura, consulte os [cenários apoiados pelo HLI.](hana-supported-scenario.md)

## <a name="storage"></a>Armazenamento

O layout de armazenamento para SAP HANA em Azure (Grandes Instâncias) é configurado pela SAP HANA on Azure `service management` através das diretrizes recomendadas pela SAP. Estas diretrizes estão documentadas nos [requisitos de armazenamento SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

Os tamanhos ásperos dos diferentes volumes com os diferentes SKUs de grandes instâncias HANA são documentados em [SAP HANA (Grandes Instâncias) visão geral e arquitetura em Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomeação dos volumes de armazenamento constam do quadro seguinte:

| Utilização de armazenamento | Nome do monte | Nome do volume | 
| --- | --- | ---|
| Dados hana | /hana/dados/SID/mnt0000\<m> | IP de armazenamento:/hana_data_SID_mnt00001_tenant_vol |
| Tronco de HANA | /hana/log/SID/mnt0000\<m> | IP de armazenamento:/hana_log_SID_mnt00001_tenant_vol |
| Backup de registo HANA | /hana/log/backups | IP de armazenamento:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhada | /hana/shared/SID | ARMAZENAMENTO IP:/hana_shared_SID_mnt00001_tenant_vol/partilhado |
| usr/seiva | /usr/sap/SID | IP de armazenamento:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* é o ID do sistema de instância HANA. 

*O inquilino* é uma enumeração interna de operações quando se destaca um inquilino.

HANA usr/seiva compartilham o mesmo volume. A nomenclatura dos pontos de montagem inclui o ID do sistema das instâncias HANA, bem como o número de montagem. Em implantações de escala, há apenas um suporte, como mnt00001. Em implantações de escala, por outro lado, vê-se tantos montes como nós operários e mestres. 

Para ambientes de escala, os volumes de backup de dados, registos e registos são partilhados e ligados a cada nó na configuração de escala. Para configurações que são múltiplas instâncias SAP, um conjunto diferente de volumes é criado e ligado à unidade HANA Large Instance. Para obter detalhes do layout de armazenamento para o seu cenário, consulte [cenários apoiados pelo HLI](hana-supported-scenario.md).

Quando olhamos para uma unidade HANA Large Instance, percebemos que as unidades vêm com um volume generoso de disco para HANA/dados, e que existe um volume HANA/log/backup. A razão pela qual fizemos o HANA/dados tão grandes é que as fotos de armazenamento que lhe oferecemos como cliente estão usando o mesmo volume de disco. Quanto mais fotos de armazenamento executa, mais espaço é consumido por instantâneos nos volumes de armazenamento atribuídos. 

O volume HANA/log/backup não é suposto ser o volume para cópias de segurança da base de dados. É dimensionado para ser usado como o volume de backup para as cópias de segurança do registo de transações HANA. Para obter mais informações, consulte [SAP HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Além do armazenamento fornecido, pode adquirir capacidade de armazenamento adicional em incrementos de 1-TB. Este armazenamento adicional pode ser adicionado como novos volumes a uma HANA Large Instance.

Durante o embarque com o SAP HANA em Azure, `service management` o cliente especifica um ID do utilizador (UID) e iD de grupo (GID) para o grupo de utilizadores e sapsys sidadm (por exemplo: 1000.500). Durante a instalação do sistema SAP HANA, deve utilizar estes mesmos valores. Como pretende implantar várias instâncias HANA numa unidade, obtém vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento de implantação é necessário definir:

- O SID das diferentes instâncias HANA (sidadm é derivado dele).
- Os tamanhos de memória dos diferentes casos de HANA. O tamanho da memória por instância define o tamanho dos volumes em cada conjunto de volumes.

Com base nas recomendações do fornecedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui o arranque LUN):

- nfs rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Estes pontos de montagem são configurados em /etc/fstab como mostrado nos seguintes gráficos:

![fstab de volumes montados na unidade HANA Large Instance](./media/hana-installation/image1_fstab.PNG)

A saída do comando df -h numa unidade de Grande Instância S72m HANA parece:

![A screenshot mostra a saída do comando para a unidade HANA Large Instance.](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos selos de Grande Instância são sincronizados com servidores NTP. Quando sincroniza as unidades SAP HANA em Azure (Grandes Instâncias) e VMs Azure contra um servidor NTP, não deverá haver uma deriva significativa entre a infraestrutura e as unidades de cálculo em selos Azure ou Large Instance.

Para otimizar o SAP HANA para o armazenamento utilizado por baixo, desa estale os seguintes parâmetros de configuração SAP HANA:

- max_parallel_io_requests 128
- async_read_submit em
- async_write_submit_ative
- async_write_submit_blocks todos
 
Para as versões SAP HANA 1.0 até SPS12, estes parâmetros podem ser definidos durante a instalação da base de dados SAP HANA, conforme descrito na [nota SAP #2267798 - Configuração da base de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Também pode configurar os parâmetros após a instalação da base de dados SAP HANA utilizando a estrutura hdbparam. 

O armazenamento utilizado em HANA Large Instances tem uma limitação do tamanho do ficheiro. A [limitação do tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro nos sistemas de ficheiros EXT3, a HANA não está consciente implicitamente da limitação de armazenamento imposta pelo armazenamento HANA Large Instances. Como resultado, a HANA não criará automaticamente um novo ficheiro de dados quando o limite de tamanho do ficheiro de 16TB for atingido. À medida que a HANA tenta aumentar o ficheiro para além de 16 TB, a HANA reportará erros e o servidor de índice falhará no final.

> [!IMPORTANT]
> Para evitar que a HANA tente cultivar ficheiros de dados para além do limite de tamanho de ficheiro de 16 TB do armazenamento HANA Large Instance, é necessário definir os seguintes parâmetros no ficheiro de configuração SAP HANA global.ini
> 
> - datavolume_striping=verdade
> - datavolume_striping_size_gb = 15000
> - Consulte também a nota SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Esteja atento à nota SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Com o SAP HANA 2.0, a estrutura hdbparam foi depreciada. Como resultado, os parâmetros devem ser definidos utilizando comandos SQL. Para obter mais informações, consulte [a nota SAP #2399079: Eliminação do hdbparam em HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para saber mais sobre o layout de armazenamento para a sua arquitetura.


**Próximos passos**

- Consulte a [Instalação HANA no HLI](hana-example-installation.md)










































 







 
