---
title: Acessar o armazenamento de BLOBs do Azure de Azure Databricks usando Azure Key Vault tutorial
description: Este tutorial descreve como acessar o armazenamento de BLOBs do Azure de Azure Databricks usando segredos armazenados em um cofre de chaves.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 45c5be8b203daf21697f3cb6dad4ecadb6449339
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976518"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Tutorial: Acessar o armazenamento de BLOBs do Azure de Azure Databricks usando Azure Key Vault

Este tutorial descreve como acessar o armazenamento de BLOBs do Azure de Azure Databricks usando segredos armazenados em um cofre de chaves.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento e um contêiner de BLOB
> * Criar um Azure Key Vault e adicionar um segredo
> * Criar um espaço de trabalho Azure Databricks e adicionar um escopo secreto
> * Acessar seu contêiner de blob de Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser executado usando a **assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, vá para seu perfil e altere sua assinatura para pré- **pago**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para vCPUs em sua região. Ao criar seu espaço de trabalho Azure Databricks, você pode selecionar o tipo de preço de **avaliação (Premium-14 dias gratuitos DBUs)** para dar ao espaço de trabalho o acesso Premium Azure Databricks DBUs por 14 dias.

## <a name="create-a-storage-account-and-blob-container"></a>Criar uma conta de armazenamento e um contêiner de BLOB

