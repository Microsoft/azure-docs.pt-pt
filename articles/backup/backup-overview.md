---
title: O que é o Backup do Azure?
description: Fornece uma visão geral do serviço de backup do Azure e como ele contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre).
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: e3577a1a0da7809298697c55c84662bc15b0f1e7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639632"
---
# <a name="what-is-azure-backup"></a>O que é o Backup do Azure?

O serviço de backup do Azure faz backup de dados para a nuvem Microsoft Azure. Você pode fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure.


## <a name="why-use-azure-backup"></a>Porquê utilizar o Backup do Azure?

O backup do Azure oferece estes benefícios principais:

- **Descarregar backup local**: O backup do Azure oferece uma solução simples para fazer backup de seus recursos locais para a nuvem. Obtenha backup de curto e longo prazo sem a necessidade de implantar soluções de backup locais complexas.
- **Fazer backup de VMs IaaS do Azure**: O backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos serviços de recuperação com um gerenciado interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode facilmente restaurar conforme necessário.
- **Dimensione facilmente** -o backup do Azure usa a potência subjacente e a escala ilimitada da nuvem do Azure para fornecer alta disponibilidade sem sobrecarga de manutenção ou monitoramento.
- **Obter transferência de dados ilimitada**: O backup do Azure não limita a quantidade de dados de entrada ou saída que você transfere ou cobra pelos dados que são transferidos.
    - Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.
    - Se você executar um backup inicial offline usando o serviço de importação/exportação do Azure para importar grandes quantidades de dados, haverá um custo associado aos dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md).
