---
title: Acerca das cópias de segurança de VMs do Azure
description: Saiba mais sobre a cópia de segurança de VM do Azure e tenha em atenção algumas práticas recomendadas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 1e80b2083a2fce90259ac0634d9e7f796f459fcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880967"
---
# <a name="about-azure-vm-backup"></a>Acerca das cópias de segurança de VMs do Azure

Este artigo descreve como o [serviço de cópia de segurança do Azure](backup-introduction-to-azure-backup.md) efetua uma cópia de segurança das máquinas virtuais do Azure (VMs).

## <a name="backup-process"></a>Processo de cópia de segurança

Eis como cópia de segurança do Azure é concluída uma cópia de segurança para VMs do Azure:

1. Para as VMs do Azure que estão selecionadas para cópia de segurança, cópia de segurança do Azure inicia uma tarefa de cópia de segurança, de acordo com a agenda de cópia de segurança que especificar.
1. Durante a primeira cópia de segurança, uma extensão de cópia de segurança está instalada na VM, se a VM está em execução.
    - Para VMs do Windows, o _VMSnapshot_ extensão está instalada.
    - Para VMs do Linux, o _VMSnapshotLinux_ extensão está instalada.
1. Para as VMs do Windows que estão a ser executado, cópia de segurança coordena com Windows Volume de cópia sombra Service (VSS) para tirar um instantâneo consistente com a aplicação da VM.
    - Por predefinição, a cópia de segurança permite cópias de segurança completas do VSS.
    - Se a cópia de segurança não é possível tirar um instantâneo consistente com a aplicação, em seguida, demora um instantâneo consistente com ficheiros do armazenamento subjacente (porque não escritas das aplicações ocorrem enquanto a VM está parada).
1. Para VMs do Linux, a cópia de segurança demora uma cópia de segurança consistente com ficheiros. Para instantâneos consistentes com a aplicação, terá de personalizar os scripts de pré/pós.
1. Depois de cópia de segurança tira o instantâneo, transfere os dados para o cofre. 
    - A cópia de segurança está otimizada fazendo o backup de cada disco VM em paralelo.
    - Para cada disco que é a cópia de segurança, cópia de segurança do Azure lê os blocos no disco e identifica e transfere apenas os blocos de dados alterados (o delta) desde a cópia de segurança anterior.
    - Dados de instantâneos podem não ser imediatamente copiados para o cofre. Poderá demorar algumas horas em períodos de pico. Tempo total de cópia de segurança para uma VM será inferior a 24 horas para políticas de cópia de segurança diárias.

1. Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

