---
title: Fazer uma cópia de segurança de aplicação - serviço de aplicações do Azure
description: Saiba como criar cópias de segurança das suas aplicações no serviço de aplicações do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7e697329e83b530157e490b04f5155d28d243bb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61271802"
---
# <a name="back-up-your-app-in-azure"></a>Efetuar cópia de segurança da sua aplicação no Azure
A funcionalidade de cópia de segurança e restauro no [App Service do Azure](overview.md) permite-lhe facilmente criar cópias de segurança de aplicação com base numa agenda ou manualmente. Pode restaurar a aplicação para um instantâneo de um estado anterior ao substituir a aplicação existente ou restaurar para outra aplicação. 

Para obter informações sobre como restaurar uma aplicação de cópia de segurança, consulte [restaurar uma aplicação no Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>O que obtém uma cópia de segurança
Serviço de aplicações, pode criar cópias de segurança as seguintes informações para uma conta de armazenamento do Azure e o contentor que configurou a aplicação para utilizar. 

* Configuração da aplicação
* Conteúdo do ficheiro
* Base de dados ligada à sua aplicação

As seguintes soluções de base de dados são suportadas com a funcionalidade cópia de segurança: 
   - [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
   - [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
   - [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL na aplicação](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Cada cópia de segurança é uma cópia offline completa da sua aplicação, não uma atualização incremental.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* A funcionalidade de cópia de segurança e restauro requer o plano do serviço de aplicações no **padrão** escalão ou **Premium** escalão. Para obter mais informações sobre o seu plano do serviço de aplicações para utilizar um escalão mais elevado de dimensionamento, veja [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md).  
  **Premium** escalão permite um maior número de diário fazer uma cópia no-BREAK que **padrão** escalão.
* Precisa de uma conta de armazenamento do Azure e o contentor na mesma subscrição que a aplicação que pretende criar cópias de segurança. Para obter mais informações sobre contas de armazenamento do Azure, consulte [descrição geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* As cópias de segurança podem ser até 10 GB de conteúdo de aplicação e a base de dados. Se o tamanho da cópia de segurança excede este limite, obterá um erro.
* Cópias de segurança de SSL ativada a base de dados do Azure para MySQL não é suportada. Se uma cópia de segurança estiver configurada, obterá com falhas de cópias de segurança.
* Cópias de segurança de SSL ativada a base de dados do Azure para PostgreSQL não é suportada. Se uma cópia de segurança estiver configurada, obterá com falhas de cópias de segurança.
* Bases de dados do MySQL na aplicação são automaticamente uma cópia de segurança sem qualquer configuração. Se fizer manualmente as definições de bases de dados do MySQL na aplicação, por exemplo, adicionar as cadeias de ligação, as cópias de segurança poderão não funcionar corretamente.
* Utilizar uma firewall ativada a conta de armazenamento como o destino para as cópias de segurança não é suportado. Se uma cópia de segurança estiver configurada, obterá com falhas de cópias de segurança.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
1. Na [portal do Azure](https://portal.azure.com), navegue até à página da sua aplicação, selecione **cópias de segurança**. O **cópias de segurança** é apresentada a página.
   
    ![Página de cópias de segurança][ChooseBackupsPage]
   
   > [!NOTE]
   > Se vir a mensagem seguinte, clique nele para atualizar o seu plano do serviço de aplicações para poder continuar com cópias de segurança.
   > Para obter mais informações, consulte [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md).  
   > ![Escolha a conta de armazenamento](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Na **cópia de segurança** página, clique em **configurar**
![clique em configurar](./media/web-sites-backup/ClickConfigure1.png)
3. Na **configuração de cópia de segurança** página, clique em **armazenamento: Não configurado** para configurar uma conta de armazenamento.
   
    ![Escolher conta de armazenamento][ChooseStorageAccount]
4. Escolher o destino da cópia de segurança, selecionando um **conta de armazenamento** e **contentor**. A conta de armazenamento têm de pertencer à mesma subscrição que a aplicação que pretende criar cópias de segurança. Se desejar, pode criar uma nova conta de armazenamento ou um novo contentor nas respectivas páginas. Quando tiver terminado, clique em **selecione**.
   
    ![Escolher conta de armazenamento](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Na **configuração de cópia de segurança** página que ainda é deixada aberta, pode configurar **base de dados de cópia de segurança**, em seguida, selecione as bases de dados que pretende incluir nas cópias de segurança (base de dados SQL ou MySQL), em seguida, clique em **OK**.  
   
    ![Escolher conta de armazenamento](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Para uma base de dados a aparecer nesta lista, sua cadeia de ligação tem de existir na **cadeias de ligação** secção a **as definições da aplicação** página para a sua aplicação. 
   >
   > Bases de dados do MySQL na aplicação são automaticamente uma cópia de segurança sem qualquer configuração. Se fizer manualmente as definições de bases de dados do MySQL na aplicação, por exemplo, adicionar as cadeias de ligação, as cópias de segurança poderão não funcionar corretamente.
   > 
   > 
6. Na **configuração de cópia de segurança** página, clique em **guardar**.    
7. Na **cópias de segurança** página, clique em **cópia de segurança**.
   
    ![Botão de BackUpNow][BackUpNow]
   
    Verá uma mensagem de progresso durante o processo de cópia de segurança.

Quando a conta de armazenamento e o contentor estiver configurado, pode iniciar uma cópia de segurança manual em qualquer altura.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar cópias de segurança automatizadas
1. Na **configuração de cópia de segurança** página, defina **cópia de segurança agendada** para **no**. 
   
    ![Escolher conta de armazenamento](./media/web-sites-backup/05ScheduleBackup1.png)
2. Agenda de cópia de segurança opções serão exibida, defina **agendado cópias de segurança** ao **no**, em seguida, configure a agenda de cópia de segurança conforme pretendido e clique em **OK**.
   
    ![Permita cópias de segurança automatizadas][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurar cópias de segurança parciais
Por vezes, não quer criar cópias de segurança tudo na sua aplicação. Eis alguns exemplos:

* [Configurar cópias de segurança semanais](#configure-automated-backups) da sua aplicação que contém o conteúdo estático, que nunca são alterados, como o antigo postagens de blog ou imagens.
* A aplicação tem mais de 10 GB de conteúdo (que é a quantidade máxima, que pode criar cópias de segurança ao mesmo tempo).
* Não deseja fazer backup dos arquivos de log.

As cópias de segurança parciais permitem que escolher exatamente quais arquivos que pretende criar cópias de segurança.

> [!NOTE]
> Bases de dados individuais na cópia de segurança podem ser de 4GB no máximo, mas o tamanho máximo total da cópia de segurança é de 10GB

### <a name="exclude-files-from-your-backup"></a>Excluir ficheiros da sua cópia de segurança
Suponha que tem uma aplicação que contém ficheiros de registo e imagens estáticas que tenham sido cópia de segurança uma vez e não será alterado. Nesses casos, pode excluir esses ficheiros e pastas de que está a ser armazenados em suas futuras cópias de segurança. Para excluir ficheiros e pastas de suas cópias de segurança, crie uma `_backup.filter` de ficheiros a `D:\home\site\wwwroot` pasta da sua aplicação. Especifique a lista de ficheiros e pastas que pretende excluir neste ficheiro. 

É uma forma fácil de aceder aos seus ficheiros utilizar o Kudu. Clique em **avançadas ferramentas -> Go** definição para a sua aplicação web acessar o Kudu.

![Kudu através do portal][kudu-portal]

Identifique as pastas que pretende excluir da suas cópias de segurança.  Por exemplo, pretende filtrar os ficheiros e pastas realçada.

![Pasta de imagens][ImagesFolder]

Crie um ficheiro chamado `_backup.filter` e colocar a lista anterior no arquivo, mas remover `D:\home`. Liste um diretório ou arquivo por linha. Pelo que deve ser o conteúdo do ficheiro:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Carregue `_backup.filter` do ficheiro para o `D:\home\site\wwwroot\` diretório da sua utilização do site [ftp](deploy-ftp.md) ou qualquer outro método. Se desejar, pode criar o ficheiro diretamente com o Kudu `DebugConsole` e inserir o conteúdo aqui.

Executar cópias de segurança da mesma forma que deve fazer isso, normalmente [manualmente](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups). Agora, quaisquer ficheiros e pastas especificados no `_backup.filter` foi excluída da futuras cópias de segurança agendadas ou iniciados manualmente. 

> [!NOTE]
> Restaurar cópias de segurança parciais do seu site da mesma forma que faria [restaurar uma cópia de segurança regular](web-sites-restore.md). O processo de restauração faz a coisa certa.
> 
> Quando um backup completo é restaurado, todo o conteúdo no site é substituído pelo que quer que esteja na cópia de segurança. Se um ficheiro está no site, mas não na cópia de segurança é eliminado. Mas, quando uma cópia de segurança parcial é restaurada, qualquer conteúdo que está localizado em um dos diretórios de bloqueadas ou qualquer arquivo bloqueado, é deixado como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como são armazenadas as cópias de segurança
Depois de efetuar um ou mais cópias de segurança para a sua aplicação, as cópias de segurança são visíveis no **contentores** página da sua conta de armazenamento e a sua aplicação. A conta de armazenamento, cada cópia de segurança consiste num`.zip` ficheiro que contém os dados de cópia de segurança e uma `.xml` ficheiro que contém um manifesto do `.zip` os conteúdos do ficheiro. Pode deszipe e procurar estes ficheiros, se desejar acessar suas cópias de segurança sem realmente executar uma restauração de aplicação.

A cópia de segurança da base de dados para a aplicação é armazenada na raiz do ficheiro. zip. Para uma base de dados SQL, isso é um ficheiro BACPAC (sem extensão de ficheiro) e pode ser importado. Para criar uma base de dados SQL com base na exportação BACPAC, veja [importar um ficheiro BACPAC para criar uma nova base de dados do utilizador](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Alterar qualquer um dos arquivos no seu **websitebackups** contentor pode fazer com que a cópia de segurança para se tornar inválido e, portanto, não restaurável.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de cópia de segurança com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para exemplos, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Exemplos do Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Próximos Passos
Para obter informações sobre como restaurar uma aplicação a partir de uma cópia de segurança, consulte [restaurar uma aplicação no Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

