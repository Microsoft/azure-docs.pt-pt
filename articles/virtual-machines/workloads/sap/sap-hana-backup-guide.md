---
title: Guia de backup para SAP HANA em máquinas virtuais do Azure | Microsoft Docs
description: O guia de backup do SAP HANA fornece duas possibilidades de backup principais para SAP HANA em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 05a4b8e8034e1c354a4209244694aeb2fc2c6007
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078753"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de segurança para SAP HANA em Máquinas Virtuais do Azure

## <a name="getting-started"></a>Introdução

O guia de backup para SAP HANA em execução em máquinas virtuais do Azure só descreverá os tópicos específicos do Azure. Para ver os itens relacionados ao backup geral SAP HANA, verifique a documentação do SAP HANA (consulte _SAP Hana documentação de backup_ mais adiante neste artigo).

O foco deste artigo é de duas possibilidades de backup principais para SAP HANA em máquinas virtuais do Azure:

- Backup do HANA para o sistema de arquivos em uma máquina virtual Linux do Azure (consulte [SAP Hana backup do Azure no nível do arquivo](sap-hana-backup-file-level.md))
- Backup do HANA baseado em instantâneos de armazenamento usando o recurso de instantâneo do Azure Storage blob manualmente ou o serviço de backup do Azure (consulte [SAP Hana backup com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md))

O SAP HANA oferece uma API de backup, que permite que as ferramentas de backup de terceiros se integrem diretamente com o SAP HANA. (Que não está dentro do escopo deste guia.) Não há nenhuma integração direta do SAP HANA com o serviço de backup do Azure disponível agora com base nessa API.

