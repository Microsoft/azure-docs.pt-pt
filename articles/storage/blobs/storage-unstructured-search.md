---
title: 'Tutorial: Procurar dados não estruturados no armazenamento de Blobs do Azure'
description: 'Tutorial: A procurar dados não estruturados no armazenamento de Blobs com o Azure search.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: acf01788a1ac8ed8ca476941c402801abfbd8adf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011312"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Tutorial: Procurar dados não estruturados no armazenamento na cloud

Neste tutorial, irá aprender a procurar dados não estruturados usando [Azure Search](../../search/search-what-is-azure-search.md), usando dados armazenados no armazenamento de Blobs do Azure. Os dados não estruturados são os dados que não estão organizados de maneira predefinida ou não têm um modelo de dados. Um exemplo é um arquivo. txt.

Este tutorial requer que tenha uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de recursos
> * Criar uma conta de armazenamento
> * Criar um contentor
> * Carregar dados para o contentor
> * Criar um serviço de pesquisa através do portal
> * Ligar um serviço de pesquisa a uma conta de armazenamento
> * Criar uma origem de dados
> * Configurar o índice
> * Criar um indexador
> * Utilizar o serviço de pesquisa para pesquisar o seu contentor

## <a name="prerequisites"></a>Pré-requisitos

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção para criar um novo grupo de recursos ou utilize um grupo de recursos existente. Este tutorial cria um novo grupo de recursos.

