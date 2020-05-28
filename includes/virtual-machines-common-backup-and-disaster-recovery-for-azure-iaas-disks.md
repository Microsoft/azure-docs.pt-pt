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
ms.openlocfilehash: 6981b6acaf0281c1643e2d8ac3933e0fa892e3c2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84124457"
---
Este artigo explica como planear a recuperação de backup e desastres (DR) de máquinas virtuais IaaS (VMs) e discos em Azure. Este documento abrange discos geridos e não geridos.

Primeiro, cobrimos as capacidades de tolerância a falhas incorporadas na plataforma Azure que ajuda a proteger-se contra falhas locais. Discutimos então os cenários de catástrofe que não estão totalmente cobertos pelas capacidades incorporadas. Também mostramos vários exemplos de cenários de carga de trabalho onde diferentes considerações de backup e DR podem ser aplicadas. Em seguida, revemos possíveis soluções para os discos DR de IaaS.

## <a name="introduction"></a>Introdução

A plataforma Azure utiliza vários métodos para redundância e tolerância a falhas para ajudar a proteger os clientes de falhas de hardware localizadas. Falhas locais podem incluir problemas com uma máquina de servidor de armazenamento Azure que armazena parte dos dados para um disco virtual ou falhas de um SSD ou HDD nesse servidor. Tais falhas isoladas no componente de hardware podem ocorrer durante as operações normais.

A plataforma Azure foi concebida para ser resiliente a estas falhas. Grandes desastres podem resultar em falhas ou na inacessibilidade de muitos servidores de armazenamento ou até mesmo de um centro de dados inteiro. Embora os seus VMs e discos estejam normalmente protegidos contra falhas localizadas, são necessários passos adicionais para proteger a sua carga de trabalho de falhas catastróficas em toda a região, como um grande desastre, que pode afetar o seu VM e discos.

Além da possibilidade de falhas na plataforma, podem ocorrer problemas com uma aplicação do cliente ou dados. Por exemplo, uma nova versão da sua aplicação pode inadvertidamente alterar os dados que a fazem quebrar. Nesse caso, é possível reverter a aplicação e os dados para uma versão anterior que contenha o último bom estado conhecido. Isto requer a manutenção de backups regulares.

Para a recuperação regional de desastres, tem de apoiar os seus discos VM IaaS para outra região.

Antes de analisarmos as opções de backup e DR, vamos recapitular alguns métodos disponíveis para lidar com falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência azure IaaS

*A resiliência* refere-se à tolerância para falhas normais que ocorrem em componentes de hardware. Resiliência é a capacidade de recuperar de falhas e continuar a funcionar. Não é uma questão de evitar falhas, mas de responder às falhas de forma a evitar períodos de indisponibilidade ou a perda de dados. O objetivo da resiliência é fazer com que a aplicação volte para um estado totalmente funcional após uma falha. Máquinas e discos virtuais azure são projetados para serem resistentes a falhas comuns de hardware. Vejamos como a plataforma Azure IaaS proporciona esta resiliência.

Uma máquina virtual é constituída principalmente por duas partes: um servidor de computação e os discos persistentes. Ambos afetam a tolerância à falha de uma máquina virtual.

Se o servidor de anfitriões da computação Azure que alberga o seu VM experimentar uma falha de hardware, o que é raro, o Azure foi projetado para restaurar automaticamente o VM noutro servidor. Se este cenário, o seu computador reiniciar e o VM voltar depois de algum tempo. O Azure deteta automaticamente tais falhas de hardware e executa recuperações para ajudar a garantir que o VM do cliente está disponível o mais rapidamente possível.