1. Na portal do Azure, selecione **criar um recurso** > **armazenamento**. Em seguida, selecione **conta de armazenamento**.

   ![Localizar recurso de conta de armazenamento do Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecione sua assinatura e grupo de recursos ou crie um novo grupo de recursos. Em seguida, insira um nome de conta de armazenamento e escolha um local. Selecione **revisão + criar**.

   ![Definir propriedades da conta de armazenamento](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Se a validação não for bem-sucedida, resolva os problemas e tente novamente. Se a validação for bem-sucedida, selecione **criar** e aguarde até que a conta de armazenamento seja criada.

4. Navegue até sua conta de armazenamento recém-criada e selecione **BLOBs** em **Serviços** na página **visão geral** . Em seguida, selecione **+ contêiner** e insira um nome de contêiner. Selecione **OK**.

   ![Criar novo contentor](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Localize um arquivo que você deseja carregar em seu contêiner de armazenamento de BLOBs. Se você não tiver um arquivo, use um editor de texto para criar um novo arquivo de texto com algumas informações. Neste exemplo, um arquivo chamado **HW. txt** contém o texto "Olá, mundo". Salve o arquivo de texto localmente e carregue-o no contêiner de armazenamento de BLOBs.

   ![Carregar arquivo para contêiner](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Retorne à sua conta de armazenamento e selecione **chaves de acesso** em **configurações**. Copie o **nome da conta de armazenamento** e a **chave 1** em um editor de texto para uso posterior neste tutorial.

   ![Localizar chaves de acesso da conta de armazenamento](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Criar um Azure Key Vault e adicionar um segredo

1. Na portal do Azure, selecione **criar um recurso** e insira **Key Vault** na caixa de pesquisa.

   ![Criar uma caixa de pesquisa de recursos do Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. O recurso Key Vault é selecionado automaticamente. Selecione **Criar**.

   ![Criar um recurso de Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na página **criar cofre de chaves** , insira as informações a seguir e mantenha os valores padrão para os campos restantes:

   |Propriedade|Description|
   |--------|-----------|
   |Nome|Um nome exclusivo para o cofre de chaves.|
   |Subscription|Escolha uma subscrição.|
   |Resource group|Escolha um grupo de recursos ou crie um novo.|
   |Location|Escolher uma localização.|

   ![Propriedades do cofre de chaves do Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Depois de fornecer as informações acima, selecione **Criar**. 

4. Navegue até o cofre de chaves recém-criado no portal do Azure e selecione **segredos**. Em seguida, selecione **+ gerar/importar**. 

   ![Gerar novo segredo do cofre de chaves](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na página **criar um segredo** , forneça as seguintes informações e mantenha os valores padrão para os campos restantes:

   |Propriedade|Value|
   |--------|-----------|
   |Opções de carregamento|Manual|
   |Nome|Nome amigável para sua chave de conta de armazenamento.|
   |Value|key1 da sua conta de armazenamento.|

   ![Propriedades para novo segredo do cofre de chaves](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Salve o nome da chave em um editor de texto para uso posterior neste tutorial e selecione **criar**. Em seguida, navegue até o menu **Propriedades** . Copie o **nome DNS** e a **ID de recurso** para um editor de texto para uso posterior no tutorial.

   ![Copiar Azure Key Vault o nome DNS e a ID do recurso](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Criar um espaço de trabalho Azure Databricks e adicionar um escopo secreto

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Em **Azure Databricks serviço**, forneça os seguintes valores para criar um espaço de trabalho do databricks.

   |Propriedade  |Descrição  |
   |---------|---------|
   |Nome da área de trabalho     | Indique um nome para a sua área de trabalho do Databricks        |
   |Subscription     | Na lista pendente, selecione a sua subscrição do Azure.        |
   |Resource group     | Selecione o mesmo grupo de recursos que contém o cofre de chaves. |
   |Location     | Selecione o mesmo local que o seu Azure Key Vault. Para todas as regiões disponíveis, consulte [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
   |Escalão de Preço     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Propriedades do espaço de trabalho do databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecione **Criar**.

3. Navegue até o recurso de Azure Databricks recém-criado no portal do Azure e selecione **Iniciar espaço de trabalho**.

   ![Iniciar Azure Databricks espaço de trabalho](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Quando o espaço de trabalho do Azure Databricks estiver aberto em uma janela separada, acrescente **#secrets/createscope** à URL. A URL deve ter o seguinte formato: 

   **https://< \location >. azuredatabricks. net/? o = < \id > #secrets/createscope**.

5. Insira um nome de escopo e insira o Azure Key Vault nome DNS e a ID de recurso que você salvou anteriormente. Salve o nome do escopo em um editor de texto para uso posterior neste tutorial. Em seguida, selecione **Criar**.

   ![Criar escopo secreto no espaço de trabalho Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Acessar seu contêiner de blob de Azure Databricks

1. Na home page do espaço de trabalho Azure Databricks, selecione **novo cluster** em **tarefas comuns**.

   ![Criar um novo Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Insira um nome de cluster e selecione **criar cluster**. A criação do cluster leva alguns minutos para ser concluída.

3. Depois que o cluster for criado, navegue até o home page do seu espaço de trabalho Azure Databricks, selecione **novo bloco de anotações** em **tarefas comuns**.

   ![Criar um novo Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Insira um nome de bloco de anotações e defina o idioma como Python. Defina o cluster para o nome do cluster que você criou na etapa anterior.

5. Execute o comando a seguir para montar o contêiner de armazenamento de BLOBs. Lembre-se de alterar os valores das seguintes propriedades:

   * seu nome de contêiner
   * seu-Storage-Account-Name
   * nome de montagem
   * chave de configuração
   * nome do escopo
   * nome da chave

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **Mount-Name** é um caminho DBFS que representa onde o contêiner de armazenamento de BLOBs ou uma pasta dentro do contêiner (especificado na fonte) serão montados.
   * **conf-Key** pode ser `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` ou`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **Scope-Name** é o nome do escopo secreto que você criou na seção anterior. 
   * **nome-da-chave** é o nome do segredo que você criou para a chave da conta de armazenamento no cofre de chaves.

   ![Criar montagem de armazenamento de BLOBs no bloco de anotações](./media/store-secrets-azure-key-vault/command1.png)

6. Execute o comando a seguir para ler o arquivo de texto em seu contêiner de armazenamento de BLOBs para um dataframe. Altere os valores no comando para corresponder ao nome de montagem e ao nome do arquivo.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Ler arquivo em dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. Use o comando a seguir para exibir o conteúdo do arquivo.

   ```python
   df.show()
   ```
   ![Mostrar dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Para desmontar o armazenamento de BLOBs, execute o seguinte comando:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Desmontar conta de armazenamento](./media/store-secrets-azure-key-vault/command4.png)

9. Observe que depois que a montagem tiver sido desmontada, você não poderá mais ler a partir da sua conta de armazenamento de BLOBs.

   ![Erro de desmontagem da conta de armazenamento](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua todo o grupo de recursos com as seguintes etapas:

1. No menu à esquerda no portal do Azure, selecione grupos de **recursos** e navegue até o grupo de recursos.

2. Selecione **excluir grupo de recursos** e digite o nome do grupo de recursos. Em seguida, selecione **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para saber como implementar um ambiente de databricks injetado VNet com um ponto de extremidade de serviço habilitado para Cosmos DB.
> [!div class="nextstepaction"]
> [Tutorial: Implementar Azure Databricks com um ponto de extremidade Cosmos DB](service-endpoint-cosmosdb.md)
