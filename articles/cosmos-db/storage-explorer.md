---
title: Gerir recursos DB da Azure Cosmos utilizando o Azure Storage Explorer
description: Aprenda a conectar-se à Azure Cosmos DB e gerencie os seus recursos utilizando o Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1948ae186662c7f60f4d49c19a4d48b424a38f7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047484"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Trabalhar com dados com o Explorador de Armazenamento do Azure

A utilização do Azure Cosmos DB no Explorador de Armazenamento do Azure permite aos utilizadores gerir as entidades do Azure Cosmos DB, manipular dados, atualizar os procedimentos e acionadores armazenados, bem como outras entidades do Azure, como blobs e filas de Armazenamento. Agora, pode utilizar a mesma ferramenta para gerir as diferentes entidades do Azure num único local. Neste momento, o Azure Storage Explorer suporta contas cosmos configuradas para APIs DE SQL, MongoDB, Graph e Table.


## <a name="prerequisites"></a>Pré-requisitos

Uma conta Cosmos com API SQL ou API da Azure Cosmos para a MongoDB. Se não tiver uma conta, pode criar uma no portal do Azure, conforme descrito em [Azure Cosmos DB: criar uma aplicação Web da SQL API com .NET e o portal do Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalação

Instale os bits mais recentes do Explorador de Armazenamento do Azure aqui: [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), suportamos agora a versão para Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

1. Depois de instalar o **Azure Storage Explorer,** selecione o ícone **plug-in** à esquerda, como mostra a seguinte imagem:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Selecione o ícone plug-in para ligar":::

2. Selecione **Adicionar uma Conta do Azure** e selecione **Iniciar Sessão**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Ligue-se à subscrição Azure necessária":::

2. Na caixa de diálogo **Azure' Insira,** selecione **Iniciar súplica**e, em seguida, insira as suas credenciais Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Inscreva-se na sua assinatura Azure":::

3. Selecione a sua subscrição da lista e, em seguida, **selecione Aplicar**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Escolha um ID de subscrição da lista para filtrar":::

    O painel do Explorador atualiza e apresenta as contas na subscrição selecionada.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Selecione uma conta DB Azure Cosmos da lista disponível":::

    Ligou com êxito a **conta do Cosmos DB** à subscrição do Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ligar ao Azure Cosmos DB através de uma cadeia de ligação

Uma forma alternativa de ligar ao Azure Cosmos DB é utilizar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Cosmos DB** e selecione **Ligar ao Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Ligar ao Azure Cosmos DB através de uma cadeia de ligação":::

2. Atualmente, apenas suporta a API do SQL e de Tabela. Escolha API, cole **a cadeia de ligação**, introduza **a etiqueta conta**de entrada, selecione **Seguinte** para verificar o resumo e, em seguida, selecione **Connect** para ligar a conta DB do Azure Cosmos. Para obter informações sobre a recuperação da cadeia de ligação primária, consulte [obter a cadeia de ligação](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Insira a sua cadeia de ligação":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Ligar ao Azure Cosmos DB através de um emulador local

Utilize os seguintes passos para ligar ao Azure Cosmos DB pelo Emulador, só suporta atualmente a conta de SQL.

1. Instale o Emulador e inicie. Para saber como instalar o Emulador, veja [Emulador do Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Cosmos DB** e selecione **Ligar ao Emulador do Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Ligue-se ao Azure Cosmos DB do emulador":::

3. Atualmente, apenas suporta a API do SQL. Colar **Cadeia de Ligação**, **etiqueta de conta**de entrada , selecione **Seguinte** para verificar o resumo e, em seguida, selecione **Connect** para ligar a conta DB do Azure Cosmos. Para obter informações sobre a recuperação da cadeia de ligação primária, consulte [obter a cadeia de ligação](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Ligue-se ao Cosmos DB a partir do diálogo do emulador":::


## <a name="azure-cosmos-db-resource-management"></a>Gestão de recursos do Azure Cosmos DB

Pode gerir uma conta do Azure Cosmos DB ao efetuar as seguintes operações:
* Abra a conta no portal do Azure
* Adicione o recurso à Lista de Acesso Rápido
* Procure e atualize os recursos
* Criar e eliminar bases de dados
* Criar e eliminar coleções
* Crie, edite, elimine e filtre documentos
* Faça a gestão de procedimentos armazenados, acionadores e funções definidas pelo utilizador

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Ao clicar com o botão direito do rato numa subscrição no painel do Explorador, pode efetuar muitas tarefas de ação rápida:

* Clique com o botão direito do rato numa conta do Azure Cosmos DB ou numa base de dados, selecione **Abrir no Portal** e faça a gestão dos recursos no browser no portal do Azure.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Abrir no Portal":::

* Também pode adicionar a conta do Azure Cosmos DB, a base de dados e a coleção ao **Acesso Rápido**.
* **Procurar a partir daqui** permite procurar por palavra-chave no caminho selecionado.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="procurar a partir daqui":::

### <a name="database-and-collection-management"></a>Gestão de bases de dados e coleções

#### <a name="create-a-database"></a>Criar uma base de dados

- Clique com o botão direito do rato na conta do Azure Cosmos DB, selecione **Criar Base de Dados**, introduza o nome da base de dados e prima **Enter** para concluir.

  :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Crie uma base de dados na sua conta Azure Cosmos":::

#### <a name="delete-a-database"></a>Eliminar uma base de dados

- Clique com o botão direito na base de dados, selecione **Delete Database**e selecione **Sim** na janela pop-up. O nó da base de dados é eliminado e a conta do Azure Cosmos DB é atualizada automaticamente.

  :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Eliminar a primeira base de dados":::

  :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Eliminar as segundas bases de dados":::

#### <a name="create-a-collection"></a>Criar uma coleção

1. Clique com o direito na sua base de dados, escolha **Create Collection**e, em seguida, forneça as seguintes informações como **ID de recolha,** **capacidade de armazenamento,** etc. Clique **em OK** para terminar.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Criar primeira coleção na base de dados":::

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Criar segunda coleção na base de dados":::

2. Selecione **Unlimited** para ser capaz de especificar a chave de partição e, em seguida, selecione **OK** para terminar.

    Se for utilizada uma chave de partição quando criar uma coleção, depois de concluída a criação, o valor da chave de partição não pode ser alterado na coleção.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Configurar uma chave de partição":::

#### <a name="delete-a-collection"></a>Delete a collection (Eliminar uma coleção)

- Clique com o botão direito na coleção, selecione **Delete Collection**e, em seguida, selecione **Sim** na janela pop-up.

    O nó da coleção é eliminado e a base de dados é atualizada automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Apagar uma das coleções":::

### <a name="document-management"></a>Gestão de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos

- Para criar um novo documento, abra **documentos** na janela esquerda, selecione **Novo Documento,** edite o conteúdo no painel direito e, em seguida, selecione **Guardar**. Também pode atualizar um documento existente e, em seguida, selecionar **Guardar**. As alterações podem ser eliminadas ao clicar em **Eliminar**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Criar um novo documento":::

#### <a name="delete-a-document"></a>Eliminar um documento

- Clique no botão **Eliminar** para eliminar o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos

- Edite o filtro do documento introduzindo uma [consulta SQL](how-to-sql-query.md) e, em seguida, selecione **Aplicar**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Consulta de documentos específicos":::

### <a name="graph-management"></a>Gestão de gráficos

#### <a name="create-and-modify-vertex"></a>Criar e modificar um vértice

1. Para criar um novo vértice, abra o **Gráfico** a partir da janela esquerda, selecione **New Vertex,** edite o conteúdo e, em seguida, selecione **OK**.
2. Para modificar um vértice existente, selecione o ícone da caneta no painel direito.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Modifique o vértice de um gráfico":::

#### <a name="delete-a-graph"></a>Eliminar um gráfico

- Para eliminar um vértice, selecione o ícone do recipiente de reciclagem ao lado do nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráfico

- Edite o filtro de gráfico introduzindo uma [consulta de gremlin](gremlin-support.md) e, em seguida, selecione **Apply Filter**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Executar uma consulta de gráfico":::

### <a name="table-management"></a>Gestão de tabelas

#### <a name="create-and-modify-table"></a>Criar e modificar tabela

1. Para criar uma nova tabela, abra **as Entidades** a partir da janela esquerda, selecione **Adicionar,** editar o conteúdo no diálogo **'Adicionar Entidade',** adicionar propriedade clicando no botão **Adicionar Propriedade,** em seguida, selecione **Insira**.
2. Para modificar uma tabela, **selecione Editar,** modifique o conteúdo e, em seguida, selecione **Update**.

   :::image type="content" source="./media/storage-explorer/table.png" alt-text="Criar e modificar uma tabela":::

#### <a name="import-and-export-table"></a>Importar e exportar tabela

1. Para importar, **selecione o** botão Importar e escolha uma tabela existente.
2. Para exportar, selecione **botão Exportação** e escolha um destino.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Importar ou exportar um quadro":::

#### <a name="delete-entities"></a>Eliminar entidades

- Selecione as entidades e selecione o botão **Delete**.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Eliminar uma tabela":::

#### <a name="query-table"></a>Tabela de consulta

- Clique no botão **de consulta,** na condição de consulta de entrada e, em seguida, selecione executar o botão **'Executar'.** Feche o painel de Consulta, ao clicar no botão **Fechar Consulta**.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Dados de consulta da tabela":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerir procedimentos armazenados, acionadores e UDFs

* Para criar um procedimento armazenado, na árvore esquerda, clique à direita **Procedimento armazenado,** escolha **Criar Procedimento Armazenado,** introduza um nome à esquerda, digite os scripts de procedimento armazenado na janela direita e, em seguida, selecione **Criar**.
* Também pode editar os procedimentos armazenados existentes clicando duas vezes, fazendo a atualização e, em seguida, clicando em **Update** para guardar, ou selecione **Descartar** para cancelar a alteração.

  :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Criar e gerir procedimentos armazenados":::

* As operações de **Acionadores** e **UDF** são semelhantes às dos **Procedimentos Armazenados**.

## <a name="troubleshooting"></a>Resolução de problemas

O [Azure Cosmos DB no Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) é uma aplicação autónoma que permite ligar a contas do Azure Cosmos DB alojadas no Azure e Clouds Soberanas do Windows, macOS ou Linux. Permite aos utilizadores gerir as entidades do Azure Cosmos DB, manipular dados, atualizar os procedimentos e acionadores armazenados, bem como outras entidades do Azure, como blobs e filas de Armazenamento.

São soluções para problemas comuns detetados para o Azure Cosmos DB no Explorador de Armazenamento.

### <a name="sign-in-issues"></a>Problemas de início de sessão

Antes de prosseguir, tente reiniciar a aplicação e verifique se os problemas podem ser corrigidos.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia de certificados

Existem algumas razões pelas quais pode estar a ver este erro, sendo as duas mais comuns:

+ Está por detrás de um *representante transparente,* o que significa que alguém (como o seu departamento de TI) está a intercetar o tráfego HTTPS, desencriptar e, em seguida, criptografá-lo usando um certificado auto-assinado.

+ Está a executar software, como software antivírus, que está a injetar um certificado TLS/SSL auto-assinado nas mensagens HTTPS que recebe.

Quando o Explorador de Armazenamento encontra um destes "certificados autoassinados", já não consegue saber se a mensagem HTTPS que está a receber foi adulterada. No entanto, se tiver uma cópia do certificado autoassinado, pode indicar ao Explorador de Armazenamento para confiar no mesmo. Se não tiver a certeza de quem está a injetar o certificado, pode tentar descobrir através dos seguintes passos:

1. Instalar OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões simples)
     - Mac e Linux: devem estar incluídos no sistema operativo
2. Executar OpenSSL
    - Windows: aceda ao diretório de instalação, a **/bin/** e faça duplo clique em **openssl.exe**.
    - Mac e Linux: execute **openssl** a partir de um terminal
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procure certificados autoassinados. Se não tiver a certeza de quais são autoassinados, verifique se o assunto ("s:") e o emissor ("i:") são os mesmos.
5.  Depois de encontrar certificados autoassinados, copie e cole tudo entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----** num novo ficheiro .cer para cada um deles.
6.  Abrir o Explorador de Armazenamento e, em seguida, ir para **editar**  >  **certificados SSL**  >  **Certificados de Importação Certificados**. Com o seletor de ficheiros, procure, selecione e abra os ficheiros .cer que criou.

Se não conseguir encontrar nenhum certificado autoassinado com os passos acima, pode enviar comentários para obter mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível obter subscrições

Se não conseguir obter as suas subscrições depois de iniciar sessão com êxito:

- Verifique se a sua conta tem acesso às subscrições ao assinar no [portal Azure](https://portal.azure.com/)
- Certifique-se de que tem sessão iniciada no ambiente correto ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemanha](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) ou Ambiente Personalizado/Azure Stack)
- Se estiver por trás de um proxy, certifique-se de que configurou o proxy do Explorador de Armazenamento corretamente
- Tente remover e adicionar novamente a conta
- Tente eliminar os seguintes ficheiros do diretório raiz (como: C:\Users\ContosoUser) e, em seguida, adicionar novamente a conta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Ver mensagens de erro na consola das ferramentas de programação (f12) ao iniciar sessão

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Verifique se a consola de ferramentas de desenvolvimento é diferente de quaisquer erros":::

#### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se não conseguir ver a página de autenticação:

- Consoante a velocidade da sua ligação, pode demorar algum tempo a carregar a página de início de sessão. Aguarde, pelo menos, um minuto antes de fechar a caixa de diálogo de autenticação
- Se estiver por trás de um proxy, certifique-se de que configurou o proxy do Explorador de Armazenamento corretamente
- Abra a consola de programador ao premir a tecla F12. Veja as respostas na consola de programador e se consegue encontrar alguma pista para a autenticação não estar a funcionar

#### <a name="cannot-remove-account"></a>Não é possível remover a conta

Se não conseguir remover uma conta ou se a ligação de reautenticação não fizer nada

- Tente eliminar os seguintes ficheiros do diretório raiz e, em seguida, adicionar novamente a conta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se quiser remover os recursos de Armazenamento SAS anexados, elimine:
  - a pasta %AppData%/StorageExplorer do Windows
  - /Utilizadores/<your_name>/Biblioteca/Aplicação SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Terá de reintroduzir todas as suas credenciais** se eliminar estes ficheiros


### <a name="httphttps-proxy-issue"></a>Problema de proxy Http/Https

Não é possível listar os nós do Azure Cosmos DB na árvore à esquerda quando configurar um proxy http/https num ASE. Pode utilizar o explorador de dados do Azure Cosmos DB no portal do Azure como uma solução neste momento.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema do nó "Desenvolvimento" no nó "Local e Anexado"

Não há resposta depois de selecionar o nó "Desenvolvimento" no nó "Local e Anexado" na árvore esquerda.  O comportamento é esperado. O emulador local do Azure Cosmos DB será suportado na versão seguinte.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Nó de desenvolvimento":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Erro ao anexar a conta do Azure Cosmos DB no nó "Local e Anexado"

Se vir o erro abaixo depois de anexar uma conta do Azure Cosmos DB no nó "Local e Anexado", verifique se está a utilizar a cadeia de ligação correta.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Erro ao anexar o Azure Cosmos DB no nó Local e Anexado":::

### <a name="expand-azure-cosmos-db-node-error"></a>Erro ao expandir o nó do Azure Cosmos DB

Pode ver o erro abaixo ao tentar expandir os nós da árvore à esquerda.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Erro ao expandir o nó do Azure Cosmos DB":::

Experimente as sugestões seguintes:

- Verifique se a conta do Azure Cosmos DB está em aprovisionamento e tente novamente quando a conta estiver a ser criada com êxito.
- Se a conta estiver nos nós "Acesso Rápido" ou "Local e Anexado", verifique se a conta foi eliminada. Se for o caso, tem de remover o nó manualmente.

## <a name="next-steps"></a>Passos seguintes

* Veja o vídeo seguinte para ver como utilizar o Azure Cosmos DB no Explorador de Armazenamento do Azure: [Utilizar o Azure Cosmos DB no Explorador de Armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Explorador de Armazenamento e como ligar mais serviços em [Introdução ao Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
