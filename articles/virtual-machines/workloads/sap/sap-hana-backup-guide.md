---
title: Guia de backup para SAP HANA em Máquinas Virtuais Azure  Microsoft Docs
description: Guia de backup para SAP HANA fornece duas grandes possibilidades de backup para SAP HANA em máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 8de83cbb7060e6ca5390720a4a241be71bb9dc92
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617434"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de backup para SAP HANA em Máquinas Virtuais Azure

## <a name="getting-started"></a>Introdução

O guia de backup para sap HANA em execução em Máquinas virtuais Azure só descreverá tópicos específicos de Azure. Para artigos relacionados com a cópia de segurança Geral SAP HANA, consulte a documentação SAP HANA (consulte a documentação de _backup SAP HANA_ mais tarde neste artigo).

O foco deste artigo está em duas grandes possibilidades de backup para sap HANA em máquinas virtuais Azure:

- Backup HANA para o sistema de ficheiros numa Máquina Virtual Azure Linux (ver [Backup SAP HANA Azure no nível de ficheiro)](sap-hana-backup-file-level.md)
- Backup HANA baseado em instantâneos de armazenamento utilizando a função de instantâneo de armazenamento Azure manualmente ou Serviço de Backup Azure (ver [backup SAP HANA com base em instantâneos](sap-hana-backup-storage-snapshots.md)de armazenamento)

O SAP HANA oferece uma API de reserva, que permite que ferramentas de backup de terceiros se integrem diretamente com o SAP HANA. (Isto não está no âmbito deste guia.) Não existe uma integração direta do SAP HANA com o serviço de backup Azure disponível neste momento com base nesta API.

O SAP HANA é oficialmente suportado em vários tipos de VM Azure, como o Azure M-Series. Para obter uma lista completa de VMs Azure certificados sAP HANA, consulte as [Plataformas IaaS Certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)find Certified . Este artigo será atualizado à medida que novas ofertas para sap HANA no Azure ficam disponíveis.

Existe também uma solução híbrida SAP HANA disponível no Azure, onde o SAP HANA funciona não virtualizado em servidores físicos. No entanto, este guia de backup SAP HANA Azure cobre um ambiente azure puro onde o SAP HANA funciona num VM Azure, e não em SAP HANA a funcionar em &quot;grandes instâncias.&quot; Ver [sap HANA (grandes instâncias) visão geral e arquitetura no Azure](hana-overview-architecture.md) para mais informações sobre esta solução de backup em &quot;grandes instâncias&quot; com base em instantâneos de armazenamento.

