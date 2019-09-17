---
title: Acerca das cópias de segurança de VMs do Azure
description: Saiba mais sobre o backup de VM do Azure e observe algumas práticas recomendadas.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: db3e4b8a8abea4718f5779790906bf45591d221c
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018695"
---
# <a name="an-overview-of-azure-vm-backup"></a>Uma visão geral do backup de VM do Azure

Este artigo descreve como o [serviço de backup do Azure](backup-introduction-to-azure-backup.md) faz backup de VMs (máquinas virtuais) do Azure.

## <a name="backup-process"></a>Processo de backup

Veja como o backup do Azure conclui um backup para VMs do Azure:

1. Para VMs do Azure que são selecionadas para backup, o backup do Azure inicia um trabalho de backup de acordo com o agendamento de backup especificado.
1. Durante o primeiro backup, uma extensão de backup é instalada na VM se a VM estiver em execução.
    - Para VMs do Windows, a extensão _VMSnapshot_ é instalada.
    - Para VMs do Linux, a extensão _VMSnapshotLinux_ é instalada.
1. Para VMs do Windows em execução, o backup coordena com o Windows Serviço de Cópias de Sombra de Volume (VSS) para obter um instantâneo consistente com o aplicativo da VM.
    - Por padrão, o backup faz backups completos do VSS.
    - Se o backup não puder usar um instantâneo consistente com o aplicativo, ele usará um instantâneo consistente com o arquivo do armazenamento subjacente (porque não ocorre nenhuma gravação do aplicativo enquanto a VM está parada).
1. Para VMs do Linux, o backup usa um backup consistente com o arquivo. Para instantâneos consistentes com o aplicativo, você precisa personalizar manualmente os scripts Pre/post.
1. Depois que o backup faz o instantâneo, ele transfere os dados para o cofre.
    - O backup é otimizado com o backup de cada disco de VM em paralelo.
    - Para cada disco que está sendo submetido a backup, o backup do Azure lê os blocos no disco e identifica e transfere apenas os blocos de dados que foram alterados (o Delta) desde o backup anterior.
    - Os dados de instantâneo podem não ser copiados imediatamente para o cofre. Pode levar algumas horas em horários de pico. O tempo total de backup de uma VM será menor que 24 horas para políticas de backup diárias.
 1. As alterações feitas em uma VM do Windows após a habilitação do backup do Azure são:
    -   Microsoft Visual C++ 2013 redistribuível (x64)-o 12.0.40660 está instalado na VM
    -   O tipo de inicialização do VSS (serviço de cópias de sombra de volume) foi alterado para automático do manual
    -   O serviço do Windows IaaSVmProvider foi adicionado

1. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

