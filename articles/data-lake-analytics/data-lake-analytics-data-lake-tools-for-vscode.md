---
title: Utilizar Azure Data Lake Tools for Visual Studio Code
description: Aprenda a usar ferramentas do Lago de Dados Azure para o Código do Estúdio Visual para criar, testar e executar scripts U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e7da5454581e0e414dc832d4fcec50277f3b7f40
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221252"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utilizar Azure Data Lake Tools for Visual Studio Code

Neste artigo, saiba como pode utilizar ferramentas do Lago de Dados Azure para código de estúdio visual (Código VS) para criar, testar e executar scripts U-SQL. A informação também é abordada no seguinte vídeo:

[![Leitor de vídeo: Ferramentas do Lago de Dados Azure para código VS](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Pré-requisitos

Azure Data Lake Tools for VS Code suporta Windows, Linux e macOS.A corrida local U-SQL e o depurg local funcionam apenas no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Para MacOS e Linux:

- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
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
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   SAÍDA @departments      PARA "/Output/departments.csv" USANDO Outputters.Csv();

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

Uma forma mais conveniente de ativar o comando **ADL: Register Assembly** é clicar à direita no ficheiro .dll no File Explorer.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Para se registar através do comando ADL: Register Assembly (Advanced)

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: Register Assembly (Avançado)**.
3. Especifique o caminho de montagem local.
4. O ficheiro JSON é apresentado. Reveja e edite as dependências de montagem e os parâmetros de recursos, se necessário. As instruções são apresentadas na janela **de saída.** Para proceder ao registo de montagem, guarde (Ctrl+S) o ficheiro JSON.

   ![Ficheiro JSON com dependências de montagem e parâmetros de recursos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- A Azure Data Lake Tools autodetects se o DLL tem alguma dependência de montagem. As dependências são exibidas no ficheiro JSON depois de detetadas.
>- Pode carregar os seus recursos DLL (por exemplo, .txt, .png e .csv) como parte do registo de montagem.

Outra forma de acionar o comando **ADL: Register Assembly (Advanced)** é clicar à direita no ficheiro .dll no File Explorer.

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

Pode criar um script de extração para ficheiros .csv, .tsv e .txt utilizando o comando **ADL: Criar SCRIPT EXTRACT** ou a partir do explorador do Lago de Dados Azure.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Para criar um script de extração usando um comando

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e **introduza ADL: Criar script EXTRACT**.
2. Especifique o caminho completo para um ficheiro de armazenamento Azure e selecione a tecla 'Entrar'.
3. Selecione uma conta.
4. Para um ficheiro .txt, selecione um delimiter para extrair o ficheiro.

![Processo para criar um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base nas suas entradas. Para um script que não consiga detetar as colunas, escolha uma entre as duas opções. Caso contrário, apenas um guião será gerado.

![Resultado da criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Para criar um script de extração do explorador

Outra forma de criar o script de extração é através do menu de clique à direita (atalho) no ficheiro .csv, .tsv ou .txt na Azure Data Lake Store ou no armazenamento Azure.

![Comando "Criar SCRIPT EXTRACT" a partir do menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integre com a Azure Data Lake Analytics através de um comando

Pode aceder aos recursos do Azure Data Lake Analytics para listar contas, aceder a metadados e ver trabalhos de análise.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Para listar as contas Azure Data Lake Analytics sob a sua assinatura Azure

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Inserir **ADL: Contas de lista**. As contas aparecem no painel **de saída.**

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Para aceder aos metadados Azure Data Lake Analytics

1. Selecione Ctrl+Shift+P e, em seguida, introduza **ADL: Tabelas de listas**.
2. Selecione uma das contas Data Lake Analytics.
3. Selecione uma das bases de dados data lake analytics.
4. Selecione um dos esquemas. Pode ver a lista de mesas.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Para ver empregos do Azure Data Lake Analytics

1. Abra a paleta de comando (Ctrl+Shift+P) e selecione **ADL: Show Jobs**.
2. Selecione um Data Lake Analytics ou uma conta local.
3. Aguarde que a lista de emprego apareça para a conta.
4. Selecione um trabalho na lista de trabalho. Data Lake Tools abre a vista de trabalho no painel direito e exibe algumas informações na saída do Código VS.

    ![Lista de emprego](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integre com a Azure Data Lake Store através de um comando

Pode utilizar comandos relacionados com a Azure Data Lake Store para:

- [Percorra os recursos da Azure Data Lake Store](#list-the-storage-path)
- [Pré-visualizar o ficheiro Azure Data Lake Store](#preview-the-storage-file)
- Faça o upload do ficheiro diretamente para a Azure Data Lake Store em VS Code
- Faça o download do ficheiro diretamente da Azure Data Lake Store em VS Code

### <a name="list-the-storage-path"></a>Listar o caminho de armazenamento

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Para listar o caminho de armazenamento através da paleta de comando

1. Clique com o botão direito no editor de scripts e selecione **ADL: Caminho da lista**.
2. Escolha a pasta da lista ou selecione **Insira um caminho** ou **navegue pelo caminho raiz**. (Estamos usando **Enter um caminho** como um exemplo.)
3. Selecione a sua conta Data Lake Analytics.
4. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /output/).  

A paleta de comando lista as informações do caminho com base nas suas entradas.

![Resultados do caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma forma mais conveniente de listar o caminho relativo é através do menu de atalho.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Para listar o caminho de armazenamento através do menu de atalho

Clique com o botão direito na cadeia do caminho e selecione **Caminho da Lista**.

!["Caminho da Lista" no menu do atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Pré-visualizar o ficheiro de armazenamento

1. Clique com o botão direito no editor de scripts e selecione **ADL: Preview File**.
2. Selecione a sua conta Data Lake Analytics.
3. Introduza um percurso de arquivo de armazenamento Azure (por exemplo, /output/SearchLog.txt).

O ficheiro abre no Código VS.

![Passos e resultado para pré-visualização do ficheiro de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra forma de pré-visualizar o ficheiro é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor do script.

### <a name="upload-a-file-or-folder"></a>Faça upload de um ficheiro ou pasta

1. Clique com o botão direito no editor de scripts e selecione **Upload File** ou **Upload Folder**.
2. Escolha um ficheiro ou vários ficheiros se selecionou o **Upload File**ou escolher toda a pasta se selecionou **a Pasta do Upload**. Em seguida, selecione **Carregar**.
3. Escolha a pasta de armazenamento na lista ou selecione **Insira um caminho** ou **navegue pelo caminho raiz**. (Estamos usando **Enter um caminho** como um exemplo.)
4. Selecione a sua conta Data Lake Analytics.
5. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /output/).
6. **Selecione Escolha a Pasta Corrente** para especificar o seu destino de upload.

![Passos e resultado para o upload de um ficheiro ou pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Outra forma de enviar ficheiros para armazenamento é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor do script.

Pode [monitorizar o estado do upload](#check-storage-tasks-status).

### <a name="download-a-file"></a>Transferir um ficheiro

Pode descarregar um ficheiro utilizando o comando **ADL: Download File** ou **ADL: Download File (Advanced)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Para descarregar um ficheiro através do comando ADL: Download File (Advanced)

1. Clique com o botão direito no editor de script e, em seguida, selecione **Download File (Advanced)**.
2. O Código VS apresenta um ficheiro JSON. Pode introduzir caminhos de ficheiros e descarregar vários ficheiros ao mesmo tempo. As instruções são apresentadas na janela **de saída.** Para proceder ao download do ficheiro ou dos ficheiros, guarde (Ctrl+S) o ficheiro JSON.

    ![Ficheiro JSON com caminhos para download de ficheiros](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A janela **Saída** exibe o estado de descarregamento do ficheiro.

![Janela de saída com estado de descarregamento](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Pode [monitorizar o estado do download](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Para descarregar um ficheiro através do comando ADL: Download File

1. Clique com o botão direito no editor de scripts, selecione **Download File**e, em seguida, selecione a pasta de destino na caixa de diálogo **Select Folder.**

1. Escolha a pasta da lista ou selecione **Insira um caminho** ou **navegue pelo caminho raiz**. (Estamos usando **Enter um caminho** como um exemplo.)

1. Selecione a sua conta Data Lake Analytics.

1. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /output/) e, em seguida, escolha um ficheiro para descarregar.

![Passos e resultado para o descarregamento de um ficheiro](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Outra forma de descarregar ficheiros de armazenamento é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor do script.

Pode [monitorizar o estado do download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Verificar o estado das tarefas de armazenamento

O estado de upload e descarregamento aparece na barra de estado. Selecione a barra de estado e, em seguida, o estado aparece no **separador OUTPUT.**

![Barra de estado e detalhes de saída](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integre com a Azure Data Lake Analytics do explorador

Depois de iniciar sessão, todas as subscrições da sua conta Azure estão listadas no painel esquerdo, em **AZURE DATALAKE**.

![Explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadados navegação

Expanda a sua subscrição Azure. No nó **U-SQL Databases,** pode navegar pela base de dados U-SQL e ver pastas como **Schemas,** **Credenciais,** **Conjuntos,** **Tabelas**e **Índice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestão de metadados data Lake Analytics

Expandir **bases de dados U-SQL**. Pode criar uma base de dados, esquema, tabela, tipo de tabela, índice ou estatística clicando no nó correspondente e, em seguida, selecionando **Script para Criar** no menu de atalho. Na página de script aberta, edite o script de acordo com as suas necessidades. Em seguida, submeta o trabalho clicando-o à direita e selecionando **ADL: Submeter o trabalho**.

Depois de terminar de criar o item, clique no nó à direita e, em seguida, **selecione Refresh** para mostrar o item. Também pode eliminar o item clicando-o à direita e, em seguida, selecionando **Delete**.

![Comando "Script to Create" no menu de atalho no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Página de script para o novo item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics inscrição de montagem

Pode registar um conjunto na base de dados correspondente clicando no nó **de Conjuntos** e, em seguida, selecionando o **conjunto de Registos**.

![Comando "Registar montagem" no menu de atalho para o nó de Montagem](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integre com a Azure Data Lake Store do explorador

Navegue pela **Data Lake Store:**

- Pode clicar no nó da pasta e depois utilizar o **"Refresh**", **Eliminar**, **Carregar,** **Carregar Pasta,** **Copiar Caminho Relativo**e Copiar comandos Full **Path** no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Pode clicar no nó de ficheiro e depois utilizar o **pré-visualização**, **baixar**, **eliminar**, **criar script EXTRACT** (disponível apenas para ficheiros CSV, TSV e TXT), Copiar Caminho **Relativo**e Copiar comandos Do **Caminho Completo** no menu de atalho.

   ![Comandos de menu de atalho para um nó de ficheiro no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integre o armazenamento Azure Blob do explorador

Procurar no armazenamento blob:

- Pode clicar com o botão direito do nó do recipiente blob e, em seguida, utilizar os comandos **Refresh**, **Delete Blob**e Carregar os comandos **Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de recipiente blob sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Pode clicar com o botão direito no nó de pasta e, em seguida, utilizar os comandos **Refresh** and **Upload Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Pode clicar no nó de ficheiro e depois utilizar o **pré-visualização/Edição**, **Descarregar**, **Eliminar**, **Criar Script EXTRACT** (disponível apenas para ficheiros CSV, TSV e TXT), Copiar Caminho **Relativo**e Copiar comandos Do **Caminho Completo** no menu de atalho.

    ![Comandos de menu de atalho para um nó de ficheiro sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Abra o explorador do Lago de Dados no portal

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. **Insira o Open Web Azure Storage Explorer** ou clique com o direito para clicar num caminho relativo ou no caminho completo no editor de scripts e, em seguida, selecione Open Web **Azure Storage Explorer**.
3. Selecione uma conta Data Lake Analytics.

Data Lake Tools abre o caminho de armazenamento Azure no portal Azure. Pode encontrar o caminho e visualizar o ficheiro a partir da web.

## <a name="additional-features"></a>Características adicionais

Data Lake Tools for VS Code suporta as seguintes funcionalidades:

- **IntelliSense autocomplete**: As sugestões aparecem em janelas pop-up em torno de itens como palavras-chave, métodos e variáveis. Diferentes ícones representam diferentes tipos de objetos:

  - Tipo de dados scala
  - Tipo de dados complexo
  - UDTs embutidos
  - Coleção e classes .NET
  - Expressões C#
  - UDFs, UDOs e UDAAGs embutidos
  - Funções U-SQL
  - Funções de janela U-SQL

    ![Tipos de objetos IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense autocomplete em data lake analytics metadados**: Data Lake Tools descarrega a informação de metadados Data Lake Analytics localmente. A funcionalidade IntelliSense povoa automaticamente objetos dos metadados Data Lake Analytics. Estes objetos incluem a base de dados, esquema, tabela, vista, função de valor de tabela, procedimentos e conjuntos C#.

  ![Metadados intellisense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Marcador de erro IntelliSense**: Data Lake Tools sublinha erros de edição para U-SQL e C#.
- **Destaques de sintaxe**: Data Lake Tools usa cores para diferenciar itens como variáveis, palavras-chave, tipos de dados e funções.

    ![Sintaxe com várias cores](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Recomendamos que atualize para Azure Data Lake Tools para a versão 2.3.3000.4 ou posterior. As versões anteriores já não estão disponíveis para transferência e foram preteridas.  

## <a name="next-steps"></a>Passos seguintes

- [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics em CÓDIGO VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL corrida local e depurar local com Código de Estúdio Visual](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Começar com Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL utilizando ferramentas do Data Lake para Estúdio Visual](data-lake-analytics-data-lake-tools-get-started.md)