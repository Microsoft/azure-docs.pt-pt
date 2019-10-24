---
title: Instalar SAP HANA em SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como instalar SAP HANA em um SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 516f61775060b3e4073ed9d623545d4f227563ed
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750350"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar SAP HANA (instâncias grandes) no Azure

Antes de ler este artigo, familiarize-se com os [termos comuns do Hana em instâncias grandes](hana-know-terms.md) e as [SKUs de instâncias grandes do Hana](hana-available-skus.md).

A instalação do SAP HANA é sua responsabilidade. Você pode iniciar a instalação de um novo SAP HANA no servidor do Azure (instâncias grandes) depois de estabelecer a conectividade entre as redes virtuais do Azure e as unidades de instância grande do HANA. 

> [!Note]
> Por política do SAP, a instalação do SAP HANA deve ser executada por uma pessoa que passou pelo exame de associação de tecnologia SAP certificado, pelo exame de certificação de instalação SAP HANA ou por quem é um integrador de sistemas certificado pelo SAP (SI).

Quando estiver planejando instalar o HANA 2,0, confira [Nota de suporte SAP #2235581-SAP Hana: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E) para garantir que o sistema operacional tenha suporte com o SAP Hana versão que você está instalando. O sistema operacional com suporte para HANA 2,0 é mais restritivo do que o sistema operacional com suporte para HANA 1,0. 

> [!IMPORTANT] 
> Para as unidades do tipo II, atualmente há suporte apenas para a versão do sistema operacional SLES 12 SP2. 

Valide o seguinte antes de começar a instalação do HANA:
- [Unidades de HLI](#validate-the-hana-large-instance-units)
- [Configuração do sistema operacional](#operating-system)
- [Configuração da rede](#networking)
- [Configuração do armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar as unidades de instância grande do HANA

Depois de receber a unidade de instância grande do HANA da Microsoft, valide as seguintes configurações e ajuste conforme necessário.

A **primeira etapa** depois de receber a instância grande do Hana e estabelecer o acesso e a conectividade com as instâncias é fazer check-in portal do Azure se as instâncias estão aparecendo com as SKUs e o sistema operacional corretos. Leia [controle de instâncias grandes do Azure Hana por meio de portal do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) para as etapas necessárias para executar as verificações.

A **segunda etapa** depois que você recebe a instância grande do Hana e estabelece acesso e conectividade com as instâncias é registrar o sistema operacional da instância com seu provedor de sistema operacional. Esta etapa inclui o registro do sistema operacional SUSE Linux em uma instância do SUSE SMT que é implantada em uma VM no Azure. 

A unidade de instância grande do HANA pode se conectar a essa instância do SMT. (Para obter mais informações, consulte [como configurar o servidor SMT para SuSE Linux](hana-setup-smt.md)). Como alternativa, seu sistema operacional Red Hat precisa ser registrado com o Gerenciador de assinaturas do Red Hat ao qual você precisa se conectar. Para obter mais informações, consulte os comentários em [o que é SAP Hana no Azure (instâncias grandes)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Essa etapa é necessária para aplicar patches no sistema operacional, que é responsabilidade do cliente. Para o SUSE, localize a documentação para instalar e configurar o SMT nesta página sobre a [instalação do SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **terceira etapa** é verificar se há novos patches e correções do lançamento/versão do sistema operacional específico. Verifique se o nível de patch da instância grande do HANA está no estado mais recente. Pode haver casos em que os patches mais recentes não estão incluídos. Depois de assumir uma unidade de instância grande do HANA, é obrigatório verificar se os patches precisam ser aplicados.

A **quarta etapa** é conferir as notas SAP relevantes para instalar e configurar SAP Hana na versão/liberação específica do sistema operacional. Devido à alteração de recomendações ou alterações nas notas SAP ou configurações que dependem de cenários de instalação individuais, a Microsoft nem sempre conseguirá configurar uma unidade de instância grande do HANA perfeitamente. 

Portanto, é obrigatório para você como um cliente ler as notas SAP relacionadas a SAP HANA para sua versão exata do Linux. Verifique também as configurações do versão/versão do sistema operacional e aplique as definições de configuração, caso ainda não tenha feito isso.

Especificamente, verifique os seguintes parâmetros e, eventualmente, ajuste para:

- NET. Core. rmem_max = 16777216
- NET. Core. wmem_max = 16777216
- NET. Core. rmem_default = 16777216
- NET. Core. wmem_default = 16777216
- NET. Core. optmem_max = 16777216
- NET. IPv4. TCP _rmem = 65536 16777216 16777216
- NET. IPv4. TCP _wmem = 65536 16777216 16777216

A partir do SLES12 SP1 e RHEL 7,2, esses parâmetros devem ser definidos em um arquivo de configuração no diretório/etc/sysctl.d Por exemplo, um arquivo de configuração com o nome 91-NetApp-HANA. conf deve ser criado. Para versões mais antigas do SLES e RHEL, esses parâmetros devem ser definidos em/etc/sysctl. conf.

Para todas as versões de RHEL a partir do RHEL 6,3, tenha em mente: 
- O parâmetro SunRPC. TCP _slot_table_entries = 128 deve ser definido em/etc/modprobe. d/SunRPC-local. conf. Se o arquivo não existir, você precisará criá-lo primeiro adicionando a entrada: 
    - opções SunRPC tcp_max_slot_table_entries = 128

A **quinta etapa** é verificar a hora do sistema de sua unidade de instância grande do Hana. As instâncias são implantadas com um fuso horário do sistema. Esse fuso horário representa o local da região do Azure no qual o carimbo de instância grande do HANA está localizado. Você pode alterar a hora do sistema ou o fuso horário das instâncias que você possui. 

Se você solicitar mais instâncias em seu locatário, precisará adaptar o fuso horário das instâncias entregues recentemente. A Microsoft não se aprofunda no fuso horário do sistema que você configurou com as instâncias após o transferência. Assim, as instâncias implantadas recentemente podem não ser definidas no mesmo fuso horário que foi alterado para. É sua responsabilidade que o cliente adapte o fuso horário das instâncias que foram passadas, se necessário. 

A **sexta etapa** é verificar etc/hosts. À medida que as lâminas são passadas, elas têm endereços IP diferentes que são atribuídos para finalidades diferentes. Verifique o arquivo etc/hosts. Quando as unidades são adicionadas a um locatário existente, não espere etc/hosts dos sistemas implantados recentemente mantidos corretamente com os endereços IP dos sistemas que foram entregues anteriormente. É sua responsabilidade como cliente garantir que uma instância implantada recentemente possa interagir e resolver os nomes das unidades que você implantou anteriormente em seu locatário. 


## <a name="operating-system"></a>Sistema operativo

> [!IMPORTANT] 
> Para unidades do tipo II, atualmente há suporte apenas para a versão do sistema operacional SLES 12 SP2. 

O espaço de permuta da imagem do sistema operacional entregue é definido como 2 GB, de acordo com a [Nota de suporte do SAP #1999997-FAQ: SAP Hana memória](https://launchpad.support.sap.com/#/notes/1999997/E). Como um cliente, se você quiser uma configuração diferente, deverá defini-la por conta própria.

O [SuSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/download/) é a distribuição do Linux instalada para SAP Hana no Azure (instâncias grandes). Essa distribuição específica fornece recursos específicos do SAP "prontos" (incluindo parâmetros predefinidos para executar o SAP no SLES com eficiência).

Consulte [biblioteca de recursos/White papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na rede de comunidade do SAP (SCN) para vários recursos úteis relacionados à implantação de SAP Hana no SLES (incluindo a configuração de alta disponibilidade, proteção de segurança que é específicas para operações SAP e muito mais).

Veja a seguir o SAP adicional e útil para links relacionados ao SUSE:

- [SAP HANA no site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – proteção contra vírus SLES para SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP)

Veja a seguir as notas de suporte da SAP que são aplicáveis à implementação de SAP HANA no SLES 12:

- [Nota de suporte da SAP #1944799 – diretrizes de SAP HANA para instalação do sistema operacional SLES](http://service.sap.com/sap/support/notes/1944799)
- [Nota de suporte da SAP #2205917 – as configurações de so recomendadas do SAP HANA DB para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota de suporte da SAP #1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte da SAP #171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte da SAP #1391070 – soluções de UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para SAP Hana](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para executar SAP Hana em instâncias grandes do Hana. As versões do RHEL 6,7 e 7,2 estão disponíveis. Observe que, ao contrário das VMs nativas do Azure em que apenas RHEL 7,2 e versões mais recentes têm suporte, as instâncias grandes do HANA oferecem suporte a RHEL 6,7 também. No entanto, é recomendável usar uma versão RHEL 7. x.

A seguir estão os links adicionais do SAP no Red Hat relacionados:
- [SAP Hana no site do Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Veja a seguir as notas de suporte do SAP que são aplicáveis à implementação de SAP HANA no Red Hat:

- [Nota de suporte da SAP #2009879 diretrizes de SAP HANA para o sistema operacional Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Nota de suporte da SAP #2292690-SAP HANA DB: configurações de so recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota de suporte da SAP #2247020-SAP HANA DB: configurações de so recomendadas para RHEL 6,7](https://launchpad.support.sap.com/#/notes/2247020)
- [Nota de suporte da SAP #1391070 – soluções de UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Nota de suporte da SAP #2228351-Linux: SAP HANA SPS de banco de dados 11 revisão 110 (ou superior) no RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Nota de suporte da SAP #2397039-perguntas frequentes: SAP no RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Nota de suporte SAP #1496410-Red Hat Enterprise Linux 6. x: instalação e atualização](https://launchpad.support.sap.com/#/notes/1496410)
- [Nota de suporte SAP #2002167-Red Hat Enterprise Linux 7. x: instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Sincronização de horário

Os aplicativos SAP criados na arquitetura do SAP NetWeaver são sensíveis a diferenças de tempo para os vários componentes que compõem o sistema SAP. Os despejos curtos do SAP ABAP com o título do erro de ZDATE Large \_LARGE \_TIME \_DIFF provavelmente são familiares. Isso ocorre porque esses despejos curtos aparecem quando a hora do sistema de diferentes servidores ou VMs está se afastando muito.

Para SAP HANA no Azure (instâncias grandes), a sincronização de tempo feita no Azure não se aplica às unidades de computação nos carimbos de instância grande. Essa sincronização não é aplicável para a execução de aplicativos SAP em VMs nativas do Azure, pois o Azure garante que a hora do sistema seja sincronizada corretamente. 

Como resultado, você deve configurar um servidor de horário separado que pode ser usado por servidores de aplicativos SAP em execução em VMs do Azure e pelo SAP HANA instâncias de banco de dados que estão sendo executadas em instâncias grandes do HANA. A infraestrutura de armazenamento em carimbos de instância grande é sincronizada por tempo com servidores NTP.


## <a name="networking"></a>Funcionamento em Rede
Supomos que você seguiu as recomendações em projetando suas redes virtuais do Azure e conectando essas redes virtuais às instâncias grandes HANA, conforme descrito nos seguintes documentos:

- [Visão geral e arquitetura do SAP HANA (instância grande) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que vale a pena mencionar sobre a rede das unidades individuais. Cada unidade de instância grande do HANA vem com dois ou três endereços IP que são atribuídos a duas ou três portas NIC. Três endereços IP são usados em configurações de expansão do HANA e no cenário de replicação de sistema do HANA. Um dos endereços IP atribuídos à NIC da unidade está fora do pool de IPS do servidor descrito em [visão geral do SAP Hana (instâncias grandes) e arquitetura no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Para obter mais informações sobre os detalhes de Ethernet para sua arquitetura, consulte os [cenários com suporte](hana-supported-scenario.md)para o HLI.

## <a name="storage"></a>Armazenamento

O layout de armazenamento para SAP HANA no Azure (instâncias grandes) é configurado por SAP HANA no Azure `service management` por meio das diretrizes recomendadas do SAP. Essas diretrizes estão documentadas na white paper de [requisitos de armazenamento SAP Hana](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) . 

Os tamanhos aproximados dos diferentes volumes com os diferentes SKUs do HANA em instâncias grandes estão documentados em [visão geral e arquitetura do SAP Hana (instâncias grandes) no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomenclatura dos volumes de armazenamento estão listadas na tabela a seguir:

| Uso do armazenamento | Nome da montagem | Nome do volume | 
| --- | --- | ---|
| Dados do HANA | /hana/data/SID/mnt0000 \<m > | IP de armazenamento:/hana_data_SID_mnt00001_tenant_vol |
| Log do HANA | /hana/log/SID/mnt0000 \<m > | IP de armazenamento:/hana_log_SID_mnt00001_tenant_vol |
| Backup de log do HANA | /hana/log/backups | IP de armazenamento:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhado | /hana/shared/SID | IP de armazenamento:/hana_shared_SID_mnt00001_tenant_vol/compartilhado |
| usr/SAP | /usr/sap/SID | IP de armazenamento:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Sid* é a ID do sistema de instância do Hana. 

O *locatário* é uma enumeração interna de operações ao implantar um locatário.

HANA usr/SAP compartilham o mesmo volume. O nomenclatura da montagem inclui a ID do sistema das instâncias do HANA, bem como o número de montagem. Em implantações de expansão, há apenas uma montagem, como mnt00001. Em implantações em expansão, por outro lado, você vê tantas montagens quanto tem nós de trabalho e mestres. 

Para ambientes de expansão, os volumes de dados, de log e de backup de log são compartilhados e anexados a cada nó na configuração de expansão. Para configurações que são várias instâncias SAP, um conjunto diferente de volumes é criado e anexado à unidade de instância grande do HANA. Para obter detalhes de layout de armazenamento para seu cenário, consulte [cenários com suporte para HLI](hana-supported-scenario.md).

Ao examinar uma unidade de instância grande do HANA, você percebe que as unidades vêm com um volume de disco generosa para HANA/data e que há um volume HANA/log/backup. O motivo pelo qual fizemos o HANA/dados tão grande é que os instantâneos de armazenamento que oferecemos a você como cliente estão usando o mesmo volume de disco. Quanto mais instantâneos de armazenamento você executar, mais espaço será consumido por instantâneos em seus volumes de armazenamento atribuídos. 

O volume HANA/log/backup não deve ser o volume de backups de banco de dados. Ele é dimensionado para ser usado como o volume de backup para os backups de log de transações do HANA. Para obter mais informações, consulte a [alta disponibilidade e a recuperação de desastres do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Além do armazenamento fornecido, você pode adquirir capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser adicionado como novos volumes a uma instância grande do HANA.

Durante a integração com SAP HANA no Azure `service management`, o cliente especifica uma ID de usuário (UID) e ID de grupo (GID) para o usuário SIDADM e o grupo SAPs (por exemplo: 1.000.500). Durante a instalação do sistema de SAP HANA, você deve usar esses mesmos valores. Como você deseja implantar várias instâncias do HANA em uma unidade, você obtém vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implantação, você precisa definir:

- O SID das diferentes instâncias do HANA (SIDADM é derivado dela).
- Os tamanhos de memória das diferentes instâncias do HANA. O tamanho da memória por instância define o tamanho dos volumes em cada conjunto de volumes individual.

Com base nas recomendações do provedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui o LUN de inicialização):

- NFS RW, versa = 4, Hard, Timese = 600, rsize = 1048576, wSize = 1048576, intr, noatime, Lock 0 0

Esses pontos de montagem são configurados em/etc/fstab, conforme mostrado nos gráficos a seguir:

![fstab de volumes montados na unidade de instância grande do HANA](./media/hana-installation/image1_fstab.PNG)

A saída do comando df-h em uma unidade de instância grande do S72m HANA é semelhante a:

![fstab de volumes montados na unidade de instância grande do HANA](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos carimbos de instância grande são sincronizados com os servidores NTP. Quando você sincroniza o SAP HANA nas unidades do Azure (instâncias grandes) e nas VMs do Azure em um servidor NTP, não deve haver descompasso de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou em carimbos de instância grande.

Para otimizar SAP HANA para o armazenamento usado abaixo, defina os seguintes parâmetros de configuração de SAP HANA:

- max_parallel_io_requests 128
- async_read_submit em
- async_write_submit_active em
- async_write_submit_blocks tudo
 
Para as versões do SAP HANA 1,0 até SPS12, esses parâmetros podem ser definidos durante a instalação do banco de dados do SAP HANA, conforme descrito em [SAP observação #2267798 configuração do banco de dados SAP Hana](https://launchpad.support.sap.com/#/notes/2267798).

Você também pode configurar os parâmetros após a instalação do banco de dados do SAP HANA usando a estrutura hdbparam. 

O armazenamento usado no HANA em instâncias grandes tem uma limitação de tamanho de arquivo. A [limitação de tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por arquivo. Ao contrário das limitações de tamanho de arquivo nos sistemas de arquivos EXT3, o HANA não reconhece implicitamente a limitação de armazenamento imposta pelo armazenamento do SAP HANA em instâncias grandes. Como resultado, o HANA não criará automaticamente um novo arquivo de dados quando o limite de tamanho de arquivo de 16TB for atingido. Como o HANA tenta aumentar o arquivo para além de 16 TB, o HANA relatará erros e o servidor de índice falhará no final.

> [!IMPORTANT]
> Para impedir que o HANA tente aumentar os arquivos de dados além do limite de tamanho de arquivo de 16 TB do armazenamento de instância grande do HANA, você precisa definir os seguintes parâmetros no arquivo de configuração global. ini SAP HANA
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Consulte também SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Lembre-se do SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Com o SAP HANA 2,0, a estrutura hdbparam foi preterida. Como resultado, os parâmetros devem ser definidos usando comandos SQL. Para obter mais informações, consulte [SAP note #2399079: eliminação de hdbparam no Hana 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte [cenários compatíveis](hana-supported-scenario.md) com o HLI para saber mais sobre o layout de armazenamento para sua arquitetura.


**Passos seguintes?**

- Consulte a [instalação do Hana em HLI](hana-example-installation.md)










































 







 




