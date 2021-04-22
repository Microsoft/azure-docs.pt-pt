---
title: Utilizar Azure Data Lake Tools for Visual Studio Code
description: Aprenda a usar ferramentas do Lago de Dados Azure para o Código do Estúdio Visual para criar, testar e executar scripts U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878583"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utilizar Azure Data Lake Tools for Visual Studio Code

Neste artigo, saiba como pode utilizar ferramentas do Lago de Dados Azure para código de estúdio visual (Código VS) para criar, testar e executar scripts U-SQL. A informação também é abordada no seguinte vídeo:

[![Leitor de vídeo: Ferramentas do Lago de Dados Azure para código VS](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Pré-requisitos

Azure Data Lake Tools for VS Code suporta Windows, Linux e macOS. A corrida local U-SQL e o depurg local funcionam apenas no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Para macOS e Linux:

- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalar ferramentas do Lago de Dados Azure

Depois de instalar os pré-requisitos, pode instalar ferramentas do Lago de Dados Azure para código VS.

### <a name="to-install-azure-data-lake-tools"></a>Para instalar ferramentas do Lago de Dados Azure

1. Abra o Visual Studio Code.
2. Selecione **extensões** no painel esquerdo. Insira **as ferramentas do Lago de Dados Azure** na caixa de pesquisa.
3. Selecione **Instalar** ao lado **das ferramentas do Lago de Dados Azure**.

   ![Seleções para instalar ferramentas do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Após alguns segundos, o botão **instalar** muda para **Recarregar**.
4. Selecione **Recarregar** para ativar a extensão **Azure Data Lake Tools.**
5. Selecione **Reload Window** para confirmar. Pode ver **as ferramentas do Lago de Dados Azure** no painel **de extensões.**

## <a name="activate-azure-data-lake-tools"></a>Ativar ferramentas do lago de dados Azure

Crie um ficheiro .usql ou abra um ficheiro .usql existente para ativar a extensão.

## <a name="work-with-u-sql"></a>Trabalhar com u-SQL

Para trabalhar com o U-SQL, é necessário abrir um ficheiro U-SQL ou uma pasta.

### <a name="to-open-the-sample-script"></a>Para abrir o roteiro da amostra

Abra a paleta de comando (Ctrl+Shift+P) e **introduza ADL: Open Sample Script**. Abre outro exemplo desta amostra. Também pode editar, configurar e submeter um script sobre este caso.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Para abrir uma pasta para o seu projeto U-SQL

1. A partir do Código do Estúdio Visual, selecione o menu **'Ficheiro'** e, em seguida, selecione **Abrir Pasta**.
2. Especifique uma pasta e, em seguida, **selecione Selecione Pasta**.
3. Selecione o menu **'Ficheiro'** e, em seguida, selecione **Novo**. Um ficheiro Sem título-1 é adicionado ao projeto.
4. Introduza o seguinte código no ficheiro Untitled-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   SAÍDA @departments     PARA "/Output/departments.csv" USANDO Outputters.Csv();

    O script cria um ficheiro departments.csv com alguns dados incluídos na pasta /saída.

5. Guarde o ficheiro como **myUSQL.usql** na pasta aberta.

### <a name="to-compile-a-u-sql-script"></a>Para compilar um script U-SQL

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Introduza **ADL: Compile Script**. Os resultados da compilação aparecem na janela **de saída.** Também pode clicar com o direito num ficheiro de script e, em seguida, selecionar **ADL: Compilar Script** para compilar um trabalho U-SQL. O resultado da compilação aparece no painel **de saída.**

### <a name="to-submit-a-u-sql-script"></a>Para submeter um script U-SQL

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: Submeta-se a emprego.** Também pode clicar à direita num ficheiro de script e, em seguida, selecionar **ADL: Submeter Job**.

Depois de submeter um trabalho U-SQL, os registos de submissão aparecem na janela **de saída** no Código VS. A visão do trabalho aparece no painel certo. Se a submissão for bem sucedida, a URL de trabalho também aparece. Você pode abrir o URL de trabalho em um navegador web para rastrear o estado de trabalho em tempo real.

No **separador RESUMO** DA Vista de Emprego, pode ver os detalhes do trabalho. As funções principais incluem reenviar um script, duplicar um script e abrir no portal. No separador **DATA** da vista de trabalho, pode consultar os ficheiros de entrada, ficheiros de saída e ficheiros de recursos. Os ficheiros podem ser descarregados para o computador local.

![Separador sumário na vista de trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Separador de dados na visão de emprego](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Para definir o contexto padrão

Pode definir o contexto padrão para aplicar esta definição em todos os ficheiros de script se não tiver definido parâmetros para ficheiros individualmente.

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Introduza **aDL: Definir o contexto padrão**. Ou clique com o botão direito no editor de scripts e selecione **ADL: Definir o contexto padrão**.
3. Escolha a conta, base de dados e esquema que deseja. A definição é guardada para o xxx_settings.jsno ficheiro de configuração.

   ![Conjunto de conta, base de dados e esquema como o contexto padrão](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Para definir parâmetros de script

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Introduza **ADL: Definir parâmetros de script**.
3. O xxx_settings.jsem arquivo é aberto com as seguintes propriedades:

   - **conta**: Uma conta Azure Data Lake Analytics sob a sua assinatura Azure que é necessária para compilar e executar empregos U-SQL. Precisa de configurar a conta do computador antes de compilar e executar trabalhos U-SQL.
   - **base de dados**: Uma base de dados na sua conta. O padrão é **mestre.**
   - **esquema:** Um esquema debaixo da sua base de dados. O padrão é **dbo**.
   - **opcionalSettings:**
        - **prioridade**: O intervalo de prioridades é de 1 a 1000, com 1 como prioridade máxima. O valor predefinido é **1000**.
        - **graus DeParallelismo**: O paralelismo varia de 1 a 150. O valor predefinido é o paralelismo máximo permitido na sua conta Azure Data Lake Analytics.

   ![Conteúdo do ficheiro JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Depois de guardar a configuração, as informações de conta, base de dados e esquemas aparecem na barra de estado no canto inferior esquerdo do ficheiro .usql correspondente se não tiver um contexto padrão configurado.

### <a name="to-set-git-ignore"></a>Para definir Git ignorar

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: set Git Ignore**.

   - Se não tiver um ficheiro **.gitIgnore** na sua pasta de trabalho VS Code, é criado um ficheiro chamado **.gitIgnore** na sua pasta. Quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) são adicionados no ficheiro por padrão. Pode fazer mais atualizações se necessário.
   - Se já tiver um ficheiro **.gitIgnore** na sua pasta de trabalho VS Code, a ferramenta adiciona quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache,** **obj**) no seu ficheiro **.gitIgnore** se os quatro itens não estiverem incluídos no ficheiro.

   ![Itens no ficheiro .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Trabalhar com ficheiros por trás do código: C Sharp, Python e R

A azure Data Lake Tools suporta vários códigos personalizados. Para obter instruções, consulte [Develop U-SQL com Python, R e C Sharp para Azure Data Lake Analytics em CÓDIGO VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Trabalhar com conjuntos

Para obter informações sobre o desenvolvimento de conjuntos, consulte [conjuntos U-SQL para trabalhos de Azure Data Lake Analytics]().

Pode utilizar ferramentas do Data Lake para registar conjuntos de códigos personalizados no catálogo Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Para registar uma montagem

Pode registar a montagem através do comando **ADL: Register Assembly** ou **ADL: Register Assembly (Advanced).**

### <a name="to-register-through-the-adl-register-assembly-command"></a>Para se registar através do ADL: Registar o comando da Assembléia

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: Assembleia de registo**.
3. Especifique o caminho de montagem local.
4. Selecione uma conta Data Lake Analytics.
5. Selecione uma base de dados.

O portal é aberto num browser e exibe o processo de registo de montagem.  

Uma forma mais conveniente de acionar o comando **ADL: Register Assembly** é clicar com o direito no ficheiro .dll no File Explorer.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Para se registar através do comando ADL: Register Assembly (Advanced)

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: Register Assembly (Avançado)**.
3. Especifique o caminho de montagem local.
4. O ficheiro JSON é apresentado. Reveja e edite as dependências de montagem e os parâmetros de recursos, se necessário. As instruções são apresentadas na janela **de saída.** Para proceder ao registo de montagem, guarde (Ctrl+S) o ficheiro JSON.

   ![Ficheiro JSON com dependências de montagem e parâmetros de recursos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- A Azure Data Lake Tools autodetects se o DLL tem alguma dependência de montagem. As dependências são exibidas no ficheiro JSON depois de detetadas.
>- Pode fazer o upload dos seus recursos DLL (por exemplo, .txt, .png e .csv) como parte do registo de montagem.

Outra forma de acionar o comando **ADL: Register Assembly (Advanced)** é clicar com o direito no ficheiro .dll no File Explorer.

O seguinte código U-SQL demonstra como convocar uma montagem. Na amostra, o nome de montagem é *testado.*

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Utilize a execução local U-SQL e o depurg local para utilizadores do Windows

A execução local da U-SQL testa os seus dados locais e valida o seu script localmente antes de o seu código ser publicado no Data Lake Analytics. Pode utilizar a função de depurado local para completar as seguintes tarefas antes de o seu código ser submetido ao Data Lake Analytics:

- Depurar o seu código C# por trás.
- Passe o código.
- Valide o seu script localmente.

A funcionalidade de depurações locais e locais funciona apenas em ambientes Windows, e não é suportada em sistemas operativos baseados em macOS e Linux.

Para obter instruções sobre a execução local e depuro local, consulte [a corrida local U-SQL e o depurg local com Código do Estúdio Visual.](data-lake-tools-for-vscode-local-run-and-debug.md)

## <a name="connect-to-azure"></a>Ligar ao Azure

Antes de poder compilar e executar scripts U-SQL no Data Lake Analytics, tem de ligar-se à sua conta Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Para ligar ao Azure usando um comando

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.

2. Insira **ADL: Login**. A informação de login aparece no direito inferior.

   ![Insira o comando de login](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Notificação sobre o sent-in e a autenticação](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Selecione **Copy & Open** para abrir a página de [login](https://aka.ms/devicelogin). Cole o código na caixa e, em seguida, **selecione Continue**.

    ![Página de login](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Siga as instruções para iniciar serção na página web. Quando estiver ligado, o nome da sua conta Azure aparece na barra de estado no canto inferior esquerdo da janela do Código VS.

> [!NOTE]
>
> - As Ferramentas do Lago de Dados assinam automaticamente na próxima vez que não assinar.
> - Se a sua conta tiver dois fatores ativados, recomendamos que utilize a autenticação do telefone em vez de utilizar um PIN.

Para assinar, introduza o comando **ADL: Logout**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Para ligar a Azure do explorador

Expandir **AZURE DATALAKE**, selecione **Iniciar súm em Azure**, e, em seguida, siga o passo 3 e o passo 4 de [Ligar a Azure utilizando um comando](#sign-in-by-command).

![Seleção "Iniciar súpido em Azure" no explorador](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Não pode sair do explorador. Para assinar, consulte [Ligar-se ao Azure utilizando um comando](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Criar um script de extração

Pode criar um script de extração para .csv, .tsv e .txt ficheiros utilizando o comando **ADL: Criar SCRIPT EXTRACT** ou a partir do explorador do Lago de Dados Azure.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Para criar um script de extração usando um comando

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e **introduza ADL: Criar script EXTRACT**.
2. Especifique o caminho completo para um ficheiro de armazenamento Azure e selecione a tecla 'Entrar'.
3. Selecione uma conta.
4. Para um ficheiro .txt, selecione um delimiter para extrair o ficheiro.

![Processo para criar um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base nas suas entradas. Para um script que não consiga detetar as colunas, escolha uma entre as duas opções. Caso contrário, apenas um guião será gerado.

![Resultado da criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Para criar um script de extração do explorador

Outra forma de criar o script de extração é através do menu de clique direito (atalho) no .csv, .tsv, ou .txt ficheiro na Azure Data Lake Store ou no armazenamento Azure Blob.

![Comando "Criar SCRIPT EXTRACT" a partir do menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Passos seguintes

- [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics em CÓDIGO VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL corrida local e depurar local com Código de Estúdio Visual](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Começar com Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL utilizando ferramentas do Data Lake para Estúdio Visual](data-lake-analytics-data-lake-tools-get-started.md)
