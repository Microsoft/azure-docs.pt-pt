---
title: Restaurar aplicação - serviço de aplicações do Azure
description: Saiba como restaurar a aplicação a partir de uma cópia de segurança.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832512"
---
# <a name="restore-an-app-in-azure"></a>Restaurar uma aplicação no Azure
Este artigo mostra como restaurar uma aplicação no [App Service do Azure](../app-service/overview.md) que anteriormente efetuou uma cópia (consulte [volta a sua aplicação no Azure](manage-backup.md)). Pode restaurar a aplicação com as suas bases de dados ligadas a pedido para um estado anterior ou criar uma nova aplicação com base em uma das cópias de segurança da sua aplicação original. Serviço de aplicações do Azure suporta as seguintes bases de dados para cópia de segurança e restauro:
- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL na aplicação](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Restaurar a partir de cópias de segurança está disponível para aplicações em execução no **padrão** e **Premium** escalão. Para obter informações sobre como aumentar verticalmente a sua aplicação, consulte [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md). **Premium** escalão permite um maior número de cópias de segurança diárias para ser executada que **padrão** escalão.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar uma aplicação a partir de uma cópia de segurança existente
1. Na **definições** página da sua aplicação no portal do Azure, clique em **cópias de segurança** para apresentar o **cópias de segurança** página. Em seguida, clique em **restaurar**.
   
    ![Escolha o restauro agora][ChooseRestoreNow]
2. Na **restaurar** , primeiro selecione a origem de cópia de segurança.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    O **cópia de segurança de aplicação** opção mostra-lhe todas as cópias de segurança existentes da aplicação atual e pode facilmente selecionar um.
    O **armazenamento** opção permite-lhe selecionar qualquer ficheiro ZIP de cópia de segurança a partir de qualquer conta de armazenamento do Azure existente e um contentor na sua subscrição.
    Se estiver a tentar restaurar uma cópia de segurança de outra aplicação, utilize o **armazenamento** opção.
3. Em seguida, especifique o destino para o restauro de aplicação no **destino de restauro**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se escolher **substituir**, todos os dados existentes na sua aplicação atual são eliminados e substituídos. Antes de clicar em **OK**, certifique-se de que é exatamente o que deseja fazer.
   > 
   > 
   
   > [!WARNING]
   > Se o serviço de aplicações está a escrever dados na base de dados enquanto estiver a restaurar, poderá resultar no sintomas, tais como a violação de chave primária e a perda de dados. Recomenda-se para parar o serviço de aplicações em primeiro lugar, antes de começar a restaurar a base de dados.
   > 
   > 
   
    Pode selecionar **aplicação existente** para restaurar a cópia de segurança de aplicação para outra aplicação no mesmo grupo de recursos. Antes de utilizar esta opção, deverá ter já criado outra aplicação no seu grupo de recursos com o espelhamento de configuração de base de dados para um definido na cópia de segurança de aplicação. Também pode criar uma **New** aplicação para restaurar o seu conteúdo.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Baixe ou eliminar uma cópia de segurança de uma conta de armazenamento
1. Do principal **navegue** página do portal do Azure, selecione **contas de armazenamento**. É apresentada uma lista de contas de armazenamento existentes.
2. Selecione a conta de armazenamento que contém a cópia de segurança que pretende transferir ou eliminar. É apresentada a página para a conta de armazenamento.
3. Na página de conta de armazenamento, selecione o contentor que pretende
   
    ![Contentores de vista][ViewContainers]
4. Selecione o ficheiro de cópia de segurança que pretende transferir ou eliminar.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **baixe** ou **eliminar** consoante o que deseja fazer.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorizar uma operação de restauro
Para ver os detalhes sobre o sucesso ou falha da operação de restauro de aplicação, navegue para o **registo de atividades** página no portal do Azure.  
 

Role para baixo para encontrar o desejado a operação de restauro e clique para selecioná-lo.

A página de detalhes apresenta as informações disponíveis relacionados com a operação de restauro.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de cópia de segurança com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para exemplos, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Exemplos do Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
