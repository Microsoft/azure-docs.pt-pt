---
title: Backup e restauro da base de dados azure Analysis Services Microsoft Docs
description: Este artigo descreve como fazer backup e restaurar metadados de modelos e dados de uma base de dados dos Serviços de Análise Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aa98a13b84e89c90e29525fb6743ac33faf1d917
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871295"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

Apoiar as bases de dados de modelos tabular nos Serviços de Análise azure é praticamente o mesmo que para os Serviços de Análise no local. A principal diferença é onde armazena os seus ficheiros de reserva. Os ficheiros de reserva devem ser guardados num contentor numa conta de [armazenamento Azure](../storage/common/storage-create-storage-account.md). Pode utilizar uma conta de armazenamento e um contentor que já possui, ou podem ser criados ao configurar as definições de armazenamento para o seu servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar num novo serviço de faturação. Para saber mais, consulte [o Preço de Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/)
> 
> 

> [!NOTE]
> Se a conta de armazenamento estiver noutra região, configure as definições de firewall da conta de armazenamento para permitir o acesso a partir de **redes Selecionadas**. Na **gama 'Endereço**firewall', especifique a gama de endereços IP para a região onde o servidor de Serviços de Análise se encontra. Configurar as definições de firewall da conta de armazenamento para permitir o acesso a partir de todas as redes é suportado, no entanto, escolher redes Selecionadas e especificar uma gama de endereços IP é preferível. Para saber mais, consulte [a conectividade da rede FAQ](analysis-services-network-faq.md#backup-and-restore).

Os backups são guardados com uma extensão .abf. Para modelos tabulares na memória, tanto os dados do modelo como os metadados são armazenados. Para os modelos tabular DirectQuery, apenas os metadados do modelo são armazenados. As cópias de segurança podem ser comprimidas e encriptadas, dependendo das opções que escolher.


## <a name="configure-storage-settings"></a>Configurar as definições de armazenamento
Antes de fazer o backup, tem de configurar as definições de armazenamento para o seu servidor.


### <a name="to-configure-storage-settings"></a>Para configurar as definições de armazenamento
1.  No portal Azure > **Definições,** clique em **Backup**.

    ![Backups em Definições](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **ativar e,** em seguida, clique em **Definições de Armazenamento**.

    ![Ativar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione a sua conta de armazenamento ou crie uma nova.

4. Selecione um recipiente ou crie um novo.

    ![Selecionar o contentor](./media/analysis-services-backup/aas-backup-container.png)

5. Guarde as definições de reserva.

    ![Guardar as definições de backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Cópia de segurança

### <a name="to-backup-by-using-ssms"></a>Para fazer backup usando SSMS

1. No SSMS, clique na direita numa base de dados > **Back Up**.

2. No**ficheiro de backup**da base de dados > de **backup,** clique em **Navegar**.

3. No **ficheiro Guardar como** diálogo, verifique o caminho da pasta e, em seguida, digite um nome para o ficheiro de reserva. 

4. No diálogo **'Backup Database',** selecione opções.

    Permitir a **sobreescrita** do ficheiro - Selecione esta opção para substituir ficheiros de backup com o mesmo nome. Se esta opção não for selecionada, o ficheiro que está a guardar não pode ter o mesmo nome que um ficheiro que já existe no mesmo local.

    **Aplicar compressão** - Selecione esta opção para comprimir o ficheiro de reserva. Os ficheiros de backup comprimidos guardam espaço no disco, mas requerem uma utilização ligeiramente mais elevada do CPU. 

    **Encriptar ficheiro de backup** - Selecione esta opção para encriptar o ficheiro de reserva. Esta opção requer uma palavra-passe fornecida pelo utilizador para proteger o ficheiro de reserva. A palavra-passe impede a leitura dos dados de cópia de segurança de quaisquer outros meios que não uma operação de restauro. Se optar por encriptar cópias de segurança, guarde a palavra-passe num local seguro.

5. Clique em **OK** para criar e guardar o ficheiro de cópia de segurança.


### <a name="powershell"></a>PowerShell
Utilize [cmdlet backup-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)

## <a name="restore"></a>Restauro
Ao restaurar, o seu ficheiro de reserva deve estar na conta de armazenamento configurada para o seu servidor. Se precisar de mover um ficheiro de reserva de um local de origem para a sua conta de armazenamento, utilize o [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou o utilitário da linha de comando [AzCopy.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Se estiver a restaurar a partir de um servidor no local, deve remover todos os utilizadores de domínio das funções do modelo e adicioná-los de volta às funções de utilizadores do Azure Ative Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar usando SSMS

1. No SSMS, clique na direita numa base de dados > **Restaurar**.

2. No diálogo **'Backup Database',** no **ficheiro 'Backup',** clique em **Navegar**.

3. No diálogo **'Localizar Ficheiros** de Base de Dados', selecione o ficheiro que pretende restaurar.

4. Na base de **dados Restaurar,** selecione a base de dados.

5. Especifique as opções. As opções de segurança devem corresponder às opções de backup que utilizou ao fazer backup.


### <a name="powershell"></a>PowerShell

Utilize o cmdlet [Restore-ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)


## <a name="related-information"></a>Informações relacionadas

[Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)  
[Alta disponibilidade](analysis-services-bcdr.md)      
[Conectividade de rede de serviços de análise FAQ](analysis-services-network-faq.md)