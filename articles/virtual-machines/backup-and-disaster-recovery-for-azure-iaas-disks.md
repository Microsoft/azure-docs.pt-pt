---
title: Backup e recuperação de desastres para discos IaaS em VMs Azure
description: Este artigo explica como planear a recuperação de backup e desastres de máquinas virtuais e discos IaaS em Azure. Este documento abrange discos geridos e não geridos.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507911"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Backup e recuperação de desastres para discos Azure IaaS

Este artigo explica como planear o backup e recuperação de desastres (DR) das máquinas virtuais IaaS (VMs) e discos em Azure. Este documento abrange discos geridos e não geridos.

Primeiro, cobrimos as capacidades de tolerância a falhas incorporadas na plataforma Azure que ajuda a proteger contra falhas locais. Em seguida, discutimos os cenários de catástrofe não totalmente cobertos pelas capacidades incorporadas. Também mostramos vários exemplos de cenários de carga de trabalho onde podem ser aplicadas diferentes considerações de backup e DR. Em seguida, revemos possíveis soluções para o DR dos discos IaaS.

## <a name="introduction"></a>Introdução

A plataforma Azure utiliza vários métodos de redundância e tolerância a falhas para ajudar a proteger os clientes de falhas de hardware localizadas. As falhas locais podem incluir problemas com uma máquina de servidor de armazenamento Azure que armazena parte dos dados para um disco virtual ou falhas de um SSD ou HDD nesse servidor. Tais falhas isoladas do componente de hardware podem ocorrer durante as operações normais.

A plataforma Azure foi concebida para ser resistente a estas falhas. Grandes desastres podem resultar em falhas ou na inacessibilidade de muitos servidores de armazenamento ou mesmo de um centro de dados inteiro. Embora os seus VMs e discos estejam normalmente protegidos contra falhas localizadas, são necessários passos adicionais para proteger a sua carga de trabalho contra falhas catastróficas em toda a região, como um grande desastre, que pode afetar o seu VM e discos.

Além da possibilidade de falhas na plataforma, podem ocorrer problemas com uma aplicação ou dados do cliente. Por exemplo, uma nova versão da sua aplicação pode inadvertidamente fazer uma alteração nos dados que a faz quebrar. Nesse caso, é possível que queira reverter a aplicação e os dados para uma versão anterior que contenha o último bom estado conhecido. Isto requer a manutenção de cópias de segurança regulares.

Para a recuperação de desastres regionais, você deve apoiar os seus discos IaaS VM para uma região diferente.

Antes de olharmos para as opções de backup e DR, vamos recapitular alguns métodos disponíveis para lidar com falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de Azure IaaS

*Resiliência* refere-se à tolerância para falhas normais que ocorrem em componentes de hardware. Resiliência é a capacidade de recuperar de falhas e continuar a funcionar. Não é uma questão de evitar falhas, mas de responder às falhas de forma a evitar períodos de indisponibilidade ou a perda de dados. O objetivo da resiliência é fazer com que a aplicação volte para um estado totalmente funcional após uma falha. As máquinas virtuais e os discos Azure são projetados para serem resistentes a falhas comuns de hardware. Vejamos como a plataforma Azure IaaS proporciona esta resiliência.

Uma máquina virtual consiste principalmente em duas partes: um servidor compute e os discos persistentes. Ambos afetam a tolerância à falha de uma máquina virtual.

Se o servidor anfitrião do cálculo Azure que alberga o seu VM experimentar uma falha de hardware, o que é raro, o Azure foi concebido para restaurar automaticamente o VM noutro servidor. Se este cenário, o seu computador reinicia e o VM volta a aparecer passado algum tempo. O Azure deteta automaticamente tais falhas de hardware e executa recuperações para ajudar a garantir que o VM do cliente está disponível o mais rapidamente possível.