O SAP HANA é oficialmente suportado em vários tipos de VM do Azure, como a série M do Azure. Para obter uma lista completa de VMs do Azure certificadas SAP HANA, confira [Localizar plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Este artigo será atualizado conforme novas ofertas para SAP HANA no Azure se tornarem disponíveis.

Há também uma solução híbrida SAP HANA disponível no Azure, em que SAP HANA é executado não virtualizado em servidores físicos. No entanto, isso SAP Hana guia de backup do Azure abrange um ambiente puro do Azure em que SAP Hana é executado em uma &quot;VM do Azure, não SAP Hana em execução em instâncias grandes.&quot; Consulte [visão geral e arquitetura do SAP Hana (instâncias grandes) no Azure](hana-overview-architecture.md) para obter mais informações sobre essa &quot;solução de&quot; backup em instâncias grandes com base em instantâneos de armazenamento.

Informações gerais sobre os produtos SAP com suporte no Azure podem ser encontradas na [Observação SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533).

As três figuras a seguir fornecem uma visão geral do SAP HANA opções de backup usando recursos nativos do Azure atualmente, e também mostram três cenários de backup futuros potenciais. Os artigos relacionados [SAP Hana backup do Azure no nível de arquivo](sap-hana-backup-file-level.md) e [SAP Hana backup com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descrevem essas opções com mais detalhes, incluindo considerações de tamanho e desempenho para backups de SAP Hana com vários terabytes de tamanho.

![Esta figura mostra duas possibilidades para salvar o estado atual da VM](media/sap-hana-backup-guide/image001.png)

Esta figura mostra a possibilidade de salvar o estado da VM atual, seja por meio do serviço de backup do Azure ou instantâneo manual dos discos de VM. Com essa abordagem,&#39;não é necessário gerenciar SAP Hana backups. O desafio do cenário de instantâneo de disco é a consistência do sistema de arquivos e um estado de disco consistente com o aplicativo. O tópico de consistência é abordado na seção _SAP Hana consistência de dados ao_ obter instantâneos de armazenamento mais adiante neste artigo. Os recursos e as restrições do serviço de backup do Azure relacionados a backups SAP HANA também são discutidos posteriormente neste artigo.

![Esta figura mostra opções para fazer um backup de arquivo de SAP HANA dentro da VM](media/sap-hana-backup-guide/image002.png)

Esta figura mostra opções para fazer um backup de arquivo SAP HANA dentro da VM e, em seguida, armazenar os arquivos de backup do HANA em outro lugar usando ferramentas diferentes. Fazer um backup do HANA requer mais tempo do que uma solução de backup baseada em instantâneo, mas tem vantagens em relação à integridade e à consistência. Mais detalhes são fornecidos posteriormente neste artigo.

![Esta figura mostra um potencial futuro SAP HANA cenário de backup](media/sap-hana-backup-guide/image003.png)

Esta figura mostra um potencial futuro SAP HANA cenário de backup. Se SAP HANA permitisse fazer backups de um secundário de replicação, ele adicionaria opções adicionais para estratégias de backup. Atualmente, não é possível de acordo com uma postagem no SAP HANA wiki:

_&quot;É possível fazer backups no lado secundário?_

_Não, atualmente você só pode fazer backups de dados e de log no lado principal. Se o backup de log automático estiver habilitado, depois de tomada ao lado secundário, os backups de log serão gravados automaticamente.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para backup do HANA

### <a name="sap-hana-backup-documentation"></a>SAP HANA a documentação de backup

- [Introdução à administração de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar o backup do HANA usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar backups de dados (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Perguntas frequentes sobre o backup de SAP HANA no [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Perguntas frequentes sobre SAP HANA instantâneos de banco de dados e armazenamento no [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de arquivos de rede inadequados para backup e recuperação no [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Por que SAP HANA backup?

O armazenamento do Azure oferece disponibilidade e confiabilidade prontas para uso (consulte [introdução ao armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md) para obter mais informações sobre o armazenamento do Azure).

O mínimo para &quot;backup&quot; é contar com os SLAs do Azure, mantendo os SAP Hana dados e arquivos de log nos VHDs do Azure anexados à VM do SAP Hana Server. Essa abordagem aborda falhas de VM, mas não possíveis danos à SAP HANA arquivos de log e dados, ou erros lógicos, como excluir dados ou arquivos por acidente. Os backups também são necessários por motivos legais ou de conformidade. Em suma, sempre há uma necessidade de backups de SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a exatidão do backup de SAP HANA
Ao usar instantâneos de armazenamento, é recomendável executar uma restauração de teste em um sistema diferente. Essa abordagem fornece uma maneira de garantir que um backup esteja correto e processos internos de backup e restauração funcionam conforme o esperado. Embora esse seja um obstáculo significativo no local, é muito mais fácil realizar na nuvem fornecendo os recursos necessários temporariamente para essa finalidade.

Tenha em mente que fazer uma restauração simples e verificar se o HANA está em execução não é suficiente. O ideal é que seja necessário executar uma verificação de consistência de tabela para ter certeza de que o banco de dados restaurado está bem. O SAP HANA oferece vários tipos de verificações de consistência descritas em [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

As informações sobre a verificação de consistência de tabela também podem ser encontradas no site do SAP em [verificações de consistência de catálogo e tabela](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Para backups de arquivo padrão, uma restauração de teste não é necessária. Há duas ferramentas de SAP HANA que ajudam a verificar qual backup pode ser usado para restauração: hdbbackupdiag e hdbbackupcheck. Consulte [verificando manualmente se uma recuperação é possível](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obter mais informações sobre essas ferramentas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras do backup do HANA versus instantâneo de armazenamento

&#39;O SAP não dá preferência ao backup do Hana versus ao instantâneo de armazenamento. Ele lista seus prós e contras, portanto, é possível determinar qual deles usar dependendo da situação e da tecnologia de armazenamento disponível (consulte [planejando sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

No Azure, esteja ciente do fato de que o recurso&#39;de instantâneo de blob do Azure não garante a consistência do sistema de arquivos (consulte [usando instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A próxima seção, _SAP Hana a consistência de dados ao fazer instantâneos de armazenamento_, discute algumas considerações sobre esse recurso.

Além disso, é importante entender as implicações de cobrança ao trabalhar com frequência com instantâneos de BLOB, conforme descrito neste artigo: [Entender como](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)os instantâneos acumulam encargos&#39;— não é tão óbvio quanto usar discos virtuais do Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA a consistência de dados ao tirar instantâneos de armazenamento

A consistência do aplicativo e do sistema de arquivos é um problema complexo ao tirar instantâneos de armazenamento. A maneira mais fácil de evitar problemas seria desligar SAP HANA, ou talvez até mesmo a máquina virtual inteira. Um desligamento pode ser factível com uma demonstração ou protótipo, ou até mesmo um sistema de desenvolvimento, mas não é uma opção para um sistema de produção.

No Azure, é preciso ter em mente que o recurso&#39;de instantâneo de blob do Azure não garante a consistência do sistema de arquivos. No entanto, ele funciona bem usando o recurso de instantâneo SAP HANA, desde que haja apenas um único disco virtual envolvido. Mas, mesmo com um único disco, os itens adicionais precisam ser verificados. A [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre backups de SAP Hana por meio de instantâneos de armazenamento. Por exemplo, ele menciona que, com o sistema de arquivos XFS, é necessário executar o **xfs\_Freeze** antes de iniciar um instantâneo de armazenamento para garantir a consistência (consulte [xfs\_Freeze (8)-página do manual do Linux](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre **xfscongelar\_** ).

O tópico de consistência se torna ainda mais desafiador em um caso em que um único sistema de arquivos abrange vários discos/volumes. Por exemplo, usando o mdadm ou o LVM e a distribuição. A observação SAP mencionada acima diz:

_&quot;Mas tenha em mente que o sistema de armazenamento precisa garantir a consistência de e/s ao criar um instantâneo de armazenamento por volume de dados SAP HANA, ou seja, o instantâneo de um volume de dados específico do serviço SAP HANA deve ser uma operação atômica.&quot;_

Supondo que haja um sistema de arquivos XFS abrangendo quatro discos virtuais do Azure, as etapas a seguir fornecem um instantâneo consistente que representa a área de dados do HANA:

- Preparação de instantâneo do HANA
- Congelar o sistema de arquivos (por exemplo, **usar\_XFS Freeze**)
- Criar todos os instantâneos de blob necessários no Azure
- Descongele o sistema de arquivos
- Confirmar o instantâneo do HANA

A recomendação é usar o procedimento acima em todos os casos para estar no lado seguro, não importa qual sistema de arquivos. Ou se for um único disco ou distribuição, via mdadm ou LVM em vários discos.

É importante confirmar o instantâneo do HANA. Devido à &quot;cópia em gravação,&quot; SAP Hana pode não exigir espaço em disco adicional no modo de preparação de instantâneo. &#39;Também não é possível iniciar novos backups até que o instantâneo de SAP Hana seja confirmado.

O serviço de backup do Azure usa extensões de VM do Azure para cuidar da consistência do sistema de arquivos. Essas extensões de VM não estão disponíveis para uso autônomo. Uma delas ainda precisa gerenciar SAP HANA consistência. Consulte o artigo relacionado [SAP Hana backup do Azure no nível do arquivo](sap-hana-backup-file-level.md) para obter mais informações.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup SAP HANA

O artigo SAP HANA [planejamento de sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) informa um plano básico para fazer backups:

- Instantâneo de armazenamento (diário)
- Concluir o backup de dados usando o formato de arquivo ou bacint (uma vez por semana)
- Backups de log automáticos

Opcionalmente, uma delas poderia ficar completamente sem instantâneos de armazenamento; Eles podem ser substituídos por backups Delta do HANA, como backups incrementais ou diferenciais (consulte [backups Delta](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

O guia de administração do HANA fornece uma lista de exemplos. Ele sugere que uma recuperação SAP HANA a um ponto específico no tempo usando a seguinte sequência de backups:

1. Backup de dados completo
2. Cópia de segurança diferencial
3. Backup incremental 1
4. Backup incremental 2
5. Backups de log

Em relação a um cronograma exato sobre quando e com que frequência um tipo específico de backup deve ocorrer, não é possível fornecer uma diretriz geral — ele é muito específico do cliente e depende de quantas alterações de dados ocorrem no sistema. Uma recomendação básica do lado do SAP, que pode ser vista como orientação geral, é fazer um backup completo do HANA uma vez por semana.
Em relação aos backups de log, consulte a documentação SAP HANA backups de [log](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

O SAP também recomenda fazer uma manutenção do catálogo de backup para impedir que ele aumente infinitamente (consulte [manutenção para catálogo de backup e armazenamento de backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Arquivos de configuração do SAP HANA

Conforme mencionado nas perguntas frequentes no [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148), os arquivos de configuração SAP Hana não fazem parte de um backup padrão do Hana. Eles não são essenciais para restaurar um sistema. A configuração do HANA pode ser alterada manualmente após a restauração. Caso um queira obter a mesma configuração personalizada durante o processo de restauração, é necessário fazer backup dos arquivos de configuração do HANA separadamente.

Se os backups padrão do HANA forem para um sistema de arquivos de backup dedicado do HANA, também será possível copiar os arquivos de configuração para o mesmo sistema de arquivos de backup e, em seguida, copiar tudo para o destino de armazenamento final, como o armazenamento de BLOBs frio.

### <a name="sap-hana-cockpit"></a>SAP HANA cockpit

O SAP HANA cockpit oferece a possibilidade de monitorar e gerenciar SAP HANA por meio de um navegador. Ele também permite o tratamento de backups de SAP HANA e, portanto, pode ser usado como uma alternativa ao SAP HANA Studio e ABAP DBACOCKPIT (consulte a [ferramenta cockpit do SAP Hana](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para obter mais informações).

![Esta figura mostra a tela de administração do banco de dados SAP HANA cockpit](media/sap-hana-backup-guide/image004.png)

Esta figura mostra a tela de administração do banco de dados SAP HANA cockpit e o bloco backup à esquerda. Ver o bloco de backup requer permissões de usuário apropriadas para a conta de logon.

![Os backups podem ser monitorados na SAP HANA cockpit enquanto estão em andamento](media/sap-hana-backup-guide/image005.png)

Os backups podem ser monitorados na SAP HANA cockpit enquanto estão em andamento e, após a conclusão, todos os detalhes de backup estão disponíveis.

![Um exemplo que usa o Firefox em uma VM SLES 12 do Azure com área de trabalho GNOME](media/sap-hana-backup-guide/image006.png)

As capturas de tela anteriores foram feitas de uma VM do Windows do Azure. Este é um exemplo usando o Firefox em uma VM SLES 12 do Azure com área de trabalho GNOME. Ele mostra a opção de definir SAP HANA agendas de backup no SAP HANA cockpit. Como também é possível ver, ele sugere data/hora como um prefixo para os arquivos de backup. No SAP Hana Studio, o prefixo padrão é &quot;backup\_&quot; de\_dados completo ao fazer um backup de arquivo completo. É recomendável usar um prefixo exclusivo.

### <a name="sap-hana-backup-encryption"></a>SAP HANA a criptografia de backup

O SAP HANA oferece criptografia de dados e log. Se SAP HANA dados e o log não forem criptografados, os backups também não serão criptografados. Cabe ao cliente usar alguma forma de solução de terceiros para criptografar os backups de SAP HANA. Consulte [criptografia de volume de dados e log](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) para saber mais sobre a criptografia de SAP Hana.

Em Microsoft Azure, um cliente poderia usar o recurso de criptografia de VM IaaS para criptografar. Por exemplo, um pode usar discos de dados dedicados anexados à VM, que são usados para armazenar SAP HANA backups e, em seguida, fazer cópias desses discos.

O serviço de backup do Azure pode lidar com VMs/discos criptografados (consulte [como fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](../../../backup/backup-azure-vms-encryption.md)).

Outra opção seria manter a VM SAP HANA e seus discos sem criptografia e armazenar os SAP HANA arquivos de backup em uma conta de armazenamento para a qual a criptografia foi habilitada (consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Configuração de teste

### <a name="test-virtual-machine-on-azure"></a>Testar máquina virtual no Azure

Para executar nossos testes, uma instalação SAP HANA em uma VM GS5 do Azure foi usada para os seguintes testes de backup/restauração. Os princípios são os mesmos para as VMs da série M.

![Esta figura mostra parte do portal do Azure visão geral da VM de teste do HANA](media/sap-hana-backup-guide/image007.png)

Esta figura mostra parte do portal do Azure visão geral da VM de teste do HANA.

### <a name="test-backup-size"></a>Tamanho do backup de teste

![Essa figura foi obtida do console de backup no HANA Studio e mostra o tamanho do arquivo de backup de 229 GB para o servidor de índice do HANA](media/sap-hana-backup-guide/image008.png)

Uma tabela fictícia foi preenchida com dados para obter um tamanho de backup de dados total de mais de 200 GB para derivar dados de desempenho realistas. A figura foi tirada do console de backup no HANA Studio e mostra o tamanho do arquivo de backup de 229 GB para o servidor de índice do HANA. Para os testes, o prefixo de backup padrão "COMPLETE_DATA_BACKUP" no SAP HANA Studio foi usado. Em sistemas de produção reais, um prefixo mais útil deve ser definido. SAP HANA cockpit sugere data/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Ferramenta de teste para copiar arquivos diretamente no armazenamento do Azure

Para transferir SAP HANA arquivos de backup diretamente para o armazenamento de BLOBs do Azure ou compartilhamentos de arquivos do Azure, a ferramenta blobxfer foi usada porque dá suporte a ambos os destinos e pode ser facilmente integrada a scripts de automação devido à sua interface de linha de comando. A ferramenta blobxfer está disponível no [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Estimativa do tamanho do backup de teste

É importante estimar o tamanho do backup de SAP HANA. Essa estimativa ajuda a melhorar o desempenho definindo o tamanho máximo do arquivo de backup para vários arquivos de backup, devido ao paralelismo durante uma cópia de arquivo. (Esses detalhes são explicados posteriormente neste artigo.) Também é necessário decidir se um backup completo ou um backup Delta (incremental ou diferencial) devem ser efetuados.

Felizmente, há uma instrução SQL simples que estima o tamanho dos arquivos de backup: **selecione \* de M\_estimativas\_de\_tamanho de backup** (consulte [estimar o espaço necessário no sistema de arquivos para obter dados) Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![A saída dessa instrução SQL corresponde quase exatamente ao tamanho real do backup de dados completo em disco](media/sap-hana-backup-guide/image009.png)

Para o sistema de teste, a saída dessa instrução SQL corresponde quase exatamente ao tamanho real do backup de dados completo em disco.

### <a name="test-hana-backup-file-size"></a>Testar o tamanho do arquivo de backup do HANA

![O console de backup do HANA Studio permite restringir o tamanho máximo do arquivo de arquivos de backup do HANA](media/sap-hana-backup-guide/image010.png)

O console de backup do HANA Studio permite restringir o tamanho máximo do arquivo de arquivos de backup do HANA. No ambiente de exemplo, esse recurso possibilita obter vários arquivos de backup menores em vez do arquivo de backup de 1 230 GB. O tamanho de arquivo menor tem um impacto significativo no desempenho (consulte o artigo relacionado [SAP Hana backup do Azure no nível do arquivo](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumo

Com base nos resultados de teste, as tabelas a seguir mostram os prós e os contras de soluções para fazer backup de um banco de dados SAP HANA em execução em máquinas virtuais do Azure.

**Fazer backup de SAP HANA no sistema de arquivos e copiar arquivos de backup posteriormente para o destino de backup final**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Manter backups do HANA em discos de VM                      |Nenhum esforço de gerenciamento adicional     |Consome o espaço em disco da VM local           |
|Ferramenta Blobxfer para copiar arquivos de backup para o armazenamento de BLOBs |Paralelismo para copiar vários arquivos, escolher para usar o armazenamento de BLOBs frio | Manutenção adicional de ferramentas e script personalizado | 
|Cópia de blob por meio do PowerShell ou da CLI                    |Nenhuma ferramenta adicional necessária, pode ser realizada por meio do Azure PowerShell ou da CLI |processo manual, o cliente precisa cuidar do script e do gerenciamento de BLOBs copiados para restauração|
|Copiar para o compartilhamento NFS                                  |Pós-processamento de arquivos de backup em outra VM sem impacto no servidor HANA|Processo de cópia lenta|
|Cópia Blobxfer para o serviço de arquivos do Azure                |Não tem espaço suficiente em discos de VM locais|Não há suporte direto para gravação pelo backup do HANA, restrição de tamanho do compartilhamento de arquivos atualmente em 5 TB|
|Agente de backup do Azure                                 | Seria uma solução preferida         | Atualmente não disponível no Linux    |



**SAP HANA de backup com base em instantâneos de armazenamento**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Serviço de backup do Azure                               | Permite o backup de VM com base em instantâneos de BLOB | Quando não estiver usando a restauração em nível de arquivo, ela exigirá a criação de uma nova VM para o processo de restauração, que, em seguida, implica a necessidade de uma nova chave de licença de SAP HANA|
|Instantâneos de blob manual                              | Flexibilidade para criar e restaurar discos de VM específicos sem alterar a ID de VM exclusiva|Todo o trabalho manual, que deve ser feito pelo cliente|

## <a name="next-steps"></a>Passos Seguintes
* [SAP Hana backup do Azure no nível do arquivo](sap-hana-backup-file-level.md) descreve a opção de backup baseado em arquivo.
* [SAP Hana backup com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de backup baseado em instantâneo de armazenamento.
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
