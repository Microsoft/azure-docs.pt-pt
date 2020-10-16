---
title: Restaurar app a partir de backup
description: Saiba como restaurar a sua aplicação a partir de uma cópia de segurança. Algumas bases de dados ligadas podem ser restauradas juntamente com a aplicação numa única operação.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: c1b0ce62905424032c2100a1a032fa43ba97578f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084862"
---
# <a name="restore-an-app-in-azure"></a>Restaurar uma aplicação no Azure
Este artigo mostra-lhe como restaurar uma aplicação no [Azure App Service](../app-service/overview.md) que já fez anteriormente (ver [Back up your app in Azure).](manage-backup.md) Pode restaurar a sua aplicação com as suas bases de dados ligadas a pedido de um estado anterior ou criar uma nova aplicação com base numa das cópias de segurança da sua aplicação original. O Azure App Service suporta as seguintes bases de dados para cópia de segurança e restauro:
- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Restaurar a partir de backups está disponível para aplicações em execução no nível **Standard** e **Premium.** Para obter informações sobre o escalonamento da sua aplicação, consulte [Scale up uma aplicação em Azure](manage-scale-up.md). **O** nível premium permite que um maior número de backups diários sejam realizados do que o nível **Standard.**

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar uma aplicação a partir de uma cópia de segurança existente
1. Na página **de Definições** da sua aplicação no portal Azure, clique em **Backups** para exibir a página **de Backups.** Em seguida, clique em **Restaurar**.
   
    ![Escolha restaurar agora][ChooseRestoreNow]
2. Na página **'Restaurar',** selecione primeiro a fonte de reserva.
   
    ![Screenshot que mostra onde selecionar a fonte de reserva.](./media/web-sites-restore/021ChooseSource1.png)
   
    A opção **de backup da App** mostra-lhe todas as cópias de segurança existentes da aplicação atual, e pode facilmente selecionar uma.
    A opção **de Armazenamento** permite selecionar qualquer ficheiro ZIP de reserva de qualquer conta e recipiente de Armazenamento Azure existente na sua subscrição.
    Se estiver a tentar restaurar uma cópia de segurança de outra aplicação, utilize a opção **Storage.**
3. Em seguida, especifique o destino para a restauração da aplicação no **destino Restaurar.**
   
    ![Screenshot que mostra onde especificar o destino para a restauração da aplicação.](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se escolher **Overwrite,** todos os dados existentes na sua aplicação atual são apagados e substituídos. Antes de clicar **em OK,** certifique-se de que é exatamente o que quer fazer.
   > 
   > 
   
   > [!WARNING]
   > Se o Serviço de Aplicações estiver a escrever dados para a base de dados enquanto o está a restaurar, pode resultar em sintomas como violação da CHAVE PRIMÁRIA e perda de dados. Sugere-se que pare primeiro o Serviço de Aplicações antes de começar a restaurar a base de dados.
   > 
   > 
   
    Pode selecionar **a App Existente** para restaurar a cópia de segurança da aplicação para outra aplicação no mesmo grupo de recursos. Antes de utilizar esta opção, já deveria ter criado outra aplicação no seu grupo de recursos com configuração de base de dados espelhada para a definida na cópia de segurança da aplicação. Também pode criar uma **nova** aplicação para restaurar o seu conteúdo.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Faça o download ou elimine uma cópia de segurança de uma conta de armazenamento
1. A partir da página principal de **navegação** do portal Azure, selecione **Contas de Armazenamento**. É apresentada uma lista das suas contas de armazenamento existentes.
2. Selecione a conta de armazenamento que contém a cópia de segurança que pretende descarregar ou eliminar. A página da conta de armazenamento é apresentada.
3. Na página da conta de armazenamento, selecione o recipiente que deseja
   
    ![Ver Contentores][ViewContainers]
4. Selecione o ficheiro de cópia de segurança que pretende descarregar ou eliminar.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **Baixar** ou **Eliminar** dependendo do que pretende fazer.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorize uma operação de restauro
Para ver detalhes sobre o sucesso ou falha da operação de restauro da aplicação, navegue para a página **'Registo de Atividades'** no portal Azure.  
 

Desloque-se para baixo para encontrar a operação de restauro desejada e clique para selecioná-la.

A página de detalhes exibe as informações disponíveis relacionadas com a operação de restauro.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de backup com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/).

Para amostras, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Amostras Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
