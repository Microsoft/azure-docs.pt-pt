---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd10bd2a04bfb2a3e3316d86e64a98c75c12e36d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76530927"
---
Este artigo explica como planejar o backup e a recuperação de desastre (DR) de VMs (máquinas virtuais) IaaS e discos no Azure. Este documento aborda discos gerenciados e não gerenciados.

Primeiro, abordamos os recursos internos de tolerância a falhas na plataforma do Azure que ajudam a proteger contra falhas locais. Em seguida, discutiremos os cenários de desastre não totalmente cobertos pelos recursos internos. Também mostramos vários exemplos de cenários de carga de trabalho em que diferentes considerações de backup e recuperação de desastre podem ser aplicadas. Em seguida, examinamos possíveis soluções para a DR de discos de IaaS.

## <a name="introduction"></a>Introdução

A plataforma Azure usa vários métodos de redundância e tolerância a falhas para ajudar a proteger os clientes contra falhas de hardware localizadas. Falhas locais podem incluir problemas com uma máquina do servidor de armazenamento do Azure que armazena parte dos dados de um disco virtual ou falhas de um SSD ou HDD nesse servidor. Essas falhas de componentes de hardware isolados podem ocorrer durante operações normais.

A plataforma Azure foi projetada para ser resiliente a essas falhas. Desastres grandes podem resultar em falhas ou na inacessibilidade de muitos servidores de armazenamento ou até mesmo em um datacenter inteiro. Embora suas VMs e discos normalmente estejam protegidos contra falhas localizadas, etapas adicionais são necessárias para proteger sua carga de trabalho contra falhas catastróficas em toda a região, como um grande desastre, que pode afetar a VM e os discos.

Além da possibilidade de falhas de plataforma, podem ocorrer problemas com um aplicativo de cliente ou dados. Por exemplo, uma nova versão do seu aplicativo pode inadvertidamente fazer uma alteração nos dados que faz com que ele seja interrompido. Nesse caso, talvez você queira reverter o aplicativo e os dados para uma versão anterior que contenha o último estado válido conhecido. Isso requer a manutenção de backups regulares.

Para a recuperação de desastre regional, você deve fazer backup de seus discos de VM IaaS em uma região diferente.

Antes de examinarmos as opções de backup e recuperação de desastre, vamos recapitular alguns métodos disponíveis para lidar com falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de IaaS do Azure

*Resiliência* refere-se à tolerância para falhas normais que ocorrem em componentes de hardware. Resiliência é a capacidade de se recuperar de falhas e continuar a funcionar. Não é uma questão de evitar falhas, mas de responder às falhas de forma a evitar períodos de indisponibilidade ou a perda de dados. O objetivo da resiliência é fazer com que a aplicação volte para um estado totalmente funcional após uma falha. As máquinas virtuais e os discos do Azure são projetados para serem resilientes a falhas comuns de hardware. Vejamos como a plataforma IaaS do Azure fornece essa resiliência.

Uma máquina virtual consiste principalmente em duas partes: um servidor de computação e os discos persistentes. Ambos afetam a tolerância a falhas de uma máquina virtual.

Se o servidor host de computação do Azure que hospeda sua VM apresentar uma falha de hardware, o que é raro, o Azure foi projetado para restaurar automaticamente a VM em outro servidor. Se esse cenário, o computador é reinicializado e a VM é colocada em backup após algum tempo. O Azure detecta automaticamente essas falhas de hardware e executa recuperações para ajudar a garantir que a VM do cliente esteja disponível assim que possível.

Em relação aos discos de IaaS, a durabilidade dos dados é essencial para uma plataforma de armazenamento persistente. Os clientes do Azure têm aplicativos de negócios importantes em execução no IaaS e dependem da persistência dos dados. O Azure cria a proteção para esses discos de IaaS, com três cópias redundantes dos dados armazenados localmente. Essas cópias fornecem alta durabilidade contra falhas locais. Se um dos componentes de hardware que contém o disco falhar, sua VM não será afetada, pois há duas cópias adicionais para dar suporte a solicitações de disco. Ele funciona bem, mesmo se dois componentes de hardware diferentes que dão suporte a um disco falharem ao mesmo tempo (o que é raro). 

