---
title: Utilizar do Azure Data Lake Tools para Visual Studio Code
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio Code para criar, testar e executar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531611"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Utilizar do Azure Data Lake Tools para Visual Studio Code

Neste artigo, saiba como pode utilizar o Azure Data Lake Tools para Visual Studio Code (código de VS) para criar, testar e executar scripts U-SQL. As informações também são abordadas no vídeo seguinte:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Pré-requisitos

O Azure Data Lake Tools para o VS Code suporta o Windows, Linux e macOS. Depuração de execução e local local no U-SQL funciona apenas no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Para MacOS e Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalar as ferramentas do Azure Data Lake

Depois de instalar os pré-requisitos, pode instalar o Azure Data Lake Tools para o VS Code.

**Para instalar as ferramentas do Azure Data Lake**

1. Abra o Visual Studio Code.
2. Selecione **extensões** no painel esquerdo. Introduza **ferramentas do Azure Data Lake** na caixa de pesquisa.
3. Selecione **instale** junto a **do Azure Data Lake Tools**. 

   ![Seleções para instalar as ferramentas do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Após alguns segundos, o **instale** botão muda para **recarregar**.
4. Selecione **recarregar** para ativar a **do Azure Data Lake Tools** extensão.
5. Selecione **janela de recarregamento** para confirmar. Pode ver **ferramentas do Azure Data Lake** no **extensões** painel.

 
## <a name="activate-azure-data-lake-tools"></a>Ativar as ferramentas do Azure Data Lake
Crie um ficheiro de .usql ou abrir um arquivo de .usql existente para ativar a extensão. 


## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

Para trabalhar com o U-SQL, precisa abrir um ficheiro de U-SQL ou uma pasta.

**Para abrir o script de exemplo**

Abra a paleta de comandos (Ctrl + Shift + P) e introduza **ADL: Abra o Script de exemplo**. Ele abre-se outra instância deste exemplo. Também pode editar, configurar e submeter um script nesta instância.

**Para abrir uma pasta para o seu projeto de U-SQL**

1. A partir do Visual Studio Code, selecione o **arquivo** e, em seguida, selecione **Abrir pasta**.
2. Especifique uma pasta e, em seguida, selecione **selecionar pasta**.
3. Selecione o **arquivo** e, em seguida, selecione **New**. Um ficheiro sem título-1 é adicionado ao projeto.
4. Introduza o seguinte código no ficheiro sem título-1:

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

    O script cria um ficheiro de departments.csv com alguns dados incluídos na pasta /output.

5. Guarde o ficheiro como **myUSQL.usql** na pasta aberta.

**Para compilar um script de U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Introduza **ADL: Compilar o Script**. Os resultados de compilação são apresentados no **saída** janela. Pode também com o botão direito um ficheiro de script e, em seguida, selecione **ADL: Compilar o Script** para compilar uma tarefa U-SQL. O resultado de compilação aparece no **saída** painel.
 
**Para submeter um script de U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Introduza **ADL: Submeter a tarefa**. Pode também com o botão direito um ficheiro de script e, em seguida, selecione **ADL: Submeter a tarefa**. 

Depois de submeter uma tarefa U-SQL, os registos de submissão constar da **saída** janela no VS Code. A vista de tarefas é apresentado no painel da direita. Se a submissão for bem sucedida, o URL da tarefa é demasiado apresentada. É possível abrir o URL da tarefa num navegador da web para controlar o estado da tarefa em tempo real. 

Na vista de tarefa **resumo** guia, pode ver os detalhes da tarefa. As principais funções incluem volte a submeter um script, duplicar um script e abra no portal. Na vista de tarefa **dados** separador, pode consultar os ficheiros de entrada, ficheiros de saída e arquivos de recursos. Ficheiros podem ser transferidos para o computador local.

![Separador Resumo na vista de tarefa](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Separador de dados na vista de tarefa](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Para definir o contexto predefinido**

Pode definir o contexto predefinido para aplicar esta definição a todos os ficheiros de script, se não tiver definido os parâmetros para os ficheiros individualmente.

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Introduza **ADL: Definir o contexto predefinido**. Ou o editor de scripts com o botão direito e selecione **ADL: Definir o contexto predefinido**.
3. Escolha a conta, base de dados e de esquema que pretende. A definição é guardada o ficheiro de configuração xxx_settings.json.

   ![Conta, base de dados e esquema definido como o contexto predefinido](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Para definir parâmetros de script**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Introduza **ADL: Definir parâmetros de Script**.
3. O arquivo de xxx_settings.json é aberto com as seguintes propriedades:

   - **account**: Uma conta do Azure Data Lake Analytics com a sua subscrição do Azure que é necessário para compilar e executar tarefas U-SQL. Tem de configurar a conta de computador antes de compilar e executar tarefas de U-SQL.
   - **database**: Uma base de dados na sua conta. A predefinição é **mestre**.
   - **schema**: Um esquema em sua base de dados. A predefinição é **dbo**.
   - **optionalSettings**:
        - **Prioridade**: O intervalo de prioridade é de 1 a 1000, com 1 como a prioridade mais alta. O valor predefinido é **1000**.
        - **degreeOfParallelism**: O intervalo de paralelismo é entre 1 e 150. O valor predefinido é o paralelismo máximo permitido na sua conta do Azure Data Lake Analytics. 

   ![Conteúdo do ficheiro JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Depois de guardar a configuração, a conta, base de dados e informações de esquema aparecem na barra de estado no canto inferior esquerdo do correspondente arquivo .usql se não tiver um contexto de predefinido configurado.

**Para definir o Git ignorar**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Introduza **ADL: Conjunto Git Ignore**.

   - Se não tiver uma **. gitignore** ficheiro na sua pasta de trabalho do VS Code, um arquivo chamado **. gitignore** é criado na sua pasta. Quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) são adicionados ao arquivo por predefinição. Se for necessário, pode fazer mais atualizações.
   - Se já tiver um **. gitignore** ficheiro na sua pasta de trabalho do VS Code, a ferramenta adiciona quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) no seu **. gitignore** ficheiro se os quatro itens não foram incluídos no ficheiro.

   ![Itens no ficheiro. gitignore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Trabalhar com arquivos code-behind: C Sharp, Python e R

Ferramentas do Azure Data Lake suporta vários códigos personalizados. Para obter instruções, consulte [desenvolver U-SQL com o Python, R e C-Sharp para o Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Trabalhar com assemblies

Para obter informações sobre o desenvolvimento de assemblies, consulte [assemblies de desenvolver U-SQL para tarefas de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Pode utilizar ferramentas do Data Lake para registar os assemblies de código personalizado no catálogo do Data Lake Analytics.

**Para registar uma assemblagem**

Pode registrar o assembly por meio do **ADL: Registe-se a assemblagem** ou **ADL: Registrar o Assembly (avançado)** comando.

**Para se registar através do ADL: Registar o comando de assemblagem**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2.  Introduza **ADL: Registar o Assembly**. 
3.  Especifique o caminho local do assembly. 
4.  Selecione uma conta do Data Lake Analytics.
5.  Selecione uma base de dados.

O portal é aberto num browser e apresenta o processo de registo de assemblagem.  

Uma forma mais conveniente para acionar o **ADL: Registar o Assembly** comando é para o ficheiro. dll no Explorador de ficheiros com o botão direito. 

**Para se registar através do ADL: Registar o comando de assemblagem (avançado)**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2.  Introduza **ADL: Registar o Assembly (avançado)**. 
3.  Especifique o caminho local do assembly. 
4.  O ficheiro JSON é apresentado. Rever e editar os parâmetros de recurso e as dependências do assembly, se necessário. As instruções são apresentadas no **saída** janela. Para avançar para o registo de assemblagem, guarde, por exemplo, o ficheiro JSON (Ctrl + S).

    ![Ficheiro JSON com as dependências do assembly e parâmetros do recurso](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- O Azure Data Lake Tools autodetects se a DLL tem dependências do assembly. As dependências são apresentadas no ficheiro JSON, depois que estiverem a ser detetados. 
>- Pode carregar os recursos DLL (por exemplo,. txt,. PNG e. csv) como parte do registo de assemblagem. 

Outra forma para acionar o **ADL: Registrar o Assembly (avançado)** comando é para o ficheiro. dll no Explorador de ficheiros com o botão direito. 

O código de U-SQL seguinte demonstra como chamar um assembly. No exemplo, é o nome do assembly *testar*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Utilizar a execução local do U-SQL e depuração local para os utilizadores do Windows
Execução local do U-SQL testa os seus dados locais e valida o script localmente antes de seu código é publicado ao Data Lake Analytics. Pode utilizar a funcionalidade de depuração local para concluir as seguintes tarefas para que seu código é submetido para o Data Lake Analytics: 
- Depure o c# code-behind. 
- Percorrer o código. 
- Valide o script localmente.

A funcionalidade de depuração local de execução e local só funciona em ambientes Windows e não é suportada no macOS e sistemas operacionais baseados em Linux.

Para obter instruções sobre como executar local e depuração local, consulte [execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Ligar ao Azure

Antes de poder compilar e executar scripts U-SQL no Data Lake Analytics, tem de ligar à sua conta do Azure.

<b id="sign-in-by-command">Para ligar ao Azure com um comando</b>

1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2.  Introduza **ADL: Login**. As informações de início de sessão é apresentada na parte inferior direita.

    ![Introduzir o comando de início de sessão](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Notificação sobre o início de sessão e autenticação](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Selecione **copie e abra** para abrir o [página Web de início de sessão](https://aka.ms/devicelogin). Cole o código na caixa e, em seguida, selecione **continuar**.

    ![Página Web de início de sessão](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Siga as instruções para iniciar sessão a partir da página da Web. Quando está conectado, o nome da sua conta do Azure é apresentada na barra de estado no canto inferior esquerdo da janela do VS Code. 

> [!NOTE] 
>- Ferramentas do Data Lake automaticamente iniciada na próxima vez que se não terminar.
>- Se a sua conta tem dois fatores ativadas, recomendamos que utilize autenticação do telefone, em vez de utilizar um PIN.


Para terminar sessão, introduza o comando **ADL: Fim de sessão**.

**Para ligar ao Azure a partir do Explorador**

Expanda **AZURE DATALAKE**, selecione **iniciar sessão no Azure**e, em seguida, siga o passo 3 e o passo 4 do [para ligar ao Azure com um comando](#sign-in-by-command).

![Seleção de "Iniciar sessão no Azure" no Explorador](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Não pode terminar sessão do explorer. Para terminar sessão, veja [para ligar ao Azure com um comando](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Criar um script de extração 
Pode criar um script de extração de arquivos. txt,. tsv e. csv, utilizando o comando **ADL: Criar Script de EXTRAIR** ou a partir do Explorador do Azure Data Lake.

**Para criar um script de extração com um comando**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e introduza **ADL: Criar Script de EXTRAÇÃO**.
2. Especifique o caminho completo para um ficheiro de armazenamento do Azure e selecione a tecla Enter.
3. Selecione uma conta.
4. Para um ficheiro. txt, selecione um delimitador para extrair o arquivo. 

![Processo de criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base em suas entradas. Para obter um script que não é possível detetar as colunas, escolha uma das duas opções. Caso contrário, apenas um script será gerado.

![Resultado da criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Para criar um script de extração a partir do explorer**

Outra forma de criar o script de extração é através do menu de contexto (atalho) no. csv,. tsv ou arquivo. txt no armazenamento do Azure Data Lake Store ou de Blobs do Azure.

![Comando "Criar Script de EXTRAÇÃO" no menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrar com o Azure Data Lake Analytics, através de um comando

Pode aceder a recursos do Azure Data Lake Analytics para contas de lista, metadados de acesso e ver tarefas de análise. 

**Para listar as contas de Azure Data Lake Analytics com a sua subscrição do Azure**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Introduza **ADL: Listar contas**. As contas de constar da **saída** painel.

**Para aceder aos metadados do Azure Data Lake Analytics**

1.  Selecione Ctrl + Shift + P e, em seguida, introduza **ADL: Listar as tabelas**.
2.  Selecione uma das contas do Data Lake Analytics.
3.  Selecione uma das bases de dados do Data Lake Analytics.
4.  Selecione um dos esquemas. Pode ver a lista de tabelas.

**Para ver tarefas do Azure Data Lake Analytics**
1.  Abra a paleta de comandos (Ctrl + Shift + P) e selecione **ADL: Mostrar tarefas**. 
2.  Selecione uma conta local ou o Data Lake Analytics. 
3.  Aguarde que a lista de tarefas seja exibido para a conta.
4.  Selecione uma tarefa na lista de tarefas. Ferramentas do Data Lake abre a vista de tarefas no painel da direita e apresenta algumas informações no resultado do VS Code.

    ![Lista de tarefas](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrar com o Azure Data Lake Store através de um comando

Pode utilizar os comandos relacionados com o Azure Data Lake Store para:
 - [Navegue pelos recursos do Azure Data Lake Store](#list-the-storage-path) 
 - [Pré-visualização do ficheiro do Azure Data Lake Store](#preview-the-storage-file) 
 - Carregar o ficheiro diretamente para o Azure Data Lake Store no VS Code
 - Transfira o ficheiro diretamente a partir do Azure Data Lake Store no VS Code

### <a name="list-the-storage-path"></a>Listar o caminho de armazenamento 

**Para listar o caminho de armazenamento por meio da paleta de comandos**

1. O editor de scripts com o botão direito e selecione **ADL: Listar caminho**.
2. Escolha a pasta na lista ou selecione **introduza um caminho** ou **procurar a partir do caminho de raiz**. (Estamos usando **introduza um caminho** como exemplo.) 
3. Selecione a sua conta do Data Lake Analytics.
4. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, / saída /).  

A paleta de comando lista as informações de caminho com base em suas entradas.

![Resultados do caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

É uma forma mais conveniente para listar o caminho relativo através do menu de atalho.

**Para listar o caminho de armazenamento através do menu de atalho**

A cadeia de caminho com o botão direito e selecione **caminho da lista**.

!["Caminho list" no menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Pré-visualização do ficheiro de armazenamento

1. O editor de scripts com o botão direito e selecione **ADL: Pré-visualizar ficheiros**.
2. Selecione a sua conta do Data Lake Analytics. 
3. Introduza um caminho de ficheiro de armazenamento do Azure (por exemplo, /output/SearchLog.txt). 

O ficheiro abre-se no VS Code.

![Passos e resultados para visualizar o ficheiro de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra forma de visualizar o ficheiro é através do menu de atalho no caminho completo do ficheiro ou caminho relativo do ficheiro no editor de script. 

### <a name="upload-a-file-or-folder"></a>Carregar um ficheiro ou pasta

1. O editor de scripts com o botão direito e selecione **carregar o ficheiro** ou **carregar pasta**.
2. Escolha um ou vários ficheiros, se tiver selecionado **carregar o ficheiro**, ou escolha a pasta inteira, se tiver selecionado **carregar pasta**. Em seguida, selecione **carregar**. 
3. Escolha a pasta de armazenamento na lista ou selecione **introduza um caminho** ou **procurar a partir do caminho de raiz**. (Estamos usando **introduza um caminho** como exemplo.) 
4. Selecione a sua conta do Data Lake Analytics. 
5. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, / saída /). 
6. Selecione **escolher a pasta atual** para especificar o destino de carregamento.

![Passos e resultados para carregar um ficheiro ou pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Outra forma de carregar ficheiros para o armazenamento é através do menu de atalho no caminho completo do ficheiro ou caminho relativo do ficheiro no editor de script.

Pode [monitorizar o estado de carregamento](#check-storage-tasks-status).


### <a name="download-a-file"></a>Transferir um ficheiro 
Pode transferir um ficheiro com o comando **ADL: Transferir o ficheiro** ou **ADL: Transferir ficheiro (avançado)**.

**Para transferir um ficheiro através do ADL: Baixe o comando de ficheiro (avançado)**
1. O editor de scripts com o botão direito e, em seguida, selecione **baixar o arquivo (avançado)**.
2. VS Code mostra um ficheiro JSON. Pode introduzir os caminhos de ficheiro e baixar vários ficheiros ao mesmo tempo. As instruções são apresentadas no **saída** janela. Para continuar para transferir o ficheiro ou ficheiros, guarde, por exemplo, o ficheiro JSON (Ctrl + S).

    ![Ficheiro JSON com caminhos de download do arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

O **saída** janela exibe o status do download de arquivo.

![Janela de saída com o estado de download](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Pode [monitorar o status do download](#check-storage-tasks-status).

**Para transferir um ficheiro através do ADL: Baixe o comando de ficheiro**

1. O editor de scripts com o botão direito, selecione **baixar arquivo**e, em seguida, selecione a pasta de destino a partir do **selecionar pasta de** caixa de diálogo.
2. Escolha a pasta na lista ou selecione **introduza um caminho** ou **procurar a partir do caminho de raiz**. (Estamos usando **introduza um caminho** como exemplo.) 
3. Selecione a sua conta do Data Lake Analytics. 
4. Navegue para ou introduza o caminho da pasta de armazenamento (por exemplo, / saída /) e, em seguida, escolha um ficheiro para transferir.

![Passos e o resultado para transferir um ficheiro](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Outra forma de transferir os ficheiros de armazenamento é através do menu de atalho no caminho completo do ficheiro ou caminho relativo do ficheiro no editor de script.

Pode [monitorar o status do download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Verificar o estado de tarefas de armazenamento
O estado de carregamento e transferência é apresentada na barra de status. Selecione a barra de status e, em seguida, o estado é apresentado no **saída** separador.

![Barra de status e detalhes de saída](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrar com o Azure Data Lake Analytics a partir do Explorador

Depois de iniciar sessão, todas as subscrições para a sua conta do Azure estão listadas no painel esquerdo, em **AZURE DATALAKE**. 

![Explorador do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navegação de metadados do Data Lake Analytics

Expanda a sua subscrição do Azure. Sob o **bases de dados U-SQL** nó, pode navegar pelos sua base de dados U-SQL e como visualizar pastas **esquemas**, **credenciais**, **Assemblies**, **Tabelas**, e **índice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestão de entidades de metadados do Data Lake Analytics

Expanda **bases de dados U-SQL**. Pode criar uma base de dados, esquema, tabela, tipo de tabela, índice ou estatística clicando com o botão direito no nó correspondente e, em seguida, selecionando **Script para criar** no menu de atalho. Na página de script aberto, edite o script, de acordo com suas necessidades. Em seguida, submeter a tarefa ao clicar com botão direito-lo e selecionar **ADL: Submeter a tarefa**. 

Depois de acabar de criar o item, faça duplo clique no nó e, em seguida, selecione **atualizar** para mostrar o item. Também pode eliminar o item, clicar com botão direito-lo e, em seguida, selecionando **eliminar**.

![Comando de "Para criar o script" no menu de atalho no Explorador do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Página de script para o novo item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registo de assemblagem do Data Lake Analytics

Pode registrar um assembly no banco de dados correspondente clicando com o **Assemblies** nó e, em seguida, selecionando **registrar assembly**.

![Comando de "Registar o assembly" no menu de atalho para o nó de Assemblies](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrar com o Azure Data Lake Store a partir do Explorador

Navegue até **Data Lake Store**:

- Pode clicar com o botão direito no nó de pasta e, em seguida, utilizar o **Atualize**, **eliminar**, **carregar**, **carregar pasta**, **cópia Caminho relativo**, e **copiar o caminho completo** comandos no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta no Explorador do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Pode com o botão direito no nó de ficheiro e, em seguida, utilizar o **pré-visualização**, **transferir**, **eliminar**, **criar Script de EXTRAIR** (disponível apenas para CSV TSV e os ficheiros TXT), **caminho relativo de cópia**, e **caminho completo do cópia** comandos no menu de atalho.

   ![Comandos de menu de atalho para um nó de ficheiro no Explorador do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrar com o armazenamento de Blobs do Azure a partir do explorer

Navegar para o armazenamento de BLOBs:

- Pode clicar com o botão direito no nó do contentor de BLOBs e, em seguida, utilizar o **Atualize**, **eliminar contentor de BLOBs**, e **carregar Blob** comandos no menu de atalho.

   ![Comandos de menu de atalho para um nó do contentor de BLOBs no armazenamento de BLOBs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Pode clicar com o botão direito no nó de pasta e, em seguida, utilizar o **Atualize** e **carregar Blob** comandos no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta no armazenamento de BLOBs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Pode com o botão direito no nó de ficheiro e, em seguida, utilizar o **pré-visualização/editar**, **transferir**, **eliminar**, **criar Script de EXTRAIR** (disponível apenas para os ficheiros CSV, TSV e TXT), **caminho relativo de cópia**, e **caminho completo do cópia** comandos no menu de atalho.

    ![Comandos de menu de atalho para um nó de ficheiro no armazenamento de BLOBs](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Abra o Explorador do Data Lake no portal
1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Introduza **Explorador de armazenamento de Azure Web aberto** ou com o botão direito um caminho relativo ou o caminho completo no editor de script e, em seguida, selecione **Abrir Explorador de armazenamento do Azure Web**.
3. Selecione uma conta do Data Lake Analytics.

Ferramentas do Data Lake abre o caminho de armazenamento do Azure no portal do Azure. Pode encontrar o caminho e o arquivo da web de pré-visualização.

## <a name="additional-features"></a>Funcionalidades adicionais

Data Lake Tools para o VS Code suporta as seguintes funcionalidades:

-   **Preenchimento automático de IntelliSense**: Sugestões aparecem em janelas de pop-up em torno de itens, como palavras-chave, métodos e variáveis. Representam os ícones de diferentes tipos diferentes de objetos:

    - Tipo de dados scala
    - Tipo de dados complexos
    - UDTs incorporados
    - Coleção de .NET e classes
    - Expressões de linguagem c#
    - UDFs incorporadas c#, UDOs e UDAAGs 
    - Funções de U-SQL
    - Modos de U-SQL
 
    ![Tipos de objeto do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Preenchimento automático de IntelliSense nos metadados do Data Lake Analytics**: Ferramentas do Data Lake transfere as informações de metadados do Data Lake Analytics localmente. O recurso IntelliSense popula os objetos dos metadados do Data Lake Analytics. Estes objetos incluem a base de dados, esquema, tabela, vista, função com valor de tabela, procedimentos e assemblagens c#.
 
    ![Metadados do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Marcador de erro do IntelliSense**: Ferramentas do Data Lake sublinha erros de edição para U-SQL e C#. 
-   **Destaques de sintaxe**: Cores de usos de ferramentas do Data Lake para diferenciar itens como variáveis, palavras-chave, tipos de dados e funções. 

    ![Sintaxe com várias cores](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Recomendamos que Atualize para o Azure Data Lake Tools para Visual Studio versão 2.3.3000.4 ou posterior. As versões anteriores já não estão disponíveis para transferência e foram preteridas.  
   
## <a name="next-steps"></a>Passos Seguintes
- [Desenvolver o U-SQL com o Python, R e C Sharp para o Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
