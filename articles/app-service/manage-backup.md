---
title: Efetuar cópia de segurança de uma aplicação
description: Saiba como criar cópias de segurança das suas apps no Azure App Service. Executar cópias de segurança manuais ou agendadas. Personalize cópias de segurança incluindo a base de dados anexa.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 7c00205e2931400caa64f35db962d94a732f2524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714498"
---
# <a name="back-up-your-app-in-azure"></a>Efetuar cópia de segurança da sua aplicação no Azure
A funcionalidade de Backup e Restauro no [Azure App Service](overview.md) permite-lhe criar facilmente cópias de segurança de aplicações manualmente ou em horário. Pode configurar as cópias de segurança para serem mantidas até um tempo indefinido. Pode restaurar a aplicação para uma imagem instantânea de um estado anterior, sobressacando a aplicação existente ou restaurando para outra aplicação.

Para obter informações sobre a restauração de uma aplicação a partir de cópia de segurança, consulte [Restaurar uma aplicação no Azure.](web-sites-restore.md)

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>O que é apoiado
O Serviço de Aplicações pode fazer o back up das seguintes informações para uma conta de armazenamento Azure e um contentor que configuraram a sua aplicação para utilizar. 

* Configuração de aplicações
* Conteúdo do ficheiro
* Base de dados ligada à sua aplicação

As seguintes soluções de base de dados são suportadas com recurso de backup: 

