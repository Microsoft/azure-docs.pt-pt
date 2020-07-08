---
title: Acerca das cópias de segurança de VMs do Azure
description: Neste artigo, saiba como o serviço Azure Backup apoia as máquinas Azure Virtual e como seguir as melhores práticas.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 9838f4993e71f2991500af0e152abee36f996050
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322914"
---
# <a name="an-overview-of-azure-vm-backup"></a>Uma visão geral do backup Azure VM

Este artigo descreve como o [serviço de backup Azure](backup-introduction-to-azure-backup.md) faz backup de máquinas virtuais Azure (VMs).

A Azure Backup fornece backups independentes e isolados para proteger contra a destruição não intencional dos dados nos seus VMs. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão incorporada de pontos de recuperação. A configuração e o dimensionamento são simples, as cópias de segurança são otimizadas e pode facilmente restaurar conforme necessário.

Como parte do processo de backup, [é tirada](#snapshot-creation)uma imagem instantânea , e os dados são transferidos para o cofre dos Serviços de Recuperação sem impacto nas cargas de trabalho de produção. O instantâneo proporciona diferentes níveis de consistência, como [descrito aqui.](#snapshot-consistency)

O Azure Backup também tem ofertas especializadas para cargas de dados de base de dados como [SQL Server](backup-azure-sql-database.md) e [SAP HANA](sap-hana-db-about.md) que estão conscientes da carga de trabalho, oferecem RPO de 15 minutos (objetivo de ponto de recuperação), e permitem a cópia de segurança e restauro de bases de dados individuais.

## <a name="backup-process"></a>Processo de cópia de segurança

Eis como a Azure Backup completa uma cópia de segurança para os VMs do Azure:

1. Para os VMs Azure que são selecionados para cópia de segurança, o Azure Backup inicia um trabalho de backup de acordo com o horário de backup especificado.
1. Durante a primeira cópia de segurança, é instalada uma extensão de backup no VM se o VM estiver em funcionamento.
    - Para os VMs do Windows, a [extensão VMSnapshot](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) está instalada.
    - Para os VMs Linux, a [extensão VMSnapshotLinux](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) está instalada.
1. Para os VMs do Windows que estão em execução, as coordenadas de backup com o Windows Volume Shadow Copy Service (VSS) para tirar uma imagem consistente da aplicação do VM.
    - Por predefinição, o Backup recebe cópias de segurança VSS completas.
    - Se o Backup não conseguir tirar uma imagem consistente da aplicação, então é necessário um instantâneo consistente de ficheiro do armazenamento subjacente (porque não ocorre nenhuma aplicação escrita enquanto o VM é parado).
1. Para os VMs Linux, o Backup recebe uma cópia de segurança consistente com ficheiros. Para instantâneos consistentes com aplicações, é necessário personalizar manualmente scripts pré/post.
1. Depois de o Backup tirar a fotografia, transfere os dados para o cofre.
    - A cópia de segurança é otimizada através da cópia de segurança em paralelo com cada disco VM.
    - Para cada disco que está a ser apoiado, o Azure Backup lê os blocos no disco e identifica e transfere apenas os blocos de dados que mudaram (o delta) desde a cópia de segurança anterior.
    - Os dados do instantâneo podem não ser imediatamente copiados para o cofre. Pode levar algumas horas em horas de pico. O tempo total de backup para um VM será inferior a 24 horas para as políticas diárias de backup.
1. As alterações efetuadas a um Windows VM após a ativação do Backup Azure são:
    - Microsoft Visual C++ 2013 Redistributable (x64) - 12.0.40660 está instalado no VM
    - Tipo de arranque do serviço Volume Shadow Copy (VSS) alterado para automático a partir de manual
    - Serviço IaaSVmProvider Windows é adicionado

1. Quando a transferência de dados está completa, o instantâneo é removido e é criado um ponto de recuperação.

![Arquitetura de backup de máquina virtual Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Encriptação de backups Azure VM

Quando faz backup de VMs Azure com Backup Azure, os VMs são encriptados em repouso com encriptação do serviço de armazenamento (SSE). O Azure Backup também pode fazer backup de VMs Azure que são encriptados usando Azure Disk Encryption.

**Encriptação** | **Detalhes** | **Suporte**
--- | --- | ---
**Azure Disk Encryption** | A Azure Disk Encryption encripta tanto os discos de SO como os discos de dados para VMs Azure.<br/><br/> A encriptação do disco Azure integra-se com chaves de encriptação BitLocker (BEKs), que são salvaguardadas num cofre-chave como segredos. A encriptação do disco Azure também se integra com chaves de encriptação chave Azure Key Vault (KEKs). | O Azure Backup suporta o backup de VMs Azure geridos e não geridos encriptados apenas com BEKs, ou com BEKs juntamente com KEKs.<br/><br/> Tanto beks como KEKs são apoiados e encriptados.<br/><br/> Como keks e BEKs estão apoiados, os utilizadores com as permissões necessárias podem restaurar chaves e segredos de volta para o cofre chave, se necessário. Estes utilizadores também podem recuperar o VM encriptado.<br/><br/> Chaves e segredos encriptados não podem ser lidos por utilizadores não autorizados ou pela Azure.
**SSE** | Com a SSE, o Azure Storage fornece encriptação em repouso, encriptando automaticamente os dados antes de os armazenar. O Azure Storage também desencripta os dados antes de os recuperar. | O Azure Backup utiliza SSE para encriptação em repouso de VMs Azure.

Para VMs Azure geridos e não geridos, o Backup suporta ambos os VMs encriptados apenas com BEKs ou VMs encriptados com BEKs juntamente com KEKs.

Os BEKs (segredos) e KEKs (chaves) estão encriptados. Só podem ser lidos e utilizados quando são devolvidos ao cofre por utilizadores autorizados. Nem utilizadores não autorizados, nem a Azure, podem ler ou usar chaves ou segredos com apoio.

As BEKs também estão apoiadas. Assim, se os BEKs estiverem perdidos, os utilizadores autorizados podem restaurar as BEKs no cofre de chaves e recuperar os VMs encriptados. Apenas os utilizadores com o nível de permissões necessários podem fazer o back up e restaurar VMs encriptados ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação instantânea

O Azure Backup tira fotografias de acordo com o horário de reserva.

- **VMs do Windows:** Para os VMs do Windows, o serviço de cópia de segurança coordena com o VSS para tirar uma imagem consistente dos discos VM.  Por predefinição, o Azure Backup recebe uma cópia de segurança completa do VSS (trunca os registos de aplicação como o SQL Server no momento da cópia de segurança para obter uma cópia de segurança consistente ao nível da aplicação).  Se estiver a utilizar uma base de dados do SQL Server na cópia de segurança do Azure VM, pode modificar a definição para obter uma cópia de segurança VSS Copy (para preservar registos). Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **VMs Linux:** Para tirar fotos consistentes de aplicativos de VMs Linux, use a estrutura de pré-script e pós-script do Linux para escrever os seus próprios scripts personalizados para garantir a consistência.

  - O Azure Backup invoca apenas os scripts pré/post escritos por si.
  - Se os pré-scripts e pós-scripts forem executados com sucesso, o Azure Backup marca o ponto de recuperação como consistente com a aplicação. No entanto, quando está a usar scripts personalizados, é responsável pela consistência da aplicação.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar as escrituras.

## <a name="snapshot-consistency"></a>Consistência instantânea

A tabela a seguir explica os diferentes tipos de consistência instantânea:

**Instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Consistente com aplicação** | As cópias de segurança consistentes em termos de aplicações captam o conteúdo da memória e as operações de I/O pendentes. Os instantâneos consistentes com aplicações utilizam um escritor VSS (ou scripts pré/post para Linux) para garantir a consistência dos dados da aplicação antes de ocorrer uma cópia de segurança. | Quando está a recuperar um VM com uma imagem consistente com uma aplicação, o VM arranca. Não há corrupção ou perda de dados. As aplicações começam num estado consistente. | Windows: Todos os escritores da VSS sucederam<br/><br/> Linux: Os scripts pré/post são configurados e bem sucedidos
**Sistema de ficheiros consistente** | As cópias de segurança consistentes do sistema de ficheiros proporcionam consistência, tirando uma imagem instantânea de todos os ficheiros ao mesmo tempo.<br/><br/> | Quando está a recuperar um VM com uma imagem consistente do sistema de ficheiros, o VM arranca. Não há corrupção ou perda de dados. As aplicações precisam implementar o seu próprio mecanismo de "correção" para garantir que os dados restaurados são consistentes. | Windows: Alguns escritores do VSS falharam <br/><br/> Linux: Padrão (se os scripts pré/post não estiverem configurados ou falhados)
**Consistente com acidentes** | Normalmente, ocorrem instantâneos consistentes em acidentes se um VM Azure desligar no momento da cópia de segurança. Apenas os dados que já existem no disco no momento da cópia de segurança são capturados e apoiados. | Começa com o processo de arranque VM seguido de uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou operações de escrita que não tenham sido transferidas para o disco antes do acidente se perderem. As aplicações implementam a sua própria verificação de dados. Por exemplo, uma aplicação de base de dados pode usar o seu registo de transações para verificação. Se o registo de transações tiver entradas que não estão na base de dados, o software de base de dados ressa o resultado até que os dados sejam consistentes. | A VM está em estado de encerramento (parado/deallocated).

## <a name="backup-and-restore-considerations"></a>Considerações sobre a cópia de segurança e o restauro

**Consideração** | **Detalhes**
--- | ---
**Disco** | A cópia de segurança dos discos VM é paralela. Por exemplo, se um VM tiver quatro discos, o serviço de cópia de segurança tenta fazer backup dos quatro discos em paralelo. A cópia de segurança é incremental (apenas dados alterados).
**Agendamento** |  Para reduzir o tráfego de backup, faça uma cópia de segurança de diferentes VMs em diferentes horas do dia e certifique-se de que os tempos não se sobrepõem. Fazer cópias de segurança das VMs ao mesmo tempo provoca congestão do tráfego.
**Preparação de backups** | Tenha em mente o tempo necessário para preparar o backup. O tempo de preparação inclui a instalação ou a atualização da extensão da cópia de segurança e o acionamento de um instantâneo de acordo com o agendamento da cópia de segurança.
**Transferência de dados** | Considere o tempo necessário para o Azure Backup identificar as alterações incrementais da cópia de segurança anterior.<br/><br/> Numa cópia de segurança incremental, o Azure Backup determina as alterações ao calcular a soma de verificação do bloco. Se um bloco for alterado, será assinalado para transferência para o cofre. O serviço analisa os blocos identificados para tentar minimizar ainda mais a quantidade de dados a transferir. Após avaliar todos os blocos alterados, o Azure Backup transfere as alterações para o cofre.<br/><br/> Pode existir um atraso entre o momento da captura do instantâneo e a sua cópia para o cofre. Em horas de pico, pode levar até oito horas para as fotos serem transferidas para o cofre. O tempo de cópia de segurança de uma VM será inferior a 24 horas se for uma cópia de segurança diária.
**Cópia de segurança inicial** | Apesar de o tempo total das cópias de segurança incrementais ser inferior a 24 horas, este poderá não ser o caso para a primeira cópia de segurança. O tempo necessário para a cópia de segurança inicial dependerá do tamanho dos dados e de quando a cópia de segurança é processada.
**Restaurar a fila** | Os processos de Backup Azure restauram os trabalhos de várias contas de armazenamento ao mesmo tempo, e coloca os pedidos de restauro numa fila.
**Restaurar cópia** | Durante o processo de restauro, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo total de restauro depende das operações de E/S por segundo (IOPS) e do rendimento da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras aplicações que escrevam e lêem.

### <a name="backup-performance"></a>Desempenho de backup

Estes cenários comuns podem afetar o tempo total de backup:

- **Adicionar um novo disco a um Azure VM protegido:** Se um VM estiver a passar por uma cópia de segurança incremental e for adicionado um novo disco, o tempo de cópia de segurança aumentará. O tempo total de backup pode durar mais de 24 horas devido à replicação inicial do novo disco, juntamente com a replicação delta dos discos existentes.
- **Discos fragmentados:** As operações de backup são mais rápidas quando as mudanças de disco são contíguas. Se as alterações forem espalhadas e fragmentadas através de um disco, a cópia de segurança será mais lenta.
- **Churn de disco:** Se os discos protegidos que estão a passar por uma cópia de segurança incremental tiverem uma vibração diária de mais de 200 GB, a cópia de segurança pode demorar muito tempo (mais de oito horas) a ser concluída.
- **Versões de backup:** A versão mais recente do Backup (conhecida como versão Instant Restore) utiliza um processo mais otimizado do que a comparação de checkum para identificar alterações. Mas se estiver a utilizar o Instant Restore e tiver eliminado uma imagem de backup, a cópia de segurança muda para a comparação de dados. Neste caso, a operação de reserva excederá 24 horas (ou falhará).

## <a name="best-practices"></a>Melhores práticas

Quando está a configurar cópias de segurança da VM, sugerimos que siga estas práticas:

- Modifique as horas de agendamento predefinidas que são definidas numa política. Por exemplo, se a hora predefinida na política for 12:00, aumente o tempo em vários minutos para que os recursos sejam utilizados de forma ideal.
- Se estiver a restaurar os VMs de um único cofre, recomendamos vivamente que utilize [diferentes contas de armazenamento v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para garantir que a conta de armazenamento do alvo não seja estrangulada. Por exemplo, cada VM deve ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restaurados, utilize 10 contas de armazenamento diferentes.
- Para a cópia de segurança dos VM que estão a utilizar armazenamento premium, com Instant Restore, recomendamos a atribuição de *50%* de espaço livre do espaço total de armazenamento atribuído, que é necessário **apenas** para a primeira cópia de segurança. Os 50% de espaço livre não são um requisito para as cópias de segurança após a primeira cópia de segurança estar concluída
- O limite ao número de discos por conta de armazenamento é relativo à frequência de acesso aos discos pelas aplicações que estão a ser executadas numa VM IaaS (infraestrutura como serviço). Como prática geral, se 5 a 10 discos ou mais estiverem presentes numa única conta de armazenamento, equilibre a carga ao mover alguns discos, de modo a separar as contas de armazenamento.

## <a name="backup-costs"></a>Custos de backup

Os VMs Azure apoiados com Azure Backup estão sujeitos ao [preço de Backup Azure](https://azure.microsoft.com/pricing/details/backup/).

A faturação só começa quando o primeiro reforço bem sucedido terminar. Neste momento, começa a faturação tanto para armazenamento como para VMs protegidos. A faturação continua enquanto os dados de reserva do VM estiverem guardados num cofre. Se parares a proteção para um VM, mas os dados de backup para o VM existem num cofre, a faturação continua.

A faturação de um VM especificado só para se a proteção for interrompida e todos os dados de backup forem eliminados. Quando a proteção para e não existem trabalhos de backup ativos, o tamanho da última cópia de segurança VM bem sucedida torna-se o tamanho de instância protegido usado para a fatura mensal.

O cálculo do tamanho de instâncias protegidas baseia-se no tamanho *real* do VM. O tamanho do VM é a soma de todos os dados no VM, excluindo o armazenamento temporário. Os preços baseiam-se nos dados reais armazenados nos discos de dados, não no tamanho máximo suportado para cada disco de dados que está ligado ao VM.

Da mesma forma, a conta de armazenamento de backup baseia-se na quantidade de dados armazenados no Azure Backup, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, tome um VM de tamanho A2 standard que tenha dois discos de dados adicionais com um tamanho máximo de 32 TB cada. A tabela a seguir mostra os dados reais armazenados em cada um destes discos:

**Disco** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do SO | 32 TB | 17 GB
Disco local/temporário | 135 GB | 5 GB (não incluído para cópia de segurança)
Disco de dados 1 | 32 TB| 30 GB
Disco de dados 2 | 32 TB | 0 GB

O tamanho real do VM neste caso é de 17 GB + 30 GB + 0 GB = 47 GB. Este tamanho de instância protegida (47 GB) torna-se a base para a fatura mensal. À medida que a quantidade de dados no VM cresce, o tamanho de instância protegida usado para alterações de faturação correspondem.

## <a name="next-steps"></a>Próximos passos

Agora, [prepare-se para o reforço Azure VM](backup-azure-arm-vms-prepare.md).
