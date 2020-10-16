---
title: Utilize funções Azure para executar uma tarefa de limpeza de bases de dados
description: Utilize funções Azure para agendar uma tarefa que se conecta à Base de Dados Azure SQL para limpar periodicamente as linhas.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207259"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Utilize funções Azure para ligar a uma Base de Dados Azure SQL

Este artigo mostra-lhe como usar funções Azure para criar um trabalho programado que se conecta a uma Base de Dados Azure SQL ou Azure SQL Managed Instance. O código de função limpa as linhas numa tabela na base de dados. A nova função C# é criada com base num modelo de gatilho de temporizador pré-definido no Visual Studio 2019. Para suportar este cenário, também deve definir uma cadeia de ligação de base de dados como uma definição de aplicação na aplicação de função. Para Azure SQL Managed Instance, é necessário [permitir que o ponto final público](../azure-sql/managed-instance/public-endpoint-configure.md) possa ligar-se a partir de Funções Azure. Este cenário usa uma operação a granel contra a base de dados. 

Se esta for a sua primeira experiência a trabalhar com funções C# deve ler a referência do [programador Azure Functions C#](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Preencha os passos no artigo Crie a [sua primeira função utilizando](functions-create-your-first-function-visual-studio.md) o Visual Studio para criar uma aplicação de função local que vise a versão 2.x ou uma versão posterior do tempo de execução. Também deve ter publicado o seu projeto numa aplicação de função em Azure.

+ Este artigo demonstra um comando Transact-SQL que executa uma operação de limpeza a granel na tabela **SalesOrderHeader** na base de dados de amostras AdventureWorksLT. Para criar a base de dados de amostras AdventureWorksLT, preencha os passos no artigo [Crie uma base de dados na Base de Dados Azure SQL utilizando o portal Azure](../azure-sql/database/single-database-create-quickstart.md).

+ Tem de adicionar uma [regra de firewall ao nível](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) do servidor para o endereço IP público do computador que utiliza para este arranque rápido. Esta regra é necessária para poder aceder à placa sql database a partir do seu computador local.  

## <a name="get-connection-information"></a>Obter informações da ligação

É necessário obter o fio de ligação para a base de dados que criou quando concluiu [Criar uma base de dados na Base de Dados Azure SQL utilizando o portal Azure](../azure-sql/database/single-database-create-quickstart.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Selecione **as cadeias de ligação** em **Definições** e copie o fio de ligação **completo ADO.NET.** Para a cadeia de ligação de cópia de instância gerida Azure SQL para ponto final público.

    ![Copie a cadeia de ligação de ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação

Uma aplicação de função aloja a execução das suas funções no Azure. Como melhor prática de segurança, guarde as cordas de ligação e outros segredos nas definições da aplicação de funções. A utilização das definições de aplicação impede a divulgação acidental da cadeia de ligação com o seu código. Pode aceder às definições da aplicação para a sua aplicação de função a partir do Visual Studio.

Deve ter publicado previamente a sua aplicação para a Azure. Se ainda não o fez, [publique a sua aplicação de função no Azure.](functions-develop-vs.md#publish-to-azure)

1. No Solution Explorer, clique com o botão direito no projeto da aplicação de função e escolha **as**  >  **definições do Serviço de Aplicações Edit editar Azure**. Selecione **Adicionar a definição**, em **novo nome de definição de aplicação,** `sqldb_connection` escreva e selecione **OK**.

    ![Definições de aplicação para a aplicação de função.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Na nova definição **de sqldb_connection,** cole a cadeia de ligação copiada na secção anterior para o campo **Local** e substitua e coloque os `{your_username}` espaços `{your_password}` reservados por valores reais. **Selecione Inserir valor do local** para copiar o valor atualizado no campo **Remote** e, em seguida, selecione **OK**.

    ![Adicione a definição da cadeia de ligação SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    As cadeias de ligação são armazenadas encriptadas em Azure **(Remote**). Para evitar fugas de segredos, os local.settings.jsno ficheiro do projeto **(Local**) devem ser excluídos do controlo de origem, tais como através de um ficheiro .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adicione o pacote SqlClient ao projeto

Tem de adicionar o pacote NuGet que contém a biblioteca SqlClient. Esta biblioteca de acesso a dados é necessária para ligar à Base de Dados SQL.

1. Abra o seu projeto de aplicação de funções local no Visual Studio 2019.

1. No Solution Explorer, clique com o botão direito no projeto da aplicação de funções e escolha **Gerir pacotes NuGet**.

1. No separador **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.

1. Na página **System.Data.SqlClient,** selecione versão `4.5.1` e, em seguida, clique em **Instalar**.

1. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**.

1. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

Agora, pode adicionar o código de função C# que se conecta à sua Base de Dados SQL.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função acionada por temporizador

1. No Solution Explorer, clique com o botão direito no projeto da aplicação de funções e escolha adicionar a função **Add**  >  **New Azure**.

1. Com o modelo **de Funções Azure** selecionado, nomeie o novo item algo como `DatabaseCleanup.cs` e selecione **Adicionar**.

1. Na caixa de diálogo da **função New Azure,** escolha o **gatilho do Temporizador** e, em seguida, **OK**. Este diálogo cria um ficheiro de código para a função de temporizador acionada.

1. Abra o novo ficheiro de código e adicione as seguintes declarações na parte superior do ficheiro:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Substitua a função existente `Run` pelo seguinte código:

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

    Esta função é de 15 em 15 segundos para atualizar a `Status` coluna com base na data da nave. Para saber mais sobre o gatilho do temporizador, consulte o [gatilho do Temporizador para as Funções Azure](functions-bindings-timer.md).

1. Prima **F5** para iniciar a aplicação de função. A janela de execução [Azure Functions Core Tools](functions-develop-local.md) abre-se atrás do Estúdio Visual.

1. A 15 segundos do arranque, a função funciona. Observe a saída e note o número de linhas atualizadas na tabela **SalesOrderHeader.**

    ![Ver os registos de funções.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na primeira execução, deverá atualizar 32 linhas de dados. Após as execuções, não há linhas de dados, a não ser que escorra alterações aos dados da tabela SalesOrderHeader para que mais linhas sejam selecionadas pela `UPDATE` declaração.

Se pretender [publicar esta função,](functions-develop-vs.md#publish-to-azure)lembre-se de alterar o `TimerTrigger` atributo para um cron [mais](functions-bindings-timer.md#ncrontab-expressions) razoável do que a cada 15 segundos.

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a usar. Funções com Aplicações Lógicas para integrar com outros serviços.

> [!div class="nextstepaction"]
> [Criar uma função que se integre com Apps Lógicas](functions-twitter-email.md)

Para obter mais informações sobre Funções, consulte os seguintes artigos:

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
