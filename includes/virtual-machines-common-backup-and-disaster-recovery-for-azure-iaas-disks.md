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
ms.openlocfilehash: 570330f47d2c610032c9c6646231c2320b2257be
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732672"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Cópia de segurança e recuperação após desastre para discos IaaS do Azure

Este artigo explica como planejar a cópia de segurança e recuperação após desastre (DR) de máquinas virtuais (VMs) IaaS e os discos no Azure. Este documento aborda os discos geridos e não geridos.

Em primeiro lugar, vamos abordar os recursos de tolerância a falhas incorporadas na plataforma do Azure que o ajuda a proteger contra falhas de locais. Vamos discutir, em seguida, os cenários de desastre totalmente não abrangidos pelos recursos internos. Também vamos mostrar vários exemplos de cenários de carga de trabalho onde podem aplicar diferente cópia de segurança e considerações de DR. Em seguida, vamos rever o possíveis soluções para os discos de DR de IaaS.

## <a name="introduction"></a>Introdução

A plataforma do Azure utiliza vários métodos para redundância e tolerância a falhas para ajudar a proteger os clientes contra falhas de hardware localizadas. Falhas locais podem incluir problemas com a uma máquina do servidor de armazenamento do Azure que armazena a parte dos dados para um disco virtual ou falhas de um SSD ou HDD nesse servidor. Tais falhas do componente de hardware isolados podem acontecer durante operações normais.

A plataforma do Azure foi concebida para ser resiliente a essas falhas. Desastres de grandes podem resultar em falhas ou inaccessibility de vários servidores de armazenamento ou até mesmo um todo o datacenter. Embora as VMs e os discos normalmente são protegidos contra falhas localizadas, passos adicionais são necessários para proteger a sua carga de trabalho de falhas catastróficas de toda a região, por exemplo, um desastre grave, que podem afetar a sua VM e os discos.

Para além da possibilidade de falhas de plataforma, podem ocorrer problemas com uma aplicação de cliente ou dados. Por exemplo, uma nova versão do seu aplicativo inadvertidamente poderá fazer uma alteração aos dados que faz com que ele quebrar. Nesse caso, poderá reverter a aplicação e os dados para uma versão anterior que contém o último Estado bom conhecido. Isso exige a manutenção de cópias de segurança regulares.

Para a recuperação de desastre regional, é necessário fazer backup os discos da VM de IaaS para uma região diferente.

Antes de observarmos cópia de segurança e opções de DR, vamos recapitular alguns métodos disponíveis para lidar com falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de IaaS do Azure

*Resiliência* refere-se para a tolerância de falhas normais que ocorrem nos componentes de hardware. A resiliência é a capacidade de recuperar de falhas e continuar a funcionar. Não se trata de evitar falhas, mas a responder a falhas de uma forma que evita o tempo de inatividade ou perda de dados. O objetivo da resiliência é fazer com que a aplicação volte para um estado totalmente funcional após uma falha. Máquinas virtuais do Azure e os discos foram concebidos para ser resiliente a falhas de hardware comum. Vamos ver como a plataforma de IaaS do Azure fornece este resiliência.

Uma máquina virtual consiste principalmente em duas partes: um servidor de computação e os discos persistentes. Ambos afetam a tolerância a falhas de uma máquina virtual.

Se o servidor de anfitrião de computação do Azure que aloja a sua VM sofre uma falha de hardware, que é raro, o Azure foi concebido para restaurar automaticamente a VM noutro servidor. Se este cenário, os reinícios de computador e a VM é reativado após algum tempo. O Azure automaticamente detecta essas falhas de hardware e executa recuperações para ajudar a garantir que o cliente VM está disponível logo que possível.

