---
title: Utilize as funções do Azure para executar uma tarefa de limpeza da base de dados | Documentos da Microsoft
description: Utilize as funções do Azure para agendar uma tarefa que se liga a base de dados do Azure SQL para limpar periodicamente de linhas.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 19a5fe4c087d477ff15d2237a36d1c4ecaa0e070
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65908153"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Utilize as funções do Azure para ligar a uma base de dados do SQL do Azure

Este artigo mostra-lhe como utilizar as funções do Azure para criar uma tarefa agendada que se liga a uma instância de base de dados do Azure SQL. O código de função limpa linhas numa tabela na base de dados. O novo C# função é criada com base num modelo de Acionador de temporizador predefinidas no Visual Studio 2019. Para suportar este cenário, também tem de definir uma cadeia de ligação de base de dados como uma definição de aplicação na aplicação de função. Este cenário utiliza uma operação em massa na base de dados. 

Se esta for a sua primeira experiência ao trabalhar com C# funções, deve ler o [as funções do Azure C# referência para programadores](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua os passos no artigo [criar a primeira função com o Visual Studio](functions-create-your-first-function-visual-studio.md) para criar uma aplicação de função local que tenha como destino a versão 2.x do runtime. Deve também publicou seu projeto para uma aplicação de funções no Azure.

+ Este artigo demonstra um comando do Transact-SQL que executa uma operação de limpeza em massa no **SalesOrderHeader** tabela na base de dados de exemplo AdventureWorksLT. Para criar a base de dados de exemplo do AdventureWorksLT, conclua os passos no artigo [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

+ Tem de adicionar uma [regra de firewall ao nível do servidor](../sql-database/sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que utilizar para este início rápido. Esta regra deve ser capaz de aceder a instância de base de dados SQL do computador local.  

## <a name="get-connection-information"></a>Obter informações da ligação

Tem de obter a cadeia de ligação da base de dados que criou quando concluiu [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Selecione **cadeias de ligação** sob **definições** e copie o completa **ADO.NET** cadeia de ligação.

    ![Copie a cadeia de ligação do ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de ligação

Uma aplicação de função aloja a execução das suas funções no Azure. Como melhor prática de segurança, armazene cadeias de ligação e outros segredos nas suas definições de aplicação de função. Usar as configurações do aplicativo impede a divulgação acidental de cadeia de ligação com o seu código. Pode acessar as definições da aplicação para a sua aplicação de função diretamente a partir do Visual Studio.

Deve ter anteriormente publicado a aplicação para o Azure. Se ainda não fez isso, [publicar a aplicação de funções no Azure](functions-develop-vs.md#publish-to-azure).

1. No Explorador de soluções, faça duplo clique o projeto de aplicação de função e escolha **Publish** > **gerir definições da aplicação...** . Selecione **Adicionar definição**, na **novo nome de definição de aplicação**, tipo `sqldb_connection`e selecione **OK**.

    ![Definições da aplicação para a aplicação de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. No novo **sqldb_connection** definição, cole a cadeia de ligação que copiou na secção anterior para o **Local** campo e substituir `{your_username}` e `{your_password}` marcadores de posição pelo real valores. Selecione **inserir o valor a partir de locais** para copiar o valor atualizado para o **remoto** campo e, em seguida, selecione **OK**.

    ![Adicione definição da cadeia de ligação SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    As cadeias de ligação são armazenadas de forma encriptada no Azure (**remoto**). Para impedir o vazamento segredos, o ficheiro de projeto Settings (**Local**) devem ser excluídos da controle de origem, tal como através de um ficheiro. gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adicione o pacote do SqlClient ao projeto

Terá de adicionar o pacote NuGet que contém a biblioteca do SqlClient. Esta biblioteca de acesso de dados é necessária para ligar a uma base de dados SQL.

1. Abra o projeto de aplicação de função local no Visual Studio 2019.

1. No Explorador de soluções, faça duplo clique o projeto de aplicação de função e escolha **gerir pacotes NuGet**.

1. No separador **Procurar**, procure ```System.Data.SqlClient``` e, quando o encontrar, selecione-o.

1. Na **SqlClient** página, selecione versão `4.5.1` e, em seguida, clique em **instalar**.

1. Quando a instalação estiver concluída, reveja as alterações e, em seguida, clique em **OK** para fechar a janela **Pré-visualização**.

1. Se for apresentada uma janela **Aceitação de Licença**, clique em **Aceito**.

Agora, pode adicionar o função código c# que se liga a base de dados SQL.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função acionada por temporizador

1. No Explorador de soluções, faça duplo clique o projeto de aplicação de função e escolha **Add** > **função do Azure novo**.

1. Com o **as funções do Azure** modelo selecionado, nomeie o novo item de algo como `DatabaseCleanup.cs` e selecione **Add**.

1. Na **função do Azure nova** caixa de diálogo caixa, escolha **acionador de temporizador** e, em seguida **OK**. Esta caixa de diálogo cria um ficheiro de código para a função de acionada por temporizador.

1. Abra o ficheiro de código novo e adicione o seguinte utilizando declarações na parte superior do ficheiro:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Substituir o existente `Run` função com o código a seguir:

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

    Esta função é executada a cada 15 segundos para atualizar o `Status` coluna com base na data de remessa. Para saber mais sobre o acionador de temporizador, veja [acionador de temporizador das funções do Azure](functions-bindings-timer.md).

1. Prima **F5** para iniciar a aplicação de função. O [ferramentas de núcleo de funções do Azure](functions-develop-local.md) é aberta a janela de execução por trás do Visual Studio.

1. Em 15 segundos após o arranque, a função é executada. Observe a saída e anote o número de linhas atualizadas na **SalesOrderHeader** tabela.

    ![Ver os registos de função.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na primeira execução, deve atualizar 32 linhas de dados. Seguintes execuções de atualização sem linhas de dados, a menos que fizer alterações aos dados de tabela SalesOrderHeader, de modo a que mais linhas estão selecionadas pelo `UPDATE` instrução.

Se planeja [publicar esta função](functions-develop-vs.md#publish-to-azure), não se esqueça de alterar a `TimerTrigger` atributo para um mais razoável [agenda cron](functions-bindings-timer.md#cron-expressions) a cada 15 segundos.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, Aprenda a utilizar. Funções com o Logic Apps para integrar com outros serviços.

> [!div class="nextstepaction"]
> [Criar uma função que se integra com o Logic Apps](functions-twitter-email.md)

Para obter mais informações sobre as funções, consulte os artigos seguintes:

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.  
