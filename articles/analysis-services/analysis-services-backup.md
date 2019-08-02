---
title: Backup e restauração de banco de dados Azure Analysis Services | Microsoft Docs
description: Descreve como fazer backup e restaurar um banco de dados Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 15d2d0a043271da18d7884b684ffe8cf5c1f4dc8
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619200"
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

O backup de bancos de dados de modelo de tabela no Azure Analysis Services é muito semelhante ao Analysis Services local. A principal diferença é onde você armazena seus arquivos de backup. Os arquivos de backup devem ser salvos em um contêiner em uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Você pode usar uma conta de armazenamento e um contêiner que já tem ou pode ser criado ao definir as configurações de armazenamento para o servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar em um novo serviço faturável. Para saber mais, confira [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Os backups são salvos com uma extensão. ABF. Para modelos de tabela na memória, os dados de modelo e os metadados são armazenados. Para modelos de tabela do DirectQuery, somente os metadados do modelo são armazenados. Os backups podem ser compactados e criptografados, dependendo das opções escolhidas.


## <a name="configure-storage-settings"></a>Definir configurações de armazenamento
Antes de fazer backup, você precisa definir as configurações de armazenamento para o servidor.


### <a name="to-configure-storage-settings"></a>Para definir as configurações de armazenamento
1.  Em portal do Azure > **configurações**, clique em **backup**.

    ![Backups em configurações](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **habilitado**e em **configurações de armazenamento**.

    ![Ativar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione sua conta de armazenamento ou crie uma nova.

4. Selecione um contêiner ou crie um novo.

    ![Selecionar o contentor](./media/analysis-services-backup/aas-backup-container.png)

5. Salve as configurações de backup.

    ![Salvar configurações de backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Criar cópia de segurança

### <a name="to-backup-by-using-ssms"></a>Para fazer backup usando o SSMS

1. No SSMS, clique com o botão direito do mouse em um banco de dados > **backup**.

2. Em**arquivo de backup**do banco de **dados** > de backup, clique em **procurar**.

3. Na caixa de diálogo **salvar arquivo como** , verifique o caminho da pasta e digite um nome para o arquivo de backup. 

4. Na caixa de diálogo **banco de dados de backup** , selecione opções.

    **Permitir substituição de arquivo** – Selecione esta opção para substituir arquivos de backup de mesmo nome. Se essa opção não estiver selecionada, o arquivo que você está salvando não poderá ter o mesmo nome de um arquivo que já existe no mesmo local.

    **Aplicar compactação** – Selecione esta opção para compactar o arquivo de backup. Arquivos de backup compactados economizam espaço em disco, mas exigem uma utilização de CPU ligeiramente maior. 

    **Criptografar arquivo de backup** -Selecione esta opção para criptografar o arquivo de backup. Essa opção requer uma senha fornecida pelo usuário para proteger o arquivo de backup. A senha impede a leitura dos dados de backup em outros meios do que uma operação de restauração. Se você optar por criptografar os backups, armazene a senha em um local seguro.

5. Clique em **OK** para criar e salvar o arquivo de backup.


### <a name="powershell"></a>PowerShell
Use o cmdlet [backup-asdatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) .

## <a name="restore"></a>Restaurar
Ao restaurar, o arquivo de backup deve estar na conta de armazenamento que você configurou para o servidor. Se você precisar mover um arquivo de backup de um local para sua conta de armazenamento, use [Gerenciador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou o utilitário de linha de comando [AzCopy](../storage/common/storage-use-azcopy.md) . 



> [!NOTE]
> Se você estiver restaurando de um servidor local, deverá remover todos os usuários de domínio das funções do modelo e adicioná-los de volta às funções como Azure Active Directory usuários.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar usando o SSMS

1. No SSMS, clique com o botão direito do mouse em um banco de dados > **restaurar**.

2. Na caixa de diálogo **banco de dados de backup** , em arquivo de **backup**, clique em **procurar**.

3. Na caixa de diálogo **Localizar arquivos de banco de dados** , selecione o arquivo que você deseja restaurar.

4. Em **restaurar banco de dados**, selecione o banco de dados.

5. Especifique as opções. As opções de segurança devem corresponder às opções de backup usadas durante o backup.


### <a name="powershell"></a>PowerShell

Use o cmdlet [Restore-asdatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) .


## <a name="related-information"></a>Informações relacionadas

[Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)  
[Elevada disponibilidade](analysis-services-bcdr.md)     
[Gerenciar Azure Analysis Services](analysis-services-manage.md)