Informações gerais sobre produtos SAP suportados no Azure podem ser encontradas na [Nota SAP 1928533 .](https://launchpad.support.sap.com/#/notes/1928533)

As três figuras seguintes dão uma visão geral das opções de backup SAP HANA usando as capacidades nativas do Azure atualmente, e também mostram três potenciais cenários de backup futuros. Os artigos relacionados [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) e [backup SAP HANA com base em instantâneos](sap-hana-backup-storage-snapshots.md) de armazenamento descrevem estas opções com mais detalhes, incluindo considerações de tamanho e desempenho para backups SAP HANA que são multiterabytes em tamanho.

![Este número mostra duas possibilidades para salvar o estado vm atual](media/sap-hana-backup-guide/image001.png)

Este valor mostra a possibilidade de salvar o estado VM atual, seja através do serviço de backup Azure ou do instantâneo manual dos discos VM. Com esta abordagem,&#39;não é preciso gerir os backups da SAP HANA. O desafio do cenário de instantâneo do disco é a consistência do sistema de ficheiros e um estado de disco consistente com aplicações. O tópico de consistência é discutido na secção _SAP HANA consistência_ de dados ao tirar fotos de armazenamento mais tarde neste artigo. As capacidades e restrições do serviço de backup Azure relacionados com cópias de segurança SAP HANA também são discutidas mais tarde neste artigo.

![Este valor mostra opções para obter um backup de ficheiroS SAP HANA dentro do VM](media/sap-hana-backup-guide/image002.png)

Esta figura mostra opções para tomar uma cópia de segurança de ficheiroS SAP HANA dentro do VM e, em seguida, armazená-lo ficheiros de backup HANA em outro lugar usando diferentes ferramentas. Tomar uma cópia de segurança HANA requer mais tempo do que uma solução de backup baseada em instantâneos, mas tem vantagens em relação à integridade e consistência. Mais detalhes são fornecidos mais tarde neste artigo.

![Este número mostra um potencial cenário de backup SAP HANA](media/sap-hana-backup-guide/image003.png)

Este número mostra um potencial cenário de apoio sap HANA. Se o SAP HANA permitisse retirar cópias de segurança de uma réplica secundária, adicionaria opções adicionais para estratégias de backup. Atualmente não é possível de acordo com um post no SAP HANA Wiki:

_&quot;é possível ter reforços no lado secundário?_

_Não, atualmente só pode pegar em dados e registar cópias de segurança no lado primário. Se a cópia de segurança automática do registo estiver ativada, após a aquisição para o lado secundário, as cópias de segurança de registo serão automaticamente escritas lá.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para backup HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de backup SAP HANA

- [Introdução à Administração SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando a sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar Backup HANA usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar backups de dados (Cockpit SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- FAQ sobre backup SAP HANA em [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sobre base de dados SAP HANA e fotos de armazenamento em [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de ficheiros de rede inadequados para backup e recuperação em [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Por que sap HANA backup?

O armazenamento azure oferece disponibilidade e fiabilidade fora da caixa (ver [Introdução ao Armazenamento Microsoft Azure](../../../storage/common/storage-introduction.md) para obter mais informações sobre o armazenamento do Azure).

O mínimo para &quot;&quot; de backup é confiar nos SLAs Azure, mantendo os dados SAP HANA e ficheiros de registo em VHDs Azure ligados ao VM do servidor SAP HANA. Esta abordagem abrange falhas vm, mas não danos potenciais nos dados e ficheiros de registo SAP HANA, ou erros lógicos como apagar dados ou ficheiros por acidente. Os backups também são necessários por razões legais ou de conformidade. Em suma, há sempre a necessidade de backups SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a correção da cópia de segurança SAP HANA
Ao utilizar instantâneos de armazenamento, recomenda-se a realização de um teste de restauro num sistema diferente. Esta abordagem fornece uma forma de garantir que um backup está correto, e os processos internos para backup e restauro de trabalho como esperado. Embora este seja um obstáculo significativo no local, é muito mais fácil de realizar na nuvem fornecendo recursos necessários temporariamente para este fim.

Tenha em mente que fazer uma simples restauração e verificar se hana está a funcionar não é suficiente. Idealmente, deve-se fazer uma verificação de consistência da tabela para se certificar de que a base de dados restaurada está boa. O SAP HANA oferece vários tipos de controlos de consistência descritos na [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

As informações sobre a verificação de consistência da tabela também podem ser encontradas no site da SAP em [Verificações](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)de Consistência de Tabela e Catálogo .

Para cópias de segurança de ficheiros padrão, não é necessário um restauro de teste. Existem duas ferramentas SAP HANA que ajudam a verificar que backup pode ser usado para restaurar: hdbbackupdiag e hdbbackupcheck. Consulte [manualmente se uma Recuperação é possível](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obter mais informações sobre estas ferramentas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras de backup HANA versus instantâneo de armazenamento

O SAP não&#39;dá preferência a qualquer cópia de segurança HANA versus instantâneo de armazenamento. Ele lista os seus prós e contras, para que se possa determinar qual usar dependendo da situação e da tecnologia de armazenamento disponível (ver Planeamento Da Sua Estratégia de [Backup e Recuperação).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

No Azure, esteja ciente de que a função&#39;de instantâneo blob Azure não garante a consistência do sistema de ficheiros (ver [Utilizar imagens blob com PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A secção seguinte, _a consistência dos dados do SAP HANA ao tirar fotografias_de armazenamento, discute algumas considerações em relação a esta funcionalidade.

Além disso, é preciso compreender as implicações da faturação quando se trabalha frequentemente com imagens&#39;blob como descrito neste artigo: Compreender como os [Snapshots Acumulam Encargos](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— não é tão óbvio como usar discos virtuais Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados do SAP HANA ao tirar fotos de armazenamento

O sistema de ficheiros e a consistência da aplicação são um problema complexo ao tirar fotografias de armazenamento. A maneira mais fácil de evitar problemas seria desligar o SAP HANA, ou talvez até toda a máquina virtual. Uma paragem pode ser possível com uma demonstração ou protótipo, ou mesmo um sistema de desenvolvimento, mas não é uma opção para um sistema de produção.

No Azure, é preciso ter em mente que a&#39;função de instantâneo blob Azure não garante a consistência do sistema de ficheiros. No entanto, funciona bem utilizando a função de instantâneo SAP HANA, desde que haja apenas um único disco virtual envolvido. Mas mesmo com um único disco, itens adicionais têm de ser verificados. [O SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre cópias de segurança SAP HANA através de instantâneos de armazenamento. Por exemplo, menciona que, com o sistema de ficheiros XFS, é necessário executar **xfs\_congelar** antes de iniciar um instantâneo de armazenamento para garantir a consistência (ver [xfs\_congelar(8) - Página](https://linux.die.net/man/8/xfs_freeze) do homem linux para detalhes sobre **xfs\_congelar).**

O tópico de consistência torna-se ainda mais desafiante num caso em que um único sistema de ficheiros abrange vários discos/volumes. Por exemplo, utilizando mdadm ou LVM e striping. A nota sap mencionada acima indica:

_&quot;Mas tenha em mente que o sistema de armazenamento tem de garantir a consistência do I/S, ao mesmo tempo que cria um instantâneo de armazenamento por volume de dados SAP HANA, ou seja, a visualização instantânea de um volume de dados específico do serviço SAP HANA deve ser uma operação atómica.&quot;_

Assumindo que existe um sistema de ficheiros XFS que abrange quatro discos virtuais Azure, os seguintes passos fornecem uma imagem consistente que representa a área de dados hana:

- HANA snapshot preparar
- Congelar o sistema de ficheiros (por exemplo, utilizar **xfs\_congelar)**
- Crie todos os instantâneos de bolhas necessários no Azure
- Descongelar o sistema de ficheiros
- Confirme o instantâneo da HANA

Recomendação é utilizar o procedimento acima em todos os casos para estar no lado seguro, independentemente do sistema de ficheiros. Ou se for um único disco ou striping, via mdadm ou LVM através de vários discos.

É importante confirmar o instantâneo da HANA. Devido ao &quot;Copy-on-Write,&quot; SAP HANA pode não necessitar de espaço adicional para o disco enquanto estiver neste modo de preparação de instantâneos. Também&#39;não é possível iniciar novos backups até que o instantâneo SAP HANA seja confirmado.

O serviço de backup Azure utiliza extensões VM Azure para cuidar da consistência do sistema de ficheiros. Estas extensões VM não estão disponíveis para uso autónomo. Ainda temos de gerir a consistência do SAP HANA. Consulte o artigo relacionado [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) para obter mais informações.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup SAP HANA

O artigo da SAP HANA que planeia a sua estratégia de [backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) indica um plano básico para fazer backups:

- Instantâneo de armazenamento (diariamente)
- Complete a cópia de segurança de dados utilizando o formato de ficheiro ou bacint (uma vez por semana)
- Backups automáticos de registo

Opcionalmente, pode-se ficar completamente sem instantâneos de armazenamento; podem ser substituídos por backups delta HANA, como cópias de segurança incrementais ou diferenciais (ver [Delta Backups).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)

O guia da Administração HANA fornece uma lista de exemplos. Sugere que se recupere o SAP HANA a um ponto específico no tempo utilizando a seguinte sequência de cópias de segurança:

1. Backup completo de dados
2. Cópia de segurança diferencial
3. Backup incremental 1
4. Backup incremental 2
5. Backups de registo

Quanto a um calendário exato sobre quando e com que frequência um tipo de backup específico deve acontecer, não é possível dar uma orientação geral — é demasiado específica para o cliente, e depende de quantas mudanças de dados ocorrem no sistema. Uma recomendação básica do lado do SAP, que pode ser visto como orientação geral, é fazer um backup completo da HANA uma vez por semana.
No que diz respeito às cópias de segurança de registo, consulte as [cópias](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)de segurança de registo de documentação SAP HANA .

A SAP também recomenda fazer alguma limpeza do catálogo de backup para evitar que cresça infinitamente (ver [Limpeza para Backup Catalog e Backup Storage).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)

### <a name="sap-hana-configuration-files"></a>Ficheiros de configuração SAP HANA

Tal como indicado no FAQ no [SAP Note 1642148,](https://launchpad.support.sap.com/#/notes/1642148)os ficheiros de configuração SAP HANA não fazem parte de uma cópia de segurança padrão da HANA. Não são essenciais para restaurar um sistema. A configuração HANA pode ser alterada manualmente após a restauração. No caso de se querer obter a mesma configuração personalizada durante o processo de restauro, é necessário fazer cópias de tempo dos ficheiros de configuração HANA separadamente.

Se as cópias de segurança padrão da HANA forem para um sistema de ficheiros de backup DEDICADO HANA, também se pode copiar os ficheiros de configuração para o mesmo sistema de ficheiros de reserva e, em seguida, copiar tudo junto para o destino de armazenamento final como armazenamento de bolhas frescas.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

O SAP HANA Cockpit oferece a possibilidade de monitorizar e gerir o SAP HANA através de um browser. Também permite o manuseamento de backups SAP HANA, e por isso pode ser usado como uma alternativa ao SAP HANA Studio e ABAP DBACOCKPIT (ver [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para mais informações).

![Este número mostra o Ecrã de Administração da Base de Dados do Cockpit SAP HANA](media/sap-hana-backup-guide/image004.png)

Este número mostra o Ecrã de Administração da Base de Dados do Cockpit SAP HANA e o azulejo de reserva à esquerda. Ver o azulejo de reserva requer permissões adequadas do utilizador para a conta de login.

![As cópias de segurança podem ser monitorizadas no Cockpit SAP HANA enquanto estão em curso](media/sap-hana-backup-guide/image005.png)

As cópias de segurança podem ser monitorizadas no Cockpit SAP HANA enquanto estão em curso e, uma vez terminados, todos os detalhes de backup estão disponíveis.

![Um exemplo usando firefox em um Azure SLES 12 VM com gnome desktop](media/sap-hana-backup-guide/image006.png)

As imagens anteriores foram feitas a partir de um VM Do Windows Azure. Este é um exemplo usando firefox em um Azure SLES 12 VM com gnome desktop. Mostra a opção de definir os horários de backup da SAP HANA no Cockpit SAP HANA. Como podem os poder ver, sugere data/hora como prefixo para os ficheiros de backup. No Estúdio SAP HANA, o prefixo predefinido é &quot;COMPLETE\_DATA\_BACKUP&quot; ao fazer uma cópia de segurança completa do ficheiro. Recomenda-se a utilização de um prefixo único.

### <a name="sap-hana-backup-encryption"></a>Encriptação de backup SAP HANA

O SAP HANA oferece encriptação de dados e registos. Se os dados e o registo do SAP HANA não estiverem encriptados, as cópias de segurança também não estão encriptadas. Cabe ao cliente utilizar alguma forma de solução de terceiros para encriptar as cópias de segurança SAP HANA. Consulte [a encriptação de dados e volume](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) de registo para saber mais sobre a encriptação SAP HANA.

No Microsoft Azure, um cliente poderia utilizar a funcionalidade de encriptação IaaS VM para encriptar. Por exemplo, pode-se utilizar discos de dados dedicados ligados ao VM, que são usados para armazenar cópias sap HANA e, em seguida, fazer cópias destes discos.

O serviço de backup Azure pode manusear VMs/discos encriptados (ver [como fazer backup e restaurar máquinas virtuais encriptadas com backup Azure](../../../backup/backup-azure-vms-encryption.md)).

Outra opção seria manter o VM SAP HANA e os seus discos sem encriptação, e armazenar os ficheiros de backup SAP HANA numa conta de armazenamento para a qual a encriptação foi ativada (ver Encriptação do Serviço de [Armazenamento Azure para Dados em Repouso).](../../../storage/common/storage-service-encryption.md)

## <a name="test-setup"></a>Configuração do teste

### <a name="test-virtual-machine-on-azure"></a>Teste máquina virtual em Azure

Para realizar os nossos testes, foi utilizada uma instalação SAP HANA num VM Azure GS5 para os seguintes testes de backup/restauro. Os princípios são os mesmos que para os VMs da Série M.

![Este número mostra parte da visão geral do portal Azure para o VM de teste HANA](media/sap-hana-backup-guide/image007.png)

Este número mostra uma parte da visão geral do portal Azure para o VM de teste HANA.

### <a name="test-backup-size"></a>Tamanho de backup de teste

![Este valor foi retirado da consola de reserva no HANA Studio e mostra o tamanho do ficheiro de reserva de 229 GB para o servidor de índice HANA](media/sap-hana-backup-guide/image008.png)

Uma tabela de bonecos foi preenchida com dados para obter um tamanho total de backup de dados superior a 200 GB para obter dados de desempenho realistas. O valor foi retirado da consola de reserva no HANA Studio e mostra o tamanho do ficheiro de reserva de 229 GB para o servidor de índice HANA. Para os testes, foi utilizado o prefixo de reserva padrão "COMPLETE_DATA_BACKUP" no Estúdio SAP HANA. Nos sistemas de produção reais, deve ser definido um prefixo mais útil. SAP HANA Cockpit sugere data/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Ferramenta de teste para copiar ficheiros diretamente para armazenamento Azure

Para transferir ficheiros de backup SAP HANA diretamente para o armazenamento de blob Azure, ou ações de ficheiro STE, a ferramenta blobxfer foi usada porque suporta ambos os alvos e pode ser facilmente integrada em scripts de automação devido à sua interface de linha de comando. A ferramenta blobxfer está disponível no [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Estimativa do tamanho da cópia de segurança de teste

É importante estimar o tamanho de reserva do SAP HANA. Esta estimativa ajuda a melhorar o desempenho definindo o tamanho máximo do ficheiro de backup para uma série de ficheiros de backup, devido ao paralelismo durante uma cópia de ficheiro. (Esses detalhes são explicados mais tarde neste artigo.) Deve-se também decidir se deve fazer uma cópia de segurança completa ou uma cópia de segurança delta (incremental ou diferencial).

Felizmente, existe uma simples declaração SQL que estima o tamanho dos ficheiros de backup: **selecione \* de M\_BACKUP\_TAMANHO\_ESTIMATIVAS** (ver Estimar o espaço necessário no Sistema de [Ficheiros para uma Cópia](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)de Segurança de Dados ).

![A saída desta declaração sQL corresponde quase exatamente ao tamanho real da cópia de segurança completa dos dados no disco](media/sap-hana-backup-guide/image009.png)

Para o sistema de teste, a saída desta declaração SQL corresponde quase exatamente ao tamanho real da cópia de segurança completa dos dados no disco.

### <a name="test-hana-backup-file-size"></a>Tamanho do ficheiro de backup HANA testa

![A consola de backup HANA Studio permite restringir o tamanho máximo de ficheiros de backup HANA](media/sap-hana-backup-guide/image010.png)

A consola de backup HANA Studio permite restringir o tamanho máximo de ficheiros hana. No ambiente da amostra, essa funcionalidade permite obter vários ficheiros de backup menores em vez de um ficheiro de reserva de 230 GB. O tamanho do ficheiro mais pequeno tem um impacto significativo no desempenho (ver o artigo relacionado [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumo

Com base nos resultados dos testes, as tabelas a seguir mostram prós e contras de soluções para apoiar uma base de dados SAP HANA em execução em máquinas virtuais Azure.

**Faça backup sap HANA para o sistema de ficheiros e copie ficheiros de backup depois para o destino final de backup**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Mantenha cópias de segurança HANA em discos VM                      |Sem esforços adicionais de gestão     |Come o espaço de disco VM local           |
|Ferramenta Blobxfer para copiar ficheiros de backup para armazenamento blob |Paralelismo para copiar vários ficheiros, escolha para usar armazenamento de bolhas frescas | Manutenção adicional de ferramentas e scripts personalizados | 
|Cópia blob via Powershell ou CLI                    |Nenhuma ferramenta adicional necessária, pode ser realizada através do Azure Powershell ou CLI |processo manual, o cliente tem que cuidar da scripting e gestão de bolhas copiadas para restaurar|
|Copiar para a parte nFS                                  |Pós-processamento de ficheiros de backup noutros VM sem impacto no servidor HANA|Processo de cópia lenta|
|Cópia blobxfer para serviço de ficheiros Azure                |Não come espaço em discos VM locais|Nenhum suporte de escrita direta por backup HANA, restrição de tamanho da partilha de ficheiros atualmente em 5 TB|
|Azure Backup Agent                                 | Seria a solução preferida         | Atualmente não disponível no Linux    |



**Backup SAP HANA com base em instantâneos de armazenamento**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Serviço de Backup Azure                               | Permite backup VM com base em instantâneos blob | Ao não utilizar a restauração do nível de ficheiro, requer a criação de um novo VM para o processo de restauro, o que implica a necessidade de uma nova chave de licença SAP HANA|
|Instantâneos de bolhas manuais                              | Flexibilidade para criar e restaurar discos VM específicos sem alterar o ID VM único|Todo o trabalho manual, que tem que ser feito pelo cliente|

## <a name="next-steps"></a>Passos seguintes
* [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) descreve a opção de backup baseada em ficheiros.
* A cópia de [segurança SAP HANA com base em instantâneos](sap-hana-backup-storage-snapshots.md) de armazenamento descreve a opção de backup baseada em instantâneos de armazenamento.
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
