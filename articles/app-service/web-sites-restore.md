---
title: Restaurar serviço de Azure App de aplicativo
description: Saiba como restaurar seu aplicativo a partir de um backup.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 519cf5388b095c7ca6e0ae7d978608f0824dc3a2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066506"
---
# <a name="restore-an-app-in-azure"></a>Restaurar uma aplicação no Azure
Este artigo mostra como restaurar um aplicativo no [serviço Azure app](../app-service/overview.md) do qual você fez backup anteriormente (consulte [fazer backup de seu aplicativo no Azure](manage-backup.md)). Você pode restaurar seu aplicativo com seus bancos de dados vinculados sob demanda para um estado anterior ou criar um novo aplicativo com base em um dos backups do seu aplicativo original. Azure App serviço dá suporte aos seguintes bancos de dados para backup e restauração:
- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL no aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

A restauração de backups está disponível para aplicativos em execução na camada **Standard** e **Premium** . Para obter informações sobre como escalar verticalmente seu aplicativo, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md). A camada **Premium** permite que um número maior de backups diários seja realizado do que a camada **Standard** .

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar um aplicativo de um backup existente
1. Na página **configurações** do seu aplicativo no portal do Azure, clique em **backups** para exibir a página **backups** . Em seguida, clique em **restaurar**.
   
    ![Escolha restaurar agora][ChooseRestoreNow]
2. Na página **restaurar** , primeiro selecione a fonte de backup.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    A opção **backup do aplicativo** mostra todos os backups existentes do aplicativo atual, e você pode selecioná-lo facilmente.
    A opção de **armazenamento** permite selecionar qualquer arquivo zip de backup de qualquer conta de armazenamento do Azure existente e contêiner em sua assinatura.
    Se você estiver tentando restaurar um backup de outro aplicativo, use a opção de **armazenamento** .
3. Em seguida, especifique o destino para a restauração do aplicativo no **destino da restauração**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se você escolher **substituir**, todos os dados existentes em seu aplicativo atual serão apagados e substituídos. Antes de clicar em **OK**, verifique se é exatamente o que você deseja fazer.
   > 
   > 
   
   > [!WARNING]
   > Se o serviço de aplicativo estiver gravando dados no banco de dado enquanto você o estiver restaurando, isso poderá resultar em sintomas como violação de chave primária e perda de dados. É recomendável parar o serviço de aplicativo primeiro antes de começar a restaurar o banco de dados.
   > 
   > 
   
    Você pode selecionar **aplicativo existente** para restaurar o backup do aplicativo para outro aplicativo no mesmo grupo de recursos. Antes de usar essa opção, você já deve ter criado outro aplicativo em seu grupo de recursos com a configuração do banco de dados de espelhamento para aquele definido no backup do aplicativo. Você também pode criar um **novo** aplicativo para restaurar seu conteúdo.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Baixar ou excluir um backup de uma conta de armazenamento
1. Na página de **navegação** principal do portal do Azure, selecione **contas de armazenamento**. É exibida uma lista de suas contas de armazenamento existentes.
2. Selecione a conta de armazenamento que contém o backup que você deseja baixar ou excluir. A página da conta de armazenamento é exibida.
3. Na página conta de armazenamento, selecione o contêiner desejado
   
    ![Exibir contêineres][ViewContainers]
4. Selecione o arquivo de backup que você deseja baixar ou excluir.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **baixar** ou **excluir** dependendo do que você deseja fazer.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorar uma operação de restauração
Para ver detalhes sobre o êxito ou a falha da operação de restauração do aplicativo, navegue até a página **log de atividades** no portal do Azure.  
 

Role para baixo para localizar a operação de restauração desejada e clique para selecioná-la.

A página de detalhes exibe as informações disponíveis relacionadas à operação de restauração.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de backup com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para obter exemplos, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Exemplos do Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