Para garantir que você sempre mantenha três réplicas, o armazenamento do Azure gera automaticamente uma nova cópia dos dados em segundo plano se uma das três cópias ficar indisponível. Portanto, não deve ser necessário usar o RAID com discos do Azure para tolerância a falhas. Uma configuração simples de RAID 0 deve ser suficiente para distribuir os discos, se necessário, para criar volumes maiores.

Devido a essa arquitetura, o Azure fornece consistentemente a durabilidade de nível empresarial para discos de IaaS, com uma [taxa de falha anual](https://en.wikipedia.org/wiki/Annualized_failure_rate)de percentual de zero por cento do setor.

As falhas de hardware localizadas no host de computação ou na plataforma de armazenamento podem, às vezes, resultar na indisponibilidade temporária da VM coberta pelo [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para a disponibilidade da VM. O Azure também fornece um SLA líder do setor para instâncias de VM únicas que usam o SSDs Premium do Azure.

Para proteger as cargas de trabalho do aplicativo contra tempo de inatividade devido à indisponibilidade temporária de um disco ou VM, os clientes podem usar [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). Duas ou mais máquinas virtuais em um conjunto de disponibilidade fornecem redundância para o aplicativo. Em seguida, o Azure cria essas VMs e discos em domínios de falha separados com diferentes componentes de energia, rede e servidor.

Por causa desses domínios de falha separados, as falhas de hardware localizadas normalmente não afetam várias VMs no conjunto ao mesmo tempo. Ter domínios de falha separados fornece alta disponibilidade para seu aplicativo. É considerada uma boa prática usar conjuntos de disponibilidade quando a alta disponibilidade é necessária. A próxima seção aborda o aspecto de recuperação de desastre.

### <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre

A recuperação de desastres é a capacidade de recuperação de incidentes raros, mas importantes. Esses incidentes incluem falhas não transitórias em larga escala, como interrupção do serviço que afeta toda a região. A recuperação de desastre inclui backup e arquivamento de dados e pode incluir intervenção manual, como a restauração de um banco de dados de um backup.

A proteção interna da plataforma Azure contra falhas localizadas pode não proteger totalmente as VMs/discos se um desastre importante causar interrupções em grande escala. Essas interrupções em larga escala incluem eventos catastróficos, como se um datacenter for atingido por um furacão, terremoto, fogo ou se houver uma falha de unidade de hardware em grande escala. Além disso, você pode encontrar falhas devido a problemas de aplicativos ou dados.

Para ajudar a proteger suas cargas de trabalho de IaaS contra interrupções, você deve planejar a redundância e fazer backups para habilitar a recuperação. Para a recuperação de desastres, você deve fazer backup em uma localização geográfica diferente fora do site primário. Essa abordagem ajuda a garantir que o backup não seja afetado pelo mesmo evento que originalmente afetou a VM ou os discos. Para obter mais informações, consulte [recuperação de desastre para aplicativos do Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Suas considerações de recuperação de desastre podem incluir os seguintes aspectos:

- Alta disponibilidade: a capacidade do aplicativo de continuar em execução em um estado íntegro, sem tempo de inatividade significativo. Por *estado íntegro*, esse Estado significa que o aplicativo está respondendo e os usuários podem se conectar ao aplicativo e interagir com ele. Determinados aplicativos e bancos de dados críticos talvez precisem estar sempre disponíveis, mesmo quando há falhas na plataforma. Para essas cargas de trabalho, talvez seja necessário planejar a redundância para o aplicativo, bem como os dados.

- Durabilidade dos dados: em alguns casos, a principal consideração é garantir que os dados sejam preservados se ocorrer um desastre. Portanto, talvez seja necessário um backup dos dados em um site diferente. Para essas cargas de trabalho, talvez você não precise de redundância total para o aplicativo, mas apenas um backup regular dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de backup e recuperação de desastre

Vejamos alguns exemplos típicos de cenários de carga de trabalho do aplicativo e as considerações para planejar a recuperação de desastres.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: principais soluções de banco de dados

Considere um servidor de banco de dados de produção, como SQL Server ou Oracle, que pode dar suporte à alta disponibilidade. Os usuários e aplicativos de produção críticos dependem desse banco de dados. O plano de recuperação de desastres para este sistema pode precisar dar suporte aos seguintes requisitos:

- Os dados devem ser protegidos e recuperáveis.
- O servidor deve estar disponível para uso.

O plano de recuperação de desastre pode exigir a manutenção de uma réplica do banco de dados em uma região diferente como um backup. Dependendo dos requisitos de disponibilidade do servidor e recuperação de dados, a solução pode variar de um site de réplica ativo-ativo ou ativo-passivo para backups offline periódicos dos dados. Os bancos de dados relacionais, como SQL Server e Oracle, fornecem várias opções de replicação. Para SQL Server, use [SQL Server grupos de disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) para alta disponibilidade.

Bancos de dados NoSQL, como o MongoDB, também dão suporte a [réplicas](https://docs.mongodb.com/manual/replication/) para redundância. As réplicas para alta disponibilidade são usadas.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: um cluster de VMs redundantes

Considere uma carga de trabalho tratada por um cluster de VMs que fornece redundância e balanceamento de carga. Um exemplo é um cluster Cassandra implantado em uma região. Esse tipo de arquitetura já fornece um alto nível de redundância nessa região. No entanto, para proteger a carga de trabalho de uma falha de nível regional, considere distribuir o cluster entre duas regiões ou fazer backups periódicos para outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: carga de trabalho do aplicativo IaaS

Vamos examinar a carga de trabalho do aplicativo IaaS. Por exemplo, esse aplicativo pode ser uma carga de trabalho de produção típica em execução em uma VM do Azure. Pode ser um servidor Web ou servidor de arquivos que contém o conteúdo e outros recursos de um site. Ele também pode ser um aplicativo de negócios personalizado em execução em uma VM que armazenou seus dados, recursos e estado do aplicativo nos discos de VM. Nesse caso, é importante garantir que você faça backups regularmente. A frequência de backup deve ser baseada na natureza da carga de trabalho da VM. Por exemplo, se o aplicativo for executado todos os dias e modificar os dados, o backup deverá ser feito a cada hora.

Outro exemplo é um servidor de relatórios que efetua pull de dados de outras fontes e gera relatórios agregados. A perda dessa VM ou dos discos pode levar à perda dos relatórios. No entanto, pode ser possível executar novamente o processo de relatório e gerar novamente a saída. Nesse caso, você não tem realmente perda de dados, mesmo se o servidor de relatórios for atingido com um desastre. Como resultado, você pode ter um nível mais alto de tolerância para perder parte dos dados no servidor de relatórios. Nesse caso, backups menos frequentes são uma opção para reduzir os custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: problemas de dados de aplicativo IaaS

Problemas de dados de aplicativos IaaS são outra possibilidade. Considere um aplicativo que computa, mantém e fornece dados comerciais críticos, como informações sobre preços. Uma nova versão do seu aplicativo tinha um bug de software que computava incorretamente os preços e corromperam os dados de comércio existentes servidos pela plataforma. Aqui, o melhor curso de ação é reverter para a versão anterior do aplicativo e os dados. Para habilitar isso, faça backups periódicos do seu sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação de desastre: backup do Azure 

O [backup do Azure](https://azure.microsoft.com/services/backup/) é usado para backups e Dr e funciona com discos [gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md) , bem como com discos não gerenciados. Você pode criar um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup.

Se você usar [SSDs Premium](../articles/virtual-machines/windows/disks-types.md), [discos gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md)ou outros tipos de disco com a opção de [armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md) , será especialmente importante fazer backups periódicos de recuperação de desastre. O backup do Azure armazena os dados em seu cofre de serviços de recuperação para retenção de longo prazo. Escolha a opção de [armazenamento com redundância geográfica](../articles/storage/common/storage-redundancy-grs.md) para o cofre dos serviços de recuperação de backup. Essa opção garante que os backups sejam replicados para uma região do Azure diferente para proteger contra desastres regionais.

Para discos não gerenciados, você pode usar o tipo de armazenamento com redundância local para discos de IaaS, mas verifique se o backup do Azure está habilitado com a opção de armazenamento com redundância geográfica para o cofre dos serviços de recuperação.

> [!NOTE]
> Se você usar a opção armazenamento com redundância [geográfica](../articles/storage/common/storage-redundancy-grs.md) ou [armazenamento com redundância geográfica com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para seus discos não gerenciados, ainda precisará de instantâneos consistentes para backup e recuperação de desastre. Use o [backup do Azure](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes](#alternative-solution-consistent-snapshots).

 A tabela a seguir é um resumo das soluções disponíveis para DR.

| Cenário | Replicação automática | Solução de DR |
| --- | --- | --- |
| Discos SSD Premium | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento com redundância local não gerenciado | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento com redundância geográfica não gerenciado | Entre regiões ([armazenamento com redundância geográfica](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| Discos de armazenamento com redundância geográfica com acesso de leitura não gerenciado | Entre regiões ([armazenamento com redundância geográfica com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

A alta disponibilidade é melhor satisfeita com o uso de Managed disks em um conjunto de disponibilidade junto com o backup do Azure. Se você usar discos não gerenciados, ainda poderá usar o backup do Azure para DR. Se você não conseguir usar o backup do Azure, pegar [instantâneos consistentes](#alternative-solution-consistent-snapshots), conforme descrito em uma seção posterior, é uma solução alternativa para backup e recuperação de desastre.

Suas opções de alta disponibilidade, backup e recuperação de desastre em níveis de aplicativo ou infraestrutura podem ser representadas da seguinte maneira:

| Nível |   Elevada disponibilidade   | Backup ou recuperação de desastre |
| --- | --- | --- |
| Candidatura | SQL Server AlwaysOn | Azure Backup |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento com redundância geográfica com instantâneos consistentes |

### <a name="using-azure-backup"></a>Utilizar o Azure Backup 

O [backup do Azure](../articles/backup/backup-azure-vms-introduction.md) pode fazer backup de suas VMs que executam Windows ou Linux no cofre dos serviços de recuperação do Azure. Fazer backup e restaurar dados críticos para os negócios é complicado pelo fato de que é necessário fazer backup dos dados críticos para os negócios, enquanto os aplicativos que produzem os dados estão em execução. 

Para resolver esse problema, o backup do Azure fornece backups consistentes com o aplicativo para cargas de trabalho da Microsoft. Ele usa o serviço de sombra de volume para garantir que os dados sejam gravados corretamente no armazenamento. Para VMs do Linux, o modo de consistência de backup padrão é backups consistentes com o arquivo, pois o Linux não tem funcionalidade equivalente ao serviço de sombra de volume, como no caso do Windows. Para computadores Linux, consulte [backup consistente com o aplicativo de VMs Linux do Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Fluxo de backup do Azure][1]

Quando o backup do Azure inicia um trabalho de backup no horário agendado, ele dispara a extensão de backup instalada na VM para fazer um instantâneo pontual. Um instantâneo é levado em coordenação com o serviço de sombra de volume para obter um instantâneo consistente dos discos na máquina virtual sem precisar desligá-lo. A extensão de backup na VM libera Todas as gravações antes de tirar um instantâneo consistente de todos os discos. Depois de tirar o instantâneo, os dados são transferidos pelo backup do Azure para o cofre de backup. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que foram alterados após o último backup.

Para restaurar, você pode exibir os backups disponíveis por meio do backup do Azure e, em seguida, iniciar uma restauração. Você pode criar e restaurar os backups do Azure por meio do [portal do Azure](https://portal.azure.com/), [usando o PowerShell](../articles/backup/backup-azure-vms-automation.md)ou usando o [CLI do Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Etapas para habilitar um backup

Use as etapas a seguir para habilitar backups de suas VMs usando o [portal do Azure](https://portal.azure.com/). Há alguma variação dependendo do cenário exato. Consulte a documentação do [backup do Azure](../articles/backup/backup-azure-vms-introduction.md) para obter detalhes completos. O backup do Azure também [dá suporte a VMs com discos gerenciados](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Criar um cofre dos serviços de recuperação para uma VM:

    a. Na [portal do Azure](https://portal.azure.com/), procure **todos os recursos** e encontre **cofres dos serviços de recuperação**.

    b. No menu **cofres dos serviços de recuperação** , clique em **Adicionar** e siga as etapas para criar um novo cofre na mesma região que a VM. Por exemplo, se sua VM estiver na região oeste dos EUA, selecione oeste dos EUA para o cofre.

1.  Verifique a replicação de armazenamento para o cofre recém-criado. Acesse o cofre em **cofres dos serviços de recuperação** e vá para **Propriedades** > **configuração de backup** > **atualização**. Verifique se a opção de **armazenamento com redundância geográfica** está selecionada por padrão. Essa opção garante que seu cofre seja replicado automaticamente para um datacenter secundário. Por exemplo, seu cofre no oeste dos EUA é replicado automaticamente para o leste dos EUA.

1.  Configure a política de backup e selecione a VM na mesma interface do usuário.

1.  Verifique se o agente de backup está instalado na VM. Se sua VM for criada usando uma imagem da galeria do Azure, o agente de backup já estará instalado. Caso contrário (ou seja, se você usar uma imagem personalizada), use as instruções para [instalar o agente de VM em uma máquina virtual](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Depois que as etapas anteriores forem concluídas, o backup será executado em intervalos regulares, conforme especificado na política de backup. Se necessário, você pode disparar o primeiro backup manualmente do painel do cofre no portal do Azure.

Para automatizar o backup do Azure usando scripts, consulte [cmdlets do PowerShell para backup de VM](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Etapas para recuperação

Se precisar reparar ou recriar uma VM, você poderá restaurar a VM de qualquer um dos pontos de recuperação de backup no cofre. Há duas opções diferentes para executar a recuperação:

-   Você pode criar uma nova VM como uma representação pontual de sua VM de backup.

-   Você pode restaurar os discos e, em seguida, usar o modelo para a VM para personalizar e recompilar a VM restaurada.

Para obter mais informações, consulte as instruções para [usar o portal do Azure para restaurar máquinas virtuais](../articles/backup/backup-azure-arm-restore-vms.md). Este documento também explica as etapas específicas para restaurar as VMs de backup para um datacenter emparelhado usando seu cofre de backup com redundância geográfica se houver um desastre no datacenter primário. Nesse caso, o backup do Azure usa o serviço de computação da região secundária para criar a máquina virtual restaurada.

Você também pode usar o PowerShell para [criar uma nova VM a partir de discos restaurados](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: instantâneos consistentes

Se não for possível usar o backup do Azure, você poderá implementar seu próprio mecanismo de backup usando instantâneos. Criar instantâneos consistentes para todos os discos usados por uma VM e, em seguida, replicar esses instantâneos para outra região é complicado. Por esse motivo, o Azure considera o uso do serviço de backup como uma opção melhor do que criar uma solução personalizada.

Se você usar armazenamento com redundância geográfica com acesso de leitura/armazenamento com redundância geográfica para discos, os instantâneos serão replicados automaticamente para um datacenter secundário. Se você usar o armazenamento com redundância local para discos, você precisará replicar os dados por conta própria. Para obter mais informações, consulte [fazer backup de discos de VM não gerenciados do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto em um ponto específico no tempo. Um instantâneo incorre em cobrança pelo tamanho incremental dos dados que ele contém. Para obter mais informações, consulte [criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Criar instantâneos enquanto a VM estiver em execução

Embora você possa tirar um instantâneo a qualquer momento, se a VM estiver em execução, ainda haverá dados sendo transmitidos para os discos. Os instantâneos podem conter operações parciais que estavam em trânsito. Além disso, se houver vários discos envolvidos, os instantâneos de discos diferentes podem ter ocorrido em momentos diferentes. Esses cenários podem fazer com que os instantâneos sejam descoordenados. Essa falta de ordination é especialmente problemática para volumes distribuídos cujos arquivos podem ser corrompidos caso as alterações tenham sido feitas durante o backup.

Para evitar essa situação, o processo de backup deve implementar as seguintes etapas:

1.  Congele todos os discos.

1.  Libere todas as gravações pendentes.

1.  [Crie um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md) para todos os discos.

Alguns aplicativos do Windows, como SQL Server, fornecem um mecanismo de backup coordenado por meio de um serviço de sombra de volume para criar backups consistentes com o aplicativo. No Linux, você pode usar uma ferramenta como o *fsfreeze* para coordenar os discos. Essa ferramenta fornece backups consistentes com o arquivo, mas não instantâneos consistentes com o aplicativo. Esse processo é complexo, portanto, você deve considerar o uso [do backup do Azure](../articles/backup/backup-azure-vms-introduction.md) ou de uma solução de backup de terceiros que já implemente esse procedimento.

O processo anterior resulta em uma coleção de instantâneos coordenados para todos os discos de VM, representando uma exibição pontual específica da VM. Esse é um ponto de restauração de backup para a VM. Você pode repetir o processo em intervalos agendados para criar backups periódicos. Consulte [copiar os backups para outra região](#copy-the-snapshots-to-another-region) para obter as etapas para copiar os instantâneos para outra região para Dr.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Criar instantâneos enquanto a VM estiver offline

Outra opção para criar backups consistentes é desligar a VM e tirar instantâneos de blob de cada disco. Tirar instantâneos de blob é mais fácil do que coordenar instantâneos de uma VM em execução, mas requer alguns minutos de inatividade.

1. Desligar a VM.

1. Crie um instantâneo de cada blob de disco rígido virtual, que leva apenas alguns segundos.

    Para criar um instantâneo, você pode usar o [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), a [API REST do armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), [CLI do Azure](/cli/azure/)ou uma das bibliotecas de cliente de armazenamento do Azure, como [a biblioteca de cliente de armazenamento para .net](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Inicie a VM, o que encerra o tempo de inatividade. Normalmente, todo o processo é concluído em alguns minutos.

Esse processo gera uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauração de backup para a VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copiar os instantâneos para outra região

A criação de instantâneos sozinho pode não ser suficiente para DR. Você também deve replicar os backups de instantâneo para outra região.

Se você usar armazenamento com redundância geográfica ou armazenamento com redundância geográfica com acesso de leitura para seus discos, os instantâneos serão replicados para a região secundária automaticamente. Pode haver alguns minutos de atraso antes da replicação. Se o datacenter primário ficar inativo antes que os instantâneos concluam a replicação, você não poderá acessar os instantâneos do datacenter secundário. A probabilidade disso é pequena.

> [!NOTE]
> Apenas ter os discos em um armazenamento com redundância geográfica ou em uma conta de armazenamento com redundância geográfica com acesso de leitura não protege a VM contra desastres. Você também deve criar instantâneos coordenados ou usar o backup do Azure. Isso é necessário para recuperar uma VM em um estado consistente.

Se você usar o armazenamento com redundância local, deverá copiar os instantâneos para uma conta de armazenamento diferente imediatamente após a criação do instantâneo. O destino de cópia pode ser uma conta de armazenamento com redundância local em uma região diferente, resultando na cópia estar em uma região remota. Você também pode copiar o instantâneo para uma conta de armazenamento com redundância geográfica com acesso de leitura na mesma região. Nesse caso, o instantâneo é replicado lentamente para a região secundária remota. O backup é protegido contra desastres no site primário após a conclusão da cópia e da replicação.

Para copiar seus instantâneos incrementais para DR com eficiência, examine as instruções em [fazer backup de discos de VM não gerenciados do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

![Fazer backup de discos de VM não gerenciados do Azure com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação de instantâneos

Para recuperar um instantâneo, copie-o para criar um novo BLOB. Se você estiver copiando o instantâneo da conta primária, poderá copiar o instantâneo para o blob de base do instantâneo. Esse processo reverte o disco para o instantâneo. Esse processo é conhecido como promoção do instantâneo. Se você estiver copiando o backup de instantâneo de uma conta secundária, no caso de uma conta de armazenamento com redundância geográfica com acesso de leitura, deverá copiá-la para uma conta primária. Você pode copiar um instantâneo [usando o PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou usando o utilitário AzCopy. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comando AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Para VMs com vários discos, você deve copiar todos os instantâneos que fazem parte do mesmo ponto de restauração coordenado. Depois de copiar os instantâneos para BLOBs VHD graváveis, você pode usar os BLOBs para recriar sua VM usando o modelo para a VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

SQL Server em execução em uma VM têm seus próprios recursos internos para fazer backup de seu banco de dados SQL Server no armazenamento de BLOBs do Azure ou em um compartilhamento de arquivos. Se a conta de armazenamento for de armazenamento com redundância geográfica ou armazenamento com redundância geográfica com acesso de leitura, você poderá acessar esses backups no datacenter secundário da conta de armazenamento em caso de desastre, com as mesmas restrições conforme discutido anteriormente. Para obter mais informações, consulte [fazer backup e restaurar para SQL Server em máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Além de fazer backup e restaurar, [SQL Server grupos de disponibilidade AlwaysOn](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) podem manter réplicas secundárias de bancos de dados. Essa capacidade reduz muito o tempo de recuperação de desastre.

## <a name="other-considerations"></a>Outras considerações

Este artigo abordou como fazer backup ou tirar instantâneos de suas VMs e seus discos para dar suporte à recuperação de desastres e como usar esses backups ou instantâneos para recuperar seus dados. Com o modelo de Azure Resource Manager, muitas pessoas usam modelos para criar suas VMs e outras infraestruturas no Azure. Você pode usar um modelo para criar uma VM que tenha a mesma configuração a cada vez. Se você usar imagens personalizadas para criar suas VMs, também deverá garantir que suas imagens sejam protegidas usando uma conta de armazenamento com redundância geográfica com acesso de leitura para armazená-las.

Consequentemente, o processo de backup pode ser uma combinação de duas coisas:

- Faça backup dos dados (discos).
- Faça backup da configuração (modelos e imagens personalizadas).

Dependendo da opção de backup que você escolher, talvez seja necessário manipular o backup dos dados e da configuração, ou o serviço de backup pode lidar com tudo isso para você.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: entendendo o impacto da redundância de dados

Para contas de armazenamento no Azure, há três tipos de redundância de dados que você deve considerar em relação à recuperação de desastre: localmente redundante, com redundância geográfica ou com redundância geográfica com acesso de leitura. 

O armazenamento com redundância local retém três cópias dos dados no mesmo datacenter. Quando a VM grava os dados, todas as três cópias são atualizadas antes de o sucesso ser retornado ao chamador, de modo que você sabe que eles são idênticos. O disco é protegido contra falhas locais, pois é improvável que todas as três cópias sejam afetadas ao mesmo tempo. No caso do armazenamento com redundância local, não há redundância geográfica, portanto, o disco não está protegido contra falhas catastróficas que podem afetar um datacenter inteiro ou uma unidade de armazenamento.

Com o armazenamento com redundância geográfica e o armazenamento com redundância geográfica com acesso de leitura, três cópias dos dados são mantidas na região primária selecionada por você. Mais três cópias de seus dados são retidas em uma região secundária correspondente definida pelo Azure. Por exemplo, se você armazenar dados no oeste dos EUA, os dados serão replicados para o leste dos EUA. A retenção de cópia é feita de forma assíncrona e há um pequeno atraso entre as atualizações nos sites primário e secundário. As réplicas dos discos no site secundário são consistentes em uma base por disco (com o atraso), mas as réplicas de vários discos ativos podem não estar sincronizadas entre si. Para ter réplicas consistentes em vários discos, são necessários instantâneos consistentes.

A principal diferença entre o armazenamento com redundância geográfica e o armazenamento com redundância geográfica com acesso de leitura é que, com o armazenamento com redundância geográfica com acesso de leitura, você pode ler a cópia secundária a qualquer momento. Se houver um problema que processe os dados na região primária inacessível, a equipe do Azure fará todos os esforços para restaurar o acesso. Enquanto o primário estiver inativo, se você tiver o armazenamento com redundância geográfica com acesso de leitura habilitado, poderá acessar os dados no datacenter secundário. Portanto, se você planeja ler a partir da réplica enquanto o primário estiver inacessível, o armazenamento com redundância geográfica com acesso de leitura deverá ser considerado.

Se ele se tornar uma interrupção significativa, a equipe do Azure poderá disparar um failover geográfico e alterar as entradas DNS primárias para apontar para o armazenamento secundário. Neste ponto, se você tiver armazenamento com redundância geográfica ou armazenamento com redundância geográfica com acesso de leitura habilitado, poderá acessar os dados na região que costumava ser o secundário. Em outras palavras, se sua conta de armazenamento tiver um armazenamento com redundância geográfica e houver um problema, você poderá acessar o armazenamento secundário somente se houver um failover geográfico.

Para obter mais informações, consulte [o que fazer se ocorrer uma interrupção do armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>A Microsoft controla se ocorre um failover. O failover não é controlado por conta de armazenamento, portanto, não é decidido por clientes individuais. Para implementar a recuperação de desastre para contas de armazenamento ou discos de máquina virtual específicos, você deve usar as técnicas descritas anteriormente neste artigo.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
