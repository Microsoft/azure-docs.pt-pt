---
title: Guia de segurança para SAP HANA em máquinas de virtuais do Azure | Documentos da Microsoft
description: Guia de cópia de segurança para SAP HANA fornece duas principais possibilidades de cópia de segurança para SAP HANA em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013338"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de segurança para SAP HANA em Máquinas Virtuais do Azure

## <a name="getting-started"></a>Introdução

O guia de cópia de segurança para SAP HANA em execução em máquinas virtuais do Azure apenas descreverá tópicos específicos do Azure. Para gerais SAP HANA cópia de segurança itens relacionados, consulte a documentação do SAP HANA (consulte _documentação de cópia de segurança do SAP HANA_ mais adiante neste artigo).

O foco deste artigo é duas principais cópia de segurança possibilidades para o SAP HANA em máquinas virtuais do Azure:

- Cópia de segurança do HANA para o sistema de arquivos numa máquina de Virtual do Linux do Azure (veja [SAP HANA Azure Backup no nível de arquivo](sap-hana-backup-file-level.md))
- Cópia de segurança do HANA com base nos instantâneos de armazenamento através da funcionalidade de instantâneo de blob de armazenamento do Azure manualmente ou o serviço de cópia de segurança do Azure (veja [cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md))

SAP HANA oferece uma API, que permite a ferramentas de cópia de segurança de terceiros integrar diretamente com o SAP HANA de cópia de segurança. (Que não está dentro do escopo deste guia.) Não há nenhuma integração direta do SAP HANA com direito disponível agora baseado nessa API de serviço de cópia de segurança do Azure.