Relativas aos discos IaaS, a durabilidade dos dados é essencial para uma plataforma de armazenamento persistente. Os clientes do Azure têm aplicações empresariais importantes em execução no IaaS e elas estão dependentes da persistência dos dados. Proteção de designs do Azure para estes discos IaaS, com três cópias redundantes dos dados armazenados localmente. Estas cópias fornecem para uma elevada durabilidade contra falhas de locais. Se um dos componentes de hardware que mantém o seu disco falhar, a VM não é afetada, uma vez que existem duas cópias adicionais para suportar pedidos de disco. Ele funciona bem, mesmo se dois componentes de hardware diferente que oferecem suporte a um disco falharem ao mesmo tempo (o que é raro). 

Para garantir que sempre mantém três réplicas, armazenamento do Azure gera automaticamente uma nova cópia dos dados em segundo plano se uma das três copia fica indisponível. Por conseguinte, não deve ser necessário utilizar RAID com discos do Azure para a tolerância a falhas. Um simple RAID 0 configuração deve ser suficiente para repartição de discos, se necessário, para criar volumes maiores.

Devido a esta arquitetura, Azure forneceu consistentemente e de nível empresarial durabilidade para IaaS discos, com um líder do setor zero por cento [taxa de falhas anuais](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Falhas de hardware localizadas na computação hospedar ou na plataforma de armazenamento pode por vezes, resultado da indisponibilidade temporária da VM que é abrangida pela [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para disponibilidade da VM. Azure também fornece um SLA líder do setor para instâncias de VM individuais que utilizam o SSD premium do Azure.

Para proteger cargas de trabalho de aplicação do período de indisponibilidade devido à indisponibilidade temporária de uma VM ou disco, os clientes podem usar [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). Dois ou mais máquinas virtuais num conjunto de disponibilidade fornecer redundância para a aplicação. O Azure cria, em seguida, essas VMs e os discos em domínios de falha com diferentes componentes de alimentação, rede e servidor.

Devido a estes domínios de falha, falhas de hardware localizadas, normalmente, não afetam várias VMs no conjunto, ao mesmo tempo. Domínios de falha de ter fornece elevada disponibilidade para a sua aplicação. Ele é considerado uma boa prática utilizar conjuntos de disponibilidade quando é necessária a elevada disponibilidade. A próxima seção aborda o aspecto de recuperação após desastre.

### <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre

Recuperação após desastre é a capacidade de recuperar de incidentes raros, mas significativos. Esses incidentes incluem falhas não transitórias e em grande escala, como interrupção do serviço que afeta toda uma região. Recuperação após desastre inclui a cópia de segurança de dados e o arquivamento e pode incluir intervenção manual, como restaurar uma base de dados a partir de uma cópia de segurança.

Proteção contra falhas localizadas com incorporada da plataforma do Azure poderá não totalmente proteger VMs/discos se de grandes proporções faz com que as falhas em grande escala. Estas falhas em grande escala incluem eventos catastróficos, por exemplo, se um centro de dados é atingido por um furacão, sismo, acionar ou se houver uma falha de unidade de hardware em grande escala. Além disso, poderá encontrar falhas devido a problemas de dados ou de aplicação.

Para ajudar a proteger as cargas de trabalho de IaaS de falhas, deve planear a redundância e tenha cópias de segurança para ativar a recuperação. Para a recuperação após desastre, deve copiar em segurança numa localização geográfica diferente para fora do site primário. Esta abordagem ajuda a garantir a que sua cópia de segurança não é afetada pelo evento mesmo que originalmente afetados a VM ou os discos. Para obter mais informações, consulte [recuperação após desastre para aplicações do Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Suas considerações de DR podem incluir os seguintes aspetos:

- Elevada disponibilidade: A capacidade da aplicação para continuar em execução em bom estado de funcionamento, sem períodos de indisponibilidade significativos. Por *bom estado de funcionamento*, este estado significa que a aplicação está a responder e os utilizadores podem ligar à aplicação e interagir com ele. Determinados aplicativos de missão crítica e bases de dados poderão ser necessários para estar sempre disponível, mesmo quando existem falhas na plataforma. Estas cargas de trabalho, poderá ter de planear a redundância para o aplicativo, bem como os dados.

- Durabilidade de dados: Em alguns casos, a principal consideração é garantir que os dados são preservados se ocorrer um desastre. Por conseguinte, poderá ter uma cópia de segurança dos seus dados num site diferente. Para essas cargas de trabalho, poderá não ter redundância total para o aplicativo, mas apenas uma cópia de segurança normal dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de cópia de segurança e DR

Vamos examinar alguns exemplos típicos de cenários de carga de trabalho de aplicação e as considerações de planejamento para recuperação após desastre.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: Soluções de base de dados principais

Considere um servidor de base de dados de produção, como o SQL Server ou Oracle, o que pode suportar uma elevada disponibilidade. Aplicações de produção crítico e os utilizadores dependem desta base de dados. O plano de recuperação após desastre para este sistema poderá ter de suportar os requisitos seguintes:

- Os dados devem estar protegidos e recuperáveis.
- O servidor tem de estar disponível para utilização.

O plano de recuperação após desastre pode requerer a manutenção de uma réplica da base de dados numa região diferente como uma cópia de segurança. Consoante os requisitos de disponibilidade de servidor e recuperação de dados, a solução pode variar a partir de um site de réplica ativa-ativa ou ativa-passiva para cópias de segurança offline periódicas dos dados. Bases de dados relacionais, como o SQL Server e Oracle, fornecem várias opções para a replicação. Para o SQL Server, utilize [grupos de Disponibilidade AlwaysOn do SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) para elevada disponibilidade.

Bases de dados NoSQL, como MongoDB, também suportam [réplicas](https://docs.mongodb.com/manual/replication/) para redundância. São utilizadas as réplicas para elevada disponibilidade.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: Um cluster de VMs redundantes

Considere uma carga de trabalho processada por um cluster de VMs que fornecer redundância e balanceamento de carga. Um exemplo é um cluster do Cassandra implementado numa região. Este tipo de arquitetura já fornece um alto nível de redundância nessa região. No entanto, para proteger a carga de trabalho de uma falha ao nível regional, deve considerar propagando-se o cluster em duas regiões ou para fazer cópias de segurança periódicas para outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: Carga de trabalho de aplicação de IaaS

Vamos examinar a carga de trabalho de aplicação de IaaS. Por exemplo, esta aplicação pode ser uma carga de trabalho de produção normal em execução numa VM do Azure. Pode ser um servidor web ou servidor de ficheiros que contém o conteúdo e outros recursos de um site. Também pode ser um aplicativo de negócios personalizados internos em execução numa VM que armazenados seus dados, a recursos e o estado da aplicação nos discos de VM. Neste caso, é importante certificar-se de que fazer cópias de segurança em intervalos regulares. Frequência de cópia de segurança deve basear-se sobre a natureza da carga de trabalho VM. Por exemplo, se o aplicativo é executado todos os dias e modifica os dados, em seguida, a cópia de segurança deve ser levada a cada hora.

Outro exemplo é um servidor de relatórios que extrai dados de outras origens e gera relatórios agregados. A perda desta VM ou os discos pode levar à perda dos relatórios. No entanto, é possível voltar a executar o processo de geração de relatórios e voltar a gerar a saída. Nesse caso, realmente não tem uma perda de dados, mesmo que o servidor de relatórios é atingido com um desastre. Como resultado, poderá ter um nível mais elevado de tolerância de perda de parte dos dados no servidor de relatórios. Nesse caso, as cópias de segurança menos freqüentes são uma opção para reduzir os custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: Problemas de dados de aplicativos de IaaS

Problemas de IaaS de dados de aplicativo são outra possibilidade. Considere um aplicativo que computa, mantém e serve dados comerciais críticos, como informações sobre preços. Uma nova versão do seu aplicativo tinha um bug de software que incorretamente calculados os preços e danificado os dados de comércio existentes fornecidos pela plataforma. Aqui, o melhor curso de ação é reverter para a versão anterior da aplicação e os dados. Para ativar esta opção, efetue cópias de segurança periódicas do seu sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação de desastres: Azure Backup 

[O Azure Backup](https://azure.microsoft.com/services/backup/) é utilizado para cópias de segurança e DR, e funciona com [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md) , bem como discos não geridos. Pode criar uma tarefa de cópia de segurança com cópias de segurança baseados no tempo, fácil restauro de VM e políticas de retenção de cópia de segurança.

Se usar [premium SSDs](../articles/virtual-machines/windows/disks-types.md), [discos geridos](../articles/virtual-machines/windows/managed-disks-overview.md), ou outros tipos de disco com o [armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) opção, que é especialmente importante tornar DR periódica de cópias de segurança. Cópia de segurança do Azure armazena os dados no seu Cofre dos serviços de recuperação para a retenção de longo prazo. Escolha o [armazenamento georredundante](../articles/storage/common/storage-redundancy-grs.md) Cofre de serviços a opção para a recuperação de cópia de segurança. Essa opção assegura que as cópias de segurança são replicadas numa região diferente do Azure para salvaguardar contra catástrofes regionais.

Para discos não geridos, pode usar o tipo de armazenamento localmente redundante para discos IaaS, mas certifique-se de que a cópia de segurança do Azure está ativada com a opção de armazenamento com redundância geográfica do Cofre de serviços de recuperação.

> [!NOTE]
> Se utilizar o [armazenamento georredundante](../articles/storage/common/storage-redundancy-grs.md) ou [armazenamento georredundante com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) opção para os discos não geridos, ainda precisa instantâneos consistentes com a cópia de segurança e DR. A utilização de uma [Azure Backup](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes com](#alternative-solution-consistent-snapshots).

 A tabela seguinte é um resumo das soluções disponíveis para a DR.

| Cenário | Replicação automática | Solução de DR |
| --- | --- | --- |
| Discos SSD Premium | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed disks | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos não geridos do armazenamento localmente redundante | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento com redundância geográfica não geridos | Entre regiões ([armazenamento georredundante](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes com](#alternative-solution-consistent-snapshots) |
| Discos de armazenamento georredundante de acesso de leitura não geridos | Entre regiões ([armazenamento georredundante com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes com](#alternative-solution-consistent-snapshots) |

Elevada disponibilidade melhor é cumprida ao utilizar discos geridos num conjunto, juntamente com o Azure Backup de disponibilidade. Se utilizar discos não geridos, pode utilizar o Azure Backup para o DR. Se não for possível utilizar a cópia de segurança do Azure, em seguida, tirar [instantâneos consistentes com](#alternative-solution-consistent-snapshots), conforme descrito na secção anterior, é uma solução alternativa para cópia de segurança e DR.

As opções para elevada disponibilidade, a cópia de segurança e DR em níveis de aplicação ou a infraestrutura podem ser representadas da seguinte forma:

| Nível |   Elevada disponibilidade   | Cópia de segurança ou DR |
| --- | --- | --- |
| Aplicação | SQL Server AlwaysOn | Azure Backup |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento georredundante com instantâneos consistentes com |

### <a name="using-azure-backup"></a>Utilizar o Azure Backup 

[O Azure Backup](../articles/backup/backup-azure-vms-introduction.md) pode criar cópias de segurança as suas VMs a executar o Windows ou Linux para a recuperação do Azure o Cofre de serviços. Fazer backup e restaurar dados críticos da empresa é complicado pelo fato de que os dados críticos da empresa tem uma cópia de segurança enquanto as aplicações que produzem os dados estiverem em execução. 

Para resolver este problema, o Azure Backup fornece cópias de segurança consistentes com aplicações para cargas de trabalho da Microsoft. Ele usa o serviço de cópias de sombra de volumes para se certificar de que a dados são escritos corretamente para o armazenamento. Para VMs do Linux, apenas as cópias de segurança consistente com ficheiros são possíveis, porque o Linux não tem uma funcionalidade equivalente para o serviço de cópias de sombra de volume.

![Fluxo de cópia de segurança do Azure][1]

Quando a cópia de segurança do Azure inicia uma tarefa de cópia de segurança na hora agendada, aciona a extensão de cópia de segurança instalada na VM para tirar um instantâneo de ponto no tempo. É criado um instantâneo em coordenação com o serviço de cópias de sombra de volume para obter um instantâneo consistente com os discos na máquina virtual sem ter de encerrá-lo. A extensão de cópia de segurança na VM descarrega todas as escritas antes de tirar um instantâneo consistente de todos os discos. Depois de criar o instantâneo, os dados são transferidos pela cópia de segurança do Azure para o Cofre de cópia de segurança. Para tornar o processo de cópia de segurança mais eficiente, o serviço identifica e transfere apenas os blocos de dados que tenham sido alterados desde a última cópia de segurança.

Para restaurar, pode ver as cópias de segurança disponíveis por meio de cópia de segurança do Azure e, em seguida, iniciar um restauro. Pode criar e restaurar cópias de segurança do Azure através da [portal do Azure](https://portal.azure.com/), ao [com o PowerShell](../articles/backup/backup-azure-vms-automation.md), ou utilizando o [da CLI do Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Passos para ativar uma cópia de segurança

Utilize os seguintes passos para ativar as cópias de segurança das suas VMs ao utilizar o [portal do Azure](https://portal.azure.com/). Há algumas variações dependendo do seu cenário exato. Consulte a [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) documentação para obter detalhes completos. Azure Backup também [suporta VMs com discos geridos](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Crie um cofre dos serviços de recuperação para uma VM:

    a. Na [portal do Azure](https://portal.azure.com/), procure **todos os recursos** e localize **cofres dos serviços de recuperação**.

    b. Sobre o **cofres dos serviços de recuperação** menu, clique em **Add** e siga os passos para criar um novo cofre na mesma região que a VM. Por exemplo, se a sua VM está na região E.U.A. oeste, escolha E.U.A. oeste para o cofre.

1.  Certifique-se a replicação de armazenamento para o Cofre recém-criado. Aceder ao Cofre sob **cofres dos serviços de recuperação** e aceda à **definições** > **configuração de cópia de segurança**. Certifique-se a **armazenamento georredundante** opção está selecionada por predefinição. Essa opção assegura que o Cofre é replicado automaticamente para um datacenter secundário. Por exemplo, o Cofre na região E.U.A. oeste é replicado automaticamente para E.U.A. Leste.

1.  Configurar a política de cópia de segurança e selecione a VM na mesma interface de Usuário.

1.  Certificar-se de que o agente de cópia de segurança está instalado na VM. Se a VM é criada utilizando uma imagem da galeria do Azure, o agente de cópia de segurança já está instalado. Caso contrário (ou seja, se utilizar uma imagem personalizada), utilize as instruções para [instalar o agente VM numa máquina virtual](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Certifique-se de que a VM possibilita a conectividade de rede para o serviço de cópia de segurança para a função. Siga as instruções para [conectividade de rede](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Depois de concluir os passos anteriores, a cópia de segurança é executado em intervalos regulares, conforme especificado na política de cópia de segurança. Se necessário, pode acionar a primeira cópia de segurança manualmente a partir de dashboard do cofre, no portal do Azure.

Para automatizar a cópia de segurança do Azure com scripts, consulte [cmdlets do PowerShell para cópia de segurança VM](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Passos para a recuperação

Se precisar de reparar ou recriar uma VM, pode restaurar a VM a partir de qualquer um dos pontos de recuperação de cópia de segurança no cofre. Existem algumas opções diferentes para efetuar a recuperação:

-   Pode criar uma nova VM como uma representação de ponto no tempo da sua VM de cópia de segurança.

-   Pode restaurar os discos e, em seguida, utilize o modelo para a VM para personalizar e recriar a VM restaurada.

Para obter mais informações, consulte as instruções para [utilizar o portal do Azure para restaurar máquinas virtuais](../articles/backup/backup-azure-arm-restore-vms.md). Este documento também explica os passos específicos para restaurar uma cópia de segurança de VMs num Datacenter emparelhado com o seu Cofre de cópia de segurança georredundante se ocorrer um desastre no datacenter principal. Nesse caso, o Azure Backup utiliza o serviço de computação da região secundária para criar a máquina virtual restaurada.

Também pode utilizar o PowerShell para o [criar uma nova VM a partir de restaurar discos](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: Instantâneos consistentes com

Se não for possível utilizar a cópia de segurança do Azure, pode implementar seu próprio mecanismo de cópia de segurança através de instantâneos. Criação de instantâneos consistentes para todos os discos utilizados por uma VM e, em seguida, replicar esses instantâneos para outra região é complicado. Por esse motivo, o Azure considera a utilizar o serviço de cópia de segurança como uma opção melhor que a criação de uma solução personalizada.

Se utilizar o armazenamento georredundante armazenamento/georredundante de acesso de leitura para os discos, os instantâneos são replicados automaticamente para um datacenter secundário. Se utilizar o armazenamento localmente redundante de discos, terá de replicar os dados por conta própria. Para obter mais informações, consulte [cópia de segurança de discos VM não geridos do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto num ponto específico no tempo. Um instantâneo está sujeita à faturação para o tamanho incremental dos dados retenções. Para obter mais informações, consulte [criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Criar instantâneos, enquanto a VM está em execução

Embora pode tirar um instantâneo em qualquer altura, se a VM está em execução, ainda há a ser transmitidos para os discos de dados. Os instantâneos podem conter operações parciais que estavam em trânsito. Além disso, se existirem vários discos envolvidos, os instantâneos de discos diferentes possam ter ocorrido em momentos diferentes. Estes cenários podem fazer com que os instantâneos para ser coordenados. Essa falta de ordination conjunta é especialmente problemática para os volumes repartidos cujos ficheiros podem ser corrompidos se as alterações foram feitas durante a cópia de segurança.

Para evitar esta situação, o processo de cópia de segurança tem de implementar os seguintes passos:

1.  Congelar todos os discos.

1.  Esvaziar todas as gravações pendentes.

1.  [Criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md) para todos os discos.

Alguns aplicativos do Windows, como o SQL Server, fornecem um mecanismo de cópia de segurança coordenado através de um serviço de cópias de sombra de volume para criar cópias de segurança consistentes com aplicações. No Linux, pode utilizar uma ferramenta como o *fsfreeze* para coordenar os discos. Essa ferramenta fornece cópias de segurança consistente com ficheiros, mas não consistentes instantâneos. Este processo é complexo, portanto, deve considerar o uso [cópia de segurança do Azure](../articles/backup/backup-azure-vms-introduction.md) ou uma solução de cópia de segurança de terceiros que já tiver implementado Este procedimento.

O processo anterior resulta numa coleção de instantâneos coordenadas para todos os discos VM, que representa uma ponto anterior no tempo de vista específico da VM. Este é um ponto de restauro de cópia de segurança para a VM. Pode repetir o processo em intervalos agendados para criar cópias de segurança periódicas. Ver [copiar as cópias de segurança para outra região](#copy-the-snapshots-to-another-region) para obter os passos copiar os instantâneos para outra região para a DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Criar instantâneos, enquanto a VM estiver offline

Outra opção para criar cópias de segurança é para encerrar a VM e criar instantâneos de blob de cada disco. Instantâneos de blob é mais fácil do que a coordenação de instantâneos de uma VM em execução, mas requer alguns minutos de tempo de inatividade.

1. Encerre a VM.

1. Crie um instantâneo de cada blob de disco rígido virtual, o que leva apenas alguns segundos.

    Para criar um instantâneo, pode usar [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), o [API de REST de armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), [da CLI do Azure](/cli/azure/), ou uma das bibliotecas de cliente do armazenamento do Azure, tais como [o Biblioteca de cliente de armazenamento para .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Inicie a VM, que termina o período de indisponibilidade. Normalmente, todo o processo é concluído dentro de alguns minutos.

Este processo gera uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauro de cópia de segurança para a VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copie os instantâneos para outra região

Criação de instantâneos só pode não ser suficiente para a DR. Também têm de replicar as cópias de segurança do instantâneo para outra região.

Se utilizar o armazenamento georredundante ou armazenamento georredundante com acesso de leitura para os discos, em seguida, os instantâneos são replicados automaticamente para a região secundária. Pode haver alguns minutos de atraso antes da replicação. Se o datacenter primário ficar inativo antes dos instantâneos de concluir a replicação, não é possível aceder aos instantâneos entre o datacenter secundário. A probabilidade de isso é pequena.

> [!NOTE]
> Apenas ter discos num armazenamento georredundante ou acesso de leitura georredundante conta de armazenamento não protege a VM de desastres. Também tem de criar instantâneos de coordenada ou utilizar o Azure Backup. Isto é necessário para recuperar uma VM para um estado consistente.

Se utilizar o armazenamento localmente redundante, tem de copiar os instantâneos para uma conta de armazenamento diferentes imediatamente depois de criar o instantâneo. O destino de cópia pode ser uma conta de armazenamento localmente redundante numa região diferente, resultando na cópia a ser numa região remota. Também pode copiar o instantâneo para uma conta de armazenamento georredundante de acesso de leitura na mesma região. Neste caso, o instantâneo ociosamente é replicado para a região secundária remota. A cópia de segurança está protegida contra catástrofes no site primário depois do copiar e replicação esteja concluída.

Para copiar os instantâneos incrementais com eficiência para DR, reveja as instruções em [cópia de segurança do Azure discos VM não geridos com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

![Criar cópias de segurança do Azure discos VM não geridos com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação a partir de instantâneos

Para obter um instantâneo, copiá-lo para criar um blob de novo. Se estiver a copiar o instantâneo da conta principal, pode copiar o instantâneo ao longo para o blob de base do instantâneo. Este processo reverte o disco para o instantâneo. Este processo é conhecido como promover o instantâneo. Se estiver a copiar a cópia de segurança do instantâneo de uma conta secundária, no caso de uma conta de armazenamento georredundante de acesso de leitura, tem de o copiar para uma conta principal. Pode copiar um instantâneo por [com o PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou utilizando o utilitário AzCopy. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Para VMs com vários discos, tem de copiar todos os instantâneos que fazem parte do mesmo ponto de restauro coordenada. Depois de copiar os instantâneos para blobs de VHD graváveis, pode utilizar os blobs para recriar a VM com o modelo para a VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

SQL Server em execução numa VM possui seus próprio capacidades incorporadas para cópia de segurança de base de dados do SQL Server para armazenamento de Blobs do Azure ou um ficheiro de partilhar. Se a conta de armazenamento é armazenamento georredundante ou armazenamento georredundante com acesso de leitura, pode acessar essas cópias de segurança no Centro de dados secundária da conta de armazenamento em caso de desastre, com as mesmas restrições conforme discutidas anteriormente. Para obter mais informações, consulte [cópia de segurança e restauro para o SQL Server em máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Além disso, para criar cópias de segurança e restaurar, [grupos de Disponibilidade AlwaysOn do SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) pode manter as réplicas secundárias de bases de dados. Esta capacidade reduz significativamente o tempo de recuperação após desastre.

## <a name="other-considerations"></a>Outras considerações

Este artigo discutiu como fazer cópias de segurança ou tirar instantâneos das suas VMs e seus discos para suportar a recuperação após desastre e como usar essas cópias de segurança ou instantâneos para recuperar os dados. Com o modelo Azure Resource Manager, muitas pessoas usam modelos para criar as suas VMs e outras infraestruturas no Azure. Pode utilizar um modelo para criar uma VM que tenha a mesma configuração de cada vez. Se utilizar imagens personalizadas para criar as suas VMs, também deve certificar-se de que suas imagens estão protegidas com uma conta de armazenamento georredundante de acesso de leitura para armazená-las.

Conseqüentemente, o processo de cópia de segurança pode ser uma combinação das duas coisas:

- Fazer backup dos dados (discos).
- Criar cópias de segurança da configuração (modelos e imagens personalizadas).

Consoante a opção de cópia de segurança que escolher, poderá ter de lidar com a cópia de segurança dos dados e a configuração ou o serviço de cópia de segurança poderá lidar com tudo isso para.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: Compreender o impacto da redundância de dados

Para contas de armazenamento no Azure, existem três tipos de redundância de dados que deve considerar em relação a recuperação após desastre: localmente redundante, georredundante ou georredundante com acesso de leitura. 

Armazenamento localmente redundante mantém três cópias dos dados no mesmo datacenter. Quando a VM escreve os dados, todos os três cópias são atualizadas antes de sucesso é retornado ao chamador, para que saiba que são idênticos. O disco está protegido contra falhas de locais, porque é improvável que todas as três cópias são afetadas ao mesmo tempo. No caso do armazenamento localmente redundante, não existe nenhuma redundância geográfica, para que o disco não está protegido contra falhas catastróficas que podem afetar uma unidade de armazenamento ou datacenter inteira.

Com o armazenamento georredundante e o armazenamento georredundante com acesso de leitura, três cópias dos seus dados são mantidas na região primária, que está selecionado por si. Mais três cópias dos seus dados são mantidas numa região secundária correspondente, que é definido pelo Azure. Por exemplo, se armazenar dados na região E.U.A. oeste, os dados são replicados para E.U.A. Leste. Retenção de cópia é feita de forma assíncrona, e há um pequeno atraso entre as atualizações para os sites primários e secundários. Réplicas dos discos no site secundário são consistentes numa base por disco (com o atraso), mas podem não estar em sincronia com as outras réplicas dos vários discos de Active Directory. Para que as réplicas consistentes em vários discos, são necessários os instantâneos consistentes com.

A principal diferença entre o armazenamento georredundante e o armazenamento georredundante com acesso de leitura é que, com o armazenamento georredundante de acesso de leitura, pode ler a cópia secundária em qualquer altura. Se houver um problema que processa os dados na região primária inacessível, a equipa do Azure faz com que todos os esforços para restaurar o acesso. Enquanto o principal estiver inativo, se tiver o armazenamento georredundante de acesso de leitura ativado, pode aceder aos dados no datacenter secundário. Por conseguinte, se pretender ler a partir da réplica enquanto o primário não estiver acessível, em seguida, armazenamento georredundante com acesso de leitura deve ser considerado.

Se acaba por ser uma falha significativa, a equipa do Azure pode acionar uma ativação pós-falha geográfica e alterar as entradas de DNS primárias para apontar para o armazenamento secundário. Neste momento, se tiver o armazenamento georredundante ou armazenamento georredundante de acesso de leitura ativado, pode acessar os dados na região que costumavam ser secundário. Em outras palavras, se a sua conta de armazenamento é armazenamento georredundante e existe um problema, pode acessar o armazenamento secundário apenas se houver uma ativação pós-falha geográfica.

Para obter mais informações, consulte [o que fazer se ocorrer uma falha de armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>A Microsoft controla se ocorrer uma ativação pós-falha. Ativação pós-falha não é controlado por conta de armazenamento, pelo que não é a ser decidida pelos clientes individuais. Para implementar a recuperação após desastre para as contas de armazenamento específicas ou discos da máquina virtual, tem de utilizar as técnicas descritas anteriormente neste artigo.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png