Inicie sessão no [portal do Azure](https://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Um conjunto de dados de exemplo foi preparado para si, para que pode fazer usá-lo para este tutorial. Baixe [clinical trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) e deszipe-o para a sua própria pasta.

O exemplo consiste em ficheiros de texto obtidos a partir de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Este tutorial utiliza-os como arquivos de texto de exemplo que serão pesquisados utilizando os serviços do Azure Search.

## <a name="create-a-container"></a>Criar um contentor

Os contentores são semelhantes às pastas e são utilizados para armazenar blobs.

Neste tutorial, vai utilizar um único contentor para armazenar os ficheiros de texto obtidos do clinicaltrials.gov.

1. Aceda à sua conta de armazenamento no portal do Azure.

2. Selecione **procurar blobs** sob **serviço Blob**.

3. Adicione um novo contentor.

4. Nome do contentor **dados** e selecione **contentor** para o nível de acesso público.

5. Selecione **OK** para criar o contentor.

   ![Pesquisa não estruturada](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Carregar os dados de exemplo

Agora que tem um contentor, pode carregar os dados de exemplo para o mesmo.

1. Selecione o contentor e selecione **Carregar**.

2. Selecione o ícone de pasta azul junto a **ficheiros** campo e navegue até à pasta local onde extraiu os dados de exemplo.

3. Selecione todos os ficheiros extraídos e selecione **aberto**.

4. Selecione **Carregar** para iniciar o processo de carregamento.

   ![Pesquisa não estruturada](media/storage-unstructured-search/upload.png)

O processo de carregamento pode demorar um momento.

Depois de concluído, volte para o seu contentor de dados para confirmar que os ficheiros de texto foram carregados.

  ![Pesquisa não estruturada](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Criar um serviço de pesquisa

O Azure Search é uma solução de pesquisa-como-serviço cloud que dá aos programadores APIs e ferramentas para adicionar uma experiência de pesquisa com os seus dados.

Para este tutorial, utilize um serviço de pesquisa para pesquisar os arquivos de texto obtidos a partir do clinicaltrials.gov.

1. Aceda à sua conta de armazenamento no portal do Azure.

2. Role para baixo e selecione **adicionar Azure Search** sob **serviço BLOB**.

3. Em **Importar Dados**, selecione **Escolha o seu serviço**.

4. Selecione **Clique aqui para criar um novo serviço de pesquisa**.

5. Dentro de **Novo Serviço de Pesquisa**, introduza um nome exclusivo para o seu serviço de pesquisa no campo **URL**.

6. Sob **grupo de recursos**, selecione **utilizar existente** e escolha o grupo de recursos que criou anteriormente.

7. Para **escalão de preço**, selecione a **gratuito** escalão e clique em **selecionar**.

8. Selecione **Criar** para criar o serviço de pesquisa.

   ![Pesquisa não estruturada](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Ligar o serviço de pesquisa ao contentor

Agora que tem um serviço de pesquisa, pode anexá-lo ao armazenamento de blobs. Esta secção descreve o processo de escolha de uma origem de dados, criação de um índice e criação de um indexador.

1. Aceda à sua conta de armazenamento.

2. Selecione **ao Azure Search** sob **serviço BLOB**.

3. Selecione **serviço de pesquisa** dentro **importar dados**e, em seguida, clique no serviço de pesquisa que criou na secção anterior. Esta ação abre **nova origem de dados**.

### <a name="create-a-data-source"></a>Criar uma origem de dados

  Uma origem de dados especifica quais os dados a indexar e como aceder aos dados. Uma origem de dados pode ser utilizada várias vezes pelo mesmo serviço de pesquisa.

1. Introduza um nome para a origem de dados. Em **Dados a extrair**, selecione **Conteúdo e Metadados**. A origem de dados especifica as partes do blob que estão indexadas.

2. Uma vez que os blobs que está a utilizar são ficheiros de texto, definido **modo de análise** ao **texto**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/datasources.png)

3. Selecione **Contentor de Armazenamento** para listar as contas de armazenamento disponíveis.

4. Selecione a sua conta de armazenamento e, em seguida, selecione o contentor que criou anteriormente.

   ![Pesquisa não estruturada](media/storage-unstructured-search/datacontainer.png)

5. Clique em **selecionar** para regressar à **nova origem de dados**e selecione **OK** para continuar.

### <a name="configure-the-index"></a>Configurar o índice

  Um índice é uma coleção de campos da sua origem de dados que podem ser pesquisados. Definir e configurar os parâmetros nesses campos para que o serviço de pesquisa Saiba de que forma os dados devem ser pesquisados.

1. Na **importar dados**, selecione **personalizar o índice de destino**.

2. Introduza um nome para o índice no campo **Nome do índice**.

3. Selecione o **recuperável** caixa de verificação do atributo sob **metadata_storage_name**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/valuestoselect.png)

4. Selecione **OK**, o que exibe **criar um indexador**.

Os parâmetros do seu índice e os atributos que fornecer a esses parâmetros são importantes. Os parâmetros especificam *o que* especificam dados para armazenar e os atributos *como* para armazenar esses dados.

A coluna **NOME DO CAMPO** contém os parâmetros. A tabela seguinte fornece uma listagem dos atributos disponíveis e as respetivas descrições.

#### <a name="field-attributes"></a>Atributos do campo

| Atributo | Descrição |
| --- | --- |
| *Chave* |Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja utilizado nas consultas de filtro.  |
| *Ordenável* |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| *Facetável* |Permite a um campo ser utilizado numa estrutura de navegação por facetas para filtragem do utilizador auto-direcionada. Normalmente, campos com valores repetitivos que pode utilizar em conjunto para documentos de grupo (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como pesquisável no texto completo. |

### <a name="create-an-indexer"></a>Criar um indexador

  Um indexador liga uma origem de dados a um índice de pesquisa e fornece uma agenda para voltar a indexar os seus dados.

1. Introduza um nome no campo **Nome** e selecione **OK**.

   ![Pesquisa não estruturada](media/storage-unstructured-search/exindexer.png)

2. São recuperados **importar dados**. Selecione **OK** para concluir o processo de ligação.

Ligou com êxito o blob ao serviço de pesquisa. Demora alguns minutos para o portal mostrar que o índice foi preenchido. No entanto, o serviço de pesquisa começa a indexação imediatamente, para que possa começar a procurar imediatamente.

## <a name="search-your-text-files"></a>Pesquisar os ficheiros de texto

Para pesquisar os ficheiros, abra o explorador de procura dentro do índice do seu serviço de pesquisa criado recentemente.

Os passos seguintes mostram-lhe onde encontrar o explorador de procura e fornecem-lhe algumas consultas de exemplo:

1. Vá para todos os recursos e localizar o serviço de pesquisa criado recentemente.

   ![Pesquisa não estruturada](media/storage-unstructured-search/exampleurl.png)

2. Selecione o seu índice para abri-lo.

   ![Pesquisa não estruturada](media/storage-unstructured-search/overview.png)

3. Selecione **Explorador de pesquisa** para abrir o Explorador de pesquisa, onde pode fazer consultas em direto nos seus dados.

   ![Pesquisa não estruturada](media/storage-unstructured-search/indexespane.png)

4. Selecione **Pesquisar** enquanto o campo de cadeia de consulta está vazio. Uma consulta vazia devolve *todos* os dados dos seus blobs.

   ![Pesquisa não estruturada](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Efetua uma pesquisa de texto completo

Introduza **miopia** no **cadeia de consulta** campo e selecione **pesquisa**. Este passo inicia uma pesquisa dos conteúdos dos arquivos e devolve um subconjunto deles, que contém a palavra "Miopia."

  ![Pesquisa não estruturada](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Efetua uma pesquisa de propriedades do sistema

Além de uma pesquisa de texto completo, pode criar consultas que procurar por propriedades do sistema usando o `$select` parâmetro.

ENTER `$select=metadata_storage_name` para a cadeia de consulta e prima **Enter**. Isso faz com que esse campo específico retornar.

A cadeia de consulta está a modificar diretamente o URL, pelo que não são permitidos espaços. Para pesquisar vários campos, utilize uma vírgula, como em: `$select=metadata_storage_name,metadata_storage_path`

O parâmetro `$select` só pode ser utilizado em campos que foram marcados como recuperáveis ao definir o seu índice.

  ![Pesquisa não estruturada](media/storage-unstructured-search/metadatasearchunstructured.png)

Concluiu este tutorial e tem um conjunto pesquisável de dados não estruturados.

## <a name="clean-up-resources"></a>Limpar recursos

É a maneira mais fácil para remover os recursos que criou ao eliminar o grupo de recursos. Remover o grupo de recursos também elimina todos os recursos incluídos no grupo. No exemplo seguinte, remover o grupo de recursos remove a conta de armazenamento e o próprio grupo de recursos.

1. No portal do Azure, aceda à lista de grupos de recursos na sua subscrição.
2. Selecione o grupo de recursos que pretende eliminar.
3. Selecione o **eliminar grupo de recursos** e digitar o nome do grupo de recursos no campo de eliminação.
4. Selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Siga esta ligação para saber mais sobre a indexar documentos com o Azure Search:

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure com o Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)