- **Mantenha os dados seguros**: O backup do Azure fornece soluções para proteger dados em trânsito e em repouso.
- **Obter backups consistentes com o aplicativo**: Um backup consistente com o aplicativo significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de backup. O Azure Backup fornece cópias de segurança consistentes com as aplicações, o que garante que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.
- **Reter dados de curto e longo prazo**: Você pode usar cofres dos serviços de recuperação para retenção de dados de curto e longo prazo. O Azure não limita o período de tempo durante o qual os dados podem permanecer num cofre dos Serviços de Recuperação. Você pode mantê-lo por tanto tempo quanto desejar. O Azure Backup tem um limite de 9999 pontos de recuperação por instância protegida. 
- **Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com o Azure Backup, não existe nenhum custo para a utilização de dispositivos de armazenamento no local. O backup do Azure aloca e gerencia automaticamente o armazenamento de backup e usa um modelo pago conforme o uso, para que você pague apenas pelo armazenamento que consumir. [Saiba mais](https://azure.microsoft.com/pricing/details/backup) sobre preços.
- **Várias opções de armazenamento** -o backup do Azure oferece dois tipos de replicação para manter seu armazenamento/dados altamente disponíveis.
    - O [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) Replica seus dados três vezes (ele cria três cópias de seus dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
    - O [armazenamento com redundância geográfica (GRS)](../storage/common/storage-redundancy-grs.md) é a opção de replicação padrão e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). O GRS custa mais do que o LRS, mas o GRS proporciona um nível mais elevado de durabilidade aos seus dados, mesmo se ocorrer uma indisponibilidade regional.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Qual é a diferença entre o backup do Azure e o Azure Site Recovery?

Os serviços de backup e Azure Site Recovery do Azure contribuem para uma estratégia de BCDR (continuidade de negócios e recuperação de desastre) em sua empresa. O BCDR consiste em dois objetivos amplos:

- Mantenha seus dados corporativos seguros e recuperáveis quando ocorrerem interrupções.
- Mantenha seus aplicativos e cargas de trabalho em funcionamento durante o tempo de inatividade planejado e não planejado.

Ambos os serviços fornecem funcionalidades complementares, mas diferentes.

- **Azure site Recovery**: O Site Recovery fornece uma solução de recuperação de desastre para computadores locais e para VMs do Azure. Você Replica máquinas de um local primário para um secundário. Quando ocorre um desastre, você realiza o failover de máquinas no local secundário e os acessa a partir daí. Quando tudo está funcionando normalmente novamente, você faz failback de máquinas para recuperá-las no site primário.
- **Backup do Azure**: O serviço de backup do Azure faz backup de dados de computadores locais e VMs do Azure. É possível fazer backup dos dados e recuperá-los em um nível granular, incluindo backup de arquivos, pastas, estado do sistema de computador e backup de dados com reconhecimento de aplicativos. O backup do Azure lida com os dados em um nível mais granular do que Site Recovery. Por exemplo, se uma apresentação em seu laptop for corrompida, você poderá usar o backup do Azure para restaurar a apresentação. Se você quiser manter uma configuração de VM e dados seguros e acessíveis, poderá usar Site Recovery.  

Use os pontos de tabela para ajudar a descobrir suas necessidades de BCDR.

**Objetivo** | **Detalhes** | **Comparação**
--- | --- | ---
**Backup/retenção de dados** | Os dados de backup podem ser retidos e armazenados por dias, meses ou até mesmo anos, se necessário, de uma perspectiva de conformidade. | Soluções de backup como o backup do Azure permitem que você escolha os dados que deseja fazer backup e ajuste as políticas de backup e retenção com precisão.<br/><br/> Site Recovery não permite o mesmo ajuste fino.
**Objetivo de ponto de recuperação (RPO)** | A quantidade de perda de dados aceitável se uma recuperação tiver de ser efetuada. | Os backups têm mais RPO variável.<br/><br/> Os backups de VM geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs tão baixos quanto 15 minutos.<br/><br/> O Site Recovery fornece um baixo RPO, uma vez que a replicação é contínua ou frequente, para que o Delta entre a cópia de origem e de réplica seja pequeno.
**Objetivo de tempo de recuperação (RTO)** |A quantidade de tempo que demora a concluir uma recuperação ou um restauro. | Devido ao RPO maior, a quantidade de dados que uma solução de cópia de segurança necessita para processar é, normalmente, muito mais elevada, o que resulta em RTOs maiores. Por exemplo, pode demorar dias para restaurar dados de bandas, consoante o tempo que demora a transportar a banda a partir de uma localização fora das instalações.

## <a name="what-backup-scenarios-are-supported"></a>Quais cenários de backup têm suporte?

O backup do Azure pode fazer backup de ambos os computadores locais e VMs do Azure.

**Tradução** | **Cenário de backup**
--- | ---
**Backup local** |  1) execute o agente de Serviços de Recuperação do Microsoft Azure de backup do Azure (MARS) em computadores Windows locais para fazer backup de arquivos individuais e do estado do sistema. <br/><br/>2) fazer backup de computadores locais em um servidor de backup (System Center Data Protection Manager (DPM) ou Backup do Microsoft Azure Server (MABS)) e, em seguida, configurar o servidor de backup para fazer backup em um cofre dos serviços de recuperação de backup do Azure no Azure.
**VMs do Azure** | 1) habilitar o backup para VMs individuais do Azure. Quando você habilita o backup, o backup do Azure instala uma extensão para o agente de VM do Azure que está em execução na VM. O agente faz backup de toda a VM.<br/><br/> 2) execute o agente MARS em uma VM do Azure. Isso será útil se você quiser fazer backup de arquivos e pastas individuais na VM.<br/><br/> 3) fazer backup de uma VM do Azure em um servidor DPM ou MABS em execução no Azure. Em seguida, faça backup do servidor DPM/MABS em um cofre usando o backup do Azure.


## <a name="why-use-a-backup-server"></a>Por que usar um servidor de backup?
As vantagens de fazer backup de computadores e aplicativos para o armazenamento MABS/DPM e, em seguida, fazer backup do armazenamento do DPM/MABS em um cofre são as seguintes:

- Fazer backup no MABS/DPM fornece backups com reconhecimento de aplicativos otimizados para aplicativos comuns, como o SQL Server, o Exchange e o SharePoint, em outros backups de arquivo/pasta/volume e backups de estado de máquina (bare-metal, estado do sistema).
- Para computadores locais, não é necessário instalar o agente MARS em cada computador que você deseja fazer backup. Cada computador executa o agente de proteção do DPM/MABS e o agente MARS é executado somente no MABS/DPM.
- Você tem mais flexibilidade e opções de agendamento granulares para executar backups.
- Você pode gerenciar backups de vários computadores coletados em grupos de proteção em um único console. Isso é particularmente útil quando os aplicativos estão em camadas em vários computadores e você deseja fazer backup deles juntos.