SAP HANA é oficialmente suportado em vários tipos de VM do Azure, como o Azure série M. Para uma lista completa de SAP HANA certificadas VMs do Azure, consulte [encontrar plataformas IaaS Certified](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Este artigo será atualizado como novas ofertas de SAP HANA no Azure se tornarem disponível.

Também existe uma solução híbrida do SAP HANA disponível no Azure, onde SAP HANA é executada não virtualizados em servidores físicos. No entanto, este guia de cópia de segurança do Azure do SAP HANA abrange um ambiente do Azure puro, onde o SAP HANA é executada numa VM do Azure, não SAP HANA em execução no &quot;instâncias grandes.&quot; Ver [descrição geral do SAP HANA (instâncias grandes) e a arquitetura no Azure](hana-overview-architecture.md) para obter mais informações sobre esta solução de cópia de segurança no &quot;instâncias grandes&quot; com base nos instantâneos de armazenamento.

Informações gerais sobre produtos SAP suportados no Azure podem ser encontradas na [SAP 1928533 de nota](https://launchpad.support.sap.com/#/notes/1928533).

As figuras de três seguintes uma visão geral das opções de cópia de segurança do SAP HANA atualmente a utilizar as capacidades do Azure nativas e também mostram três cenários de cópia de segurança futuros possíveis. Os artigos relacionados [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) e [cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descrevem estas opções em mais detalhes, incluindo as considerações sobre o tamanho e o desempenho de SAP HANA cópias de segurança que são vários terabytes de tamanho.

![Esta figura mostra duas possibilidades para guardar o estado atual do VM](media/sap-hana-backup-guide/image001.png)

Esta figura mostra a possibilidade de salvar o estado atual do VM, por meio de serviço de cópia de segurança do Azure ou instantâneo manual de discos VM. Com esta abordagem, uma&#39;tem de gerir cópias de segurança do SAP HANA. O desafio do cenário de instantâneo do disco é a consistência do sistema de ficheiros e um Estado de disco com consistência de aplicações. O tópico de consistência é abordado na seção _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_ mais adiante neste artigo. Capacidades e restrições do serviço de cópia de segurança do Azure relacionados com cópias de segurança do SAP HANA, também são discutidas neste artigo.

![Esta figura mostra opções para fazer uma SAP HANA ficheiro de cópia de segurança dentro da VM](media/sap-hana-backup-guide/image002.png)

Esta figura mostra opções para fazer uma cópia de segurança do ficheiro do SAP HANA dentro da VM e, em seguida, armazenando-ficheiros de cópia de segurança de HANA em algum lugar de utilização diferentes ferramentas. Fazer uma cópia de segurança do HANA requer mais tempo do que uma solução de cópia de segurança baseada em instantâneo, mas tem vantagens relativamente à integridade e consistência. Neste artigo, serão fornecidos mais detalhes.

![Esta figura mostra um potencial futuro SAP HANA cópia de segurança cenário](media/sap-hana-backup-guide/image003.png)

Esta figura mostra um potencial futuro SAP HANA cópia de segurança cenário. Se o SAP HANA permitido fazer cópias de segurança de uma replicação secundária, adicionaria opções adicionais para estratégias de backup. Atualmente não é possível, de acordo com uma postagem no Wiki do SAP HANA:

_&quot;É possível efetuar cópias de segurança no lado do secundário?_

_Não, atualmente pode apenas colocar dados e iniciar as cópias de segurança no lado principal. Se a cópia de segurança de registos automático estiver ativada, após a obtenção de controlo para o lado secundário, as cópias de segurança do registo serão automaticamente gravação.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para cópia de segurança do HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de cópia de segurança do SAP HANA

- [Introdução ao SAP HANA administração](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planear a cópia de segurança e a estratégia de recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar cópia de segurança do HANA com ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendamento de Backups de dados (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- FAQ sobre o SAP HANA de cópia de segurança no [1642148 de nota SAP](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sobre instantâneos de base de dados e armazenamento do SAP HANA no [2039883 de nota SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de ficheiros de rede não são adequados para a cópia de segurança e recuperação no [1820529 de nota SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Porquê o SAP cópia de segurança HANA?

Armazenamento do Azure oferece disponibilidade e fiabilidade de imediato (consulte [introdução ao armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md) para obter mais informações sobre o armazenamento do Azure).

O mínimo de &quot;cópia de segurança&quot; é contar com os SLAs do Azure, mantendo os ficheiros de dados e de registo do SAP HANA em VHDs do Azure anexados à VM do servidor SAP HANA. Essa abordagem abrange falhas da VM, mas não potenciais danos os dados do SAP HANA e ficheiros de registo ou erros lógicos, como a exclusão de dados ou ficheiros por acidente. As cópias de segurança também são necessárias por motivos legais ou de conformidade. Em resumo, sempre há a necessidade de cópias de segurança do SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a correção da cópia de segurança do SAP HANA
Ao utilizar instantâneos de armazenamento, a executar um restauro de teste num sistema diferente, é recomendado. Esta abordagem fornece uma forma de garantir que uma cópia de segurança está corretos e internos processos para cópia de segurança e restaurar o trabalho conforme o esperado. Embora esse seja um impacto significativo obstáculo no local, é muito mais fácil de realizar na cloud ao fornecer temporariamente os recursos necessários para esta finalidade.

Tenha em mente que fazendo um restauro simple e a verificar se o HANA está a funcionar e em execução não são suficiente. O ideal é que um deve executar uma verificação de consistência da tabela para ter certeza de que constitui a base de dados restaurada. SAP HANA oferece vários tipos de verificações de consistência descritos em [1977584 de nota SAP](https://launchpad.support.sap.com/#/notes/1977584).

Também pode encontrar informações sobre a verificação de consistência da tabela no site da SAP na [tabela e verificações de consistência do catálogo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Para cópias de segurança de ficheiros padrão, um restauro de teste não é necessário. Existem duas ferramentas de SAP HANA que ajudam a verificar a cópia de segurança pode ser utilizada para restauro: hdbbackupdiag e hdbbackupcheck. Ver [manualmente a verificar se a recuperação é possível](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obter mais informações sobre estas ferramentas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras de cópia de segurança HANA em comparação com o instantâneo de armazenamento

SAP&#39;t dar preferência de qualquer cópia de segurança HANA em comparação com o instantâneo de armazenamento. Ele lista seus prós e contras, para que um possa determinar qual deles usar dependendo da situação e da tecnologia de armazenamento disponível (consulte [planear a sua cópia de segurança e a estratégia de recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

No Azure, tenha em atenção o fato de que o blob do Azure do instantâneo não de funcionalidade&#39;consistência de sistema de ficheiros de garantia de t (consulte [utilizar instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A próxima seção _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_, aborda algumas considerações sobre esta funcionalidade.

Além disso, é necessário entender as implicações de faturas, ao trabalhar com freqüência com instantâneos de blob, conforme descrito neste artigo: [Noções básicas sobre como instantâneos de acumular encargos](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— ela&#39;t tão óbvio como utilizar os discos virtuais do Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados do SAP HANA ao criar instantâneos de armazenamento

Consistência de sistema e aplicação de ficheiros é um problema complexo quando o armazenamento de instantâneos. A maneira mais fácil para evitar problemas seria encerrado de SAP HANA, ou, até mesmo, a máquina virtual inteira. Um encerramento pode ser factível com uma demonstração ou protótipo ou até mesmo um sistema de desenvolvimento, mas não é uma opção para um sistema de produção.

No Azure, é necessário ter em mente que o blob do Azure do instantâneo não de funcionalidade&#39;consistência de sistema de ficheiros de garantia de t. Ele funciona bem no entanto, utilizando o SAP HANA instantâneo recurso, desde que há apenas um disco virtual único envolvido. Mas, mesmo com um único disco, tem de ser verificado itens adicionais. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre as cópias de segurança do SAP HANA através de instantâneos de armazenamento. Por exemplo, ela menciona que, com o sistema de ficheiros XFS, é necessário para executar **xfs\_congelar** antes de iniciar um instantâneo de armazenamento para garantir a consistência (consulte [xfs\_freeze(8) - página de man do Linux ](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre **xfs\_congelar**).

O tópico de consistência se torna ainda mais desafiador caso em que um sistema de ficheiros único abrange vários discos/volumes. Por exemplo, ao utilizar mdadm ou LVM e repartição de. A nota SAP mencionadas acima Estados:

_&quot;Mas lembre-se de que o sistema de armazenamento tem de garantir a consistência de e/s ao criar um instantâneo de armazenamento por volume de dados do SAP HANA, ou seja, instantâneos de um volume de dados específicos do serviço de SAP HANA tem de ser uma operação atômica.&quot;_

Partindo do princípio de que existe um sistema de ficheiros XFS que abrange quatro discos virtuais do Azure, os passos seguintes fornecem um instantâneo consistente que representa a área de dados HANA:

- Preparar instantâneo HANA
- Fixar o sistema de ficheiros (por exemplo, usar **xfs\_congelar**)
- Criar todos os instantâneos de blob necessários no Azure
- Libertar o sistema de ficheiros
- Confirmar o instantâneo do HANA

Recomenda-se utilizar o procedimento acima em todos os casos para ser arriscar, independentemente de qual sistema de ficheiros. Ou, se for um disco único ou a repartição, através de mdadm ou LVM em vários discos.

É importante confirmar se o instantâneo do HANA. Devido ao &quot;cópia ao gravar,&quot; SAP HANA não exijam o espaço em disco adicional nisso preparar instantâneo de modo. -&#39;s também não é possível iniciar novas cópias de segurança até que o instantâneo do SAP HANA foi confirmado.

O serviço de cópia de segurança do Azure utiliza extensões de VM do Azure para lidar com a consistência do sistema de ficheiros. Essas extensões VM não estão disponíveis para utilização autónoma. Ainda é necessário gerir a consistência do SAP HANA. Consulte o artigo relacionado [cópia de segurança do SAP HANA do Azure no nível de arquivo](sap-hana-backup-file-level.md) para obter mais informações.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de cópia de segurança de SAP HANA

O artigo de SAP HANA [planear a sua cópia de segurança e a estratégia de recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) declara um plano básico para fazer cópias de segurança:

- Armazenamento de instantâneos (diário)
- Cópia de segurança completa de dados através do formato de ficheiro ou bacint (uma vez por semana)
- Cópias de segurança do registo automático

Opcionalmente, um poderia ir completamente sem instantâneos de armazenamento; pode ser substituídos pelas cópias de segurança da delta HANA, como cópias de segurança incrementais ou diferenciais (consulte [Delta cópias de segurança](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

O guia de administração do HANA fornece uma lista de exemplo. Sugere que um recuperar o SAP HANA para um ponto específico no tempo, com a seguinte sequência das cópias de segurança:

1. Cópia de segurança de dados completa
2. Cópia de segurança diferencial
3. Cópia de segurança incremental 1
4. Cópia de segurança incremental 2
5. Backups de log

Em relação a um cronograma exato em relação a quando e com que frequência deve acontecer um tipo específico de cópia de segurança, não é possível fornecer uma orientação geral, é muito específicas do cliente e depende de quantas alterações de dados ocorrem no sistema. Uma recomendação básica do lado do SAP, que pode ser visto como orientação geral, é a marca um HANA cópia de segurança completa, uma vez por semana.
Sobre cópias de segurança de registo, consulte a documentação do SAP HANA [Backups de Log](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP também recomenda fazer alguma manutenção do catálogo de cópia de segurança para impedir que ele seja infinitamente a crescer (consulte [manutenção para o catálogo de cópia de segurança e armazenamento de cópia de segurança](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Ficheiros de configuração de SAP HANA

Conforme indicado na FAQ na [1642148 de nota SAP](https://launchpad.support.sap.com/#/notes/1642148), os ficheiros de configuração de SAP HANA não fazem parte de uma cópia de segurança padrão do HANA. Não são essenciais para restaurar um sistema. A configuração do HANA foi possível alterar manualmente após o restauro. No caso de um gostaria de obter a mesma configuração personalizada durante o processo de restauração, é necessário fazer backup dos arquivos de configuração de HANA em separado.

Se um sistema de ficheiros de cópia de segurança dedicado do HANA for standard cópias de segurança do HANA, um pode também copiar os ficheiros de configuração para o mesmo sistema de ficheiros de cópia de segurança e, em seguida, copiar todo o conteúdo em conjunto para o destino final de armazenamento, como de acesso esporádico armazenamento de Blobs.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit oferece a possibilidade de monitorar e gerenciar o SAP HANA através de um browser. Ele também permite a manipulação de cópias de segurança do SAP HANA e, por conseguinte, pode ser utilizado como uma alternativa ao SAP HANA Studio e ABAP DBACOCKPIT (consulte [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para obter mais informações).

![Esta figura mostra a tela de administração de banco de dados do SAP HANA Cockpit](media/sap-hana-backup-guide/image004.png)

Esta figura mostra a tela de administração de banco de dados do SAP HANA Cockpit e o mosaico de cópia de segurança no lado esquerdo. Ver o mosaico de cópia de segurança requer permissões de utilizador adequada para a conta de início de sessão.

![As cópias de segurança podem ser monitorizadas no SAP HANA Cockpit enquanto estiverem em curso](media/sap-hana-backup-guide/image005.png)

As cópias de segurança podem ser monitorizadas no SAP HANA Cockpit enquanto eles estão em curso e, quando for concluído, todos os detalhes de cópia de segurança estão disponíveis.

![Um exemplo com o Firefox numa VM do Azure SLES 12 com o Gnome desktop](media/sap-hana-backup-guide/image006.png)

As capturas de ecrã anteriores foram feitas a partir de uma VM do Windows Azure. Esse é um exemplo com o Firefox numa VM do Azure SLES 12 com o Gnome desktop. Mostra a opção de definir agendas de cópia de segurança do SAP HANA no SAP HANA Cockpit. Como um também pode ver, ele sugere data/hora como um prefixo para os ficheiros de cópia de segurança. No SAP HANA Studio, o prefixo predefinido é &quot;concluída\_dados\_cópia de segurança&quot; ao fazer uma cópia de segurança do ficheiro completo. É recomendado utilizar um prefixo exclusivo.

### <a name="sap-hana-backup-encryption"></a>Encriptação de cópia de segurança do SAP HANA

SAP HANA oferece encriptação de dados e de registo. Se os dados do SAP HANA e de registo que não estão encriptados, em seguida, as cópias de segurança também não estão encriptadas. É responsabilidade do cliente para utilizar alguma forma de solução de terceiros para encriptar as cópias de segurança do SAP HANA. Ver [dados e criptografia de Volume de registo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) para obter mais informações sobre a encriptação de SAP HANA.

No Microsoft Azure, um cliente pode utilizar a funcionalidade de encriptação da VM de IaaS para encriptar. Por exemplo, seria possível usar discos de dados dedicado anexados à VM, que são usados para armazenar cópias de segurança do SAP HANA, em seguida, faça cópias destes discos.

O serviço de cópia de segurança do Azure pode processar/discos de VMs encriptados (consulte [encriptados de como fazer cópias de segurança e restaurar máquinas virtuais com o Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Outra opção seria manter a VM do SAP HANA e respetivos discos sem encriptação e armazenar os ficheiros de cópia de segurança do SAP HANA numa conta de armazenamento para o qual foi ativada a encriptação (consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Configuração de teste

### <a name="test-virtual-machine-on-azure"></a>Máquina Virtual de teste no Azure

Para executar nossos testes, uma instalação de SAP HANA em GS5 VM do Azure foi utilizada para os seguintes testes de cópia de segurança/restauro. Os princípios são as mesmas usadas para VMs de série M.

![Esta figura mostra parte da descrição geral do Azure portal para a VM de teste do HANA](media/sap-hana-backup-guide/image007.png)

Esta figura mostra parte da descrição geral do Azure portal para a VM de teste do HANA.

### <a name="test-backup-size"></a>Tamanho de cópia de segurança de teste

![Esta figura foi efetuada a partir da consola de cópia de segurança no HANA Studio e mostra o tamanho do ficheiro de cópia de segurança de 229 GB para o servidor de índice do HANA](media/sap-hana-backup-guide/image008.png)

Uma tabela fictícia foi preenchida com dados para obter um tamanho de cópia de segurança do total de dados de mais de 200 GB para a derivação de dados de desempenho realista. A figura foi efetuada a partir da consola de cópia de segurança no HANA Studio e mostra o tamanho do ficheiro de cópia de segurança de 229 GB para o servidor de índice do HANA. Para os testes, utilizou-se o prefixo de cópia de segurança predefinido "COMPLETE_DATA_BACKUP" no SAP HANA Studio. Em sistemas de produção real, deve ser definido um prefixo mais úteis. SAP HANA Cockpit sugere data/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testar a ferramenta de copiar ficheiros diretamente para o armazenamento do Azure

A transferir ficheiros de cópia de segurança do SAP HANA diretamente no armazenamento de Blobs do Azure ou partilhas de ficheiros do Azure, a ferramenta de blobxfer foi utilizada uma vez que ele oferece suporte a ambos os destinos e podem ser facilmente integrado aos scripts de automatização devido à sua interface de linha de comandos. A ferramenta de blobxfer está disponível no [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Estimativa do tamanho de cópia de segurança de teste

É importante estimar o tamanho da cópia de segurança do SAP HANA. Esta estimativa ajuda a melhorar o desempenho ao definir o tamanho do ficheiro de cópia de segurança máximo para um número de ficheiros de cópia de segurança, devido a paralelismo durante uma cópia de ficheiros. (Esses detalhes são explicados neste artigo.) Um também deverá decidir se pretende fazer uma cópia de segurança completa ou uma cópia de segurança do delta (incremental ou diferencial).

Felizmente, há uma instrução de SQL simple que calcula o tamanho dos ficheiros de cópia de segurança: **selecionar \* de M\_cópia de segurança\_tamanho\_estimativas** (consulte [estimativa o espaço necessário no sistema de ficheiros para uma cópia de segurança de dados](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![O resultado da declaração SQL corresponde quase exatamente o tamanho real da cópia de segurança completa de dados no disco](media/sap-hana-backup-guide/image009.png)

Para o sistema de teste, o resultado da declaração SQL corresponde quase exatamente o tamanho real da cópia de segurança completa de dados no disco.

### <a name="test-hana-backup-file-size"></a>Tamanho do ficheiro de cópia de segurança de HANA de teste

![A consola de cópia de segurança do HANA Studio permite que alguém restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança do HANA](media/sap-hana-backup-guide/image010.png)

A consola de cópia de segurança do HANA Studio permite que alguém restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança do HANA. No ambiente de exemplo, esse recurso torna possível obter vários ficheiros de cópia de segurança mais pequenos, em vez de um ficheiro de cópia de segurança de 230 GB. Tamanho de ficheiro mais pequeno tem um impacto significativo no desempenho (consulte o artigo relacionado [cópia de segurança do SAP HANA do Azure no nível de arquivo](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumo

Com base nos resultados de teste, que as tabelas seguintes mostram os prós e contras de soluções para fazer cópias de segurança de uma base de dados do SAP HANA em execução em máquinas virtuais do Azure.

**Criar cópias de segurança SAP HANA ao sistema de arquivos e copie os ficheiros de cópia de segurança posteriormente para o destino de cópia de segurança final**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Manter cópias de segurança do HANA em discos VM                      |Sem esforços de gestão adicional     |Consome espaço de disco VM local           |
|Ferramenta de Blobxfer para copiar ficheiros de cópia de segurança para armazenamento de BLOBs |Paralelismo para copiar a vários ficheiros, a opção de utilizar o armazenamento de BLOBs de acesso esporádico | Manutenção de ferramenta adicional e a criação de scripts personalizados | 
|Cópia do blob através do Powershell ou CLI                    |Nenhuma ferramenta adicional necessária, pode ser efetuada através do Azure Powershell ou CLI |processo manual, o cliente tem que cuidar da criação de scripts e gestão de blobs copiados para o restauro|
|Copiar para a partilha NFS                                  |Pós-processamento de ficheiros de cópia de segurança em outra VM sem impacto no servidor do HANA|Processo de cópia lenta|
|Cópia de Blobxfer para o serviço de ficheiros do Azure                |Não comer espaço nos discos VM locais|Não existem direct escrever suporte por restrição de cópia de segurança, tamanho HANA de partilha de ficheiros atualmente 5 TB|
|Agente de cópia de segurança do Azure                                 | Seria a solução preferencial         | Não está atualmente disponível no Linux    |



**Cópia de segurança SAP HANA, com base nos instantâneos de armazenamento**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Serviço de cópia de segurança do Azure                               | Permite a cópia de segurança VM com base nos instantâneos de blob | Quando não utilizar o restauro ao nível do ficheiro, ela exige a criação de uma nova VM para o processo de restauração, o que implica, em seguida, a necessidade de uma nova chave de licença do SAP HANA|
|Instantâneos de manual blob                              | Flexibilidade para criar e restaurar os discos VM específicos sem alterar o ID exclusivo da VM|Todos os trabalho manual, o que precisa ser feito pelo cliente|

## <a name="next-steps"></a>Passos Seguintes
* [SAP HANA Azure Backup no nível de arquivo](sap-hana-backup-file-level.md) descreve a opção de cópia de segurança baseados em ficheiros.
* [Cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de cópia de segurança do baseadas em instantâneos de armazenamento.
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
