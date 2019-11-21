---
title: Use Azure Functions to perform a database clean up task
description: Use Azure Functions to schedule a task that connects to Azure SQL Database to periodically clean up rows.
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
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Use Azure Functions to connect to an Azure SQL Database

This article shows you how to use Azure Functions to create a scheduled job that connects to an Azure SQL Database or Azure SQL Managed Instance. The function code cleans up rows in a table in the database. The new C# function is created based on a pre-defined timer trigger template in Visual Studio 2019. To support this scenario, you must also set a database connection string as an app setting in the function app. For Azure SQL Managed Instance you need to [enable public endpoint](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) to be able to connect from Azure Functions. This scenario uses a bulk operation against the database. 

If this is your first experience working with C# Functions, you should read the [Azure Functions C# developer reference](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Complete the steps in the article [Create your first function using Visual Studio](functions-create-your-first-function-visual-studio.md) to create a local function app that targets the version 2.x runtime. You must also have published your project to a function app in Azure.

+ This article demonstrates a Transact-SQL command that executes a bulk cleanup operation in the **SalesOrderHeader** table in the AdventureWorksLT sample database. To create the AdventureWorksLT sample database, complete the steps in the article [Create an Azure SQL database in the Azure portal](../sql-database/sql-database-get-started-portal.md).

+ You must add a [server-level firewall rule](../sql-database/sql-database-get-started-portal-firewall.md) for the public IP address of the computer you use for this quickstart. This rule is required to be able access the SQL database instance from your local computer.  

## <a name="get-connection-information"></a>Obter informações da ligação

You need to get the connection string for the database you created when you completed [Create an Azure SQL database in the Azure portal](../sql-database/sql-database-get-started-portal.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Select **SQL Databases** from the left-hand menu, and select your database on the **SQL databases** page.

1. Select **Connection strings** under **Settings** and copy the complete **ADO.NET** connection string. For Azure SQL Managed Instance copy connection string for public endpoint.

    ![Copy the ADO.NET connection string.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação

Uma aplicação de função aloja a execução das suas funções no Azure. As a best security practice, store connection strings and other secrets in your function app settings. Using application settings prevents accidental disclosure of the connection string with your code. You can access app settings for your function app right from Visual Studio.

You must have previously published your app to Azure. If you haven't already done so, [Publish your function app to Azure](functions-develop-vs.md#publish-to-azure).

1. In Solution Explorer, right-click the function app project and choose **Publish** > **Manage application settings...** . Select **Add setting**, in **New app setting name**, type `sqldb_connection`, and select **OK**.

    ![Application settings for the function app.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. In the new **sqldb_connection** setting, paste the connection string you copied in the previous section into the **Local** field and replace `{your_username}` and `{your_password}` placeholders with real values. Select **Insert value from local** to copy the updated value into the **Remote** field, and then select **OK**.

    ![Add SQL connection string setting.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    The connection strings are stored encrypted in Azure (**Remote**). To prevent leaking secrets, the local.settings.json project file (**Local**) should be excluded from source control, such as by using a .gitignore file.

## <a name="add-the-sqlclient-package-to-the-project"></a>Add the SqlClient package to the project

You need to add the NuGet package that contains the SqlClient library. This data access library is needed to connect to a SQL database.

1. Open your local function app project in Visual Studio 2019.

1. In Solution Explorer, right-click the function app project and choose **Manage NuGet Packages**.

1. No separador **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.

1. In the **System.Data.SqlClient** page, select version `4.5.1` and then click **Install**.

1. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**.

1. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

Now, you can add the C# function code that connects to your SQL Database.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função acionada por temporizador

1. In Solution Explorer, right-click the function app project and choose **Add** > **New Azure function**.

1. With the **Azure Functions** template selected, name the new item something like `DatabaseCleanup.cs` and select **Add**.

1. In the **New Azure function** dialog box, choose **Timer trigger** and then **OK**. This dialog creates a code file for the timer triggered function.

1. Open the new code file and add the following using statements at the top of the file:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Replace the existing `Run` function with the following code:

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

    This function runs every 15 seconds to update the `Status` column based on the ship date. To learn more about the Timer trigger, see [Timer trigger for Azure Functions](functions-bindings-timer.md).

1. Press **F5** to start the function app. The [Azure Functions Core Tools](functions-develop-local.md) execution window opens behind Visual Studio.

1. At 15 seconds after startup, the function runs. Watch the output and note the number of rows updated in the **SalesOrderHeader** table.

    ![View the function logs.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    On the first execution, you should update 32 rows of data. Following runs update no data rows, unless you make changes to the SalesOrderHeader table data so that more rows are selected by the `UPDATE` statement.

If you plan to [publish this function](functions-develop-vs.md#publish-to-azure), remember to change the `TimerTrigger` attribute to a more reasonable [cron schedule](functions-bindings-timer.md#ncrontab-expressions) than every 15 seconds.

## <a name="next-steps"></a>Passos seguintes

Next, learn how to use. Functions with Logic Apps to integrate with other services.

> [!div class="nextstepaction"]
> [Create a function that integrates with Logic Apps](functions-twitter-email.md)

For more information about Functions, see the following articles:

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
