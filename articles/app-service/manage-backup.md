---
title: Fazer backup do serviço de Azure App de aplicativos
description: Saiba como criar backups de seus aplicativos no serviço Azure App.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a56abbcb72afc1f45683259d3bd3bf13309cda07
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886072"
---
# <a name="back-up-your-app-in-azure"></a>Efetuar cópia de segurança da sua aplicação no Azure
O recurso de backup e restauração no [serviço Azure app](overview.md) permite que você crie facilmente backups de aplicativos manualmente ou em um agendamento. Você pode configurar os backups a serem mantidos até um período indefinido de tempo. Você pode restaurar o aplicativo para um instantâneo de um estado anterior, substituindo o aplicativo existente ou restaurando em outro aplicativo.

Para obter informações sobre como restaurar um aplicativo do backup, consulte [restaurar um aplicativo no Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>O que é feito backup
O serviço de aplicativo pode fazer backup das informações a seguir em uma conta de armazenamento do Azure e um contêiner que você configurou para usar o aplicativo. 

* Configuração do aplicativo
* Conteúdo do arquivo
* Banco de dados conectado ao seu aplicativo

As seguintes soluções de banco de dados têm suporte com o recurso de backup: 

- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL no aplicativo](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Cada backup é uma cópia offline completa do seu aplicativo, não uma atualização incremental.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* O recurso de backup e restauração requer que o plano do serviço de aplicativo esteja na camada **Standard** ou **Premium** . Para obter mais informações sobre como dimensionar seu plano do serviço de aplicativo para usar uma camada mais alta, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md). A camada **Premium** permite um número maior de back-ups diários do que a camada **Standard** .
* Você precisa de uma conta de armazenamento do Azure e um contêiner na mesma assinatura que o aplicativo do qual você deseja fazer backup. Para obter mais informações sobre contas de armazenamento do Azure, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Os backups podem ter até 10 GB de conteúdo de aplicativo e de banco de dados. Se o tamanho do backup exceder esse limite, você receberá um erro.
* Não há suporte para backups do banco de dados do Azure habilitado para SSL para MySQL. Se um backup estiver configurado, você receberá os backups com falha.
* Não há suporte para backups do banco de dados do Azure habilitado para SSL para PostgreSQL. Se um backup estiver configurado, você receberá os backups com falha.
* Os bancos de dados MySQL no aplicativo são submetidos a backup automaticamente sem nenhuma configuração. Se você fizer configurações manualmente para bancos de dados MySQL no aplicativo, como adicionar cadeias de conexão, os backups podem não funcionar corretamente.
* Não há suporte para o uso de uma conta de armazenamento habilitada para firewall como destino para seus backups. Se um backup estiver configurado, você receberá os backups com falha.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
1. Na [portal do Azure](https://portal.azure.com), navegue até a página do aplicativo, selecione **backups**. A página **backups** é exibida.

    ![Página de backups](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Se você vir a seguinte mensagem, clique nela para atualizar seu plano do serviço de aplicativo antes de prosseguir com os backups.
    > Para obter mais informações, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md).
    > ![escolher a conta de armazenamento](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. Na página **backup** , selecione o **backup não está configurado. Clique aqui para configurar o backup para seu aplicativo**.

    ![Clique em configurar](./media/manage-backup/configure-start.png)

3. Na página **configuração de backup** , clique em **armazenamento não configurado** para configurar uma conta de armazenamento.

    ![Escolher conta de armazenamento](./media/manage-backup/configure-storage.png)

4. Escolha o destino do backup selecionando uma **conta de armazenamento** e um **contêiner**. A conta de armazenamento deve pertencer à mesma assinatura que o aplicativo que você deseja fazer backup. Se desejar, você pode criar uma nova conta de armazenamento ou um novo contêiner nas respectivas páginas. Quando terminar, clique em **selecionar**.

5. Na página **configuração de backup** que ainda é deixada aberta, você pode configurar o **banco de dados de backup**e, em seguida, selecionar os bancos que deseja incluir nos backups (banco de dados SQL ou MySQL) e clicar em **OK**.

    ![Escolher conta de armazenamento](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve existir na seção **cadeias de conexão** da página de **configurações do aplicativo** para seu aplicativo. 
    >
    > Os bancos de dados MySQL no aplicativo são submetidos a backup automaticamente sem nenhuma configuração. Se você fizer configurações manualmente para bancos de dados MySQL no aplicativo, como adicionar cadeias de conexão, os backups podem não funcionar corretamente.
    > 
    > 

6. Na página **configuração de backup** , clique em **salvar**.
7. Na página **backups** , clique em **backup**.

    ![Botão BackUpNow](./media/manage-backup/manual-backup.png)

    Você verá uma mensagem de progresso durante o processo de backup.

Depois que a conta de armazenamento e o contêiner estiverem configurados, você poderá iniciar um backup manual a qualquer momento.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar backups automatizados
1. Na página **configuração de backup** , defina **backup agendado** como **ativado**. 

    ![Habilitar backups automatizados](./media/manage-backup/scheduled-backup.png)

2. Configure o agendamento de backup conforme desejado e selecione **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurar backups parciais
Às vezes, você não deseja fazer backup de tudo em seu aplicativo. Eis alguns exemplos:

* Você [configura backups semanais](#configure-automated-backups) de seu aplicativo que contém conteúdo estático que nunca muda, como Postagens de blog ou imagens antigas.
* Seu aplicativo tem mais de 10 GB de conteúdo (que é o valor máximo que você pode fazer backup por vez).
* Você não deseja fazer backup dos arquivos de log.

Os backups parciais permitem que você escolha exatamente quais arquivos deseja fazer backup.

> [!NOTE]
> Bancos de dados individuais no backup podem ter 4 GB máx, mas o tamanho máximo total do backup é 10 GB

### <a name="exclude-files-from-your-backup"></a>Excluir arquivos do backup
Suponha que você tenha um aplicativo que contém arquivos de log e imagens estáticas que foram efetuadas backup uma vez e não serão alteradas. Nesses casos, você pode excluir essas pastas e arquivos de serem armazenados em seus backups futuros. Para excluir arquivos e pastas de seus backups, crie um arquivo de `_backup.filter` na pasta `D:\home\site\wwwroot` do seu aplicativo. Especifique a lista de arquivos e pastas que você deseja excluir neste arquivo. 

Você pode acessar seus arquivos navegando até `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Se solicitado, entre em sua conta do Azure.

Identifique as pastas que você deseja excluir dos seus backups. Por exemplo, você deseja filtrar a pasta e os arquivos realçados.

![Pasta de imagens](./media/manage-backup/kudu-images.png)

Crie um arquivo chamado `_backup.filter` e coloque a lista anterior no arquivo, mas remova `D:\home`. Liste um diretório ou arquivo por linha. Portanto, o conteúdo do arquivo deve ser:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Carregue `_backup.filter` arquivo no diretório `D:\home\site\wwwroot\` do seu site usando [FTP](deploy-ftp.md) ou qualquer outro método. Se desejar, você pode criar o arquivo diretamente usando kudu `DebugConsole` e inserir o conteúdo lá.

Execute backups da mesma maneira que faria normalmente, [manual](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups). Agora, todos os arquivos e pastas especificados no `_backup.filter` são excluídos dos backups futuros agendados ou iniciados manualmente. 

> [!NOTE]
> Restaure backups parciais do seu site da mesma maneira que você [restauraria um backup regular](web-sites-restore.md). O processo de restauração faz a coisa certa.
> 
> Quando um backup completo é restaurado, todo o conteúdo do site é substituído por qualquer coisa que esteja no backup. Se um arquivo estiver no site, mas não no backup, ele será excluído. Mas quando um backup parcial é restaurado, qualquer conteúdo localizado em um dos diretórios na lista de bloqueios ou em qualquer arquivo na lista de bloqueios é deixado como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como os backups são armazenados
Depois de ter feito um ou mais backups para seu aplicativo, os backups são visíveis na página **contêineres** da sua conta de armazenamento e seu aplicativo. Na conta de armazenamento, cada backup consiste em um arquivo de`.zip` que contém os dados de backup e um arquivo de `.xml` que contém um manifesto do conteúdo do arquivo de `.zip`. Você pode descompactar e procurar esses arquivos se desejar acessar os backups sem realmente executar uma restauração de aplicativo.

O backup do banco de dados para o aplicativo é armazenado na raiz do arquivo. zip. Para um banco de dados SQL, este é um arquivo BACPAC (sem extensão de arquivo) e pode ser importado. Para criar um banco de dados SQL com base na exportação de BACPAC, consulte [importar um arquivo BACPAC para criar um novo banco de dados de usuário](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Alterar qualquer um dos arquivos em seu contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de backup com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para obter exemplos, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Exemplos do Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre como restaurar um aplicativo de um backup, consulte [restaurar um aplicativo no Azure](web-sites-restore.md). 
