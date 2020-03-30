---
title: Efetuar cópia de segurança de uma aplicação
description: Saiba como criar backups das suas aplicações no Serviço de Aplicações Azure. Executar cópias de segurança manuais ou programadas. Personalize as cópias de segurança, incluindo a base de dados anexada.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 783737729601bfef3bee8741a097d4319349f18e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259334"
---
# <a name="back-up-your-app-in-azure"></a>Efetuar cópia de segurança da sua aplicação no Azure
A funcionalidade de backup e restauro no [Serviço de Aplicações Azure](overview.md) permite-lhe facilmente criar backups de aplicações manualmente ou em horário. Pode configurar as cópias de segurança a reter até um período de tempo indefinido. Pode restaurar a aplicação a uma imagem instantânea de um estado anterior, sobrecarregando a app existente ou restaurando para outra aplicação.

Para obter informações sobre o restabelecimento de uma aplicação a partir de backup, consulte [Restaurar uma aplicação no Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>O que é apoiado
O Serviço de Aplicações pode fazer o backup as seguintes informações para uma conta de armazenamento e contentor Do Azure que configuraa a sua aplicação para utilizar. 

* Configuração de aplicações
* Conteúdo do ficheiro
* Base de dados ligada à sua aplicação

As seguintes soluções de base de dados são suportadas com recurso de backup: 

- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Cada cópia de backup é uma cópia offline completa da sua aplicação, não uma atualização incremental.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* A funcionalidade Backup and Restore requer que o plano do Serviço de Aplicações esteja no nível **Standard** ou **Premium.** Para obter mais informações sobre a escala do seu plano de Serviço de Aplicações para utilizar um nível mais elevado, consulte [Scale up uma app em Azure](manage-scale-up.md). **O** nível premium permite um maior número de back ups diários do que o **nível Standard.**
* Precisa de uma conta de armazenamento Azure e um contentor na mesma subscrição que a app que pretende fazer apoio. Para obter mais informações sobre as contas de armazenamento do Azure, consulte a visão geral da conta de [armazenamento do Azure.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)
* As cópias de segurança podem chegar a 10 GB de conteúdo de aplicação e base de dados. Se o tamanho da cópia de segurança exceder este limite, terá um erro.
* As cópias de segurança da Base de Dados Azure ativada pela SSL para o MySQL não são suportadas. Se uma cópia de segurança estiver configurada, obterá cópias de segurança falhadas.
* As cópias de segurança da Base de Dados Azure ativada pela SSL para postgreSQL não são suportadas. Se uma cópia de segurança estiver configurada, obterá cópias de segurança falhadas.
* As bases de dados MySQL in-app são automaticamente apoiadas sem qualquer configuração. Se fizer configurações manualmente para bases de dados MySQL in-app, tais como adicionar cordas de ligação, as cópias de segurança podem não funcionar corretamente.
* A utilização de uma conta de armazenamento ativada por firewall, uma vez que o destino das suas cópias de segurança não é suportado. Se uma cópia de segurança estiver configurada, obterá cópias de segurança falhadas.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
1. No [portal Azure,](https://portal.azure.com)navegue para a página da sua aplicação, selecione **Backups**. A página **Backups** é apresentada.

    ![Página de backups](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Se vir a seguinte mensagem, clique nela para atualizar o seu plano de Serviço de Aplicações antes de poder proceder com cópias de segurança.
    > Para mais informações, consulte [Scale up uma app em Azure](manage-scale-up.md).
    > ![Escolha a conta de armazenamento](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. Na página **'Cópia de Segurança',** selecione **'Backup' não está configurado. Clique aqui para configurar a cópia de segurança para a sua aplicação**.

    ![Clique em Configurar](./media/manage-backup/configure-start.png)

3. Na página de **Configuração de Backup,** clique **em Armazenamento não configurado** para configurar uma conta de armazenamento.

    ![Escolha a conta de armazenamento](./media/manage-backup/configure-storage.png)

4. Escolha o seu destino de reserva selecionando uma Conta de **Armazenamento** e **um Recipiente**. A conta de armazenamento deve pertencer à mesma subscrição que a app que pretende fazer. Se desejar, pode criar uma nova conta de armazenamento ou um novo recipiente nas respetivas páginas. Quando terminar, clique em **Selecionar**.

5. Na página de **Configuração de Cópia** de Segurança que ainda está aberta, pode configurar a Base de Dados de **Backup**e, em seguida, selecionar as bases de dados que pretende incluir nas cópias de dados (base de dados SQL ou MySQL), em seguida, clique em **OK**.

    ![Escolha a conta de armazenamento](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Para que uma base de dados apareça nesta lista, a sua cadeia de ligação deve existir na secção de cadeias de **ligação** da página de definições da **Aplicação** para a sua aplicação. 
    >
    > As bases de dados MySQL in-app são automaticamente apoiadas sem qualquer configuração. Se fizer configurações manualmente para bases de dados MySQL in-app, tais como adicionar cordas de ligação, as cópias de segurança podem não funcionar corretamente.
    > 
    > 

6. Na página de **Configuração de Backup,** clique em **Guardar**.
7. Na página **Backups,** clique em **Backup**.

    ![Botão BackUpNow](./media/manage-backup/manual-backup.png)

    Vê uma mensagem de progresso durante o processo de reserva.

Uma vez configurada a conta de armazenamento e o recipiente, pode iniciar uma cópia de segurança manual a qualquer momento.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar cópias de segurança automatizadas
1. Na página de **Configuração de Backup,** delineie **a cópia de segurança programada** para **On**. 

    ![Ativar backups automatizados](./media/manage-backup/scheduled-backup.png)

2. Configure o horário de reserva conforme desejado e selecione **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurar cópias de segurança parciais
Às vezes não queres apoiar tudo na tua aplicação. Eis alguns exemplos:

* Configura [cópias de segurança semanais](#configure-automated-backups) da sua aplicação que contêm conteúdo estático que nunca muda, como publicações antigas de blogs ou imagens.
* A sua aplicação tem mais de 10 GB de conteúdo (é o valor máximo que pode fazer de cada vez).
* Não vai querer fazer o registo.

As cópias de segurança parciais permitem-lhe escolher exatamente quais os ficheiros que pretende fazer cópias de segurança.

> [!NOTE]
> As bases de dados individuais na cópia de segurança podem ser de 4GB max, mas o tamanho máximo total da cópia de segurança é de 10GB

### <a name="exclude-files-from-your-backup"></a>Excluir ficheiros da sua cópia de segurança
Suponha que tenha uma aplicação que contenha ficheiros de registo e imagens estáticas que tenham sido cópias de segurança uma vez e não vão mudar. Nesses casos, pode excluir que essas pastas e ficheiros sejam armazenados nas suas futuras cópias de segurança. Para excluir ficheiros e pastas das `_backup.filter` suas `D:\home\site\wwwroot` cópias de segurança, crie um ficheiro na pasta da sua aplicação. Especifique a lista de ficheiros e pastas que pretende excluir neste ficheiro. 

Pode aceder aos seus ficheiros navegando até `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Se solicitado, inscreva-se na sua conta Azure.

Identifique as pastas que pretende excluir das suas cópias de segurança. Por exemplo, pretende filtrar a pasta e os ficheiros destacados.

![Pasta de Imagens](./media/manage-backup/kudu-images.png)

Crie um `_backup.filter` ficheiro chamado e coloque a lista `D:\home`anterior no ficheiro, mas remova . Listar um diretório ou ficheiro por linha. Assim, o conteúdo do ficheiro deve ser:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Faça `_backup.filter` upload `D:\home\site\wwwroot\` do ficheiro para o diretório do seu site utilizando [ftp](deploy-ftp.md) ou qualquer outro método. Se desejar, pode criar o ficheiro diretamente utilizando o Kudu `DebugConsole` e inserir o conteúdo lá.

Executar cópias de segurança da mesma forma que normalmente o faria, [manualmente](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups). Agora, quaisquer ficheiros e pastas especificados `_backup.filter` estão excluídos das futuras cópias de segurança agendadas ou iniciadas manualmente. 

> [!NOTE]
> Restaure cópias parciais do seu site da mesma forma que [restauraria uma cópia de segurança regular.](web-sites-restore.md) O processo de restauro faz a coisa certa.
> 
> Quando uma cópia de segurança completa é restaurada, todo o conteúdo do site é substituído por qualquer coisa que esteja na cópia de segurança. Se um ficheiro estiver no site, mas não na cópia de segurança, é apagado. Mas quando uma cópia de segurança parcial é restaurada, qualquer conteúdo que esteja localizado numa das listas negras, ou qualquer ficheiro na lista negra, é deixado como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como as cópias de segurança são armazenadas
Depois de ter feito uma ou mais cópias de segurança para a sua aplicação, as cópias de segurança são visíveis na página de **Contentores** da sua conta de armazenamento e na sua aplicação. Na conta de armazenamento, cada`.zip` cópia de segurança consiste `.xml` num ficheiro que contém `.zip` os dados de backup e um ficheiro que contém um manifesto do conteúdo do ficheiro. Pode desapertar e navegar nestes ficheiros se quiser aceder às suas cópias de segurança sem realmente realizar uma restauração de aplicações.

A cópia de segurança da base de dados da aplicação encontra-se armazenada na raiz do ficheiro .zip. Para uma base de dados SQL, este é um ficheiro BACPAC (sem extensão de ficheiros) e pode ser importado. Para criar uma base de dados SQL baseada na exportação bacpac, consulte [importar um ficheiro BACPAC para criar uma nova base de dados](https://technet.microsoft.com/library/hh710052.aspx)de utilizadores .

> [!WARNING]
> Alterar qualquer um dos ficheiros no seu recipiente de backups de **websites** pode fazer com que a cópia de segurança se torne inválida e, portanto, não restorável.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de backup com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/overview).

Para amostras, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Amostras Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre a restauração de uma aplicação a partir de uma cópia de segurança, consulte [Restaurar uma aplicação no Azure](web-sites-restore.md). 