![Arquitetura de backup de máquina virtual do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Criptografia de backups de VM do Azure

Quando você faz backup de VMs do Azure com o backup do Azure, as VMs são criptografadas em repouso com Criptografia do Serviço de Armazenamento (SSE). O backup do Azure também pode fazer backup de VMs do Azure que são criptografadas usando Azure Disk Encryption.

**Encriptação** | **Detalhes** | **Suporte**
--- | --- | ---
**Azure Disk Encryption** | O Azure Disk Encryption criptografa tanto o sistema operacional quanto os discos de dados para VMs do Azure.<br/><br/> O Azure Disk Encryption se integra com as chaves de criptografia do BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com Azure Key Vault chaves de criptografia de chave (KEKs). | O backup do Azure dá suporte ao backup de VMs do Azure gerenciadas e não gerenciadas criptografadas somente com o BEKs ou com o BEKs junto com KEKs.<br/><br/> É feito backup e criptografia de BEKs e KEKs.<br/><br/> Como o backup de KEKs e BEKs é feito, os usuários com as permissões necessárias podem restaurar chaves e segredos de volta para o cofre de chaves, se necessário. Esses usuários também podem recuperar a VM criptografada.<br/><br/> Chaves e segredos criptografados não podem ser lidos por usuários não autorizados ou pelo Azure.
**SSE** | Com o SSE, o armazenamento do Azure fornece criptografia em repouso criptografando automaticamente os dados antes de armazená-los. O armazenamento do Azure também descriptografa os dados antes de recuperá-los. | O backup do Azure usa SSE para criptografia em repouso de VMs do Azure.

Para VMs do Azure gerenciadas e não gerenciadas, o backup dá suporte a ambas as VMs criptografadas com BEKs somente ou VMs criptografadas com BEKs junto com KEKs.

Os BEKs (segredos) e KEKs (chaves) com backup são criptografados. Eles podem ser lidos e usados somente quando são restaurados de volta ao cofre de chaves por usuários autorizados. Nem usuários não autorizados nem o Azure podem ler ou usar chaves ou segredos de backup.

Também é feito backup de BEKs. Portanto, se os BEKs forem perdidos, os usuários autorizados poderão restaurar o BEKs para o cofre de chaves e recuperar as VMs criptografadas. Somente os usuários com o nível necessário de permissões podem fazer backup e restaurar VMs criptografadas ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação de instantâneo

O backup do Azure faz instantâneos de acordo com o agendamento de backup.

- **VMs do Windows:** Para VMs do Windows, o serviço de backup coordena com o VSS para obter um instantâneo consistente com o aplicativo dos discos de VM.

  - Por padrão, o backup do Azure faz backups completos do VSS. [Saiba mais](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Para alterar a configuração de modo que o backup do Azure faça backups de cópia VSS, defina a seguinte chave do registro em um prompt de comando:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **VMs do Linux:** Para obter instantâneos consistentes com o aplicativo de VMs do Linux, use a estrutura de pré e pós-script do Linux para escrever seus próprios scripts personalizados para garantir a consistência.

  - O backup do Azure invoca apenas os scripts de pré/pós gravados por você.
  - Se os scripts anteriores e pós-scripts forem executados com êxito, o backup do Azure marcará o ponto de recuperação como consistente com o aplicativo. No entanto, quando você estiver usando scripts personalizados, você será responsável pela consistência do aplicativo.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar scripts.

### <a name="snapshot-consistency"></a>Consistência de instantâneo

A tabela a seguir explica os diferentes tipos de consistência de instantâneo:

**Instantânea** | **Detalhes** | **Automatiza** | **Consideração**
--- | --- | --- | ---
**Consistente com o aplicativo** | Backups consistentes com o aplicativo capturam conteúdo de memória e operações de e/s pendentes. Os instantâneos consistentes com o aplicativo usam um gravador VSS (ou scripts de pré/pós para Linux) para garantir a consistência dos dados do aplicativo antes que ocorra um backup. | Quando você estiver recuperando uma VM com um instantâneo consistente com o aplicativo, a VM será inicializada. Não há corrupção ou perda de dados. Os aplicativos são iniciados em um estado consistente. | Windows: Todos os gravadores VSS foram bem-sucedidos<br/><br/> Linux: Os scripts anteriores/posteriores estão configurados e tiveram êxito
**Consistente com o sistema de arquivos** | Os backups consistentes do sistema de arquivos fornecem consistência ao tirar um instantâneo de todos os arquivos ao mesmo tempo.<br/><br/> | Quando você estiver recuperando uma VM com um instantâneo consistente do sistema de arquivos, a VM será inicializada. Não há corrupção ou perda de dados. Os aplicativos precisam implementar seu próprio mecanismo de "correção" para garantir que os dados restaurados sejam consistentes. | Windows: Alguns gravadores VSS falharam <br/><br/> Linux: Padrão (se os scripts Pre/post não estiverem configurados ou falharem)
**Com consistência de falhas** | Instantâneos com consistência de falhas normalmente ocorrem se uma VM do Azure é desligada no momento do backup. Somente os dados que já existem no disco no momento do backup são capturados e submetidos a backup.<br/><br/> Um ponto de recuperação consistente com falhas não garante a consistência de dados para o sistema operacional ou o aplicativo. | Embora não haja nenhuma garantia, a VM geralmente é inicializada e, em seguida, inicia uma verificação de disco para corrigir erros de corrupção. Quaisquer operações de gravação ou dados na memória que não foram transferidas para o disco antes da falha sejam perdidas. Os aplicativos implementam sua própria verificação de dados. Por exemplo, um aplicativo de banco de dados pode usar seu log de transações para verificação. Se o log de transações tem entradas que não estão no banco de dados, o software de banco de dados reverte as transações até que eles sejam consistentes. | A VM está em estado de desligamento

## <a name="backup-and-restore-considerations"></a>Considerações de backup e restauração

**Consideração** | **Detalhes**
--- | ---
**Disco** | O backup de discos de VM é paralelo. Por exemplo, se uma VM tiver quatro discos, o serviço de backup tentará fazer backup de todos os quatro discos em paralelo. O backup é incremental (somente dados alterados).
**Scheduling** |  Para reduzir o tráfego de backup, faça backup de VMs diferentes em diferentes momentos do dia e certifique-se de que os horários não se sobreponham. O backup de VMs ao mesmo tempo causa congestionamentos de tráfego.
**Preparando backups** | Tenha em mente o tempo necessário para preparar o backup. O tempo de preparação inclui a instalação ou atualização da extensão de backup e o disparo de um instantâneo de acordo com o agendamento de backup.
**Transferência de dados** | Considere o tempo necessário para o backup do Azure identificar as alterações incrementais do backup anterior.<br/><br/> Em um backup incremental, o backup do Azure determina as alterações calculando a soma de verificação do bloco. Se um bloco for alterado, ele será marcado para transferência para o cofre. O serviço analisa os blocos identificados para tentar minimizar ainda mais a quantidade de dados a serem transferidos. Depois de avaliar todos os blocos alterados, o backup do Azure transfere as alterações para o cofre.<br/><br/> Pode haver um atraso entre tirar o instantâneo e copiá-lo para o cofre.<br/><br/> Em horários de pico, pode levar até oito horas para que os backups sejam processados. O tempo de backup de uma VM será menor que 24 horas para o backup diário.
**Backup inicial** | Embora o tempo total de backup para backups incrementais seja menor que 24 horas, esse pode não ser o caso para o primeiro backup. O tempo necessário para o backup inicial dependerá do tamanho dos dados e do processamento do backup.
**Restaurar fila** | Os processos de backup do Azure restauram trabalhos de várias contas de armazenamento ao mesmo tempo e colocam solicitações de restauração em uma fila.
**Restaurar cópia** | Durante o processo de restauração, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo total de restauração depende das operações de e/s por segundo (IOPS) e da taxa de transferência da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras gravações e leituras de aplicativo.

### <a name="backup-performance"></a>Desempenho do backup

Esses cenários comuns podem afetar o tempo total de backup:

- **Adicionando um novo disco a uma VM do Azure protegida:** Se uma VM estiver passando por um backup incremental e um novo disco for adicionado, o tempo de backup aumentará. O tempo total de backup pode durar mais de 24 horas devido à replicação inicial do novo disco, juntamente com a replicação delta de discos existentes.
- **Discos fragmentados:** As operações de backup são mais rápidas quando as alterações no disco são contíguas. Se as alterações forem distribuídas e fragmentadas em um disco, o backup será mais lento.
- **Rotatividade de disco:** Se os discos protegidos que estão passando por backup incremental tiverem uma rotatividade diária de mais de 200 GB, o backup poderá levar muito tempo (mais de oito horas) para ser concluído.
- **Versões de backup:** A versão mais recente do backup (conhecida como a versão de restauração instantânea) usa um processo mais otimizado do que a comparação de soma de verificação para identificar alterações. Mas se você estiver usando a restauração instantânea e tiver excluído um instantâneo de backup, o backup mudará para comparação de soma de verificação. Nesse caso, a operação de backup excederá 24 horas (ou falha).

## <a name="best-practices"></a>Melhores práticas

Quando você estiver configurando backups de VM, sugerimos seguir estas práticas:

- Modifique os horários de agendamento padrão que são definidos em uma política. Por exemplo, se o tempo padrão na política for 12:00, aumente o tempo em vários minutos para que os recursos sejam usados de forma ideal.
- Se você estiver restaurando VMs de um único cofre, é altamente recomendável usar [contas de armazenamento v2 de finalidade geral](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) diferentes para garantir que a conta de armazenamento de destino não seja limitada. Por exemplo, cada VM deve ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restauradas, use 10 contas de armazenamento diferentes.
- Para o backup de VMs que estão usando o armazenamento Premium, com a restauração instantânea, é recomendável alocar *50%* de espaço livre do espaço de armazenamento total alocado, que é necessário **apenas** para o primeiro backup. O espaço livre de 50% não é um requisito para backups após a conclusão do primeiro backup
- As restaurações de uma camada de armazenamento v1 de uso geral (instantâneo) serão concluídas em minutos porque o instantâneo está na mesma conta de armazenamento. As restaurações da camada de armazenamento v2 de uso geral (cofre) podem levar horas. Nos casos em que os dados estão disponíveis no armazenamento v1 de uso geral, recomendamos que você use o recurso de [restauração instantânea](backup-instant-restore-capability.md) para restaurações mais rápidas. (Se os dados precisarem ser restaurados de um cofre, isso levará mais tempo.)
- O limite no número de discos por conta de armazenamento é relativo ao nível de acesso dos discos por aplicativos em execução em uma VM de IaaS (infraestrutura como serviço). Como uma prática geral, se 5 a 10 discos ou mais estiverem presentes em uma única conta de armazenamento, equilibre a carga movendo alguns discos para separar as contas de armazenamento.

## <a name="backup-costs"></a>Custos de backup

O backup de VMs do Azure com backup do Azure está sujeito aos [preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

A cobrança não é iniciada até que o primeiro backup bem-sucedido seja concluído. Neste ponto, a cobrança de VMs protegidas e de armazenamento é iniciada. A cobrança continuará desde que os dados de backup da VM sejam armazenados em um cofre. Se você interromper a proteção de uma VM, mas os dados de backup para a VM existirem em um cofre, a cobrança continuará.

A cobrança de uma VM especificada será interrompida somente se a proteção for interrompida e todos os dados de backup forem excluídos. Quando a proteção for interrompida e não houver nenhum trabalho de backup ativo, o tamanho do último backup de VM com êxito se tornará o tamanho da instância protegida usada para a fatura mensal.

O cálculo do tamanho da instância protegida é baseado no tamanho *real* da VM. O tamanho da VM é a soma de todos os dados na VM, excluindo o armazenamento temporário. O preço é baseado nos dados reais que são armazenados nos discos de dados, não no tamanho máximo com suporte para cada disco de dados anexado à VM.

Da mesma forma, a cobrança de armazenamento de backup é baseada na quantidade de dados armazenados no backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, pegue uma VM de tamanho padrão a2 que tenha dois discos de dados adicionais com um tamanho máximo de 4 TB cada. A tabela a seguir mostra os dados reais armazenados em cada um desses discos:

**Disco** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do SO | 4095 GB | 17 GB
Disco local/temporário | 135 GB | 5 GB (não incluído para backup)
Disco de dados 1 | 4095 GB | 30 GB
Disco de dados 2 | 4095 GB | 0 GB

O tamanho real da VM, nesse caso, é de 17 GB + 30 GB + 0 GB = 47 GB. Esse tamanho de instância protegida (47 GB) se torna a base para a fatura mensal. À medida que aumenta a quantidade de dados na VM, o tamanho da instância protegida usada para correspondência de alterações de cobrança.

<a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb"></a>
## <a name="public-preview-backup-of-vm-with-disk-sizes-up-to-30-tb"></a>Pré-visualização Pública: Backup de VM com tamanhos de disco de até 30 TB

O backup do Azure agora dá suporte à visualização pública de tamanhos maiores e mais potentes do [Azure Managed disks](https://azure.microsoft.com/blog/larger-more-powerful-managed-disks-for-azure-virtual-machines/) de até 30 TB. Essa visualização fornece suporte de nível de produção para máquinas virtuais gerenciadas.

Os backups de suas máquinas virtuais com cada tamanho de disco até 30TB e um máximo de 256TB combinados para todos os discos em uma VM devem funcionar sem afetar os backups existentes. Não há nenhuma ação do usuário necessária para obter os backups em execução para os discos de tamanho grande, se a máquina virtual já estiver configurada com o backup do Azure.

Todas as máquinas virtuais do Azure com discos grandes com backup configurado devem ser submetidas a backup com êxito.

## <a name="next-steps"></a>Passos seguintes

Agora, [Prepare-se para o backup de VM do Azure](backup-azure-arm-vms-prepare.md).
