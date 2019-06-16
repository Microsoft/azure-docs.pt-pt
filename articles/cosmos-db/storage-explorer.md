---
title: Gerir recursos do Azure Cosmos DB com o Explorador de armazenamento do Azure
description: Saiba como ligar ao Azure Cosmos DB e gerir os respetivos recursos através do Explorador de armazenamento do Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 6c3c8aee51a4484013126a5959d3d275a88aa1cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242648"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Trabalhar com dados com o Explorador de Armazenamento do Azure

A utilização do Azure Cosmos DB no Explorador de Armazenamento do Azure permite aos utilizadores gerir as entidades do Azure Cosmos DB, manipular dados, atualizar os procedimentos e acionadores armazenados, bem como outras entidades do Azure, como blobs e filas de Armazenamento. Agora, pode utilizar a mesma ferramenta para gerir as diferentes entidades do Azure num único local. Neste momento, o Explorador de armazenamento do Azure suporta contas do Cosmos configuradas para SQL, MongoDB, Graph e APIs de tabela.


## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Cosmos com a API de SQL ou do Azure Cosmos DB API para MongoDB. Se não tiver uma conta, pode criar uma no portal do Azure, conforme descrito em [do Azure Cosmos DB: Criar uma aplicação de web da SQL API com .NET e o portal do Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalação

Instale os mais recentes bits de Explorador de armazenamento do Azure aqui: [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), agora, suportamos a versão do Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

