---
title: Aceder a recursos com ferramentas data lake
description: Saiba como usar as ferramentas do Lago de Dados Azure para aceder aos recursos Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754755"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Aceder a recursos com ferramentas do Lago de Dados Azure

Pode aceder facilmente aos recursos do Azure Data Lake Analytics com comandos ou ações do Azure Data Tools no Código VS.

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
2. Escolha um ficheiro ou vários ficheiros se selecionou o **Upload File** ou escolher toda a pasta se selecionou **a Pasta do Upload**. Em seguida, selecione **Carregar**.
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

1. Clique com o botão direito no editor de scripts, selecione **Download File** e, em seguida, selecione a pasta de destino na caixa de diálogo **Select Folder.**

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

Expanda a sua subscrição Azure. No nó **U-SQL Databases,** pode navegar pela base de dados U-SQL e ver pastas como **Schemas,** **Credenciais,** **Conjuntos,** **Tabelas** e **Índice**.

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

- Pode clicar no nó da pasta e depois utilizar o **"Refresh**", **Eliminar**, **Carregar,** **Carregar Pasta,** **Copiar Caminho Relativo** e Copiar comandos Full **Path** no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Pode clicar no nó de ficheiro e depois utilizar o **pré-visualização**, **baixar**, **eliminar**, **criar script EXTRACT** (disponível apenas para ficheiros CSV, TSV e TXT), Copiar Caminho **Relativo** e Copiar comandos Do **Caminho Completo** no menu de atalho.

   ![Comandos de menu de atalho para um nó de ficheiro no explorador do Lago de Dados](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integre o armazenamento Azure Blob do explorador

Procurar no armazenamento blob:

- Pode clicar com o botão direito do nó do recipiente blob e, em seguida, utilizar os comandos **Refresh**, **Delete Blob** e Carregar os comandos **Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de recipiente blob sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Pode clicar com o botão direito no nó de pasta e, em seguida, utilizar os comandos **Refresh** and **Upload Blob** no menu de atalho.

   ![Comandos de menu de atalho para um nó de pasta sob armazenamento Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Pode clicar no nó de ficheiro e depois utilizar o **pré-visualização/Edição**, **Descarregar**, **Eliminar**, **Criar Script EXTRACT** (disponível apenas para ficheiros CSV, TSV e TXT), Copiar Caminho **Relativo** e Copiar comandos Do **Caminho Completo** no menu de atalho.

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