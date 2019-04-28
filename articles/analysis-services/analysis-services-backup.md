---
title: Restauro e cópia de segurança de base de dados de Analysis Services do Azure | Documentos da Microsoft
description: Descreve como criar cópias de segurança e restaurar uma base de dados do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31e8e65b382a3a6bcad2998a0babdf9605dc4968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023958"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

Backup de bancos de dados de modelo em tabela no Azure Analysis Services é muito as mesmas que nos serviços de análise no local. A principal diferença é onde armazena os ficheiros de cópia de segurança. Ficheiros de cópia de segurança tem de ser guardados para um contentor numa [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Pode utilizar uma conta de armazenamento e um contentor que já tem, ou podem ser criadas quando configurar as definições de armazenamento para o seu servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar num novo serviço sujeito a faturação. Para obter mais informações, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

As cópias de segurança são guardadas com uma extensão de abf. Dentro da memória para modelos de tabela, os dados do modelo e os metadados são armazenados. Para modelos em tabela do DirectQuery, apenas os metadados de modelo são armazenados. As cópias de segurança podem ser comprimidas e encriptadas, dependendo das opções que escolher.


## <a name="configure-storage-settings"></a>Configurar as definições de armazenamento
Antes de criar cópias de segurança, terá de configurar as definições de armazenamento para o seu servidor.


### <a name="to-configure-storage-settings"></a>Para configurar as definições de armazenamento
1.  No portal do Azure > **configurações**, clique em **cópia de segurança**.

    ![Definições de cópias de segurança](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **Enabled**, em seguida, clique em **as definições de armazenamento**.

    ![Ativar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione a sua conta de armazenamento ou crie um novo.

4. Selecione um contentor ou crie um novo.

    ![Selecionar o contentor](./media/analysis-services-backup/aas-backup-container.png)

5. Guarde as definições de cópia de segurança.

    ![Guardar as definições de cópia de segurança](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Cópia de segurança

### <a name="to-backup-by-using-ssms"></a>A cópia de segurança com o SSMS

1. No SSMS, clique com botão direito uma base de dados > **cópia de segurança**.

2. Na **base de dados de cópia de segurança** > **ficheiro de cópia de segurança**, clique em **procurar**.

3. Na **Guardar ficheiro como** caixa de diálogo, verifique se o caminho da pasta e, em seguida, escreva um nome para o ficheiro de cópia de segurança. 

4. Na **base de dados de cópia de segurança** caixa de diálogo, selecione as opções.

    **Permitir que o ficheiro substituir** -Selecione esta opção para substituir os ficheiros de cópia de segurança do mesmo nome. Se esta opção não estiver selecionada, o arquivo que é salvo não pode ter o mesmo nome como um ficheiro que já exista na mesma localização.

    **Aplicam-se a compressão** -Selecione esta opção para compactar o ficheiro de cópia de segurança. Arquivos compactados de cópia de segurança poupar espaço em disco, mas exigem um pouco mais alta de utilização da CPU. 

    **Encriptar o ficheiro de cópia de segurança** -Selecione esta opção para encriptar o ficheiro de cópia de segurança. Esta opção requer uma palavra-passe fornecida pelo usuário para proteger o ficheiro de cópia de segurança. A palavra-passe impede a leitura dos dados de cópia de segurança de qualquer outro meio para que uma operação de restauro. Se optar por encriptar cópias de segurança, armazene a palavra-passe numa localização segura.

5. Clique em **OK** para criar e guardar o ficheiro de cópia de segurança.


### <a name="powershell"></a>PowerShell
Uso [ASDatabase de cópia de segurança](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet.

## <a name="restore"></a>Restauro
Ao restaurar, tem de ser o ficheiro de cópia de segurança na conta de armazenamento que configurou para o seu servidor. Se precisar de mover um ficheiro de cópia de segurança a partir de uma localização no local para a sua conta de armazenamento, utilize [Explorador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou o [AzCopy](../storage/common/storage-use-azcopy.md) utilitário da linha de comandos. 



> [!NOTE]
> Se estiver a restaurar a partir de um servidor no local, tem de remover todos os utilizadores de domínio a partir das funções do modelo e adicioná-los novamente para as funções como utilizadores do Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar com o SSMS

1. No SSMS, clique com botão direito uma base de dados > **restaurar**.

2. Na **base de dados de cópia de segurança** caixa de diálogo, na **ficheiros de cópia de segurança**, clique em **procurar**.

3. Na **localizar ficheiros de base de dados** caixa de diálogo, selecione o ficheiro que pretende restaurar.

4. Na **restaurar base de dados**, selecione a base de dados.

5. Especifique as opções. Opções de segurança têm de corresponder as opções de cópia de segurança que utilizou ao criar cópias de segurança.


### <a name="powershell"></a>PowerShell

Uso [restauro ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet.


## <a name="related-information"></a>Informações relacionadas

[Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)  
[Elevada disponibilidade](analysis-services-bcdr.md)     
[Gerir o Azure Analysis Services](analysis-services-manage.md)