- [Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql)
- [Base de Dados do Azure para PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Cada cópia de segurança é uma cópia completa offline da sua aplicação, não uma atualização incremental.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* A funcionalidade de Backup e Restauro requer que o plano de Serviço de Aplicações esteja no nível **Standard,** **Premium** ou **Isolado.** Para obter mais informações sobre o escalonamento do seu plano de Serviço de Aplicações para utilizar um nível mais elevado, consulte [Scale up uma aplicação em Azure](manage-scale-up.md). **Os** níveis premium e **isolado** permitem um maior número de back ups diários do que o nível **Standard.**
* Precisa de uma conta de armazenamento Azure e de um recipiente na mesma subscrição que a aplicação que pretende fazer. Para obter mais informações sobre as contas de armazenamento da Azure, consulte [a visão geral da conta de armazenamento Azure](../storage/common/storage-account-overview.md).
* As cópias de segurança podem chegar a 10 GB de conteúdo de aplicações e bases de dados. Se o tamanho da cópia de segurança exceder este limite, obtém-se um erro.
* As cópias de segurança da Base de Dados Azure ativada por TLS para o MySQL não são suportadas. Se uma cópia de segurança estiver configurada, encontrará falhas de backup.
* Não são suportadas cópias de segurança da Base de Dados Azure ativada por TLS para PostgreSQL. Se uma cópia de segurança estiver configurada, encontrará falhas de backup.
* As bases de dados MySQL in-app são automaticamente apoiadas sem qualquer configuração. Se escamar configurações manuais para bases de dados MySQL na aplicação, como adicionar cordas de ligação, as cópias de segurança podem não funcionar corretamente.
* A utilização de uma conta de armazenamento ativada por firewall, uma vez que o destino das suas cópias de segurança não é suportado. Se uma cópia de segurança estiver configurada, encontrará falhas de backup.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
1. No [portal Azure,](https://portal.azure.com)navegue para a página da sua aplicação, selecione **Backups**. A página **'Backups'** é apresentada.

    ![Página de backups](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Se vir a seguinte mensagem, clique nela para atualizar o seu plano de Serviço de Aplicações antes de poder proceder com cópias de segurança.
    > Para obter mais informações, consulte [Scale up uma aplicação em Azure](manage-scale-up.md).
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Screenshot de um banner com uma mensagem para atualizar o plano do Serviço de Aplicações para aceder à funcionalidade de Backup e Restaurar.":::
    > 
    > 

2. Na página **'Backup',** o **backup não está configurado. Clique aqui para configurar a cópia de segurança para a sua aplicação.**

    ![Clique em Configurar](./media/manage-backup/configure-start.png)

3. Na página **de Configuração de Cópia de Segurança,** clique em **Armazenamento não configurado** para configurar uma conta de armazenamento.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Screenshot da secção de armazenamento de cópia de segurança com a definição de Armazenamento não configurada selecionada.":::

4. Escolha o seu destino de reserva selecionando uma **Conta de Armazenamento** e Um **Recipiente.** A conta de armazenamento deve pertencer à mesma subscrição que a aplicação que pretende fazer. Se desejar, pode criar uma nova conta de armazenamento ou um novo recipiente nas respetivas páginas. Quando terminar, clique em **Select**.

5. Na página **de Configuração de Cópia de Segurança** que ainda está aberta, pode configurar a Base de **Dados de Backup,** em seguida, selecione as bases de dados que pretende incluir nas cópias de segurança (SQL Database ou MySQL), em seguida, clique em **OK**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Screenshot da secção de base de dados de backup mostrando o Incluir na seleção de backup.":::

    > [!NOTE]
    > Para que uma base de dados apareça nesta lista, a sua cadeia de ligação deve existir na secção **de cordas de Ligação** da página de definições de **aplicação** para a sua aplicação. 
    >
    > As bases de dados MySQL in-app são automaticamente apoiadas sem qualquer configuração. Se fizer definições para bases de dados MySQL na aplicação manualmente, como adicionar cadeias de ligação, as cópias de segurança podem não funcionar corretamente.
    > 
    > 

6. Na página **de Configuração de Cópia de Segurança,** clique **em Guardar**.
7. Na página **'Backups',** clique **em Backup**.

    ![Botão BackUpNow](./media/manage-backup/manual-backup.png)

    Vê uma mensagem de progresso durante o processo de backup.

Uma vez configurada a conta de armazenamento e o recipiente, pode iniciar uma cópia de segurança manual a qualquer momento. As cópias de segurança manuais são mantidas indefinidamente.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar backups automatizados
1. Na página **de Configuração de Cópia de Segurança,** desatado **a cópia de segurança programada** para **On**. 

    ![Ativar backups automatizados](./media/manage-backup/scheduled-backup.png)

2. Configure o horário de reserva conforme desejado e selecione **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurar backups parciais
Às vezes não queres fazer o back-up da tua aplicação. Eis alguns exemplos:

* Configura cópias de [segurança semanais](#configure-automated-backups) da sua aplicação que contém conteúdo estático que nunca muda, como publicações de blogues antigas ou imagens.
* A sua aplicação tem mais de 10 GB de conteúdo (é o valor máximo que pode fazer de cada vez).
* Não vai querer fazer o registo.

As cópias de segurança parciais permitem-lhe escolher exatamente quais os ficheiros que pretende fazer cópias de segurança.

> [!NOTE]
> As bases de dados individuais na cópia de segurança podem ser de 4GB no máximo, mas o tamanho máximo total da cópia de segurança é de 10GB

### <a name="exclude-files-from-your-backup"></a>Exclua ficheiros da sua cópia de segurança
Suponha que tem uma aplicação que contém ficheiros de registo e imagens estáticas que foram cópias de segurança uma vez e não vão mudar. Nesses casos, pode excluir que as pastas e ficheiros sejam armazenados nas suas futuras cópias de segurança. Para excluir ficheiros e pastas das suas cópias de segurança, crie um `_backup.filter` ficheiro na pasta da sua `D:\home\site\wwwroot` aplicação. Especifique a lista de ficheiros e pastas que pretende excluir neste ficheiro. 

Pode aceder aos seus ficheiros navegando para `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Se solicitado, inscreva-se na sua conta Azure.

Identifique as pastas que pretende excluir das suas cópias de segurança. Por exemplo, pretende filtrar a pasta e os ficheiros realçados.

![Pasta de imagens](./media/manage-backup/kudu-images.png)

Crie um ficheiro chamado `_backup.filter` e coloque a lista anterior no ficheiro, mas remova `D:\home` . Listar um diretório ou ficheiro por linha. Assim, o conteúdo do ficheiro deve ser:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Faça `_backup.filter` o upload do ficheiro para o `D:\home\site\wwwroot\` diretório do seu site utilizando [ftp](deploy-ftp.md) ou qualquer outro método. Se desejar, pode criar o ficheiro diretamente utilizando o Kudu `DebugConsole` e inserir o conteúdo lá.

Execute cópias de segurança da mesma forma que normalmente o faria, [manualmente](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups). Agora, quaisquer ficheiros e pastas especificadas `_backup.filter` estão excluídos das futuras cópias de segurança programadas ou iniciadas manualmente. 

> [!NOTE]
> Restaura cópias de segurança parciais do seu site da mesma forma que [restauraria uma cópia de segurança regular.](web-sites-restore.md) O processo de restauro faz a coisa certa.
> 
> Quando uma cópia de segurança completa é restaurada, todo o conteúdo do site é substituído por tudo o que estiver na cópia de segurança. Se um ficheiro estiver no site, mas não na cópia de segurança, é apagado. Mas quando uma cópia de segurança parcial é restaurada, qualquer conteúdo que esteja localizado num dos diretórios restritos, ou em qualquer ficheiro restrito, é deixado como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como as cópias de segurança são armazenadas
Depois de ter feito uma ou mais cópias de segurança para a sua aplicação, as cópias de segurança estão visíveis na página **de Contentores** da sua conta de armazenamento e na sua aplicação. Na conta de armazenamento, cada cópia de segurança consiste num `.zip` ficheiro que contém os dados de backup e um ficheiro que contém um manifesto do conteúdo do `.xml` `.zip` ficheiro. Pode desapertar e navegar nestes ficheiros se quiser aceder às suas cópias de segurança sem realmente realizar uma restauração de aplicações.

A cópia de segurança da base de dados da aplicação é armazenada na raiz do ficheiro .zip. Para a Base de Dados SQL, este é um ficheiro BACPAC (sem extensão de ficheiro) e pode ser importado. Para criar uma base de dados na Base de Dados Azure SQL com base na exportação BACPAC, consulte [importar um ficheiro BACPAC para criar uma base de dados na Base de Dados Azure SQL](../azure-sql/database/database-import.md).

> [!WARNING]
> Alterar qualquer um dos ficheiros do seu contentor **de backups** do seu website pode fazer com que a cópia de segurança se torne inválida e, portanto, não seja restauradora.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de backup com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/).

Para amostras, consulte:

- [Exemplos da CLI do Azure](samples-cli.md)
- [Amostras Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre a restauração de uma aplicação a partir de uma cópia de segurança, consulte [Restaurar uma aplicação no Azure.](web-sites-restore.md)