---
title: Backup e restauro da base de dados dos Serviços de Análise Azure Microsoft Docs
description: Este artigo descreve como fazer backup e restaurar metadados e dados de modelos a partir de uma base de dados dos Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 66d09c2faa52cee3e94402be708d654b548c0de1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507000"
---
# <a name="analysis-services-database-backup-and-restore"></a>Backup e restauro da base de dados de serviços de análise

O backup das bases de dados de modelos tabulares nos Serviços de Análise Azure é praticamente o mesmo que para os Serviços de Análise no local. A principal diferença é onde guarda os seus ficheiros de reserva. Os ficheiros de cópia de segurança devem ser guardados num contentor numa [conta de armazenamento Azure](../storage/common/storage-create-storage-account.md). Pode utilizar uma conta de armazenamento e um recipiente que já tenha, ou podem ser criados ao configurar as definições de armazenamento para o seu servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar num novo serviço fatural. Para saber mais, consulte [o Preço de Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/)
> 
> 

> [!NOTE]
> Se a conta de armazenamento estiver numa região diferente, configufique as definições de firewall da conta de armazenamento para permitir o acesso a partir de **redes selecionadas**. Na **gama**Address De Firewall , especifique o intervalo de endereços IP para a região onde se encontra o servidor de Serviços de Análise. Configurar as definições de firewall de conta de armazenamento para permitir o acesso a partir de todas as redes é suportado, no entanto é preferível escolher redes selecionadas e especificar um intervalo de endereços IP. Para saber mais, consulte [a Rede de Conectividade FAQ.](analysis-services-network-faq.md#backup-and-restore)

As cópias de segurança são guardadas com uma extensão .abf. Para os modelos tabulares em memória, os dados do modelo e os metadados são armazenados. Para os modelos tabulares DirectQuery, apenas os metadados-modelo são armazenados. As cópias de segurança podem ser comprimidas e encriptadas, dependendo das opções que escolher.


## <a name="configure-storage-settings"></a>Configurações de armazenamento de configuração
Antes de fazer o backup, tem de configurar as definições de armazenamento para o seu servidor.


### <a name="to-configure-storage-settings"></a>Para configurar as definições de armazenamento
1.  No portal Azure > **Definições,** clique em **Backup**.

    ![Backups em Definições](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique **em 'Activado'** e, em seguida, clique em **Definições de Armazenamento**.

    ![Ativar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione a sua conta de armazenamento ou crie uma nova.

4. Selecione um recipiente ou crie um novo.

    ![Selecionar o contentor](./media/analysis-services-backup/aas-backup-container.png)

5. Guarde as definições de reserva.

    ![Guardar definições de backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Para fazer backup utilizando SSMS

1. Em SSMS, clique com o botão direito numa base de dados > **Back Up**.

2. No ficheiro de backup **de backup da base de dados**de cópia de  >  **segurança,** clique em **procurar**.

3. No **ficheiro Guardar como** diálogo, verifique o caminho da pasta e, em seguida, digite um nome para o ficheiro de reserva. 

4. No diálogo **'Backup Database',** selecione opções.

    **Permitir a substituição do ficheiro** - Selecione esta opção para substituir ficheiros de cópia de segurança com o mesmo nome. Se esta opção não for selecionada, o ficheiro que está a guardar não pode ter o mesmo nome que um ficheiro que já existe no mesmo local.

    **Aplicar compressão** - Selecione esta opção para comprimir o ficheiro de reserva. Os ficheiros de cópia de segurança comprimidos guardam espaço no disco, mas requerem uma utilização ligeiramente maior do CPU. 

    **Encriptar ficheiro de backup** - Selecione esta opção para encriptar o ficheiro de cópia de segurança. Esta opção requer uma palavra-passe fornecida pelo utilizador para proteger o ficheiro de cópia de segurança. A palavra-passe impede a leitura dos dados de backup de qualquer outro meio que não uma operação de restauro. Se optar por encriptar cópias de segurança, guarde a palavra-passe num local seguro.

5. Clique **em OK** para criar e guardar o ficheiro de reserva.


### <a name="powershell"></a>PowerShell
Utilize [o cmdlet Backup-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)

## <a name="restore"></a>Restauro
Ao restaurar, o seu ficheiro de cópia de segurança deve estar na conta de armazenamento que configura para o seu servidor. Se precisar de mover um ficheiro de reserva de um local no local para a sua conta de armazenamento, utilize o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou o utilitário da linha de comando [AzCopy.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Se estiver a restaurar a partir de um servidor no local, deve remover todos os utilizadores de domínio das funções do modelo e adicioná-los de volta às funções como utilizadores do Azure Ative Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar utilizando sSMS

1. Em SSMS, clique com o botão direito numa base de dados > **Restaurar**.

2. No **diálogo backup Database,** no **ficheiro Backup,** clique em **Procurar**.

3. No diálogo **'Localizar ficheiros' de base de dados,** selecione o ficheiro que pretende restaurar.

4. Na **base de dados 'Restaurar'** selecione a base de dados.

5. Especificar opções. As opções de segurança devem corresponder às opções de backup utilizadas ao fazer o backup.


### <a name="powershell"></a>PowerShell

Utilize [o cmdlet Restore-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)


## <a name="related-information"></a>Informações relacionadas

[Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)  
[Alta disponibilidade](analysis-services-bcdr.md)      
[Rede de serviços de análise conectividade FAQ](analysis-services-network-faq.md)