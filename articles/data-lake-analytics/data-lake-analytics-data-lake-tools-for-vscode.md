---
title: Utilizar Azure Data Lake Tools for Visual Studio Code
description: Aprenda a usar ferramentas de lago de dados Azure para o Código de Estúdio Visual para criar, testar e executar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60509345"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utilizar Azure Data Lake Tools for Visual Studio Code

Neste artigo, saiba como pode usar ferramentas de lago de dados Azure para código de estúdio visual (Código VS) para criar, testar e executar scripts U-SQL. A informação também é abordada no seguinte vídeo:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas do Lago De Dados Azure para o Código VS suportam Windows, Linux e macOS.A execução local u-SQL e o depurado local funcionam apenas no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Para MacOS e Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instale ferramentas de lago de dados azure

Depois de instalar os pré-requisitos, pode instalar ferramentas do Lago De Dados Azure para o Código VS.

**Para instalar ferramentas de lago de dados Azure**

1. Abra o Visual Studio Code.
2. **Selecione Extensões** no painel esquerdo. Introduza ferramentas de **lago de dados Azure** na caixa de pesquisa.
3. **Selecione Instalar** ao lado das **ferramentas do Lago**de Dados Azure . 

   ![Seleções para instalar ferramentas de lago de dados](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Após alguns segundos, o botão **instalar** muda para **recarregar**.
4. **Selecione Recarregar** para ativar a extensão de Ferramentas do Lago de **Dados Azure.**
5. Selecione **Janela de Recarga** para confirmar. Pode ver ferramentas de **lago de dados azure** no painel de **extensões.**

 
## <a name="activate-azure-data-lake-tools"></a>Ativar ferramentas de lago de dados azure
Crie um ficheiro .usql ou abra um ficheiro .usql existente para ativar a extensão. 


## <a name="work-with-u-sql"></a>Trabalhar com a U-SQL

Para trabalhar com a U-SQL, precisa de abrir um ficheiro U-SQL ou uma pasta.

**Para abrir o script da amostra**

Abra a paleta de comando (Ctrl+Shift+P) e introduza **ADL: Open Sample Script**. Abre outra instância desta amostra. Também pode editar, configurar e submeter um guião neste caso.

**Para abrir uma pasta para o seu projeto U-SQL**

1. A partir do Código do Estúdio Visual, selecione o menu **'Ficheiro'** e, em seguida, **selecione Open Folder**.
2. Especifique uma pasta e, em seguida, selecione **selecione a pasta**.
3. Selecione o menu **'Ficheiro'** e, em seguida, selecione **New**. Um ficheiro Sem título-1 é adicionado ao projeto.
4. Introduza o seguinte código no ficheiro Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    O script cria um ficheiro departments.csv com alguns dados incluídos na pasta /saída.

5. Guarde o ficheiro como **myUSQL.usql** na pasta aberta.

**Para compilar um script U-SQL**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2. Introduza **ADL: Compile script**. Os resultados da compilação aparecem na janela **saída.** Também pode clicar num ficheiro de script e, em seguida, selecionar **ADL: Compile script** para compilar um trabalho U-SQL. O resultado da compilação aparece no painel **de saída.**
 
**Para submeter um script U-SQL**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2. Insira **ADL: Submeter o emprego**. Também pode clicar num ficheiro de script e, em seguida, selecionar **ADL: Submeter o Trabalho**. 

Depois de submeter um trabalho U-SQL, os registos de submissão aparecem na janela **de saída** no Código VS. A vista de trabalho aparece no painel certo. Se a submissão for bem sucedida, o URL de trabalho também aparece. Você pode abrir o URL de trabalho em um navegador web para rastrear o estado de trabalho em tempo real. 

No separador **Resumo** da vista de trabalho, pode ver os detalhes do trabalho. As principais funções incluem reenviar um script, duplicar um script e abrir no portal. No separador **DATA** da vista de trabalho, pode consultar os ficheiros de entrada, ficheiros de saída e ficheiros de recursos. Os ficheiros podem ser descarregados para o computador local.

![Separador resumo na vista do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Separador de dados na vista do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Para definir o contexto padrão**

Pode definir o contexto predefinido para aplicar esta definição a todos os ficheiros do script se não tiver definido parâmetros para ficheiros individualmente.

1. Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2. Introduza **ADL: Definir o contexto predefinido**. Ou clique no editor de scripts e selecione **ADL: Definir Contexto padrão**.
3. Escolha a conta, a base de dados e o esquema que deseja. A definição é guardada no ficheiro de configuração xxx_settings.json.

   ![Conta, base de dados e esquema definido como o contexto padrão](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Para definir parâmetros de script**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2. Introduza **ADL: Definir parâmetros de script**.
3. O ficheiro xxx_settings.json é aberto com as seguintes propriedades:

   - **conta**: Uma conta Azure Data Lake Analytics sob a sua subscrição Azure que é necessária para compilar e executar empregos U-SQL. Precisa de configurar a conta do computador antes de compilar e executar trabalhos u-SQL.
   - **base de**dados : Uma base de dados na sua conta. O padrão é **mestre.**
   - **schema**: Um esquema sob a sua base de dados. O padrão é **dbo**.
   - **definições opcionais**:
        - **prioridade**: O intervalo prioritário é de 1 a 1000, com 1 como prioridade máxima. O valor predefinido é **1000**.
        - **grauOfParallelismo**: A gama de paralelismos é de 1 a 150. O valor predefinido é o paralelismo máximo permitido na sua conta Azure Data Lake Analytics. 

   ![Conteúdo do ficheiro JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Depois de guardar a configuração, a conta, a base de dados e as informações de esquema aparecem na barra de estado no canto inferior esquerdo do ficheiro .usql correspondente se não tiver um contexto predefinido configurado.

**Para definir Git ignorar**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2. Introduza **ADL: Definir Git Ignore**.

   - Se não tiver um ficheiro **.gitIgnore** na sua pasta de trabalho vs Code, é criado um ficheiro denominado **.gitIgnore** na sua pasta. Quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) são adicionados no ficheiro por padrão. Pode fazer mais atualizações se necessário.
   - Se já tiver um ficheiro **.gitIgnore** na sua pasta de trabalho vs Código, a ferramenta adiciona quatro itens **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache,** **obj**) no seu ficheiro **.gitIgnore** se os quatro itens não estiverem incluídos no ficheiro.

   ![Itens no ficheiro .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Trabalhe com ficheiros code-behind: C Sharp, Python e R

A Azure Data Lake Tools suporta vários códigos personalizados. Para obter instruções, consulte [Desenvolviu U-SQL com Python, R e C Sharp para Azure Data Lake Analytics em Código VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Trabalhar com assembleias

Para obter informações sobre o desenvolvimento de conjuntos, consulte [Desenvolver conjuntos U-SQL para trabalhos de Análise](data-lake-analytics-u-sql-develop-assemblies.md)de Lagos de Dados Azure .

Pode utilizar ferramentas data lake para registar conjuntos de códigos personalizados no catálogo data Lake Analytics.

**Para registar uma assembleia**

Pode registar o conjunto através do **Comando ADL: Registo** ou **ADL: Montagem de Registo (Avançado).**

**Para se registar através da ADL: Comando de Montagem de Registo**
1.  Selecione Ctrl+Shift+P para abrir a paleta de comando.
2.  Insira **ADL: Registo de Montagem**. 
3.  Especifique o caminho de montagem local. 
4.  Selecione uma conta Data Lake Analytics.
5.  Selecione uma base de dados.

O portal é aberto num browser e exibe o processo de registo de montagem.  

Uma forma mais conveniente de acionar o **Comando ADL: Registar** o comando de montagem é clicar no ficheiro .dll no File Explorer. 

**Para se registar através do comando ADL: Register Assembly (Advanced)**
1.  Selecione Ctrl+Shift+P para abrir a paleta de comando.
2.  Insira **ADL: Registo de Montagem (Avançada)**. 
3.  Especifique o caminho de montagem local. 
4.  O ficheiro JSON é apresentado. Reveja e edite as dependências de montagem e os parâmetros de recursos, se necessário. As instruções são apresentadas na janela **saída.** Para proceder ao registo de montagem, guarde (CTRL+S) o ficheiro JSON.

    ![Arquivo JSON com dependências de montagem e parâmetros de recursos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- A Azure Data Lake Tools deteta automaticamente se o DLL tem alguma dependência de montagem. As dependências são exibidas no ficheiro JSON depois de detetadas. 
>- Pode fazer o upload dos seus recursos DLL (por exemplo, .txt, .png e .csv) como parte do registo de montagem. 

Outra forma de acionar o comando **ADL: Register Assembly (Advanced)** é clicar no ficheiro .dll no File Explorer. 

O seguinte código U-SQL demonstra como convocar um conjunto. Na amostra, o nome da montagem é *teste*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Utilize a execução local U-SQL e o depurado local para utilizadores do Windows
A execução local u-SQL testa os seus dados locais e valida o seu script localmente antes de o seu código ser publicado no Data Lake Analytics. Pode utilizar a funcionalidade de depuração local para completar as seguintes tarefas antes de o seu código ser submetido ao Data Lake Analytics: 
- Depura o teu código C# atrás. 
- Atravesse o código. 
- Valide o seu guião localmente.

A funcionalidade local de execução e dedepuração local funciona apenas em ambientes Windows, e não é suportada em sistemas operativos baseados em macOS e Linux.

Para obter instruções sobre a execução local e o depurado local, consulte [a execução local U-SQL e o depurado local com código](data-lake-tools-for-vscode-local-run-and-debug.md)de estúdio visual .


## <a name="connect-to-azure"></a>Ligar ao Azure

Antes de poder compilar e executar scripts U-SQL no Data Lake Analytics, tem de se ligar à sua conta Azure.

<b id="sign-in-by-command">Para ligar ao Azure utilizando um comando</b>

1.  Selecione Ctrl+Shift+P para abrir a paleta de comando. 
2.  Introduza **ADL: Iniciar sessão**. A informação de login aparece no direito inferior.

    ![Entrando no comando de login](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Notificação sobre inscrição e autenticação](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Selecione **Copy & Abrir** para abrir a página web de [login](https://aka.ms/devicelogin). Colá-lo na caixa e, em seguida, **selecione Continuar**.

    ![Página web de login](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Siga as instruções para iniciar sessão na página web. Quando estiver ligado, o nome da conta Azure aparece na barra de estado no canto inferior esquerdo da janela do Código VS. 

> [!NOTE] 
>- Data Lake Tools assina-o automaticamente na próxima vez se não assinar.
>- Se a sua conta tiver dois fatores ativados, recomendamos que utilize a autenticação do telefone em vez de utilizar um PIN.


Para assinar, insira o comando **ADL: Logout**.

**Para ligar a Azure do explorador**

Expandir **O AZURE DATALAKE,** selecione **Iniciar sessão em Azure,** e depois seguir o passo 3 e o passo 4 de [Ligar-se ao Azure utilizando um comando](#sign-in-by-command).

![Seleção "Iniciar sessão no Azure" no explorador](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Não pode assinar pelo explorador. Para assinar, consulte [Ligar-se ao Azure utilizando um comando](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Criar um roteiro de extração 
Pode criar um script de extração para ficheiros .csv, .tsv e .txt utilizando o comando **ADL: Criar** script extrato ou do explorador do Lago de Dados Azure.

**Para criar um script de extração usando um comando**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando e introduza **ADL: Criar script Extrato**.
2. Especifique o caminho completo para um ficheiro De armazenamento Azure e selecione a tecla Enter.
3. Selecione uma conta.
4. Para um ficheiro .txt, selecione um delimitador para extrair o ficheiro. 

![Processo para criar um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base nas suas entradas. Para um script que não consegue detetar as colunas, escolha uma entre as duas opções. Caso contrário, apenas um guião será gerado.

![Resultado da criação de um roteiro de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Para criar um script de extração do explorador**

Outra forma de criar o script de extração é através do menu de clique direito (atalho) no ficheiro .csv, .tsv ou .txt na Azure Data Lake Store ou no armazenamento Azure Blob.

![Comando "Criar script extra" a partir do menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrar com azure Data Lake Analytics através de um comando

Pode aceder aos recursos do Azure Data Lake Analytics para listar contas, aceder a metadados e ver empregos de análise. 

**Para listar as contas Azure Data Lake Analytics sob a sua subscrição Azure**

1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Insira **ADL: Lista de Contas**. As contas aparecem no painel **de saída.**

**Para aceder aos metadados do Azure Data Lake Analytics**

1.  Selecione Ctrl+Shift+P e, em seguida, introduza **ADL: Listas**.
2.  Selecione uma das contas data lake analytics.
3.  Selecione uma das bases de dados data Lake Analytics.
4.  Selecione um dos esquemas. Pode ver a lista de mesas.

**Para ver os trabalhos do Azure Data Lake Analytics**
1.  Abra a paleta de comando (Ctrl+Shift+P) e selecione **ADL: Show Jobs**. 
2.  Selecione uma conta Data Lake Analytics ou local. 
3.  Espere que a lista de trabalho apareça para a conta.
4.  Selecione um trabalho na lista de trabalho. Data Lake Tools abre a vista de trabalho no painel certo e exibe algumas informações na saída do Código VS.

    ![Lista de trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrar com a Azure Data Lake Store através de um comando

Pode utilizar comandos relacionados com a Loja de Dados Do Lago Azur para:
 - [Navegue pelos recursos da Azure Data Lake Store](#list-the-storage-path) 
 - [Pré-visualizar o ficheiro Azure Data Lake Store](#preview-the-storage-file) 
 - Faça upload do ficheiro diretamente para a Azure Data Lake Store em Código VS
 - Descarregue o ficheiro diretamente da Azure Data Lake Store em Código VS

### <a name="list-the-storage-path"></a>Enumerar o caminho de armazenamento 

**Para listar o caminho de armazenamento através da paleta de comando**

1. Clique no editor de scripts e selecione **ADL: List Path**.
2. Escolha a pasta na lista ou selecione **Entrar num caminho** ou navegar a partir do caminho **raiz**. (Estamos a usar **o Caminho de Entrar** como exemplo.) 
3. Selecione a sua conta Data Lake Analytics.
4. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /saída/).  

A paleta de comando lista a informação do caminho com base nas suas entradas.

![Resultados do caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma forma mais conveniente de listar o caminho relativo é através do menu de atalho.

**Para listar o caminho de armazenamento através do menu de atalho**

Clique na corda do caminho e selecione Caminho da **Lista**.

!["Caminho da Lista" no menu do atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Pré-visualizar o ficheiro de armazenamento

1. Clique no editor de scripts e selecione **ADL: Preview File**.
2. Selecione a sua conta Data Lake Analytics. 
3. Introduza um caminho de ficheiro de armazenamento Azure (por exemplo, /output/SearchLog.txt). 

O ficheiro abre no Código VS.

![Passos e resultado para pré-visualizar o ficheiro de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra forma de pré-visualizar o ficheiro é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor de scripts. 

### <a name="upload-a-file-or-folder"></a>Carregar um ficheiro ou pasta

1. Clique no editor de scripts e selecione **Upload File** ou **Upload Folder**.
2. Escolha um ficheiro ou vários ficheiros se selecionou o **Upload File,** ou escolha toda a pasta se selecionasse **a Pasta de Upload**. Em seguida, selecione **Carregar**. 
3. Escolha a pasta de armazenamento na lista ou selecione **Entrar num caminho** ou navegar a partir do caminho **raiz**. (Estamos a usar **o Caminho de Entrar** como exemplo.) 
4. Selecione a sua conta Data Lake Analytics. 
5. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /saída/). 
6. **Selecione Escolha a Pasta Current** para especificar o seu destino de upload.

![Passos e resultado para o upload de um ficheiro ou pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Outra forma de fazer o upload de ficheiros para o armazenamento é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor de scripts.

Pode [monitorizar o estado de upload](#check-storage-tasks-status).


### <a name="download-a-file"></a>Transferir um ficheiro 
Pode descarregar um ficheiro utilizando o comando **ADL: Download File** ou **ADL: Download File (Advanced)**.

**Para descarregar um ficheiro através do comando ADL: Download File (Advanced)**
1. Clique no editor de scripts e, em seguida, selecione **Download File (Avançado)**.
2. O Código VS apresenta um ficheiro JSON. Pode introduzir os ficheiros e descarregar vários ficheiros ao mesmo tempo. As instruções são apresentadas na janela **saída.** Para proceder ao download do ficheiro ou ficheiros, guarde (Ctrl+S) o ficheiro JSON.

    ![Arquivo JSON com caminhos para download de ficheiros](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A janela **Output** apresenta o estado de descarregamento do ficheiro.

![Janela de saída com estado de descarregamento](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Pode [monitorizar o estado de descarregamento](#check-storage-tasks-status).

**Para descarregar um ficheiro através do Comando ADL: Download File**

1. Clique no editor de scripts, selecione **Download File**e, em seguida, selecione a pasta de destino a partir da caixa de diálogo **Select Folder.**
2. Escolha a pasta na lista ou selecione **Entrar num caminho** ou navegar a partir do caminho **raiz**. (Estamos a usar **o Caminho de Entrar** como exemplo.) 
3. Selecione a sua conta Data Lake Analytics. 
4. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, /output/), e, em seguida, escolha um ficheiro para descarregar.

![Passos e resultado para descarregar um ficheiro](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Outra forma de descarregar ficheiros de armazenamento é através do menu de atalho no caminho completo do ficheiro ou do caminho relativo do ficheiro no editor de scripts.

Pode [monitorizar o estado de descarregamento](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Verifique o estado das tarefas de armazenamento
O estado de upload e descarregamento aparece na barra de estado. Selecione a barra de estado e, em seguida, o estado aparece no separador **OUTPUT.**

![Barra de estado e detalhes de saída](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integre com o Azure Data Lake Analytics do explorador

Depois de iniciar sessão, todas as subscrições da sua conta Azure estão listadas no painel esquerdo, em **AZURE DATALAKE**. 

![Explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navegação de metadados data Lake Analytics

Expanda a sua subscrição Azure. Sob o nó de bases de **dados U-SQL,** pode navegar pela sua base de dados U-SQL e ver pastas como **Schemas,** **Credenciais,** **Assembles,** **Tabelas**e **Índice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestão de entidades de metadados data Lake Analytics

Expandir bases de **dados U-SQL**. Pode criar uma base de dados, esquema, tabela, tipo de tabela, índice ou estatística clicando no nó correspondente e, em seguida, selecionando **script para criar** no menu de atalho. Na página de script aberta, edite o script de acordo com as suas necessidades. Em seguida, submeta o trabalho clicando-o à direita e selecionando **ADL: Submeter o trabalho**. 

Depois de terminar de criar o item, clique no nó direito e, em seguida, selecione **Refresh** para mostrar o item. Também pode eliminar o item clicando-o à direita e, em seguida, selecionando **Apagar**.

![Comando "Script to Create" no menu de atalho no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Página de script para o novo item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registo de montagem de Data Lake Analytics

Pode registar um conjunto na base de dados correspondente clicando no nó **assembléia** e, em seguida, selecionando o **conjunto Do Registo**.

![Comando de "montagem de registo" no menu de atalho para o nó das Assembléias](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrar com a Azure Data Lake Store do explorador

Navegue na **Data Lake Store:**

- Pode clicar no nó da pasta e, em seguida, utilizar os comandos **Refresh**, **Delete**, **Upload,** **Upload,** **Copy Relative Path**e Copy Full **Path** no menu de atalho.

   ![Menu de atalho comanda um nó de pasta no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Pode clicar no nó de ficheiros e, em seguida, utilizar os comandos **Preview**, **Download**, **Delete**, **Create EXTRACT** Script (disponível apenas para ficheiros CSV, TSV e TXT), Copiar **Caminho Relativo**e Copiar comandos Full **Path** no menu de atalho.

   ![Menu de atalho comanda um nó de arquivo no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrar com o armazenamento Azure Blob do explorador

Navegue no armazenamento blob:

- Pode clicar no nó do recipiente blob e, em seguida, utilizar o **Refresco**, **Eliminar recipiente blob**e carregar comandos **Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de recipiente blob sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Pode clicar no nó da pasta e, em seguida, utilizar os comandos **Refresh** e **Upload Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Pode clicar no nó de ficheiros e, em seguida, utilizar os comandos **Preview/Edit**, **Download**, **Delete**, **Create EXTRACT** Script (disponível apenas para ficheiros CSV, TSV e TXT), Copiar **Caminho Relativo**e Copiar comandos Full **Path** no menu de atalho.

    ![Comandos de menu de atalho para um nó de arquivo sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Abra o explorador do Lago de Dados no portal
1. Selecione Ctrl+Shift+P para abrir a paleta de comando.
2. Introduza o **Open Web Azure Storage Explorer** ou clique num caminho relativo ou no caminho completo no editor de scripts e, em seguida, selecione Open Web **Azure Storage Explorer**.
3. Selecione uma conta Data Lake Analytics.

Data Lake Tools abre o caminho de armazenamento Azure no portal Azure. Pode encontrar o caminho e pré-visualizar o ficheiro a partir da web.

## <a name="additional-features"></a>Características adicionais

Data Lake Tools for VS Code suporta as seguintes funcionalidades:

-   **IntelliSense autocomplete**: Sugestões aparecem em janelas pop-up em torno de itens como palavras-chave, métodos e variáveis. Ícones diferentes representam diferentes tipos de objetos:

    - Tipo de dados scala
    - Tipo de dados complexos
    - UDTs embutidos
    - .NET coleção e classes
    - Expressões C#
    - UDFs, UDOs e UDAAGs embutidos 
    - Funções U-SQL
    - Funções de janela U-SQL
 
    ![Tipos de objetos IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **IntelliSense autocompleto em data lake analytics metadados**: Data Lake Tools descarrega a informação de metadados data Lake Analytics localmente. A funcionalidade IntelliSense povoa automaticamente objetos dos metadados data Lake Analytics. Estes objetos incluem a base de dados, esquema, tabela, vista, função, procedimentos e conjuntos C# valorizados pela mesa.
 
    ![Metadados IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Marcador de erro IntelliSense**: Data Lake Tools sublinha erros de edição para U-SQL e C#. 
-   **Destaques de Sintaxe**: Data Lake Tools usa cores para diferenciar itens como variáveis, palavras-chave, tipos de dados e funções. 

    ![Sintaxe com várias cores](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Recomendamos que faça o upgrade para O Azure Data Lake Tools para visual studio versão 2.3.3000.4 ou posterior. As versões anteriores já não estão disponíveis para transferência e foram preteridas.  
   
## <a name="next-steps"></a>Passos seguintes
- [Desenvolva U-SQL com Python, R e C Sharp para Azure Data Lake Analytics em código VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Corrida local U-SQL e depuração local com Código de Estúdio Visual](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Começar com Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL utilizando ferramentas do Lago de Dados para Estúdio Visual](data-lake-analytics-data-lake-tools-get-started.md)