No que diz respeito aos discos IaaS, a durabilidade dos dados é fundamental para uma plataforma de armazenamento persistente. Os clientes azure têm importantes aplicações empresariais em iaaS, e dependem da persistência dos dados. O Azure projeta proteção para estes discos IaaS, com três cópias redundantes dos dados que são armazenados localmente. Estas cópias prevêem uma elevada durabilidade contra falhas locais. Se um dos componentes de hardware que detém o disco falhar, o seu VM não é afetado, porque existem duas cópias adicionais para suportar pedidos de disco. Funciona bem, mesmo que dois componentes de hardware diferentes que suportam um disco falhem ao mesmo tempo (o que é raro). 

Para garantir que mantém sempre três réplicas, o Azure Storage gera automaticamente uma nova cópia dos dados em segundo plano se uma das três cópias ficar indisponível. Por conseguinte, não deve ser necessário utilizar raid com discos Azure para tolerância a falhas. Uma configuração simples do RAID 0 deve ser suficiente para despir os discos, se necessário, para criar volumes maiores.

Devido a esta arquitetura, o Azure tem consistentemente proporcionado durabilidade de nível empresarial para discos IaaS, com uma taxa de [insucesso anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate)líder da indústria de zero por cento.

Falhas de hardware localizadas no anfitrião da computação ou na plataforma de Armazenamento podem por vezes resultar na indisponibilidade temporária do VM que é coberto pelo [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para disponibilidade de VM. A Azure também fornece um SLA líder do setor para instâncias VM únicas que usam SSDs premium Azure.

Para salvaguardar as cargas horárias de aplicação a partir do tempo de inatividade devido à indisponibilidade temporária de um disco ou VM, os clientes podem utilizar [conjuntos](../articles/virtual-machines/windows/manage-availability.md)de disponibilidade . Duas ou mais máquinas virtuais num conjunto de disponibilidade fornecem redundância para a aplicação. O Azure cria então estes VMs e discos em domínios de falha separados com diferentes componentes de potência, rede e servidor.

Devido a estes domínios de falha separados, falhas de hardware localizadas normalmente não afetam vários VMs no conjunto ao mesmo tempo. Ter domínios de avaria separados proporciona uma elevada disponibilidade para a sua aplicação. É considerado uma boa prática usar conjuntos de disponibilidade quando é necessária alta disponibilidade. A secção seguinte cobre o aspeto da recuperação de desastres.

### <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres

Recuperação de desastres é a capacidade de recuperar de raros, mas importantes, incidentes. Estes incidentes incluem falhas não transitórias e de grande escala, tais como perturbações de serviço que afetam toda uma região. A recuperação de desastres inclui cópiade segurança de dados e arquivamento, e pode incluir intervenção manual, como restaurar uma base de dados de uma cópia de segurança.

A proteção incorporada da plataforma Azure contra falhas localizadas pode não proteger totalmente os VMs/discos se uma grande catástrofe causar interrupções em larga escala. Estas interrupções em larga escala incluem eventos catastróficos, como se um datacenter fosse atingido por um furacão, terramoto, incêndio, ou se houver uma falha na unidade de hardware em larga escala. Além disso, poderá encontrar falhas devido a problemas de aplicação ou dados.

Para ajudar a proteger as suas cargas de trabalho iaaS de interrupções, deve planear a redundância e ter backups para permitir a recuperação. Para a recuperação de desastres, deve recuar num local geográfico diferente, longe do local principal. Esta abordagem ajuda a garantir que o seu backup não seja afetado pelo mesmo evento que afetou originalmente o VM ou os discos. Para mais informações, consulte a recuperação de [desastres para aplicações Azure.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

As suas considerações de DR podem incluir os seguintes aspetos:

- Elevada disponibilidade: A capacidade da aplicação para continuar a funcionar em estado saudável, sem tempo de inatividade significativo. Por *um estado saudável,* este estado significa que a aplicação é responsiva, e os utilizadores podem conectar-se à aplicação e interagir com ela. Algumas aplicações e bases de dados críticas da missão podem ser necessárias para estar sempre disponível, mesmo quando há falhas na plataforma. Para estas cargas de trabalho, poderá ter de planear o despedimento para a aplicação, bem como os dados.

- Durabilidade dos dados: Em alguns casos, a principal consideração é garantir que os dados são preservados se um desastre acontecer. Portanto, pode precisar de uma cópia de segurança dos seus dados num site diferente. Para tais cargas de trabalho, pode não precisar de redundância total para a aplicação, mas apenas uma cópia de segurança regular dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de backup e DR

Vejamos alguns exemplos típicos de cenários de carga de trabalho de aplicação e as considerações para o planeamento da recuperação de desastres.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: Principais soluções de base de dados

Considere um servidor de base de dados de produção, como o SQL Server ou o Oracle, que pode suportar uma elevada disponibilidade. Aplicações críticas de produção e utilizadores dependem desta base de dados. O plano de recuperação de desastres deste sistema poderá ter de suportar os seguintes requisitos:

- Os dados devem ser protegidos e recuperáveis.
- O servidor deve estar disponível para utilização.

O plano de recuperação de desastres pode exigir a manutenção de uma réplica da base de dados numa região diferente como um backup. Dependendo dos requisitos para a disponibilidade do servidor e recuperação de dados, a solução pode variar de um site de réplica ativa ativa ou passivo ativo até backups offline periódicos dos dados. Bases de dados relacionais, como o SQL Server e o Oracle, fornecem várias opções para a replicação. Para o Servidor SQL, utilize grupos de disponibilidade alwayson do [servidor SQL](https://msdn.microsoft.com/library/hh510230.aspx) para uma elevada disponibilidade.

As bases de dados NoSQL, como a MongoDB, também suportam [réplicas](https://docs.mongodb.com/manual/replication/) para despedimento. As réplicas para alta disponibilidade são usadas.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: Um conjunto de VMs redundantes

Considere uma carga de trabalho tratada por um conjunto de VMs que proporcionam redundância e equilíbrio de carga. Um exemplo é um aglomerado de Cassandra implantado numa região. Este tipo de arquitetura já proporciona um elevado nível de redundância naquela região. No entanto, para proteger a carga de trabalho de uma falha a nível regional, deve considerar a propagação do cluster por duas regiões ou fazer backups periódicos para outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: Carga de trabalho de aplicação iaaS

Vamos ver a carga de trabalho da aplicação IaaS. Por exemplo, esta aplicação pode ser uma carga de trabalho de produção típica em funcionamento num VM Azure. Pode ser um servidor web ou servidor de ficheiros que mantém o conteúdo e outros recursos de um site. Pode também ser uma aplicação de negócio saqueada sob medida num VM que armazenou os seus dados, recursos e estado de aplicação nos discos VM. Neste caso, é importante certificar-se de que aceita backups regularmente. A frequência de reserva deve basear-se na natureza da carga de trabalho vm. Por exemplo, se a aplicação for executado todos os dias e modificar os dados, então a cópia de segurança deve ser tomada a cada hora.

Outro exemplo é um servidor de relatórios que recolhe dados de outras fontes e gera relatórios agregados. A perda deste VM ou discos pode levar à perda dos relatórios. No entanto, pode ser possível reexecutar o processo de reporte e regenerar a produção. Nesse caso, não se tem uma perda de dados, mesmo que o servidor de relatórios seja atingido por um desastre. Como resultado, pode ter um nível mais elevado de tolerância para perder parte dos dados no servidor de relatórios. Nesse caso, os backups menos frequentes são uma opção para reduzir custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: Problemas de dados de aplicação iaaS

As questões de dados de aplicação iaaS são outra possibilidade. Considere uma aplicação que computa, mantém e serve dados comerciais críticos, como informação sobre preços. Uma nova versão da sua aplicação tinha um bug de software que calculava incorretamente os preços e corrompeu os dados de comércio existentes servidos pela plataforma. Aqui, o melhor caminho é reverter para a versão anterior da aplicação e dos dados. Para o permitir, leve cópias de segurança periódicas do seu sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação de desastres: Azure Backup 

[O Azure Backup](https://azure.microsoft.com/services/backup/) é utilizado para backups e DR, e funciona com [discos geridos,](../articles/virtual-machines/windows/managed-disks-overview.md) bem como discos não geridos. Você pode criar um trabalho de backup com backups baseados no tempo, restauração vm fácil e políticas de retenção de backup.

Se utilizar [SSDs premium,](../articles/virtual-machines/windows/disks-types.md) [discos geridos,](../articles/virtual-machines/windows/managed-disks-overview.md)ou outros tipos de discos com a opção de [armazenamento localmente redundante,](../articles/storage/common/storage-redundancy-lrs.md) é especialmente importante fazer backups de DR periódicos. A Azure Backup armazena os dados no cofre dos seus serviços de recuperação para retenção a longo prazo. Escolha a opção [de armazenamento geo-redundante](../articles/storage/common/storage-redundancy-grs.md) para o cofre de serviços de recuperação de cópias de segurança. Esta opção garante que os backups sejam replicados numa região de Azure diferente para salvaguardar as catástrofes regionais.

Para discos não geridos, pode utilizar o tipo de armazenamento localmente redundante para discos IaaS, mas certifique-se de que o Azure Backup está ativado com a opção de armazenamento georedundant para o cofre dos serviços de recuperação.

> [!NOTE]
> Se utilizar a opção de [armazenamento georedundant](../articles/storage/common/storage-redundancy-grs.md) ou de [armazenamento de acesso](../articles/storage/common/storage-redundancy.md) de leitura para os seus discos não geridos, ainda precisa de instantâneos consistentes para backup e DR. Utilize cópias [de segurança Azure](https://azure.microsoft.com/services/backup/) ou [imagens consistentes](#alternative-solution-consistent-snapshots).

 A tabela a seguir é um resumo das soluções disponíveis para DR.

| Cenário | Replicação automática | Solução DR |
| --- | --- | --- |
| Discos SSD Premium | Local[(armazenamento localmente redundante)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed disks | Local[(armazenamento localmente redundante)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento não geridos localmente redundantes | Local[(armazenamento localmente redundante)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento geo-redundantes não geridos | Região transversal[(armazenamento geo-redundante)](../articles/storage/common/storage-redundancy-grs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| Discos de armazenamento georedundantes de acesso de leitura não geridos | Região transversal[(armazenamento geo-redundante de acesso de leitura)](../articles/storage/common/storage-redundancy.md) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

A elevada disponibilidade é melhor satisfeita utilizando discos geridos num conjunto de disponibilidade, juntamente com o Azure Backup. Se utilizar discos não geridos, ainda pode utilizar o Azure Backup para DR. Se não conseguir utilizar o Azure Backup, então tirar [fotografias consistentes,](#alternative-solution-consistent-snapshots)como descrito numa secção posterior, é uma solução alternativa para backup e DR.

As suas escolhas para alta disponibilidade, backup e DR a níveis de aplicação ou infraestrutura podem ser representadas da seguinte forma:

| Nível |   Elevada disponibilidade   | Backup ou DR |
| --- | --- | --- |
| Aplicação | SQL Server AlwaysOn | Azure Backup |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento geo-redundante com instantâneos consistentes |

### <a name="using-azure-backup"></a>Utilizar o Azure Backup 

O [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) pode fazer backup dos seus VMs com windows ou Linux para o cofre de serviços de recuperação Azure. O backup e a restauração de dados críticos do negócio é complicado pelo facto de os dados críticos do negócio terem de ser apoiados enquanto as aplicações que produzem os dados estão em execução. 

Para resolver este problema, o Azure Backup fornece cópias de segurança consistentes com aplicações para cargas de trabalho da Microsoft. Utiliza o serviço de sombra de volume para garantir que os dados são corretamente escritos para armazenamento. Para os VMs Linux, o modo de consistência de backup predefinido é de cópias de segurança consistentes com ficheiros, uma vez que o Linux não possui funcionalidade equivalente ao serviço de sombra de volume como no caso do Windows. Para as máquinas Linux, consulte [backup consistente com aplicações de VMs Azure Linux](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Fluxo de backup azure][1]

Quando o Azure Backup inicia uma função de backup na hora programada, aciona a extensão de reserva instalada no VM para tirar uma foto pontual. Uma imagem instantânea é tirada em coordenação com o serviço de sombra de volume para obter uma imagem consistente dos discos na máquina virtual sem ter que desligá-lo. A extensão de reserva no VM descarrega todas as escritas antes de tirar uma foto consistente de todos os discos. Depois de tirar a foto, os dados são transferidos pela Azure Backup para o cofre de reserva. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que mudaram após a última cópia de segurança.

Para restaurar, pode ver as cópias de segurança disponíveis através do Azure Backup e, em seguida, iniciar um restauro. Pode criar e restaurar as cópias de segurança Azure através do [portal Azure,](https://portal.azure.com/)utilizando o [PowerShell,](../articles/backup/backup-azure-vms-automation.md)ou utilizando o [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Passos para permitir uma cópia de segurança

Utilize os seguintes passos para ativar as cópias de segurança dos seus VMs utilizando o [portal Azure](https://portal.azure.com/). Há alguma variação dependendo do seu cenário exato. Consulte a documentação de [Backup Azure](../articles/backup/backup-azure-vms-introduction.md) para obter todos os detalhes. O Azure Backup também [suporta VMs com discos geridos](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Crie um cofre de serviços de recuperação para um VM:

    a. No [portal Azure,](https://portal.azure.com/)navegue por **todos os recursos** e encontre cofres de Serviços de **Recuperação.**

    b. No menu de cofres dos Serviços de **Recuperação,** clique em **Adicionar** e siga os passos para criar um novo cofre na mesma região que o VM. Por exemplo, se o seu VM estiver na região oeste dos EUA, escolha os EUA Ocidentais para o cofre.

1.  Verifique a replicação do armazenamento para o cofre recém-criado. Aceda ao cofre sob **cofres de Serviços** de Recuperação e vá para **a**  >  **Atualização**de Configuração de Backup  >  **Update**Properties . Certifique-se de que a opção **de armazenamento geo-redundante** é selecionada por padrão. Esta opção garante que o seu cofre é automaticamente replicado para um centro de dados secundário. Por exemplo, o seu cofre nos EUA Ocidentais é automaticamente replicado para os EUA Orientais.

1.  Configure a política de backup e selecione o VM a partir do mesmo UI.

1.  Certifique-se de que o Agente de Reserva está instalado no VM. Se o seu VM for criado utilizando uma imagem de galeria Azure, então o Agente de Backup já está instalado. Caso contrário (isto é, se utilizar uma imagem personalizada), utilize as instruções para [instalar o agente VM numa máquina virtual](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Após a conclusão dos passos anteriores, a cópia de segurança é executado em intervalos regulares, conforme especificado na política de backup. Se necessário, pode acionar a primeira cópia manual do painel de instrumentos do cofre no portal Azure.

Para automatizar o Backup Azure utilizando scripts, consulte os [cmdlets PowerShell para backup VM](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Passos para a recuperação

Se precisar de reparar ou reconstruir um VM, pode restaurar o VM de qualquer um dos pontos de recuperação de reserva no cofre. Existem algumas opções diferentes para realizar a recuperação:

-   Você pode criar um novo VM como uma representação ponto-a-tempo do seu VM apoiado.

-   Pode restaurar os discos e, em seguida, usar o modelo para o VM personalizar e reconstruir o VM restaurado.

Para mais informações, consulte as instruções para [utilizar o portal Azure para restaurar as máquinas virtuais](../articles/backup/backup-azure-arm-restore-vms.md). Este documento também explica os passos específicos para restaurar os VMs apoiados num centro de dados emparelhado, utilizando o seu cofre de reserva geo-redundante se houver um desastre no centro de dados primário. Nesse caso, o Azure Backup utiliza o serviço Compute da região secundária para criar a máquina virtual restaurada.

Também pode utilizar o PowerShell para criar um novo VM a [partir de discos restaurados.](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: Instantâneos consistentes

Se não conseguir utilizar o Azure Backup, pode implementar o seu próprio mecanismo de backup utilizando instantâneos. Criar instantâneos consistentes para todos os discos utilizados por um VM e, em seguida, replicar esses instantâneos para outra região é complicado. Por esta razão, o Azure considera a utilização do serviço De backup como uma melhor opção do que construir uma solução personalizada.

Se utilizar armazenamento geo-redundante/armazenamento geo-redundante de acesso de leitura para discos, as imagens são automaticamente replicadas para um centro de dados secundário. Se utilizar armazenamento localmente redundante para discos, terá de replicar os dados por si mesmo. Para mais informações, consulte discos VM não [geridos azure com imagens incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto em um ponto específico no tempo. Um instantâneo incorre na faturação para o tamanho incremental dos dados que detém. Para mais informações, consulte [Criar uma imagem de blob](../articles/storage/blobs/storage-blob-snapshots.md)snapshot .

### <a name="create-snapshots-while-the-vm-is-running"></a>Crie instantâneos enquanto o VM está em execução

Embora possa tirar uma fotografia a qualquer momento, se o VM estiver em execução, ainda há dados a serem transmitidos para os discos. As fotos podem conter operações parciais que estavam em voo. Além disso, se houver vários discos envolvidos, as imagens de diferentes discos podem ter ocorrido em diferentes momentos. Estes cenários podem fazer com que as imagens sejam descoordenadas. Esta falta de coordenação é especialmente problemática para volumes listrados cujos ficheiros podem ser corrompidos se forem feitas alterações durante o backup.

Para evitar esta situação, o processo de backup deve implementar os seguintes passos:

1.  Congele todos os discos.

1.  Lave todos os escritos pendentes.

1.  [Crie uma imagem blob](../articles/storage/blobs/storage-blob-snapshots.md) para todos os discos.

Algumas aplicações do Windows, como o SQL Server, fornecem um mecanismo de backup coordenado através de um serviço de sombra de volume para criar backups consistentes com aplicações. No Linux, pode utilizar uma ferramenta como *o fsfreeze* para coordenar os discos. Esta ferramenta fornece cópias de segurança consistentes com ficheiros, mas não imagens consistentes com aplicações. Este processo é complexo, pelo que deve considerar a utilização do [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) ou uma solução de backup de terceiros que já implementa este procedimento.

O processo anterior resulta numa recolha de instantâneos coordenados para todos os discos VM, representando uma visão específica ponto-a-tempo do VM. Este é um ponto de restauro de reserva para o VM. Pode repetir o processo em intervalos programados para criar cópias de segurança periódicas. Consulte [copiar as cópias para outra região](#copy-the-snapshots-to-another-region) para obter medidas para copiar os instantâneos para outra região para DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Crie instantâneos enquanto o VM está offline

Outra opção para criar backups consistentes é desligar o VM e tirar fotos blob de cada disco. Tirar fotos blob é mais fácil do que coordenar instantâneos de um VM em execução, mas requer alguns minutos de tempo de inatividade.

1. Desligue o VM.

1. Crie uma imagem de cada bolha virtual de disco rígido, que leva apenas alguns segundos.

    Para criar um instantâneo, pode utilizar o [PowerShell,](../articles/storage/common/storage-powershell-guide-full.md)o [Azure Storage REST API,](https://msdn.microsoft.com/library/azure/ee691971.aspx) [o Azure CLI](/cli/azure/)ou uma das bibliotecas de clientes do Azure Storage, como [a biblioteca de clientes de Armazenamento para .NET.](https://msdn.microsoft.com/library/azure/hh488361.aspx)

1. Inicie o VM, que termina o tempo de inatividade. Normalmente, todo o processo termina em poucos minutos.

Este processo produz uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauro de reserva para o VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copie os instantâneos para outra região

A criação dos instantâneos por si só pode não ser suficiente para o DR. Também deve replicar as cópias de segurança instantâneas para outra região.

Se utilizar armazenamento geo-redundante ou armazenamento geo-redundante de acesso de leitura para os seus discos, então os instantâneos são replicados automaticamente para a região secundária. Pode haver alguns minutos de atraso antes da replicação. Se o centro de dados primário descer antes de as imagens terminarem a replicação, não pode aceder às imagens do centro de dados secundário. A probabilidade disto é pequena.

> [!NOTE]
> Só ter os discos numa conta de armazenamento georedundant e de acesso a leitura não protege o VM de desastres. Também deve criar instantâneos coordenados ou utilizar o Azure Backup. Isto é necessário para recuperar um VM para um estado consistente.

Se utilizar armazenamento redundante localmente, deve copiar as imagens para uma conta de armazenamento diferente imediatamente após a criação do instantâneo. O alvo da cópia pode ser uma conta de armazenamento localmente redundante numa região diferente, resultando na cópia estar numa região remota. Também pode copiar o instantâneo para uma conta de armazenamento georedundant de acesso de leitura na mesma região. Neste caso, o instantâneo é preguiçosamente replicado para a região secundária remota. A sua cópia de segurança está protegida contra desastres no local primário após a cópia e replicação estar completa.

Para copiar as suas imagens incrementais para DR de forma eficiente, reveja as instruções em discos VM não [geridos Back up Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

![Back up Discos VM não geridos Azure com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação de instantâneos

Para recuperar uma foto, copie-a para fazer uma nova bolha. Se estiver a copiar o instantâneo da conta primária, pode copiar o instantâneo para a bolha base do instantâneo. Este processo reverte o disco para o instantâneo. Este processo é conhecido como promover o instantâneo. Se estiver a copiar o backup instantâneo de uma conta secundária, no caso de uma conta de armazenamento georedundant de acesso de leitura, deve copiá-la para uma conta primária. Pode copiar uma fotografia utilizando o [PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou utilizando o utilitário AzCopy. Para mais informações, consulte os [dados de transferência com o utilitário da linha de comando AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Para VMs com vários discos, você deve copiar todos os instantâneos que fazem parte do mesmo ponto de restauro coordenado. Depois de copiar as imagens para bolhas VHD writáveis, pode utilizar as bolhas para recriar o seu VM utilizando o modelo para o VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

O SQL Server em execução num VM tem as suas próprias capacidades incorporadas para fazer o seu próprio serviço de base de dados do SQL Server para o armazenamento do Azure Blob ou uma partilha de ficheiros. Se a conta de armazenamento for armazenamento georedundant ou armazenamento geo-redundante de acesso de leitura, pode aceder a essas cópias de segurança no centro de dados secundário da conta de armazenamento em caso de desastre, com as mesmas restrições que anteriormente discutidas. Para mais informações, consulte [o Back up e restaure para o SQL Server em máquinas virtuais Azure](../articles/azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Além de fazer cópias de segurança e restauro, os grupos de [disponibilidade do SQL Server AlwaysOn](../articles/azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) podem manter réplicas secundárias de bases de dados. Esta capacidade reduz consideravelmente o tempo de recuperação de desastres.

## <a name="other-considerations"></a>Outras considerações

Este artigo discutiu como fazer backup ou tirar fotos dos seus VMs e dos seus discos para apoiar a recuperação de desastres e como usar essas cópias de segurança ou instantâneos para recuperar os seus dados. Com o modelo Azure Resource Manager, muitas pessoas usam modelos para criar os seus VMs e outras infraestruturas em Azure. Pode usar um modelo para criar um VM que tenha sempre a mesma configuração. Se utilizar imagens personalizadas para criar os seus VMs, deve também certificar-se de que as suas imagens estão protegidas através de uma conta de armazenamento georedundant de acesso de leitura para as armazenar.

Consequentemente, o seu processo de backup pode ser uma combinação de duas coisas:

- Volte a fazer o backup dos dados (discos).
- Volte a configurar a configuração (modelos e imagens personalizadas).

Dependendo da opção de backup que escolher, poderá ter de lidar com a cópia de segurança dos dados e da configuração, ou o serviço de backup pode tratar de tudo isso para si.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: Compreender o impacto da redundância de dados

Para as contas de armazenamento em Azure, existem três tipos de redundância de dados que deve considerar no que diz respeito à recuperação de desastres: localmente redundante, geo-redundante ou geo-redundante com acesso à leitura. 

O armazenamento localmente redundante retém três cópias dos dados no mesmo datacenter. Quando o VM escreve os dados, as três cópias são atualizadas antes do sucesso ser devolvido ao chamador, para que saiba que são idênticas. O seu disco está protegido contra falhas locais, porque é improvável que as três cópias sejam afetadas ao mesmo tempo. No caso de armazenamento localmente redundante, não existe geo-redundância, pelo que o disco não está protegido contra falhas catastróficas que possam afetar todo um datacenter ou unidade de armazenamento.

Com armazenamento geo-redundante e armazenamento geo-redundante de acesso à leitura, três cópias dos seus dados são retidas na região primária que é selecionada por si. Mais três cópias dos seus dados são retidas numa região secundária correspondente que é definida pelo Azure. Por exemplo, se armazenar dados nos EUA Ocidentais, os dados são replicados para os EUA Orientais. A retenção de cópias é feita assincronicamente, e há um pequeno atraso entre as atualizações para os locais primários e secundários. As réplicas dos discos no local secundário são consistentes numa base por disco (com o atraso), mas réplicas de vários discos ativos podem não estar sincronizadas umas com as outras. Para ter réplicas consistentes em vários discos, são necessárias imagens consistentes.

A principal diferença entre armazenamento geo-redundante e armazenamento geo-redundante de acesso de leitura é que, com armazenamento geo-redundante de acesso de leitura, você pode ler a cópia secundária a qualquer momento. Se houver um problema que torne os dados na região primária inacessíveis, a equipa azure faz todos os esforços para restaurar o acesso. Enquanto a primária estiver em baixo, se tiver um armazenamento geo-redundante de acesso de leitura, pode aceder aos dados no centro de dados secundário. Portanto, se planeia ler a réplica enquanto a primária é inacessível, então deve ser considerado o armazenamento geo-redundante de acesso de leitura.

Se se revelar uma paragem significativa, a equipa azure pode desencadear uma geofalha e alterar as entradas primárias de DNS para apontar para o armazenamento secundário. Neste ponto, se tiver armazenamento geo-redundante ou armazenamento geo-redundante de acesso de leitura, pode aceder aos dados da região que costumava ser o secundário. Por outras palavras, se a sua conta de armazenamento for um armazenamento geo-redundante e houver um problema, só pode aceder ao armazenamento secundário se houver uma geofalha.

Para obter informações, veja [O que fazer em caso de falha do Armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>A Microsoft controla se ocorre uma falha. A failover não é controlada por conta de armazenamento, por isso não é decidida por clientes individuais. Para implementar a recuperação de desastres para contas de armazenamento específicas ou discos de máquinas virtuais, deve utilizar as técnicas descritas anteriormente neste artigo.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
