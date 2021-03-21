---
title: Gerir os recursos DB da Azure Cosmos utilizando o Azure Storage Explorer
description: Aprenda a conectar-se à Azure Cosmos DB e gerencie os seus recursos utilizando o Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348658"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Gerir os recursos DB da Azure Cosmos utilizando o Azure Storage Explorer
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Você pode usar o explorador de armazenamento Azure para ligar-se ao Azure Cosmos DB. Permite ligar-se às contas DB do Azure Cosmos hospedadas no Azure e nuvens soberanas do Windows, macOS ou Linux.

Use a mesma ferramenta para gerir as suas diferentes entidades Azure num só local. Pode gerir entidades DB da Azure Cosmos, manipular dados, atualizar procedimentos armazenados e gatilhos, juntamente com outras entidades da Azure, como bolhas de armazenamento e filas. O Azure Storage Explorer suporta contas cosmos configuradas para SQL, MongoDB, Graph e Table APIs.

> [!NOTE]
> A integração do Azure Cosmos DB com o Explorador de Armazenamento foi preterida. As funcionalidades existentes não serão removidas durante, pelo menos, um ano a partir desta versão. Deverá utilizar a aplicação de desktop [Azure](https://portal.azure.com/)Portal , [Azure Portal](https://portal.azure.com/App/Download) ou o autónomo [Azure Cosmos DB Explorer.](data-explorer.md) As opções alternativas incluem muitas funcionalidades novas que não são atualmente suportadas no Explorador de Armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Cosmos com uma API SQL ou um API API AZURE Cosmos para a MongoDB. Se não tiver uma conta, pode criar uma no portal Azure. Ver [Azure Cosmos DB: Construa uma aplicação web SQL API com .NET e o portal Azure](create-sql-api-dotnet.md) para obter mais informações.

## <a name="installation"></a>Instalação

Para instalar os mais recentes bits do Azure Storage Explorer, consulte [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Apoiamos as versões Windows, Linux e macOS.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

1. Depois de instalar **o Azure Storage Explorer,** selecione o ícone **plug-in** no painel esquerdo.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Screenshot mostrando o ícone plug-in no painel esquerdo.":::

1. Selecione **Adicionar uma Conta do Azure** e selecione **Iniciar Sessão**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Screenshot da janela de armazenamento Connect to Azure mostrando o botão de rádio Add azure Account selecionado e o menu suspenso Azure Environment.":::

1. Na caixa de diálogo **Azure' Insira,** selecione **Iniciar súplica** e, em seguida, insira as suas credenciais Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Screenshot do Sinal na janela mostrando onde introduzir as suas credenciais para a sua subscrição Azure.":::

1. Selecione a sua subscrição na lista e selecione **Aplicar**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Screenshot do painel de Gestão de Contas, mostrando uma lista de subscrições e o botão Aplicar.":::

    O painel explorer atualiza e mostra as contas na subscrição selecionada.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Screenshot do painel Explorer, atualizado para mostrar as contas na subscrição selecionada.":::

    A sua **conta Cosmos DB** está ligada à sua subscrição Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Use uma cadeia de ligação para ligar ao Azure Cosmos DB

Pode utilizar uma cadeia de ligação para ligar a um Azure Cosmos DB. Este método suporta apenas APIs SQL e Tabela. Siga estes passos para ligar com uma cadeia de ligação:

1. Encontre **Local e Anexado** na árvore esquerda, clique à direita **nas contas DB do Cosmos** e, em seguida, selecione **Connect to Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Screenshot mostrando o menu suspenso depois de clicar à direita, com Connect to Azure Cosmos D B em destaque.":::

2. Na janela **DB de Connect to Cosmos:**
   1. Selecione a API no menu suspenso.
   1. Cole a corda de ligação na caixa **de cordas Connection.** Para obter a cadeia de ligação primária, consulte [obter a cadeia de ligação](manage-with-powershell.md#list-keys).
   1. Introduza uma **etiqueta de conta** e, em seguida, selecione **Seguinte** para verificar o resumo.
   1. Selecione **Connect** para ligar a conta DB Azure Cosmos.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Screenshot da janela Connect to Cosmos D B, mostrando o menu suspenso da API, a caixa de cordas de ligação e a caixa de etiquetas conta.":::

> [!NOTE]
> Se o Azure Storage Explorer mostrar que a cadeia de ligação DB Azure Cosmos está num formato inválido, certifique-se de que a cadeia de ligação tem um ponto e vírgula ( `;` ) no final. Um exemplo de uma cadeia de conexão DB Azure Cosmos válida seria: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Use um emulador local para ligar ao Azure Cosmos DB

Utilize os seguintes passos para ligar a um Azure Cosmos DB com um emulador. Este método suporta apenas contas SQL.

1. Instale o Emulador Cosmos DB e, em seguida, abra-o. Para instalar o emulador, consulte [o Cosmos DB Emulator](./local-emulator.md).

1. Encontre **Local e Anexado** na árvore esquerda, clique à direita **nas contas de DB do Cosmos** e, em seguida, selecione **Connect to Cosmos DB Emulator**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar à direita, com Connect to Azure Cosmos D B Emulator realçado.":::

1. Na janela **DB de Connect to Cosmos:**
   1. Cole a corda de ligação na caixa **de cordas Connection.** Para obter informações sobre a recuperação da cadeia de ligação primária, consulte [obter a cadeia de ligação](manage-with-powershell.md#list-keys).
   1. Introduza uma **etiqueta de conta** e, em seguida, selecione **Seguinte** para verificar o resumo.
   1. Selecione **Connect** para ligar a conta DB Azure Cosmos.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Screenshot da janela Connect to Cosmos D B, mostrando a caixa de cordas de ligação e a caixa de etiquetas conta.":::

## <a name="azure-cosmos-db-resource-management"></a>Gestão de recursos do Azure Cosmos DB

Utilize as seguintes operações para gerir uma conta DB da Azure Cosmos:

* Abra a conta no portal Azure.
* Adicione o recurso à lista de Acesso Rápido.
* Pesquisar e refrescar recursos.
* Criar e eliminar bases de dados.
* Criar e apagar coleções.
* Criar, editar, excluir e filtrar documentos.
* Gerir procedimentos armazenados, gatilhos e funções definidas pelo utilizador.

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Pode clicar com o direito numa subscrição no painel Explorer para executar muitas tarefas de ação rápida, por exemplo:

* Clique com o botão direito numa conta ou base de dados DB do Azure Cosmos e, em seguida, selecione **Abrir no Portal** para gerir o recurso no navegador no portal Azure.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar à direita, com Open no Portal realçado.":::

* Clique com o direito numa conta DB, base de dados ou recolha do Azure Cosmos e, em seguida, **selecione Adicionar ao Acesso Rápido** para adicioná-lo ao menu Acesso Rápido.

* Selecione **Procurar aqui** para ativar a pesquisa de palavras-chave no caminho selecionado.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Screenshot mostrando a caixa de pesquisa realçada.":::

### <a name="database-and-collection-management"></a>Gestão de bases de dados e coleções

#### <a name="create-a-database"></a>Criar uma base de dados

1. Clique com o botão direito na conta DB do Azure Cosmos e, em seguida, selecione **Criar Base de Dados**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar no botão direito, com a Base de Dados Create em destaque.":::

1. Introduza o nome da base de dados e, em seguida, prima **Enter** para completar.

#### <a name="delete-a-database"></a>Eliminar uma base de dados

1. Clique com o botão direito na base de dados e, em seguida, selecione **Eliminar Base de Dados**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar à direita, com a Base de Dados delete realçada.":::

1. Selecione **Sim** na janela pop-up. O nó da base de dados é eliminado e a conta do Azure Cosmos DB é atualizada automaticamente.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Screenshot da janela de confirmação com o botão Sim realçado.":::

#### <a name="create-a-collection"></a>Criar uma coleção

1. Clique com o botão direito na sua base de dados e, em seguida, selecione **Create Collection**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar à direita, com a Coleção Create em destaque.":::

1. Na janela Create Collection, insira as informações solicitadas, como **iD** de recolha e **capacidade de armazenamento,** e assim por diante. Selecione **OK** para terminar.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Screenshot da janela Create Collection, mostrando a caixa I D da coleção e os botões de capacidade de armazenamento.":::

1. Selecione **Unlimited** para que possa especificar uma chave de partição e, em seguida, selecione **OK** para terminar.

   > [!NOTE]
   > Se for utilizada uma chave de partição quando se cria uma coleção, uma vez concluída a criação, não pode alterar o valor da chave de partição na coleção.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Screenshot da janela Create Collection, mostrando ilimitado selecionado para capacidade de armazenamento, e a caixa de chaves de partição realçada.":::

#### <a name="delete-a-collection"></a>Delete a collection (Eliminar uma coleção)

- Clique com o botão direito na coleção, selecione **Delete Collection** e, em seguida, selecione **Sim** na janela pop-up.

    O nó da coleção é eliminado e a base de dados é atualizada automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Screenshot mostrando o menu que aparece depois de clicar à direita, com a Delete Collection em destaque.":::

### <a name="document-management"></a>Gestão de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos

- Abrir **Documentos** no painel esquerdo, selecionar **Novo Documento,** editar o conteúdo no painel direito e, em seguida, selecionar **Guardar**.
- Também pode atualizar um documento existente e, em seguida, selecionar **Guardar**. Para descartar alterações, **selecione Descartar**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Screenshot mostrando documentos destacados no painel esquerdo. No painel direito, destacam-se novos documentos, guardar e descartar.":::

#### <a name="delete-a-document"></a>Eliminar um documento

* Selecione o botão **Eliminar** para eliminar o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos

* Para editar o filtro de documentos, introduza uma [consulta SQL](./sql-query-getting-started.md)e, em seguida, selecione **Apply**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Screenshot do painel direito, mostrando botões Filter e Apply, o número de ID e a caixa de consulta realçada.":::

### <a name="graph-management"></a>Gestão de gráficos

#### <a name="create-and-modify-a-vertex"></a>Criar e modificar um vértice

* Para criar um novo vértice, abra o **Gráfico** a partir do painel esquerdo, selecione **New Vertex,** edite o conteúdo e, em seguida, selecione **OK**.
* Para modificar um vértice existente, selecione o ícone da caneta no painel direito.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Screenshot mostra gráfico selecionado no painel esquerdo e mostrando New Vertex e o ícone da caneta realçado no painel direito.":::

#### <a name="delete-a-graph"></a>Eliminar um gráfico

* Para eliminar um vértice, selecione o ícone do recipiente de reciclagem ao lado do nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráfico

* Para editar o filtro de gráfico, introduza uma [consulta de gremlin](gremlin-support.md)e, em seguida, selecione **Apply Filter**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Screenshot mostra gráfico selecionado no painel esquerdo e mostra o Filtro de Aplicação e a caixa de consulta realçada no painel direito.":::

### <a name="table-management"></a>Gestão de tabelas

#### <a name="create-and-modify-a-table"></a>Criar e modificar uma tabela

* Para criar uma nova tabela:
   1. No painel esquerdo, abra **as Entidades** e, em seguida, selecione **Adicionar**.
   1. Na caixa de diálogo **'Entidade adicionar',** edite o conteúdo.
   1. Selecione o botão **Adicionar Propriedade** para adicionar uma propriedade.
   1. Selecione **Inserir**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Screenshot mostrando Entidades destacadas no painel esquerdo, e mostrando Adicionar, Editar, Adicionar Propriedade e Inserir em destaque no painel direito.":::

* Para modificar uma tabela, **selecione Editar,** modifique o conteúdo e, em seguida, selecione **Update**.

   

#### <a name="import-and-export-table"></a>Importar e exportar tabela

* Para importar, selecione o botão **Importar** e, em seguida, escolha uma tabela existente.
* Para exportar, selecione o botão **Exportação** e, em seguida, escolha um destino.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Screenshot mostrando os botões de importação e exportação realçados no painel direito.":::

#### <a name="delete-entities"></a>Eliminar entidades

* Selecione as entidades e, em seguida, selecione o botão **Eliminar.**

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Screenshot mostrando o botão Delete realçado no painel direito, e uma janela pop-up de confirmação com Sim realçado.":::

#### <a name="query-a-table"></a>Consulta de uma mesa

- Selecione o botão **Desíduo,** insira uma condição de consulta e, em seguida, selecione o botão **'Executar'.** Para fechar o painel de perguntas, selecione o **botão Desativação.**

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Screenshot do painel direito, mostrando o botão 'Executar consulta' e o botão Desativação De Fecho realçado.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerir procedimentos armazenados, acionadores e UDFs

* Para criar um procedimento armazenado:
  1. Na árvore esquerda, clique com o botão direito **Procedimentos armazenados** e, em seguida, selecione **Create Stored Procedure**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Screenshot do painel esquerdo, mostrando o menu que aparece depois de clicar à direita, com o Procedimento Armazenado em destaque.":::
  
  1. Introduza um nome à esquerda, introduza os scripts de procedimento armazenados no painel direito e, em seguida, selecione **Criar**.
  
* Para editar um procedimento armazenado existente, clique duas vezes no procedimento, faça a atualização e, em seguida, selecione **Update** para guardar. Também pode **selecionar Descartar** para cancelar a alteração.

* As operações para **Triggers** e **UDF** são semelhantes aos **procedimentos armazenados.**

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se soluções para questões comuns que surgem quando utiliza o Azure Cosmos DB no Storage Explorer.

### <a name="sign-in-issues"></a>Problemas de início de sessão

Primeiro, reinicie a sua aplicação para ver se isso corrige o problema. Se o problema persistir, continuem a resolver problemas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia de certificados

Há algumas razões para estar a ver este erro, as duas mais comuns são:

* Estás por trás de um *representante transparente.* Alguém, como o seu departamento de TI, interceta o tráfego HTTPS, desencripta-o e, em seguida, encripta-o usando um certificado auto-assinado.

* Estás a executar software, como software antivírus. O software injeta um certificado TLS/SSL auto-assinado nas mensagens HTTPS que recebe.

Quando o Storage Explorer encontra um certificado auto-assinado, não sabe se a mensagem HTTPS que recebe é adulterada. Se tiver uma cópia do certificado auto-assinado, pode dizer ao Storage Explorer para confiar nele. Se não tem a certeza de quem injetou o certificado, então pode seguir estes passos para tentar descobrir:

1. Instalar OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): Qualquer uma das versões luminosas está bem.
     - macOS e Linux: Deve ser incluído com o seu sistema operativo.

1. Executar OpenSSL:
    * Windows: Vá ao diretório de instalação, em seguida/ **bin/**, em seguida, clique duas vezes **openssl.exe**.
    * Mac e Linux: Execute **abertura de** um terminal.
1. Executar `s_client -showcerts -connect microsoft.com:443` .
1. Procure certificados autoassinados. Se não tem a certeza, que é auto-assinado, procure em qualquer lugar que o sujeito ("s:") e emitente ("i:") sejam os mesmos.
1. Se encontrar certificados auto-assinados, copie e cole tudo, incluindo **-----BEGIN CERTIFICATE-----** **a -----END CERTIFICATE-----** para um novo . Arquivo CER para cada um.
1. Abrir o Explorador de Armazenamento e, em seguida, ir para **editar**  >  **certificados SSL**  >  **Certificados de Importação Certificados**. Utilize o apanhador de ficheiros para encontrar, selecionar e, em seguida, abrir o . Ficheiros CER que criou.

Se não encontrar certificados auto-assinados, pode enviar feedback para mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível obter subscrições

Se não conseguir recuperar as suas subscrições depois de iniciar sôm, experimente estas sugestões:

* Verifique se a sua conta tem acesso às subscrições. Para isso, inscreva-se no [portal Azure.](https://portal.azure.com/)
* Certifique-se de que se inscreveu no ambiente correto:
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Alemanha](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * Pilha de ambiente/azul personalizado
* Se estiver por detrás de um representante, certifique-se de que o representante do Explorador de Armazenamento está devidamente configurado.
* Retire a conta e, em seguida, adicione-a novamente.
* Elimine os seguintes ficheiros do seu diretório doméstico (tais como: C:\Users\ContosoUser) e, em seguida, adicione novamente a conta:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Prima a tecla F12 para abrir a consola do desenvolvedor. Observe a consola para obter mensagens de erro quando iniciar sôs a saúde.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Screenshot da consola de ferramentas de desenvolvimento, mostrando consola em destaque.":::

#### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se não conseguir ver a página de autenticação:

* Dependendo da velocidade da sua ligação, pode demorar algum tempo até que a página de inscrição seja carregada. Aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Se estiver por detrás de um representante, certifique-se de que o representante do Explorador de Armazenamento está devidamente configurado.
* Na consola de ferramentas de desenvolvimento (F12), observe as respostas para ver se encontra alguma pista do porquê da autenticação não estar a funcionar.

#### <a name="cant-remove-an-account"></a>Não é possível remover uma conta

Se não conseguir remover uma conta, ou se o link reauthenticato não fizer nada:

* Elimine os seguintes ficheiros do seu diretório de casa e, em seguida, adicione novamente a conta:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Se quiser remover os recursos de Armazenamento SAS anexados, elimine:
  * a pasta %AppData%/StorageExplorer do Windows
  * /Utilizadores/<your_name>/Biblioteca/Aplicação SUpport/StorageExplorer para macOS
  * ~/.config/StorageExplorer para Linux
  
  > [!NOTE]
  > Se eliminar estes ficheiros, **deve voltar a entrar em todas as suas credenciais**.

### <a name="httphttps-proxy-issue"></a>Problema de procuração http/https

Não é possível listar os nós DB do Azure Cosmos na árvore esquerda quando configurar um representante HTTP/HTTPS em ASE. Você pode usar O explorador de dados DB Azure Cosmos no portal Azure como um work-around.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema do nó "Desenvolvimento" no nó "Local e Anexado"

Não há resposta depois de selecionar o nó **de Desenvolvimento** sob o nó Local **e Anexado** na árvore esquerda. O comportamento é esperado.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Screenshot mostrando o nó de Desenvolvimento selecionado.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Anexar uma conta DB Azure Cosmos no erro do nó **local e anexo**

Se vir o seguinte erro depois de anexar uma conta DB Azure Cosmos no nó **Local e Anexado,** certifique-se de que está a utilizar a cadeia de ligação correta.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Screenshot da Janela pop-up de erro de recursos infantis, indicando getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Erro ao expandir o nó do Azure Cosmos DB

Pode ver o seguinte erro quando tentar expandir os nós na árvore esquerda.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Screenshot da janela pop-up de erro de recursos infantis, indicando não conseguir ligar-se a esta conta Cosmos D B.":::

Experimente estas sugestões:

* Verifique se a conta DB da Azure Cosmos está em andamento. Tente novamente quando a conta está sendo criada com sucesso.
* Se a conta estiver sob os nós **de Acesso Rápido** ou Local e **Anexo,** verifique se a conta está eliminada. Em caso afirmativo, é necessário retirar manualmente o nó.

## <a name="next-steps"></a>Passos seguintes

* Veja este vídeo para ver como usar a Azure Cosmos DB no Azure Storage Explorer: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Explorador de Armazenamento e como ligar mais serviços em [Introdução ao Explorador de Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
