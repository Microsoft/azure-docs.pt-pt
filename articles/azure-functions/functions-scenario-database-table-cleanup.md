---
title: Usar Azure Functions para executar uma tarefa de limpeza do banco de dados
description: Use Azure Functions para agendar uma tarefa que se conecta ao banco de dados SQL do Azure para limpar as linhas periodicamente.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: f70b5b83561e7c580dd7192850c8eb50be5aac0a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230386"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Usar Azure Functions para se conectar a um banco de dados SQL do Azure

Este artigo mostra como usar Azure Functions para criar um trabalho agendado que se conecta a um banco de dados SQL do Azure ou Instância Gerenciada SQL do Azure. O código de função limpa linhas em uma tabela no banco de dados. A nova C# função é criada com base em um modelo de gatilho de temporizador predefinido no Visual Studio 2019. Para dar suporte a esse cenário, você também deve definir uma cadeia de conexão de banco de dados como uma configuração de aplicativo no aplicativo de funções. Para o Azure SQL Instância Gerenciada você precisa [habilitar o ponto de extremidade público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) para poder se conectar de Azure functions. Esse cenário usa uma operação em massa no banco de dados. 

Se esta for sua primeira experiência trabalhando com C# funções, você deverá ler a [referência C# do Azure Functions Developer](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua as etapas no artigo [criar sua primeira função usando o Visual Studio](functions-create-your-first-function-visual-studio.md) para criar um aplicativo de função local direcionado para o tempo de execução da versão 2. x. Você também deve ter publicado seu projeto em um aplicativo de funções no Azure.

+ Este artigo demonstra um comando Transact-SQL que executa uma operação de limpeza em massa na tabela **SalesOrderHeader** no banco de dados de exemplo AdventureWorksLT. Para criar o banco de dados de exemplo AdventureWorksLT, conclua as etapas no artigo [criar um banco de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

+ Você deve adicionar uma [regra de firewall no nível de servidor](../sql-database/sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que você usa para este guia de início rápido. Essa regra é necessária para poder acessar a instância do banco de dados SQL do computador local.  

## <a name="get-connection-information"></a>Obter informações da ligação

Você precisa obter a cadeia de conexão para o banco de dados criado quando concluiu a [criação de um banco de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página bancos de dados **SQL** .

1. Selecione **cadeias de conexão** em **configurações** e copie a cadeia de conexão completa do **ADO.net** . Para Azure SQL Instância Gerenciada copiar cadeia de conexão para o ponto de extremidade público.

    ![Copie a cadeia de conexão ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação

Uma aplicação de função aloja a execução das suas funções no Azure. Como prática recomendada de segurança, armazene cadeias de conexão e outros segredos nas configurações do aplicativo de funções. O uso de configurações do aplicativo impede a divulgação acidental da cadeia de conexão com seu código. Você pode acessar as configurações de aplicativo para seu aplicativo de funções diretamente do Visual Studio.

Você deve ter publicado anteriormente seu aplicativo no Azure. Se você ainda não tiver feito isso, [Publique seu aplicativo de funções no Azure](functions-develop-vs.md#publish-to-azure).

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **publicar** > **gerenciar configurações do aplicativo...** . Selecione **Adicionar configuração**, em **novo aplicativo nome da configuração**, digite `sqldb_connection`e selecione **OK**.

    ![Configurações do aplicativo para o aplicativo de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Na nova configuração de **sqldb_connection** , Cole a cadeia de conexão que você copiou na seção anterior no campo **Local** e substitua `{your_username}` e `{your_password}` espaços reservados por valores reais. Selecione **Inserir valor do local** para copiar o valor atualizado para o campo **remoto** e selecione **OK**.

    ![Adicione a configuração de cadeia de conexão SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    As cadeias de conexão são armazenadas criptografadas no Azure (**remoto**). Para evitar vazamento de segredos, o arquivo de projeto local. Settings. JSON (**local**) deve ser excluído do controle do código-fonte, como usando um arquivo. gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adicionar o pacote SqlClient ao projeto

Você precisa adicionar o pacote NuGet que contém a biblioteca SqlClient. Essa biblioteca de acesso a dados é necessária para conectar-se a um SQL Database.

1. Abra seu projeto de aplicativo de função local no Visual Studio 2019.

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **gerenciar pacotes NuGet**.

1. No separador **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.

1. Na página **System. Data. SqlClient** , selecione versão `4.5.1` e clique em **instalar**.

1. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**.

1. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

Agora, você pode adicionar o C# código de função que se conecta ao seu banco de dados SQL.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função acionada por temporizador

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **adicionar** > **nova função do Azure**.

1. Com o modelo de **Azure Functions** selecionado, nomeie o novo item como `DatabaseCleanup.cs` e selecione **Adicionar**.

1. Na caixa de diálogo **nova função do Azure** , escolha **gatilho de temporizador** e, em seguida, **OK**. Essa caixa de diálogo cria um arquivo de código para a função disparada pelo temporizador.

1. Abra o novo arquivo de código e adicione as seguintes instruções using na parte superior do arquivo:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Substitua a função de `Run` existente pelo código a seguir:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Essa função é executada a cada 15 segundos para atualizar a `Status` coluna com base na data de remessa. Para saber mais sobre o gatilho de temporizador, consulte [gatilho de temporizador para Azure Functions](functions-bindings-timer.md).

1. Pressione **F5** para iniciar o aplicativo de funções. A janela de execução de [Azure Functions Core Tools](functions-develop-local.md) é aberta atrás do Visual Studio.

1. Em 15 segundos após a inicialização, a função é executada. Observe a saída e anote o número de linhas atualizadas na tabela **SalesOrderHeader** .

    ![Exiba os logs de função.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na primeira execução, você deve atualizar 32 linhas de dados. A seguir executa a atualização sem linhas de dados, a menos que você faça alterações nos dados da tabela SalesOrderHeader para que mais linhas sejam selecionadas pela instrução `UPDATE`.

Se você planeja [publicar essa função](functions-develop-vs.md#publish-to-azure), lembre-se de alterar o atributo `TimerTrigger` para uma [agenda cron](functions-bindings-timer.md#ncrontab-expressions) mais razoável do que a cada 15 segundos.

## <a name="next-steps"></a>Passos seguintes

Em seguida, saiba como usar o. Funções com aplicativos lógicos para integrar com outros serviços.

> [!div class="nextstepaction"]
> [Criar uma função que se integre com aplicativos lógicos](functions-twitter-email.md)

Para obter mais informações sobre o functions, consulte os seguintes artigos:

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