1. Depois de instalar o **Explorador de Armazenamento do Azure**, clique no ícone de **plug-in** à esquerda, conforme mostrado na imagem seguinte:
       
   ![Ícone de plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Selecione **Adicionar uma Conta do Azure** e clique em **Iniciar sessão**.

   ![Ligar a uma subscrição do Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Na caixa de diálogo **Início de sessão do Azure**, selecione **Iniciar sessão** e, em seguida, introduza as credenciais do Azure.

    ![Iniciar sessão](./media/storage-explorer/sign-in.png)

3. Selecione a sua subscrição na lista e clique em **Aplicar**.

    ![Aplicar](./media/storage-explorer/apply-subscription.png)

    O painel do Explorador atualiza e apresenta as contas na subscrição selecionada.

    ![Lista de contas](./media/storage-explorer/account-list.png)

    Ligou com êxito a **conta do Cosmos DB** à subscrição do Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ligar ao Azure Cosmos DB através de uma cadeia de ligação

Uma forma alternativa de ligar ao Azure Cosmos DB é utilizar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Cosmos DB** e selecione **Ligar ao Cosmos DB...**

    ![Ligar ao Cosmos DB através de uma cadeia de ligação](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Atualmente, apenas suporta a API do SQL e de Tabela. Escolha a API, cole a **Cadeia de Ligação**, introduza a **Etiqueta de conta**, clique em **Seguinte** para verificar o resumo e, em seguida, clique em **Ligar** para ligar a conta do Azure Cosmos DB. Para obter informações sobre como obter a cadeia de ligação, veja [Obter a cadeia de ligação](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Connection-string](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Ligar ao Azure Cosmos DB através de um emulador local

Utilize os seguintes passos para ligar ao Azure Cosmos DB pelo Emulador, só suporta atualmente a conta de SQL.

1. Instale o Emulador e inicie. Para saber como instalar o Emulador, veja [Emulador do Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Cosmos DB** e selecione **Ligar ao Emulador do Cosmos DB...**

    ![Ligar ao Cosmos DB pelo Emulador](./media/storage-explorer/emulator-entry.png)

3. Atualmente, apenas suporta a API do SQL. Cole a **Cadeia de Ligação**, introduza a **Etiqueta de conta**, clique em **Seguinte** para verificar o resumo e, em seguida, clique em **Ligar** para ligar a conta do Azure Cosmos DB. Para obter informações sobre como obter a cadeia de ligação, veja [Obter a cadeia de ligação](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Ligar ao Cosmos DB através da caixa de diálogo Emulador](./media/storage-explorer/emulator-dialog.png)


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

     ![Abrir no Portal](./media/storage-explorer/open-in-portal.png)

* Também pode adicionar a conta do Azure Cosmos DB, a base de dados e a coleção ao **Acesso Rápido**.
* **Procurar a partir daqui** permite procurar por palavra-chave no caminho selecionado.

    ![procurar a partir daqui](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestão de bases de dados e coleções
#### <a name="create-a-database"></a>Criar uma base de dados 
-   Clique com o botão direito do rato na conta do Azure Cosmos DB, selecione **Criar Base de Dados**, introduza o nome da base de dados e prima **Enter** para concluir.
       
    ![Criar base de dados](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminar uma base de dados
- Clique com o botão direito do rato na base de dados, clique em **Eliminar Base de Dados** e em **Sim** na janela de pop-up. O nó da base de dados é eliminado e a conta do Azure Cosmos DB é atualizada automaticamente.

    ![Eliminar base de dados1](./media/storage-explorer/delete-database1.png)  

    ![Eliminar base de dados2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Criar uma coleção
1. Clique com o botão direito do rato na base de dados, selecione **Criar Coleção** e, em seguida, forneça as seguintes informações, como **ID da Coleção**, **Capacidade de armazenamento**, etc. Clique em **OK** para concluir. 

    ![Criar coleção1](./media/storage-explorer/create-collection.png)

    ![Criar coleção2](./media/storage-explorer/create-collection2.png) 

2. Selecione **Ilimitado** para poder especificar a chave de partição, em seguida, clique em **OK** para concluir.

    Se for utilizada uma chave de partição quando criar uma coleção, depois de concluída a criação, o valor da chave de partição não pode ser alterado na coleção.

    ![Chave de partição](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Eliminar uma coleção
- Clique com o botão direito do rato na coleção, clique em **Eliminar Coleção** e em **Sim** na janela de pop-up. 

    O nó da coleção é eliminado e a base de dados é atualizada automaticamente.

    ![Eliminar coleção](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestão de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos
- Para criar um novo documento, abra **Documentos** na janela à esquerda, clique em **Novo Documento**, edite o conteúdo no painel direito e, em seguida, clique em **Guardar**. Também pode atualizar um documento existente e, em seguida, clicar em **Guardar**. As alterações podem ser eliminadas ao clicar em **Eliminar**.

    ![Documento](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminar um documento
- Clique no botão **Eliminar** para eliminar o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos
- Edite o filtro de documentos ao introduzir uma [consulta SQL](how-to-sql-query.md) e, em seguida, clique em **Aplicar**.

    ![Filtro de Documento](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Gestão de gráficos

#### <a name="create-and-modify-vertex"></a>Criar e modificar um vértice
1. Para criar um novo vértice, abra o **Graph** na janela esquerda, clique em **Vértice Novo**, edite o conteúdo e clique em **OK**.    
2. Para modificar um vértice existente, clique no ícone de caneta no painel direito.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Eliminar um gráfico
- Para eliminar um vértice, clique no ícone de reciclagem junto ao nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráfico
- Edite o filtro de gráfico ao introduzir uma [consulta gremlin](gremlin-support.md) e, em seguida, clique em **Aplicar Filtro**.

    ![Filtro de Gráfico](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Gestão de tabelas

#### <a name="create-and-modify-table"></a>Criar e modificar tabela
1. Para criar uma nova tabela, abra **Entidades** na janela esquerda, clique em **Adicionar**, edite o conteúdo na caixa de diálogo **Adicionar Entidade**, adicione a propriedade ao clicar no botão **Adicionar Propriedade** e clique em **Inserir**.
2. Para modificar uma tabela, clique em **Editar**, modifique o conteúdo e clique em **Atualizar**.

    ![Tabela](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importar e exportar tabela
1. Para importar, clique no botão **Importar** e escolha uma tabela existente.
2. Para exportar, clique no botão **Exportar** e escolha um destino.

    ![Importação e Exportação de Tabela](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Eliminar entidades
- Selecione as entidades e clique no botão **Eliminar**.

    ![Eliminação da tabela](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Tabela de consulta
- Clique no botão **Consulta**, introduza a condição de consulta e clique no botão **Executar Consulta**. Feche o painel de Consulta, ao clicar no botão **Fechar Consulta**.

    ![Consulta de Tabela](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerir procedimentos armazenados, acionadores e UDFs
* Para criar um procedimento armazenado, na árvore à esquerda, clique com o botão direito do rato em **Procedimento Armazenado**, selecione **Criar Procedimento Armazenado**, introduza um nome à esquerda, escreva os scripts do procedimento armazenado na janela à direita e, em seguida, clique em **Criar**. 
* Também pode editar procedimentos armazenados existentes ao fazer duplo clique, ao efetuar a atualização e, em seguida, ao clicar em **Atualizar** para guardar ou em **Eliminar** para cancelar a alteração.

    ![Procedimento armazenado](./media/storage-explorer/stored-procedure.png)
* As operações de **Acionadores** e **UDF** são semelhantes às dos **Procedimentos Armazenados**.

## <a name="troubleshooting"></a>Resolução de problemas

O [Azure Cosmos DB no Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) é uma aplicação autónoma que permite ligar a contas do Azure Cosmos DB alojadas no Azure e Clouds Soberanas do Windows, macOS ou Linux. Permite aos utilizadores gerir as entidades do Azure Cosmos DB, manipular dados, atualizar os procedimentos e acionadores armazenados, bem como outras entidades do Azure, como blobs e filas de Armazenamento.

São soluções para problemas comuns detetados para o Azure Cosmos DB no Explorador de Armazenamento.

### <a name="sign-in-issues"></a>Problemas de início de sessão

Antes de prosseguir, tente reiniciar a aplicação e verifique se os problemas podem ser corrigidos.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia de certificados

Existem algumas razões pelas quais pode estar a ver este erro, sendo as duas mais comuns:

+ Está por trás de um *proxy transparente*, que significa que alguém (por exemplo, o departamento de TI) está a intercetar o tráfego HTTPS, desencriptá-lo e encriptá-lo a utilizar um certificado autoassinado.

+ Está a executar software, como software antivírus, que está a injetar um certificado SSL autoassinado nas mensagens HTTPS recebidas.

Quando o Explorador de Armazenamento encontra um destes "certificados autoassinados", já não consegue saber se a mensagem HTTPS que está a receber foi adulterada. No entanto, se tiver uma cópia do certificado autoassinado, pode indicar ao Explorador de Armazenamento para confiar no mesmo. Se não tiver a certeza de quem está a injetar o certificado, pode tentar descobrir através dos seguintes passos:

1. Instalar o Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões simples)
     - Mac e Linux: Deve ser incluído com o sistema operativo
2. Executar o Open SSL
    - Windows: Aceda ao diretório de instalação, em seguida, **/bin/** , em seguida, faça duplo clique em **openssl.exe**.
    - Mac e Linux: execute **openssl** a partir de um terminal
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procure certificados autoassinados. Se não tiver a certeza de quais são autoassinados, verifique se o assunto ("s:") e o emissor ("i:") são os mesmos.
5.  Depois de encontrar certificados autoassinados, copie e cole tudo entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----** num novo ficheiro .cer para cada um deles.
6.  Abra o Explorador de Armazenamento e, em seguida, aceda a **Editar** > **Certificados SSL** > **Importar Certificados**. Com o seletor de ficheiros, procure, selecione e abra os ficheiros .cer que criou.

Se não conseguir encontrar nenhum certificado autoassinado com os passos acima, pode enviar comentários para obter mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível obter subscrições

Se não conseguir obter as suas subscrições depois de iniciar sessão com êxito:

- Certifique-se de que a conta tem acesso às subscrições ao iniciar sessão no [Portal do Azure](https://portal.azure.com/)
- Certifique-se de que tem sessão iniciada no ambiente correto ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemanha](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) ou Ambiente Personalizado/Azure Stack)
- Se estiver por trás de um proxy, certifique-se de que configurou o proxy do Explorador de Armazenamento corretamente
- Tente remover e adicionar novamente a conta
- Tente eliminar os seguintes ficheiros do diretório raiz (tais como: C:\Users\ContosoUser) e, em seguida, adicionar novamente a conta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Ver mensagens de erro na consola das ferramentas de programação (f12) ao iniciar sessão

![consola](./media/storage-explorer/console.png)

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
  - /Users/ < your_name >/biblioteca/aplicação suporte/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Terá de reintroduzir todas as suas credenciais** se eliminar estes ficheiros


### <a name="httphttps-proxy-issue"></a>Problema de proxy Http/Https

Não é possível listar os nós do Azure Cosmos DB na árvore à esquerda quando configurar um proxy http/https num ASE. Este é um problema conhecido e será corrigido na versão seguinte. Pode utilizar o explorador de dados do Azure Cosmos DB no portal do Azure como uma solução neste momento. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema do nó "Desenvolvimento" no nó "Local e Anexado"

Não existe nenhuma resposta depois de clicar no nó de "Desenvolvimento" no nó "Local e Anexado" na árvore à esquerda.  O comportamento é esperado. O emulador local do Azure Cosmos DB será suportado na versão seguinte.

![Nó de desenvolvimento](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Erro ao anexar a conta do Azure Cosmos DB no nó "Local e Anexado"

Se vir o erro abaixo depois de anexar uma conta do Azure Cosmos DB no nó "Local e Anexado", verifique se está a utilizar a cadeia de ligação correta.

![Erro ao anexar o Azure Cosmos DB no nó Local e Anexado](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Erro ao expandir o nó do Azure Cosmos DB

Pode ver o erro abaixo ao tentar expandir os nós da árvore à esquerda. 

![Erro ao expandir](./media/storage-explorer/expand-error.png)

Experimente as sugestões seguintes:

- Verifique se a conta do Azure Cosmos DB está em aprovisionamento e tente novamente quando a conta estiver a ser criada com êxito.
- Se a conta estiver nos nós "Acesso Rápido" ou "Local e Anexado", verifique se a conta foi eliminada. Se for o caso, tem de remover o nó manualmente.

## <a name="contact-us"></a>Contacte-nos

Se nenhuma das soluções resolver o problema, envie um e-mail à equipa de Ferramentas de Programação do Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) com detalhes sobre o problema, para o corrigir.

## <a name="next-steps"></a>Passos Seguintes

* Veja o seguinte vídeo para ver como utilizar o Azure Cosmos DB no Explorador de armazenamento do Azure: [Utilizar o Azure Cosmos DB no Explorador de armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Explorador de Armazenamento e como ligar mais serviços em [Introdução ao Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

