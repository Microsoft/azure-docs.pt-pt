---
title: Acerca das cópias de segurança de VMs do Azure
description: Neste artigo, saiba como o serviço de backup Azure apoia as máquinas Azure Virtual e como seguir as melhores práticas.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415973"
---
# <a name="an-overview-of-azure-vm-backup"></a>Uma visão geral do backup Azure VM

Este artigo descreve como o [serviço de backup Azure](backup-introduction-to-azure-backup.md) apoia as máquinas virtuais Azure (VMs).

A Azure Backup fornece cópias de segurança independentes e isoladas para se proteger da destruição não intencional dos dados dos seus VMs. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão incorporada dos pontos de recuperação. A configuração e a escala são simples, as cópias de segurança são otimizadas, e você pode facilmente restaurar conforme necessário.

Como parte do processo de backup, é tirada uma [imagem instantânea](#snapshot-creation)e os dados são transferidos para o cofre dos Serviços de Recuperação sem impacto nas cargas de trabalho de produção. O instantâneo proporciona diferentes níveis de consistência, como descrito [aqui.](#snapshot-consistency)

A Azure Backup também tem ofertas especializadas para cargas de trabalho de base de dados como [o SQL Server](backup-azure-sql-database.md) e [o SAP HANA](sap-hana-db-about.md) que estão conscientes da carga de trabalho, oferecem RPO de 15 minutos (objetivo de ponto de recuperação), e permitem a cópia de segurança e restauro de bases de dados individuais.

## <a name="backup-process"></a>Processo de cópia de segurança

Eis como o Azure Backup completa um backup para VMs Azure:

1. Para VMs Azure selecionados para backup, o Azure Backup inicia um trabalho de backup de acordo com o horário de backup que especifica.
1. Durante a primeira cópia de segurança, é instalada uma extensão de reserva no VM se o VM estiver em funcionamento.
    - Para VMs windows, a [extensão VMSnapshot](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) está instalada.
    - Para Os VMs Linux, a [extensão VMSnapshotLinux](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) está instalada.
1. Para os VMs do Windows que estão em execução, as coordenadas de backup com o Windows Volume Shadow Copy Service (VSS) para tirar uma imagem consistente com aplicações do VM.
    - Por padrão, a Backup recebe cópias de segurança VSS completas.
    - Se o Backup não consegue tirar uma fotografia consistente com a aplicação, então é preciso uma imagem consistente com ficheiros do armazenamento subjacente (porque não ocorre nenhuma aplicação enquanto o VM é parado).
1. Para os VMs Linux, o Backup requer uma cópia de segurança consistente com ficheiros. Para imagens consistentes com aplicativos, é necessário personalizar manualmente scripts pré/post.
1. Depois do Backup tirar a fotografia, transfere os dados para o cofre.
    - A cópia de segurança é otimizada através do backup de cada disco VM em paralelo.
    - Para cada disco que está a ser apoiado, o Azure Backup lê os blocos no disco e identifica e transfere apenas os blocos de dados que mudaram (o delta) desde a cópia de segurança anterior.
    - Os dados instantâneos podem não ser imediatamente copiados para o cofre. Pode levar algumas horas em horas de pico. O tempo total de backup para um VM será inferior a 24 horas para as políticas diárias de backup.
1. As alterações feitas num VM do Windows após a disponibilizada do Azure são ativadas:
    - Microsoft Visual C++ 2013 Redistribuível (x64) - 12.0.40660 está instalado no VM
    - Tipo de arranque do serviço De Cópia de Sombra de Volume (VSS) alterado para automático a partir de manual
    - Serviço IaaSVmProvider Windows é adicionado

1. Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

![Arquitetura de backup de máquina virtual Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Encriptação de backups Azure VM

Quando faz backup de VMs Azure com Backup Azure, os VMs são encriptados em repouso com encriptação do serviço de armazenamento (SSE). O Azure Backup também pode fazer backup de VMs Azure que são encriptados utilizando encriptação de disco azure.

**Encriptação** | **Detalhes** | **Suporte**
--- | --- | ---
**Azure Disk Encryption** | A encriptação do disco azure encripta os sistemas operativos e de dados para VMs Azure.<br/><br/> A encriptação do disco azure integra-se com as chaves de encriptação BitLocker (BEKs), que são salvaguardadas num cofre chave como segredos. A encriptação do disco azure também se integra com chaves de encriptação chave Azure Key Vault (KEKs). | O Azure Backup suporta backup de VMs Azure geridos e não geridos encriptados apenas com BEKs, ou com BEKs juntamente com KEKs.<br/><br/> Tanto os BEKs como os KEKs estão apoiados e encriptados.<br/><br/> Como keKs e BEKs estão apoiados, os utilizadores com as permissões necessárias podem restaurar chaves e segredos de volta ao cofre chave, se necessário. Estes utilizadores também podem recuperar o VM encriptado.<br/><br/> Chaves e segredos encriptados não podem ser lidos por utilizadores não autorizados ou pelo Azure.
**SSE** | Com a SSE, o Azure Storage fornece encriptação em repouso, encriptando automaticamente os dados antes de os armazenar. O Azure Storage também desencripta dados antes de os recuperar. | O Azure Backup utiliza a SSE para encriptação em repouso de VMs Azure.

Para VMs Azure geridos e não geridos, o Backup suporta ambos os VMs encriptados apenas com BEKs ou VMs encriptados com BEKs juntamente com KEKs.

Os BEKs (segredos) e KEKs (chaves) estão encriptados. Só podem ser lidos e usados quando são restaurados de volta ao cofre por utilizadores autorizados. Nem utilizadores não autorizados, nem o Azure, podem ler ou usar chaves ou segredos.

Os BEKs também estão apoiados. Assim, se os BEKs estiverem perdidos, os utilizadores autorizados podem restaurar os BEKs no cofre da chave e recuperar os VMs encriptados. Apenas os utilizadores com o nível de permissões necessários podem fazer o backup e restaurar VMs encriptados ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação instantânea

O Azure Backup tira fotografias de acordo com o horário de reserva.

- **VMs do Windows:** Para vMs windows, o serviço de backup coordena com VSS para tirar uma foto consistente com aplicações dos discos VM.  Por predefinição, o Azure Backup recebe uma cópia de segurança VSS completa (trunca os registos de aplicação como o SQL Server no momento da cópia de segurança para obter uma cópia de segurança consistente do nível de aplicação).  Se estiver a utilizar uma base de dados do SQL Server na cópia de segurança Azure VM, pode modificar a definição para obter uma cópia de segurança VSS Copy (para preservar registos). Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **VMs Linux:** Para tirar fotos consistentes com aplicativos de VMs Linux, use a estrutura pré-script e pós-script do Linux para escrever os seus próprios scripts personalizados para garantir a consistência.

  - O Azure Backup invoca apenas os scripts pré/post escritos por si.
  - Se os pré-scripts e pós-scripts executarem com sucesso, o Azure Backup marca o ponto de recuperação como consistente com aplicações. No entanto, quando se está a usar scripts personalizados, é supor a consistência da aplicação.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar scripts.

## <a name="snapshot-consistency"></a>Consistência instantânea

A tabela a seguir explica os diferentes tipos de consistência instantânea:

**Instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Consistente com aplicações** | Cópias de segurança consistentes com aplicações captam conteúdo de memória e operações de I/O pendentes. Os instantâneos consistentes com aplicações utilizam um escritor VSS (ou scripts pré/post para linux) para garantir a consistência dos dados da aplicação antes de ocorrer uma cópia de segurança. | Quando se está a recuperar um VM com uma imagem de instantâneo consistente com aplicações, o VM arranca. Não há dados de corrupção ou perda. As aplicações começam num estado consistente. | Janelas: Todos os escritores do VSS conseguiram<br/><br/> Linux: Os scripts pré/post são configurados e bem sucedidos
**Sistema de ficheiros consistente** | As cópias de segurança consistentes do sistema de ficheiros proporcionam consistência, tirando uma fotografia de todos os ficheiros ao mesmo tempo.<br/><br/> | Quando se está a recuperar um VM com uma imagem consistente do sistema de ficheiros, o VM arranca. Não há dados de corrupção ou perda. As aplicações precisam de implementar o seu próprio mecanismo de "fixação" para garantir que os dados restaurados são consistentes. | Janelas: Alguns escritores do VSS falharam <br/><br/> Linux: Padrão (se os scripts pré/post não estiverem configurados ou falhados)
**Consistente com acidentes** | Normalmente ocorrem instantâneos consistentes com acidentes se um VM Azure desligar no momento da cópia de segurança. Apenas os dados que já existem no disco no momento da cópia de segurança são capturados e apoiados. | Começa com o processo de arranque vM seguido de uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou operações de escrita que não tenham sido transferidas para o disco antes do acidente se perderem. As aplicações implementam a sua própria verificação de dados. Por exemplo, uma aplicação de base de dados pode usar o seu registo de transações para verificação. Se o registo de transações tiver entradas que não estejam na base de dados, o software de base de dados rerola as transações até que os dados sejam consistentes. | A VM está em estado de encerramento (parado/deallocated).

## <a name="backup-and-restore-considerations"></a>Backup e restaurar considerações

**Consideração** | **Detalhes**
--- | ---
**Disk** | A cópia de segurança dos discos VM é paralela. Por exemplo, se um VM tiver quatro discos, o serviço de backup tenta fazer backup dos quatro discos em paralelo. A cópia de segurança é incremental (apenas dados alterados).
**Agendamento** |  Para reduzir o tráfego de backup, faça backup de Diferentes VMs em diferentes horas do dia e certifique-se de que os tempos não se sobrepõem. O apoio às VMs ao mesmo tempo causa engarrafamentos.
**Preparação de backups** | Tenha em mente o tempo necessário para preparar o reforço. O tempo de preparação inclui instalar ou atualizar a extensão de reserva e desencadear uma imagem instantânea de acordo com o calendário de cópia de segurança.
**Transferência de dados** | Considere o tempo necessário para o Azure Backup identificar as alterações incrementais da cópia de segurança anterior.<br/><br/> Numa cópia de segurança incremental, a Azure Backup determina as alterações calculando a verificação do bloco. Se um bloco for alterado, está marcado para ser transferido para o cofre. O serviço analisa os blocos identificados para tentar minimizar ainda mais a quantidade de dados a transferir. Depois de avaliar todos os blocos alterados, o Azure Backup transfere as alterações para o cofre.<br/><br/> Pode haver um intervalo entre tirar a foto e copiá-la para o cofre.<br/><br/> Em horas de pico, pode levar até oito horas para que os backups sejam processados. O tempo de reserva para um VM será inferior a 24 horas para o backup diário.
**Cópia de segurança inicial** | Embora o tempo total de backup para backups incrementais seja inferior a 24 horas, isso pode não ser o caso para a primeira cópia de segurança. O tempo necessário para a cópia de segurança inicial dependerá do tamanho dos dados e quando a cópia de segurança for processada.
**Restaurar a fila** | Os processos de backup azure restauram os empregos de várias contas de armazenamento ao mesmo tempo, e coloca pedidos de restauro numa fila.
**Restaurar a cópia** | Durante o processo de restauro, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo total de restauro depende das operações de I/S por segundo (IOPS) e da entrada da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras aplicações escreve e lê.

### <a name="backup-performance"></a>Desempenho de backup

Estes cenários comuns podem afetar o tempo total de backup:

- **Adicionar um novo disco a um VM Azure protegido:** Se um VM estiver a ser submetido a uma cópia de segurança incremental e for adicionado um novo disco, o tempo de backup aumentará. O tempo total de backup pode durar mais de 24 horas devido à replicação inicial do novo disco, juntamente com a replicação delta dos discos existentes.
- **Discos fragmentados:** As operações de backup são mais rápidas quando as alterações do disco são contíguas. Se as alterações forem espalhadas e fragmentadas através de um disco, a cópia de segurança será mais lenta.
- **Churn de disco:** Se os discos protegidos que estão a ser submetidos a uma cópia de segurança incremental tiverem um churn diário superior a 200 GB, a cópia de segurança pode demorar muito tempo (mais de oito horas) a ser concluída.
- **Versões de backup:** A versão mais recente de Backup (conhecida como versão Instant Restore) utiliza um processo mais otimizado do que a comparação de verificação para identificar alterações. Mas se estiver a usar o Instant Restore e tiver eliminado uma imagem de backup, a cópia de segurança muda para a comparação de verificação. Neste caso, a operação de reserva excederá 24 horas (ou falhará).

## <a name="best-practices"></a>Melhores práticas

Quando estiver a configurar backups VM, sugerimos seguir estas práticas:

- Modifique os horários de predefinição definidos numa política. Por exemplo, se o tempo de incumprimento na apólice for de 12:00, incremente o tempo em vários minutos para que os recursos sejam otimizados.
- Se está a restaurar os VMs de um único cofre, recomendamos vivamente que utilize diferentes contas de [armazenamento v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) de uso geral para garantir que a conta de armazenamento alvo não seja estrangulada. Por exemplo, cada VM deve ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restaurados, utilize 10 contas de armazenamento diferentes.
- Para backup de VMs que estão usando armazenamento premium, com Instant Restore, recomendamos alocação *de 50%* de espaço livre do espaço total de armazenamento atribuído, que é necessário **apenas** para a primeira cópia de segurança. O espaço 50% livre não é um requisito para backups após a primeira cópia de segurança estar completa
- O limite do número de discos por conta de armazenamento é relativo à forte adesão dos discos por aplicações que estão a funcionar numa infraestrutura como VM de serviço (IaaS). Como prática geral, se 5 a 10 discos estiverem presentes numa única conta de armazenamento, equilibre a carga movendo alguns discos para separar contas de armazenamento.

## <a name="backup-costs"></a>Custos de backup

Os VMs azure apoiados com o Azure Backup estão sujeitos ao preço de [backup do Azure.](https://azure.microsoft.com/pricing/details/backup/)

A faturação só começa quando o primeiro reforço bem sucedido terminar. Neste ponto, começa a faturação tanto para os VMs de armazenamento como para os VMs protegidos. A faturação continua enquanto os dados de reserva para o VM forem armazenados num cofre. Se parar a proteção para um VM, mas os dados de backup para o VM existem num cofre, a faturação continua.

A faturação de um VM especificado só para se a proteção for interrompida e todos os dados de cópia de segurança forem eliminados. Quando a proteção para e não há trabalhos de backup ativos, o tamanho do último backup vm bem sucedido torna-se o tamanho da instância protegida usada para a fatura mensal.

O cálculo do tamanho da instância protegida baseia-se no tamanho *real* do VM. O tamanho do VM é a soma de todos os dados do VM, excluindo o armazenamento temporário. Os preços baseiam-se nos dados reais armazenados nos discos de dados, e não no tamanho máximo suportado para cada disco de dados que está ligado ao VM.

Da mesma forma, a conta de armazenamento de backup baseia-se na quantidade de dados armazenados no Azure Backup, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, tome um VM de tamanho A2-Standard que tenha dois discos de dados adicionais com um tamanho máximo de 32 TB cada. O quadro seguinte mostra os dados reais armazenados em cada um destes discos:

**Disk** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do SO | 32 TB | 17 GB
Disco local/temporário | 135 GB | 5 GB (não incluído para cópia de segurança)
Disco de dados 1 | 32 TB| 30 GB
Disco de dados 2 | 32 TB | 0 GB

O tamanho real do VM neste caso é de 17 GB + 30 GB + 0 GB = 47 GB. Este tamanho de instância protegida (47 GB) torna-se a base para a fatura mensal. À medida que a quantidade de dados no VM aumenta, o tamanho da instância protegida utilizado para alterações de faturação corresponde.

## <a name="next-steps"></a>Passos seguintes

Agora, [preparem-se para o reforço Azure VM.](backup-azure-arm-vms-prepare.md)