No que diz respeito aos discos IaaS, a durabilidade dos dados é fundamental para uma plataforma de armazenamento persistente. Os clientes da Azure têm aplicações empresariais importantes em execução no IaaS, e dependem da persistência dos dados. A Azure projeta proteção para estes discos IaaS, com três cópias redundantes dos dados que são armazenados localmente. Estas cópias proporcionam uma elevada durabilidade contra falhas locais. Se um dos componentes de hardware que detém o disco falhar, o seu VM não é afetado, porque existem duas cópias adicionais para suportar pedidos de disco. Funciona bem, mesmo que dois componentes de hardware diferentes que suportam um disco falhem ao mesmo tempo (o que é raro). 

Para garantir que mantém sempre três réplicas, o Azure Storage gera automaticamente uma nova cópia dos dados em segundo plano se uma das três cópias ficar indisponível. Por isso, não deve ser necessário utilizar RAID com discos Azure para tolerância à falha. Uma configuração simples RAID 0 deve ser suficiente para desmontar os discos, se necessário, para criar volumes maiores.

Devido a esta arquitetura, a Azure tem consistentemente proporcionado durabilidade de qualidade empresarial para discos IaaS, com uma taxa de [insucesso anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate)líder do setor.

Falhas de hardware localizadas no hospedeiro computacional ou na plataforma de armazenamento podem, por vezes, resultar na indisponibilidade temporária do VM que é coberto pelo [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para disponibilidade de VM. O Azure também fornece um SLA líder na indústria para instâncias VM únicas que usam SSDs premium Azure.

Para salvaguardar as cargas de trabalho da aplicação devido à indisponibilidade temporária de um disco ou VM, os clientes podem utilizar [conjuntos de disponibilidade](./availability.md). Duas ou mais máquinas virtuais num conjunto de disponibilidade fornecem redundância para a aplicação. Azure cria então estes VMs e discos em domínios de avaria separados com diferentes componentes de potência, rede e servidor.

Devido a estes domínios de avaria separados, falhas de hardware localizadas normalmente não afetam vários VMs no conjunto ao mesmo tempo. Ter domínios de avaria separados proporciona alta disponibilidade para a sua aplicação. É considerado uma boa prática usar conjuntos de disponibilidade quando é necessária alta disponibilidade. A secção seguinte abrange o aspeto da recuperação de desastres.

### <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres

A recuperação de desastres é a capacidade de recuperar de incidentes raros, mas graves. Estes incidentes incluem falhas não transitórias e de grande escala, como a interrupção do serviço que afeta toda uma região. A recuperação de desastres inclui cópia de segurança de dados e arquivamento, e pode incluir intervenção manual, como restaurar uma base de dados a partir de uma cópia de segurança.

A proteção incorporada da plataforma Azure contra falhas localizadas pode não proteger totalmente os VMs/discos se uma grande catástrofe causar interrupções em larga escala. Estas interrupções em larga escala incluem eventos catastróficos, como se um datacenter for atingido por um furacão, terramoto, incêndio, ou se houver uma falha da unidade de hardware em larga escala. Além disso, poderá encontrar falhas devido a problemas de aplicação ou dados.

Para ajudar a proteger as suas cargas de trabalho iaaS de falhas, deve planear a redundância e ter backups para permitir a recuperação. Para a recuperação de desastres, você deve voltar para um local geográfico diferente longe do local principal. Esta abordagem ajuda a garantir que a sua cópia de segurança não seja afetada pelo mesmo evento que afetou originalmente o VM ou os discos. Para obter mais informações, consulte [a recuperação de desastres para aplicações Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

As suas considerações de DR podem incluir os seguintes aspetos:

- Alta disponibilidade: A capacidade da aplicação para continuar a funcionar em estado saudável, sem tempo de inatividade significativo. Por *estado saudável,* este estado significa que a aplicação é responsiva, e os utilizadores podem ligar-se à aplicação e interagir com ela. Algumas aplicações e bases de dados críticas da missão podem ser necessárias para estar sempre disponíveis, mesmo quando existem falhas na plataforma. Para estas cargas de trabalho, poderá ser necessário planear a redundância para a aplicação, bem como os dados.

- Durabilidade dos dados: Em alguns casos, a principal consideração é garantir que os dados são preservados em caso de catástrofe. Portanto, poderá necessitar de uma cópia de segurança dos seus dados num site diferente. Para estas cargas de trabalho, pode não precisar de redundância total para a aplicação, mas apenas uma cópia de segurança regular dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de backup e DR

Vejamos alguns exemplos típicos de cenários típicos de carga de trabalho de aplicação e as considerações para o planeamento para a recuperação de desastres.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: Principais soluções de base de dados

Considere um servidor de base de dados de produção, como o SQL Server ou o Oracle, que possa suportar uma elevada disponibilidade. Aplicações de produção críticas e utilizadores dependem desta base de dados. O plano de recuperação de catástrofes para este sistema poderá ter de apoiar os seguintes requisitos:

- Os dados devem ser protegidos e recuperáveis.
- O servidor deve estar disponível para utilização.

O plano de recuperação de desastres pode exigir a manutenção de uma réplica da base de dados numa região diferente como apoio. Dependendo dos requisitos de disponibilidade do servidor e recuperação de dados, a solução pode variar de um site de réplica ativa ou passiva ativa até cópias de segurança offline periódicas dos dados. Bases de dados relacionais, como SQL Server e Oracle, fornecem várias opções para replicação. Para o SQL Server, utilize [os Grupos de Disponibilidade AlwaysOn do SqL](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) para obter uma elevada disponibilidade.

Bases de dados noSQL, como o MongoDB, também [suportam réplicas](https://docs.mongodb.com/manual/replication/) para redundância. As réplicas para alta disponibilidade são usadas.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: Um aglomerado de VMs redundantes

Considere uma carga de trabalho tratada por um conjunto de VMs que proporcionam redundância e equilíbrio de carga. Um exemplo é um aglomerado de Cassandra implantado numa região. Este tipo de arquitetura já proporciona um elevado nível de redundância naquela região. No entanto, para proteger a carga de trabalho de uma falha a nível regional, deve considerar a difusão do aglomerado por duas regiões ou fazer backups periódicos para outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: Carga de trabalho da aplicação iaaS

Vamos ver a carga de trabalho da aplicação iaaS. Por exemplo, esta aplicação pode ser uma carga de trabalho típica de produção em execução num VM Azure. Pode ser um servidor web ou servidor de ficheiros que detenha o conteúdo e outros recursos de um site. Também pode ser uma aplicação de negócios personalizada em execução num VM que armazenava os seus dados, recursos e estado de aplicação nos discos VM. Neste caso, é importante ter certeza de que você faz backups regularmente. A frequência de reserva deve basear-se na natureza da carga de trabalho em VM. Por exemplo, se a aplicação for executado todos os dias e modificar os dados, então a cópia de segurança deve ser tomada a cada hora.

Outro exemplo é um servidor de reporte que retira dados de outras fontes e gera relatórios agregados. A perda deste VM ou discos pode levar à perda dos relatórios. No entanto, poderá ser possível refazer o processo de reporte e regenerar a produção. Nesse caso, não tem realmente uma perda de dados, mesmo que o servidor de relatórios seja atingido por um desastre. Como resultado, poderá ter um nível mais elevado de tolerância para perder parte dos dados no servidor de relatórios. Nesse caso, as cópias de segurança menos frequentes são uma opção para reduzir custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: Problemas de dados de aplicação da IAAS

Problemas de dados de aplicação iaaS são outra possibilidade. Considere uma aplicação que calcule, mantenha e sirva dados comerciais críticos, como informação sobre preços. Uma nova versão da sua aplicação tinha um bug de software que computava incorretamente os preços e corrompia os dados de comércio existentes servidos pela plataforma. Aqui, o melhor caminho a seguir é reverter para a versão anterior da aplicação e os dados. Para ativar isto, faça cópias de segurança periódicas do seu sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação de desastres: Azure Backup 

[O Azure Backup](https://azure.microsoft.com/services/backup/) é utilizado para backups e DR, e funciona com [discos geridos,](managed-disks-overview.md) bem como discos não geridos. Você pode criar um trabalho de backup com backups baseados no tempo, fácil restauro VM e políticas de retenção de backup.

Se utilizar [SSDs premium,](disks-types.md) [discos geridos](managed-disks-overview.md)ou outros tipos de discos com a opção de armazenamento [localmente redundante,](../storage/common/storage-redundancy.md#locally-redundant-storage) é especialmente importante fazer cópias de segurança periódicas de DR. A Azure Backup armazena os dados no cofre dos seus serviços de recuperação para retenção a longo prazo. Escolha a opção [de armazenamento geo-redundante](../storage/common/storage-redundancy.md#geo-redundant-storage) para o cofre dos serviços de recuperação de backup. Esta opção garante que os backups sejam replicados numa região de Azure diferente para a salvaguarda de catástrofes regionais.

Para discos não geridos, pode utilizar o tipo de armazenamento localmente redundante para discos IaaS, mas certifique-se de que o Azure Backup está ativado com a opção de armazenamento geo-redundante para o cofre de serviços de recuperação.

> [!NOTE]
> Se utilizar a opção [de armazenamento geo-redundante ou](../storage/common/storage-redundancy.md#geo-redundant-storage) de acesso de leitura [para](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  os seus discos não geridos, ainda precisa de instantâneos consistentes para backup e DR. Utilize backup [Azure](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes](#alternative-solution-consistent-snapshots).

 O quadro a seguir é um resumo das soluções disponíveis para DR.

| Scenario | Replicação automática | Solução DR |
| --- | --- | --- |
| Discos SSD Premium | Local[(armazenamento localmente redundante)](../storage/common/storage-redundancy.md#locally-redundant-storage) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed disks | Local[(armazenamento localmente redundante)](../storage/common/storage-redundancy.md#locally-redundant-storage) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento localmente redundantes não geridos | Local[(armazenamento localmente redundante)](../storage/common/storage-redundancy.md#locally-redundant-storage) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento geo-redundantes não geridos | Região transversal[(armazenamento geodussa redundante)](../storage/common/storage-redundancy.md#geo-redundant-storage) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| Discos de armazenamento geo-redundantes de acesso não geridos | Região transversal[(armazenamento geo-redundante de acesso à leitura)](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

A alta disponibilidade é melhor satisfeita utilizando discos geridos num conjunto de disponibilidade juntamente com a Azure Backup. Se utilizar discos não geridos, ainda pode utilizar a Cópia de Segurança Azure para DR. Se não conseguir utilizar o Azure Backup, então tirar [fotografias consistentes](#alternative-solution-consistent-snapshots), como descrito numa secção posterior, é uma solução alternativa para backup e DR.

As suas escolhas para alta disponibilidade, backup e DR nos níveis de aplicação ou infraestrutura podem ser representadas da seguinte forma:

| Level |   Elevada disponibilidade   | Backup ou DR |
| --- | --- | --- |
| Aplicação | SQL Server AlwaysOn | Azure Backup |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento geo-redundante com instantâneos consistentes |

### <a name="using-azure-backup"></a>Utilizar o Azure Backup 

[O Azure Backup](../backup/backup-azure-vms-introduction.md) pode fazer backup dos seus VMs que executam o Windows ou o Linux até ao cofre dos serviços de recuperação do Azure. O backup e a restauração de dados críticos do negócio é complicado pelo facto de os dados críticos do negócio devem ser apoiados enquanto as aplicações que produzem os dados estão em execução. 

Para resolver este problema, o Azure Backup fornece cópias de segurança consistentes para a Microsoft. Utiliza o serviço de sombra de volume para garantir que os dados são escritos corretamente para o armazenamento. Para os VMs Do Linux, o modo de consistência de backup padrão é cópia de segurança consistente com ficheiros, uma vez que o Linux não possui funcionalidades equivalentes ao serviço de sombra de volume, como no caso do Windows. Para máquinas Linux, consulte [a cópia de segurança consistente da aplicação dos VMs Azure Linux](../backup/backup-azure-linux-app-consistent.md).

![Fluxo de backup Azure][1]

Quando o Azure Backup inicia um trabalho de backup na hora programada, aciona a extensão de backup instalada no VM para tirar uma foto pontual. Um instantâneo é tirado em coordenação com o serviço de sombra de volume para obter uma imagem consistente dos discos na máquina virtual sem ter que desligá-lo. A extensão de backup no VM lava todas as gravações antes de tirar uma imagem consistente de todos os discos. Depois de tirar a foto, os dados são transferidos pela Azure Backup para o cofre de reserva. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que mudaram após a última cópia de segurança.

Para restaurar, pode ver as cópias de segurança disponíveis através do Azure Backup e, em seguida, iniciar uma restauração. Pode criar e restaurar as cópias de segurança do Azure através do [portal Azure,](https://portal.azure.com/)utilizando o [PowerShell,](../backup/backup-azure-vms-automation.md)ou utilizando o [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Passos para permitir uma cópia de segurança

Utilize os seguintes passos para permitir cópias de segurança dos seus VMs utilizando o [portal Azure](https://portal.azure.com/). Há alguma variação dependendo do seu cenário exato. Consulte a documentação [da Reserva Azure](../backup/backup-azure-vms-introduction.md) para obter todos os detalhes. O Azure Backup também [suporta VMs com discos geridos.](https://azure.microsoft.com/blog/azure-managed-disk-backup/)

1.  Criar um cofre de serviços de recuperação para um VM:

    a. No [portal Azure,](https://portal.azure.com/)navegue **por todos os recursos** e encontre **cofres dos Serviços de Recuperação.**

    b. No menu de **cofres dos Serviços de Recuperação,** clique em **Adicionar** e siga os passos para criar um novo cofre na mesma região que o VM. Por exemplo, se o seu VM estiver na região oeste dos EUA, escolha os EUA ocidentais para o cofre.

1.  Verifique a replicação de armazenamento para o cofre recém-criado. Aceda ao cofre sob **cofres dos Serviços de Recuperação** e vá para **a**  >  **Atualização de Configuração de Backup** de Propriedades  >  . Certifique-se de que a opção **de armazenamento geo-redundante** é selecionada por padrão. Esta opção garante que o seu cofre é automaticamente replicado para um centro de dados secundário. Por exemplo, o seu cofre no Oeste dos EUA é automaticamente replicado para os EUA.

1.  Configure a política de backup e selecione o VM da mesma UI.

1.  Certifique-se de que o Agente de Reserva está instalado no VM. Se o seu VM for criado utilizando uma imagem da galeria Azure, então o Agente de Reserva já está instalado. Caso contrário (isto é, se utilizar uma imagem personalizada), utilize as instruções para [instalar o agente VM numa máquina virtual](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Após a conclusão dos passos anteriores, a cópia de segurança funciona em intervalos regulares, conforme especificado na política de backup. Se necessário, pode ativar manualmente a primeira cópia de segurança do painel de abóbadas do portal Azure.

Para automatizar o Azure Backup utilizando scripts, consulte [os cmdlets PowerShell para obter cópias de segurança VM](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Passos para a recuperação

Se precisar de reparar ou reconstruir um VM, pode restaurar o VM a partir de qualquer um dos pontos de recuperação de reserva no cofre. Existem algumas opções diferentes para realizar a recuperação:

-   Pode criar um novo VM como uma representação pontual do seu VM apoiado.

-   Pode restaurar os discos e, em seguida, usar o modelo para o VM personalizar e reconstruir o VM restaurado.

Para obter mais informações, consulte as instruções para [utilizar o portal Azure para restaurar as máquinas virtuais.](../backup/backup-azure-arm-restore-vms.md) Este documento também explica os passos específicos para restaurar os VMs apoiados num datacenter emparelhado, utilizando o seu cofre de backup geo-redundante se houver um desastre no centro de dados primário. Nesse caso, o Azure Backup utiliza o serviço Compute da região secundária para criar a máquina virtual restaurada.

Também pode utilizar o PowerShell para [criar um novo VM a partir de discos restaurados.](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: Instantâneos consistentes

Se não conseguir utilizar o Azure Backup, pode implementar o seu próprio mecanismo de backup utilizando instantâneos. Criar instantâneos consistentes para todos os discos usados por um VM e, em seguida, replicar esses instantâneos para outra região é complicado. Por esta razão, a Azure considera usar o serviço de Backup como uma opção melhor do que construir uma solução personalizada.

Se utilizar armazenamento geo-redundante de acesso de leitura/armazenamento geo-redundante para discos, as imagens são automaticamente replicadas num centro de dados secundário. Se utilizar armazenamento localmente redundante para discos, tem de replicar os dados por si mesmo. Para obter mais informações, consulte [discos VM não geridos por Azure com instantâneos incrementais](windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto num ponto específico do tempo. Um instantâneo incorre na faturação para o tamanho incremental dos dados que detém. Para obter mais informações, consulte [Criar uma imagem de bolha.](../storage/blobs/snapshots-overview.md)

### <a name="create-snapshots-while-the-vm-is-running"></a>Crie instantâneos enquanto o VM está em execução

Embora possa tirar uma fotografia a qualquer momento, se o VM estiver em execução, ainda há dados a serem transmitidos para os discos. As fotos podem conter operações parciais que estavam em voo. Além disso, se houver vários discos envolvidos, as imagens de diferentes discos podem ter ocorrido em momentos diferentes. Estes cenários podem fazer com que os instantâneos sejam descoordenados. Esta falta de coordenação é especialmente problemática para volumes listrados cujos ficheiros podem ser corrompidos se forem feitas alterações durante a cópia de segurança.

Para evitar esta situação, o processo de backup deve implementar as seguintes etapas:

1.  Congele todos os discos.

1.  Despeje todas as gravações pendentes.

1.  [Crie uma imagem de bolha](../storage/blobs/snapshots-manage-dotnet.md) para todos os discos.

Algumas aplicações do Windows, como o SQL Server, fornecem um mecanismo de backup coordenado através de um serviço de sombra de volume para criar cópias de segurança consistentes com aplicações. No Linux, pode utilizar uma ferramenta como *o fsfreeze* para coordenar os discos. Esta ferramenta fornece cópias de segurança consistentes com ficheiros, mas não imagens consistentes com aplicações. Este processo é complexo, pelo que deve considerar a utilização de [Backup Azure](../backup/backup-azure-vms-introduction.md) ou uma solução de backup de terceiros que já implementa este procedimento.

O processo anterior resulta numa recolha de instantâneos coordenados para todos os discos VM, representando uma visão específica do ponto de tempo do VM. Este é um ponto de restauro de reserva para o VM. Pode repetir o processo em intervalos programados para criar cópias de segurança periódicas. Consulte [copiar as cópias de segurança para outra região](#copy-the-snapshots-to-another-region) para obter etapas para copiar os instantâneos para outra região para DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Crie instantâneos enquanto o VM está offline

Outra opção para criar cópias de segurança consistentes é desligar o VM e tirar fotografias blob de cada disco. Tirar fotografias blob é mais fácil do que coordenar imagens de um VM em execução, mas requer alguns minutos de tempo de paragem.

1. Desligue o VM.

1. Crie uma imagem de cada bolha de disco rígido virtual, que leva apenas alguns segundos.

    Para criar um instantâneo, pode utilizar o [PowerShell](/powershell/module/az.storage), o [API AZure Storage REST,](/rest/api/storageservices/Snapshot-Blob) [Azure CLI,](/cli/azure/)ou uma das bibliotecas do cliente do Azure Storage, como [a biblioteca do cliente de Armazenamento para .NET](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

1. Inicie o VM, que termina o tempo de inatividade. Normalmente, todo o processo termina em poucos minutos.

Este processo produz uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauro de backup para o VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copie os instantâneos para outra região

A criação dos instantâneos por si só pode não ser suficiente para o DR. Também deve replicar os backups instantâneos para outra região.

Se utilizar armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura para os seus discos, então os instantâneos são replicados automaticamente na região secundária. Pode haver alguns minutos de atraso antes da replicação. Se o centro de dados primário descer antes que os instantâneos terminem a replicação, não é possível aceder às imagens do centro de dados secundário. A probabilidade disto é pequena.

> [!NOTE]
> Só ter os discos numa conta de armazenamento geodu redundante ou de acesso à leitura não protege o VM de desastres. Também deve criar instantâneos coordenados ou utilizar backup Azure. Isto é necessário para recuperar um VM para um estado consistente.

Se utilizar armazenamento localmente redundante, deve copiar as imagens para uma conta de armazenamento diferente imediatamente após a criação do instantâneo. O alvo da cópia pode ser uma conta de armazenamento localmente redundante numa região diferente, resultando em que a cópia esteja numa região remota. Também pode copiar o instantâneo para uma conta de armazenamento geo-redundante de acesso à leitura na mesma região. Neste caso, o instantâneo é preguiçosamente replicado para a região secundária remota. A sua cópia de segurança está protegida contra desastres no local primário após a cópia e replicação estar concluída.

Para copiar as suas imagens incrementais para DR de forma eficiente, reveja as instruções em [Back up Azure discos VM não geridos com instantâneos incrementais](windows/incremental-snapshots.md).

![Apoiar discos VM não geridos da Azure com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação de instantâneos

Para recuperar uma foto, copie-a para fazer uma nova bolha. Se estiver a copiar o instantâneo da conta primária, pode copiar o instantâneo para a bolha de base do instantâneo. Este processo reverte o disco para o instantâneo. Este processo é conhecido como promoção do instantâneo. Se estiver a copiar a cópia de segurança instantânea de uma conta secundária, no caso de uma conta de armazenamento geo-redundante de acesso à leitura, deve copiá-la para uma conta primária. Pode copiar uma imagem [utilizando o PowerShell](/powershell/module/az.storage) ou utilizando o utilitário AzCopy. Para obter mais informações, consulte [os dados de transferência com o utilitário de linha de comando AzCopy](../storage/common/storage-use-azcopy-v10.md).

Para VMs com vários discos, deve copiar todos os instantâneos que fazem parte do mesmo ponto de restauro coordenado. Depois de copiar as imagens para bolhas VHD, pode utilizar as bolhas para recriar o seu VM utilizando o modelo para o VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

O SQL Server a funcionar num VM tem as suas próprias capacidades incorporadas para fazer o back-up do seu SQL Server até ao armazenamento do Azure Blob ou a uma partilha de ficheiros. Se a conta de armazenamento for armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura, pode aceder a essas cópias de segurança no centro de dados secundário da conta de armazenamento em caso de desastre, com as mesmas restrições que previamente discutidas. Para obter mais informações, consulte [Back up e restauro para O SQL Server em máquinas virtuais Azure](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Além de fazer back up e restaurar, [os grupos de disponibilidade sql Server AlwaysOn](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) podem manter réplicas secundárias de bases de dados. Esta capacidade reduz consideravelmente o tempo de recuperação de desastres.

## <a name="other-considerations"></a>Outras considerações

Este artigo discutiu como fazer backup ou tirar fotos dos seus VMs e seus discos para apoiar a recuperação de desastres e como usar essas cópias de segurança ou instantâneos para recuperar os seus dados. Com o modelo Azure Resource Manager, muitas pessoas usam modelos para criar os seus VMs e outras infraestruturas em Azure. Pode utilizar um modelo para criar um VM que tenha sempre a mesma configuração. Se utilizar imagens personalizadas para criar os seus VMs, também deve certificar-se de que as suas imagens estão protegidas utilizando uma conta de armazenamento geo-redundante de acesso de leitura para as armazenar.

Consequentemente, o seu processo de backup pode ser uma combinação de duas coisas:

- Fazer o back up dos dados (discos).
- Fazer o back up da configuração (modelos e imagens personalizadas).

Dependendo da opção de backup que escolher, poderá ter de lidar com a cópia de segurança dos dados e da configuração, ou o serviço de cópia de segurança poderá tratar de tudo isso para si.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: Compreender o impacto da redundância de dados

Para as contas de armazenamento em Azure, existem três tipos de redundância de dados que deve considerar em relação à recuperação de desastres: localmente redundante, geo-redundante ou geo-redundante com acesso à leitura. 

O armazenamento localmente redundante retém três cópias dos dados no mesmo datacenter. Quando o VM escreve os dados, as três cópias são atualizadas antes de o sucesso ser devolvido ao chamador, para que saiba que são idênticos. O seu disco está protegido contra falhas locais, porque é improvável que as três cópias sejam afetadas ao mesmo tempo. No caso de armazenamento localmente redundante, não existe geo-redundância, pelo que o disco não está protegido contra falhas catastróficas que possam afetar todo um datacenter ou uma unidade de armazenamento.

Com armazenamento geo-redundante e armazenamento geo-redundante de acesso à leitura, três cópias dos seus dados são retidas na região primária que é selecionada por si. Mais três cópias dos seus dados são retidas numa região secundária correspondente que é definida pelo Azure. Por exemplo, se armazenar dados no Oeste dos EUA, os dados são replicados para os EUA Orientais. A retenção de cópias é feita de forma assíncronea, e há um pequeno atraso entre as atualizações para os locais primários e secundários. As réplicas dos discos no site secundário são consistentes numa base por disco (com o atraso), mas réplicas de múltiplos discos ativos podem não estar sincronizadas entre si. Para ter réplicas consistentes em vários discos, são necessários instantâneos consistentes.

A principal diferença entre o armazenamento geo-redundante e o armazenamento geo-redundante de acesso à leitura é que, com o armazenamento geo-redundante de acesso à leitura, pode ler a cópia secundária a qualquer momento. Se houver um problema que torne os dados na região primária inacessíveis, a equipa do Azure faz todos os esforços para restabelecer o acesso. Enquanto a primária está em baixo, se tiver acesso de leitura de armazenamento geo-redundante ativado, pode aceder aos dados no centro de dados secundário. Portanto, se pretender ler a partir da réplica enquanto o primário é inacessível, então deve considerar-se o armazenamento geo-redundante de acesso à leitura.

Se se revelar uma paragem significativa, a equipa Azure pode desencadear uma falha de geo-falha e alterar as entradas primárias de DNS para apontar para o armazenamento secundário. Neste momento, se tiver armazenamento geo-redundante ou armazenamento geo redundante de acesso à leitura, pode aceder aos dados da região que costumava ser o secundário. Por outras palavras, se a sua conta de armazenamento for um armazenamento geo-redundante e houver um problema, só poderá aceder ao armazenamento secundário se houver uma falha geo-failover.

Para obter informações, veja [O que fazer em caso de falha do Armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Passos seguintes

Ver [Back up Discos de Máquina Virtual não geridos com instantâneos incrementais](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png