![Arquitetura de cópia de segurança de máquina virtual do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Encriptação de cópias de segurança de VM do Azure

Quando cria cópias de segurança de VMs do Azure com o Azure Backup, as VMs são encriptadas em descanso com o Storage Service Encryption (SSE). Cópia de segurança do Azure pode também fazer cópias de segurança de VMs do Azure que são encriptados utilizando o Azure Disk Encryption.

**Encriptação** | **Detalhes** | **Suporte**
--- | --- | ---
**Azure Disk Encryption** | O Azure Disk Encryption encripta dados e SO de discos para VMs do Azure.<br/><br/> O Azure Disk Encryption integra-se com chaves de encriptação de disco BitLocker (BEKs), que são salvaguardadas como segredos num cofre de chaves. O Azure Disk Encryption também se integra com chaves de encriptação de chave do Azure Key Vault (KEKs). | O Azure Backup suporta a cópia de segurança de VMs do Azure geridos e encriptados BEKs apenas ou com BEKs juntamente com KEKs.<br/><br/> BEKs e KEKs uma cópia de segurança e encriptados.<br/><br/> Uma vez KEKs e BEKs são uma cópia de segurança, os utilizadores com as permissões necessárias, podem restaurar as chaves e segredos de volta para o Cofre de chaves se for necessário. Estes utilizadores também podem recuperar a VM encriptada.<br/><br/> Não não possível ler encriptadas chaves e segredos por utilizadores não autorizados ou pelo Azure.
**SSE** | E o SSE, o armazenamento do Azure fornece encriptação em repouso ao encriptar automaticamente os dados antes de os armazenar. O armazenamento do Azure também desencripta-os antes de recuperá-lo. | O Azure Backup utiliza o SSE para a encriptação de inativos de VMs do Azure.

Para VMs do Azure geridos e não geridos, o Backup suporta VMs encriptadas com BEKs apenas ou VMs encriptadas com BEKs juntamente com KEKs.

A cópia de segurança BEKs (segredos) e KEKs (chaves) são encriptados. Podem ser lidos e utilizados apenas quando estiver a restaurar para o Cofre de chaves por utilizadores autorizados. Nem os utilizadores não autorizados nem para o Azure, pode ler ou utilizar uma cópia de segurança de chaves ou segredos.

BEKs são também uma cópia de segurança. Então, se o BEKs são perdidos, os utilizadores autorizados podem restaurar as BEKs ao Cofre de chaves e recuperar as VMs encriptadas. Apenas os utilizadores com o nível necessário de permissões podem criar cópias de segurança e restaurar VMs encriptadas ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação do instantâneo

O Azure Backup tira instantâneos de acordo com a agenda de cópia de segurança. 

- **VMs do Windows:** Para VMs do Windows, o serviço de cópia de segurança coordena com o VSS para criar um instantâneo consistente com a aplicação dos discos da VM.

  - Por predefinição, o Azure Backup permite cópias de segurança completas do VSS. [Saiba mais](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Para alterar a definição para que o Azure Backup tira cópias de segurança de cópia VSS, defina a seguinte chave de registro num prompt de comando:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **VMs do Linux:** Para tirar instantâneos consistentes com a aplicação de VMs do Linux, utilize o script prévio de Linux e estrutura para escrever seus próprios scripts personalizados para garantir a consistência do script posterior.

  - O Azure Backup invoca apenas os scripts de pré/pós escritos por.
  - Se os pré- scripts de e os pós-scripts de executam com êxito, o Azure Backup marca o ponto de recuperação consistentes com aplicações como. No entanto, quando estiver usando scripts personalizados, está, por fim, responsável pela consistência de aplicação.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar os scripts.

### <a name="snapshot-consistency"></a>Consistência de instantâneo

A tabela seguinte explica os diferentes tipos de consistência do instantâneo:

**instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Application-consistent** | As cópias de segurança consistente com a aplicação capturam a memória conteúda e operações de e/s pendentes. Instantâneos consistentes com a aplicação utilizam um escritor VSS (ou scripts de pré/pós para Linux) para assegurar a consistência dos dados da aplicação antes de ocorre uma cópia de segurança. | Quando estiver a recuperar uma VM com um instantâneo consistente com a aplicação, a VM arranca. Não existe danos em dados ou perda. Os aplicativos começam num estado consistente. | Windows: Todos os escritores VSS foi concluída com êxito<br/><br/> Linux: Pré/pós scripts são configurados e foi concluída com êxito
**Consistente com o ficheiro de sistema** | Cópias de segurança do sistema de ficheiros fornecem consistência através de um instantâneo de todos os ficheiros ao mesmo tempo.<br/><br/> | Quando estiver a recuperar uma VM com um instantâneo consistente do sistema de ficheiros, a VM arranca. Não existe danos em dados ou perda. As aplicações necessitam implementar seu próprio mecanismo de "correção de segurança" para se certificar de que os dados restaurados são consistentes. | Windows: Alguns dos escritores VSS falhou <br/><br/> Linux: A predefinição (se os scripts de pré/pós não são configurados ou falhados)
**Crash-consistent** | Instantâneos consistentes com falhas ocorrem, normalmente, se uma VM do Azure for encerrado no momento da cópia de segurança. Apenas os dados que já existe no disco no momento da cópia de segurança são capturados e uma cópia de segurança.<br/><br/> Um ponto de recuperação consistentes com falhas não garante a consistência de dados para o sistema operativo ou a aplicação. | Embora não há garantias, normalmente, arranca a VM e, em seguida, inicia uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou operações de escrita que não foram transferidas para o disco antes da falha são perdidos. Aplicações implementam a verificação de seus próprios dados. Por exemplo, uma aplicação de base de dados pode utilizar os logs de transação para a verificação. Se o registo de transações tem entradas que não estão na base de dados, o software de base de dados agrega as transações até que os dados são consistentes. | VM está num Estado de encerramento

## <a name="backup-and-restore-considerations"></a>Considerações sobre a cópia de segurança e restauro 

**Consideração** | **Detalhes**
--- | ---
**Disco** | Cópia de segurança de discos de VM é paralela. Por exemplo, se uma VM tem quatro discos, o serviço de cópia de segurança tenta fazer cópias de segurança de todos os discos de quatro em paralelo. Cópia de segurança é incremental (só de dados alterados).
**Agendamento** |  Para reduzir o tráfego de cópia de segurança, cópia de segurança de VMs diferentes em momentos diferentes do dia e certificar-se de que os tempos de não se sobrepõem. Cópia de segurança de VMs ao mesmo tempo faz com que o tráfego jams.
**A preparar cópias de segurança** | Tenha em mente o tempo necessário para preparar a cópia de segurança. O tempo de preparação inclui instalar ou atualizar a extensão de cópia de segurança e acionar um instantâneo de acordo com a agenda de cópia de segurança.
**Transferência de dados** | Considere o tempo necessário para o Azure Backup identificar as alterações incrementais da cópia de segurança anterior.<br/><br/> Numa cópia de segurança incremental, cópia de segurança do Azure determina as alterações, calculando a soma de verificação do bloco. Se um bloco for alterado, que está marcado para transferência para o cofre. O serviço analisa os blocos identificados para tentar minimizar ainda mais a quantidade de dados a transferir. Depois de avaliar os blocos alterados, a cópia de segurança do Azure transfere as alterações para o cofre.<br/><br/> Pode haver um atraso entre o instantâneo e copiá-lo para o cofre.<br/><br/> Em períodos de pico, pode demorar até oito horas para cópias de segurança a serem processados. O tempo de cópia de segurança para uma VM será inferior a 24 horas para a cópia de segurança diária.
**Cópia de segurança inicial** | Embora o tempo total de cópia de segurança para cópias de segurança incrementais é inferior a 24 horas, o que pode não ser o caso da primeira cópia de segurança. O tempo necessário para a cópia de segurança inicial dependerá do tamanho dos dados e quando a cópia de segurança é processada.
**Restaurar a fila** | Processos de cópia de segurança do Azure restaurar trabalhos a partir de várias contas de armazenamento ao mesmo tempo, e coloca os pedidos de restauro numa fila.
**Restaurar cópia** | Durante o processo de restauro, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo de restauro total depende as operações de e/s por segundo (IOPS) e o débito da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não é carregada com outras aplicações escritas e leituras.

### <a name="backup-performance"></a>Desempenho de cópia de segurança

Esses cenários comuns podem afetar o tempo de cópia de segurança total:

- **Adicionar um novo disco a uma VM do Azure protegidos:** Se uma VM está em cópia de segurança incremental e é adicionado um novo disco, irá aumentar o tempo de cópia de segurança. O tempo de cópia de segurança total pode durar mais de 24 horas por causa da replicação inicial do novo disco, juntamente com a replicação de diferenças de discos existentes.
- **Discos fragmentados:** Operações de cópia de segurança são mais rápidas, quando as alterações de disco sejam contíguas. Se as alterações são distribuídas e fragmentadas num disco, a cópia de segurança será mais lenta. 
- **Alterações de disco:** Se os discos que estão passando por cópia de segurança incremental de protegidos têm uma diária de alterações de mais de 200 GB, cópia de segurança pode demorar muito tempo (mais de oito horas) para concluir. 
- **Versões de cópia de segurança:** A versão mais recente da cópia de segurança (conhecida como a versão de restaurar instantânea) usa um processo mais otimizado de comparação de soma de verificação para identificar as alterações. Mas se estiver usando a restaurar instantânea e eliminou um instantâneo de cópia de segurança, a cópia de segurança muda para comparação de soma de verificação. Neste caso, a operação de cópia de segurança irá exceder 24 horas (ou falha).

## <a name="best-practices"></a>Melhores práticas

Quando estiver a configurar cópias de segurança VM, sugerimos seguindo essas práticas:

- Modificar as horas de agenda predefinida que estão definidas numa política. Por exemplo, se o tempo predefinido na política 12:00 AM, incremente o período de tempo por vários minutos para que os recursos são utilizados de forma otimizada.
- Para a cópia de segurança de VMs que estão a utilizar o armazenamento premium, recomendamos a execução da versão mais recente da cópia de segurança do Azure ([restaurar instantâneas](backup-instant-restore-capability.md)). Se não estiver a executar a versão mais recente, cópia de segurança aloca aproximadamente 50 por cento de espaço de armazenamento total. O serviço de cópia de segurança requer este espaço para copiar o instantâneo para a mesma conta de armazenamento e para transferi-los para o cofre.
- Se estiver a restaurar VMs de um único cofre, recomendamos vivamente que utilize diferentes [contas de armazenamento para fins gerais v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para se certificar de que a conta de armazenamento de destino não é limitada. Por exemplo, cada VM tem de ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restauradas, utilize 10 contas de armazenamento diferente.
- As restaurações de uma camada de armazenamento para fins gerais v1 (instantâneo) serão concluídas em minutos porque o instantâneo é na mesma conta de armazenamento. Restauros da camada de armazenamento para fins gerais v2 (cofre) podem demorar horas. Em casos em que os dados estão disponíveis no armazenamento de fins gerais v1, recomendamos que utilize o [restaurar instantâneas](backup-instant-restore-capability.md) funcionalidade para restauros mais rápidos. (Se os dados têm de ser restaurados a partir de um cofre, em seguida, demorará mais tempo.)
- O limite no número de discos por conta de armazenamento é relativo ao quanto os discos estão a ser acedidos por aplicações que são executadas numa infraestrutura como serviço (IaaS) VM. Como uma prática geral, se os discos de 5 a 10 ou mais estiverem presentes numa única conta de armazenamento, balancear a carga ao mover alguns discos para separar as contas de armazenamento.

## <a name="backup-costs"></a>Custos de cópia de segurança

VMs do Azure, uma cópia de segurança com o Azure Backup estão sujeitos aos [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).

Faturação não iniciada até a conclusão da primeira cópia de segurança com êxito. Neste momento, começa a faturação para armazenamento e VMs protegidas. Faturação continua, desde que os dados de cópia de segurança para a VM é armazenados num cofre. Se parar a proteção para uma VM, mas os dados de cópia de segurança para a VM existem num cofre, continua a faturação.

Deixa de faturação de uma VM especificada apenas se a proteção é parada e todos os dados de cópia de segurança é eliminada. Quando parar a proteção e não há nenhuma tarefa de cópia de segurança Active Directory, o tamanho do último backup bem-sucedido de VM torna-se o tamanho de instância protegida utilizado para a fatura mensal.

O cálculo do tamanho de instância protegida se baseia a *real* tamanho da VM. Tamanho da VM é a soma de todos os dados na VM, excluindo o armazenamento temporário. Preço baseia-se nos dados reais que tem armazenados nos discos de dados, não no máximo suportado tamanho de cada disco de dados que está ligado à VM.

Da mesma forma, a fatura de armazenamento de cópia de segurança baseia-se na quantidade de dados que são armazenados em cópia de segurança do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, dê uma VM de tamanho A2-padrão de mensagens em fila que tiver dois discos de dados adicionais com um tamanho máximo de 4 TB cada. A tabela seguinte mostra os dados reais armazenados em cada um destes discos:

**Disco** | **Tamanho máx.** | **Dados reais presentes**
--- | --- | ---
Disco do SO | 4095 GB | 17 GB
Disco local/temporária | 135 GB | 5 GB (não incluído para cópia de segurança)
Disco de dados 1 | 4095 GB | 30 GB
Disco de dados 2 | 4095 GB | 0 GB

Neste caso, o tamanho real da VM é 17 GB + 30 GB + 0 GB = 47 GB. Este tamanho de instância protegida (47 GB) torna-se a base para a fatura mensal. À medida que aumenta a quantidade de dados na VM, o tamanho de instância protegida é utilizado para alterações de cobrança para corresponder.

## <a name="next-steps"></a>Passos Seguintes

Agora, [preparar a cópia de segurança de VM do Azure](backup-azure-arm-vms-prepare.md).