Saiba mais sobre [como o backup funciona](backup-architecture.md#architecture-back-up-to-dpmmabs) ao usar um servidor de backup e os [requisitos de suporte](backup-support-matrix-mabs-dpm.md) para servidores de backup.

## <a name="what-can-i-back-up"></a>O que posso fazer backup?

**Tradução** | **Método de backup** | **Fazer cópia de segurança**
--- | --- | ---
**VMs do Windows locais** | Executar agente MARS | Fazer backup de arquivos, pastas, estado do sistema.<br/><br/> Não há suporte para computadores Linux.
**Computadores locais** | Fazer backup no DPM/MABS | Faça backup de tudo o que é protegido pelo [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) ou [mAbs](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo.
**VMs do Azure** | Executar extensão de backup do agente de VM do Azure | Fazer backup de toda a VM
**VMs do Azure** | Executar agente MARS | Fazer backup de arquivos, pastas, estado do sistema.<br/><br/> Não há suporte para computadores Linux.
**VMs do Azure** | Fazer backup no MABS/DPM em execução no Azure | Faça backup de tudo o que é protegido pelo [mAbs](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) ou pelo [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) , incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo.

## <a name="what-backup-agents-do-i-need"></a>De quais agentes de backup preciso?

**Cenário** | **Agente**
--- | ---
**Fazer cópia de segurança de VMs do Azure** | Nenhum agente é necessário. A extensão de VM do Azure para backup é instalada na VM do Azure quando você executa o primeiro backup de VM do Azure.<br/><br/> Suporte para suporte para Windows e Linux.
**Fazer backup de computadores Windows locais** | Baixe, instale e execute o agente MARS diretamente no computador.
**Fazer backup de VMs do Azure com o agente MARS** | Baixe, instale e execute o agente MARS diretamente no computador. O agente MARS pode ser executado junto com a extensão de backup.
**Fazer backup de máquinas locais e VMs do Azure para o DPM/MABS** | O agente de proteção do DPM ou MABS é executado nos computadores que você deseja proteger. O agente MARS é executado no servidor DPM/MABS para fazer backup no Azure.

## <a name="which-backup-agent-should-i-use"></a>Qual agente de backup devo usar?

**Cópia de segurança** | **Solução** | **Limitação**
--- | --- | ---
**Quero fazer backup de uma VM do Azure inteira** | Habilite o backup da VM. A extensão de backup será configurada automaticamente na VM do Azure do Windows ou do Linux. | O backup de toda a VM é feito <br/><br/> Para VMs do Windows, o backup é consistente com o aplicativo. para o Linux, o backup é consistente com o arquivo. Se precisar de reconhecimento de aplicativos para VMs do Linux, você precisará configurá-lo com scripts personalizados.
**Quero fazer backup de arquivos/pastas específicos na VM do Azure** | Implante o agente MARS na VM.
**Quero fazer logon diretamente em computadores Windows locais** | Instale o agente MARS no computador. | Você pode fazer backup de arquivos, pastas e estado do sistema no Azure. Os backups não reconhecem o aplicativo.
**Quero fazer backup diretamente de computadores Linux locais** | Você precisa implantar o DPM ou o MABS para fazer backup no Azure. | Não há suporte para o backup do host do Linux, você só pode fazer backup do computador convidado do Linux hospedado no Hyper-V ou no VMWare.
**Quero fazer backup de aplicativos em execução no local** | Para os backups com reconhecimento de aplicativo, os computadores devem ser protegidos pelo DPM ou pelo MABS.
**Quero backup granular e flexível e configurações de recuperação para VMs do Azure** | Proteja as VMs do Azure com o MABS/DPM em execução no Azure para obter flexibilidade adicional no agendamento de backup e flexibilidade total para proteger e restaurar arquivos, pastas, volumes, aplicativos e estado do sistema.

## <a name="backup-and-retention"></a>Cópia de segurança e retenção

O Azure Backup tem um limite de 9999 pontos de recuperação, também conhecidos como cópias de segurança ou instantâneos de cópia de segurança, por *nstância protegida*.

- Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurados para criar cópias de segurança para o Azure. Uma instância está protegida depois de uma cópia de segurança de dados ter sido guardada.
- A cópia de segurança de dados é a proteção. Se a origem de dados fosse perdida ou tivesse sido danificada, a cópia de segurança poderia restaurar os dados de origem.

A tabela a seguir mostra a frequência de backup máxima para cada componente. Sua configuração de política de backup determina a rapidez com que você consome os pontos de recuperação. Por exemplo, se criar um ponto de recuperação por dia, pode manter os pontos de recuperação durante 27 anos antes de os esgotar. Se criar um ponto de recuperação por mês, pode manter os pontos de recuperação durante 833 anos antes de os esgotar. O serviço do Backup não define um limite de tempo de expiração para um ponto de recuperação.

|  | Agente do Backup do Azure | System Center DPM | Servidor do Backup do Azure | Cópia de segurança da VM do IaaS do Azure |
| --- | --- | --- | --- | --- |
| FREQUÊNCIA DE CÓPIA DE SEGURANÇA<br/> (para o cofre dos Serviços de Recuperação) |Três cópias de segurança por dia |Duas cópias de segurança por dia |Duas cópias de segurança por dia |Uma cópia de segurança por dia |
| Frequência de cópia de segurança<br/> (para o disco) |Não aplicável |A cada 15 minutos para o SQL Server<br/><br/> A cada hora para outras cargas de trabalho |A cada 15 minutos para o SQL Server<br/><br/> A cada hora para outras cargas de trabalho |Não aplicável |
| Opções de retenção |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |
| Número máximo de pontos de recuperação por instância protegida |9999|9999|9999|9999|
| Período de retenção máximo |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |
| Pontos de recuperação no disco local |Não aplicável | 64 para servidores de arquivos<br/><br/> 448 para Servidores de Aplicações | 64 para servidores de arquivos<br/><br/> 448 para Servidores de Aplicações |Não aplicável |
| Pontos de recuperação em banda |Não aplicável |Ilimitado |Não aplicável |Não aplicável |

## <a name="how-does-azure-backup-work-with-encryption"></a>Como o backup do Azure funciona com criptografia?

**Encriptação** | **Fazer backup no local** | **Fazer cópia de segurança de VMs do Azure** | **Fazer backup de SQL em VMs do Azure**
--- | --- | --- | ---
Encriptação inativa<br/> (Criptografia de dados onde ela é persistente/armazenada) | A senha especificada pelo cliente é usada para criptografar dados | O [criptografia do serviço de armazenamento do Azure (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) é usado para criptografar dados armazenados no cofre.<br/><br/> O backup criptografa os dados automaticamente antes de armazená-los. O armazenamento do Azure descriptografa os dados antes de recuperá-los. No momento, não há suporte para o uso de chaves gerenciadas pelo cliente para SSE.<br/><br/> Você pode fazer backup de VMs que usam o [Ade (Azure Disk Encryption)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para criptografar o sistema operacional e os discos de dados. O backup do Azure dá suporte a VMs criptografadas com BEK e com BEK e [Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Examine as [limitações](backup-azure-vms-encryption.md#encryption-support). | O backup do Azure dá suporte ao backup de bancos de dados SQL Server ou servidor com [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) habilitado. O backup dá suporte a TDE com chaves gerenciadas pelo Azure ou com chaves gerenciadas pelo cliente (BYOK).<br/><br/> O backup não executa nenhuma criptografia SQL como parte do processo de backup.
Criptografia em trânsito<br/> (Criptografia de dados movendo de um local para outro) | Os dados são criptografados usando AES256 e enviados para o cofre no Azure por HTTPS | No Azure, os dados entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede de backbone do Azure.<br/><br/> Para a recuperação de arquivos, o iSCSI protege os dados transmitidos entre o cofre e a VM do Azure. O túnel seguro protege o canal iSCSI. | No Azure, os dados entre o armazenamento do Azure e o cofre são protegidos por HTTPS.<br/><br/> Recuperação de arquivo não relevante para SQL.

## <a name="next-steps"></a>Passos seguintes

- [Examine](backup-architecture.md) a arquitetura e os componentes para cenários de backup diferentes.
- [Verifique](backup-support-matrix.md) os requisitos de suporte e as limitações de backup e para o [backup de VM do Azure](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
