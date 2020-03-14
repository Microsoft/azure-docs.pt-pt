---
title: Utilize funções Azure para executar uma tarefa de limpeza de bases de dados
description: Utilize funções Azure para agendar uma tarefa que se liga à Base de Dados Azure SQL para limpar periodicamente as linhas.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366819"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Utilize funções Azure para ligar a uma base de dados Azure SQL

Este artigo mostra-lhe como usar funções Azure para criar um trabalho programado que se conecta a uma Base de Dados Azure SQL ou a Instância Gerida Azure SQL. O código de função limpa as linhas numa tabela na base de dados. A C# nova função é criada com base num modelo de gatilho pré-definido no Estúdio Visual 2019. Para suportar este cenário, também deve definir uma cadeia de ligação de base de dados como uma definição de aplicação na aplicação de função. Para o Azure SQL Managed Instance, é necessário permitir que o [ponto final público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) possa ligar-se a partir das Funções Azure. Este cenário utiliza uma operação a granel contra a base de dados. 

Se esta é a C# sua primeira experiência a trabalhar com Funções, deve ler a referência do desenvolvedor de [Funções C# Azure](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Complete os passos do artigo Crie a [sua primeira função usando](functions-create-your-first-function-visual-studio.md) o Visual Studio para criar uma aplicação de função local que direcione a versão 2.x ou uma versão posterior do tempo de execução. Também deve ter publicado o seu projeto numa aplicação de função no Azure.

+ Este artigo demonstra um comando Transact-SQL que executa uma operação de limpeza a granel na tabela **SalesOrderHeader** na base de dados da amostra AdventureWorksLT. Para criar a base de dados da amostra AdventureWorksLT, complete os passos do artigo [Crie uma base de dados Azure SQL no portal Azure](../sql-database/sql-database-get-started-portal.md).

+ Tem de adicionar uma [regra de firewall ao nível do servidor](../sql-database/sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que utiliza para este arranque rápido. Esta regra é necessária para poder aceder à instância de base de dados SQL do seu computador local.  

## <a name="get-connection-information"></a>Obter informações da ligação

Tem de obter a cadeia de ligação para a base de dados criada quando completou A [Create a Azure SQL base](../sql-database/sql-database-get-started-portal.md)de dados no portal Azure .

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione Bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Selecione as cordas de **ligação** em **Definições** e copie a cadeia de ligação completa **ADO.NET.** Para o fio de ligação de cópia de cópia azure SQL Managed Instance para ponto final público.

    ![Copie a cadeia de ligação ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação

Uma aplicação de função aloja a execução das suas funções no Azure. Como uma melhor prática de segurança, guarde cordas de ligação e outros segredos nas definições da sua aplicação de função. A utilização das definições de aplicação impede a divulgação acidental da cadeia de ligação com o seu código. Pode aceder às definições de aplicativos para a sua aplicação de funções a partir do Estúdio Visual.

Deve ter publicado previamente a sua aplicação para o Azure. Se ainda não o fez, [publique a sua aplicação de funções no Azure](functions-develop-vs.md#publish-to-azure).

1. No Solution Explorer, clique no projeto da aplicação de funções e escolha **publicar** > editar definições do Serviço de **Aplicações Azure**. **Selecione adicionar definição**, em novo nome de definição de **aplicações**, digite `sqldb_connection`, e selecione **OK**.

    ![Definições de aplicação para a aplicação de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Na nova definição **de sqldb_connection,** colhe a cadeia de ligação que copiou na secção anterior no campo **Local** e substitua `{your_username}` e `{your_password}` espaços por valores reais. Selecione **Inserir valor a partir do local** para copiar o valor atualizado no campo **Remoto** e, em seguida, selecione **OK**.

    ![Adicione a definição de corda de ligação SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    As cordas de ligação são armazenadas encriptadas em Azure **(Remote).** Para evitar fugas de segredos, o ficheiro do projeto local.settings.json **(Local)** deve ser excluído do controlo de origem, como por exemplo através de um ficheiro .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adicione o pacote SqlClient ao projeto

Você precisa adicionar o pacote NuGet que contém a biblioteca SqlClient. Esta biblioteca de acesso a dados é necessária para se ligar a uma base de dados SQL.

1. Abra o seu projeto de aplicação de funções locais no Visual Studio 2019.

1. No Solution Explorer, clique no projeto da aplicação de funções e escolha **Gerir pacotes NuGet**.

1. No separador **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.

1. Na página **System.Data.SqlClient,** selecione versão `4.5.1` e, em seguida, clique em **Instalar**.

1. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**.

1. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

Agora, pode adicionar C# o código de função que se liga à sua Base de Dados SQL.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função acionada por temporizador

1. No Solution Explorer, clique no projeto da aplicação de funções e escolha **Adicionar** > **função New Azure**.

1. Com o modelo **funções Azure** selecionado, nomeie o novo item como `DatabaseCleanup.cs` e selecione **Adicionar**.

1. Na caixa de diálogo de **função New Azure,** escolha o **gatilho do Temporizador** **e,** em seguida, OK . Este diálogo cria um ficheiro de código para a função acionada pelo temporizador.

1. Abra o novo ficheiro de código e adicione o seguinte utilizando declarações na parte superior do ficheiro:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Substitua a função `Run` existente pelo seguinte código:

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

    Esta função funciona a cada 15 segundos para atualizar a coluna `Status` com base na data da nave. Para saber mais sobre o gatilho do Temporizador, consulte o gatilho do [Temporizador para funções Azure](functions-bindings-timer.md).

1. Pressione **F5** para iniciar a aplicação de funções. A janela de execução de [Ferramentas Core Funções Azur do Azure](functions-develop-local.md) abre-se atrás do Estúdio Visual.

1. A 15 segundos após o arranque, a função funciona. Observe a saída e note o número de linhas atualizadas na tabela **SalesOrderHeader.**

    ![Ver os registos de função.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na primeira execução, deve atualizar 32 linhas de dados. Após as execuções, a atualização de nenhuma linha de dados, a menos que efaça alterações nos dados da tabela SalesOrderHeader para que mais linhas sejam selecionadas pela declaração `UPDATE`.

Se pretende [publicar esta função,](functions-develop-vs.md#publish-to-azure)lembre-se de alterar o `TimerTrigger` atributo a um [cron horário](functions-bindings-timer.md#ncrontab-expressions) mais razoável do que a cada 15 segundos.

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a usar. Funções com Apps Lógicas para integrar com outros serviços.

> [!div class="nextstepaction"]
> [Criar uma função que se integre com apps lógicas](functions-twitter-email.md)

Para mais informações sobre funções, consulte os seguintes artigos:

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
