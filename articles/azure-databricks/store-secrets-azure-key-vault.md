---
title: Tutorial - Armazenamento de blob de acesso usando cofre chave usando tijolos de dados Azure
description: Este tutorial descreve como aceder ao Armazenamento Azure Blob a partir de Databricks Azure usando segredos armazenados num cofre chave.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889060"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Tutorial: Armazenamento de Blob De acesso Azure a partir de Tijolos de Dados Azure usando cofre chave Azure

Este tutorial descreve como aceder ao Armazenamento Azure Blob a partir de Databricks Azure usando segredos armazenados num cofre chave.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento e um recipiente de bolha
> * Crie um Cofre de Chave Azure e adicione um segredo
> * Criar um espaço de trabalho Azure Databricks e adicionar um âmbito secreto
> * Aceda ao seu recipiente de blob a partir de Tijolos de Dados Azure

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de teste gratuito do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), e [solicite um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota para vCPUs na sua região. Ao criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Trial (Premium - 14 dias grátis)** para dar ao espaço de trabalho acesso a DBUs de dados Premium Azure gratuitos durante 14 dias.

## <a name="create-a-storage-account-and-blob-container"></a>Criar uma conta de armazenamento e um recipiente de bolha

1. No portal Azure, selecione Criar um**armazenamento**de **recursos.** >  Em seguida, selecione **a conta de armazenamento**.

   ![Encontre o recurso da conta de armazenamento Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecione o seu grupo de subscrição e recursos ou crie um novo grupo de recursos. Em seguida, insira o nome de uma conta de armazenamento e escolha um local. Selecione **Rever + Criar**.

   ![Definir propriedades da conta de armazenamento](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Se a validação não for bem sucedida, aborde os problemas e tente novamente. Se a validação for bem sucedida, selecione **Criar** e aguarde a criação da conta de armazenamento.

4. Navegue para a sua conta de armazenamento recém-criada e selecione **Blobs** em **Serviços** na página **Overview.** Em seguida, selecione **+ Recipiente** e introduza um nome de recipiente. Selecione **OK**.

   ![Criar novo recipiente](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Localize um ficheiro que pretenda enviar para o seu recipiente de armazenamento de bolhas. Se não tiver um ficheiro, utilize um editor de texto para criar um novo ficheiro de texto com alguma informação. Neste exemplo, um ficheiro chamado **hw.txt** contém o texto "olá mundo". Guarde o seu ficheiro de texto localmente e carregue-o para o seu recipiente de armazenamento de bolhas.

   ![Upload ficheiro para contentor](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Volte à sua conta de armazenamento e selecione **chaves de acesso** em **Definições**. Copiar Nome da **conta de armazenamento** e chave **1** para um editor de texto para posterior utilização neste tutorial.

   ![Encontre chaves de acesso à conta de armazenamento](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Crie um Cofre de Chave Azure e adicione um segredo

1. No portal Azure, selecione **Criar um recurso** e introduza o Cofre **chave** na caixa de pesquisa.

   ![Criar uma caixa de pesquisa de recursos Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. O recurso Key Vault é automaticamente selecionado. Selecione **Criar**.

   ![Criar um recurso key vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na página do **cofre de chaves Criar,** introduza as seguintes informações e mantenha os valores predefinidos para os restantes campos:

   |Propriedade|Descrição|
   |--------|-----------|
   |Nome|Um nome único para o seu cofre chave.|
   |Subscrição|Escolha uma subscrição.|
   |Grupo de recursos|Escolha um grupo de recursos ou crie um novo.|
   |Localização|Escolher uma localização.|

   ![Propriedades do cofre de chaves Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Depois de fornecer as informações acima, selecione **Criar**. 

4. Navegue para o seu cofre chave recém-criado no portal Azure e selecione **Secrets**. Em seguida, **selecione + Generate/Import**. 

   ![Gerar novo segredo de cofre chave](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na página Criar uma página **secreta,** forneça as seguintes informações e mantenha os valores predefinidos para os restantes campos:

   |Propriedade|Valor|
   |--------|-----------|
   |Opções de upload|Manual|
   |Nome|Nome amigável para a chave da sua conta de armazenamento.|
   |Valor|key1 da sua conta de armazenamento.|

   ![Propriedades para novo segredo de cofre chave](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Guarde o nome chave num editor de texto para ser utilizado mais tarde neste tutorial e selecione **Criar**. Em seguida, navegue para o menu **Propriedades.** Copie o Id de Nome e **Recurso** **DNS** para um editor de texto para uso posterior no tutorial.

   ![Copiar nome DNS do cofre de chaves azure e ID de recursos](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Criar um espaço de trabalho Azure Databricks e adicionar um âmbito secreto

1. No portal Azure, selecione **Criar um recurso** > **Analytics** > **Azure Databricks**.

    ![Tijolos de dados no portal Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. No serviço **Azure Databricks,** forneça os seguintes valores para criar um espaço de trabalho databricks.

   |Propriedade  |Descrição  |
   |---------|---------|
   |Nome da área de trabalho     | Indique um nome para a sua área de trabalho do Databricks        |
   |Subscrição     | Na lista pendente, selecione a sua subscrição do Azure.        |
   |Grupo de recursos     | Selecione o mesmo grupo de recursos que contém o seu cofre chave. |
   |Localização     | Selecione o mesmo local que o seu Cofre de Chaves Azure. Para todas as regiões disponíveis, consulte [os serviços Azure disponíveis por região.](https://azure.microsoft.com/regions/services/)        |
   |Escalão de Preço     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Propriedades do espaço de trabalho de Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecione **Criar**.

3. Navegue para o seu recurso Azure Databricks recém-criado no portal Azure e selecione **Launch Workspace**.

   ![Lançar espaço de trabalho Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Assim que o seu espaço de trabalho Azure Databricks estiver aberto numa janela separada, apreenda **#secrets/criarOScope** para o URL. O URL deve ter o seguinte formato: 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Introduza um nome de âmbito e introduza o nome DNS do Cofre de Chave Azure e o ID de Recursos que guardou anteriormente. Guarde o nome de âmbito num editor de texto para ser utilizado mais tarde neste tutorial. Em seguida, selecione **Criar**.

   ![Criar âmbito secreto no espaço de trabalho azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Aceda ao seu recipiente de blob a partir de Tijolos de Dados Azure

1. A partir da página inicial do seu espaço de trabalho Azure Databricks, selecione **New Cluster** em **tarefas comuns**.

   ![Crie um novo caderno Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Introduza um nome de cluster e selecione **Criar cluster**. A criação do cluster leva alguns minutos para ser concluída.

3. Assim que o cluster for criado, navegue para a página inicial do seu espaço de trabalho Azure Databricks, selecione **Novo Caderno** em **tarefas comuns**.

   ![Crie um novo caderno Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Introduza um nome de caderno e coloque a língua em Python. Detete o cluster com o nome do cluster que criou no passo anterior.

5. Execute o seguinte comando para montar o seu recipiente de armazenamento de bolhas. Lembre-se de alterar os valores para as seguintes propriedades:

   * seu-nome de contentor
   * seu nome de conta de armazenamento
   * nome de montagem
   * config-key
   * nome do âmbito
   * nome-chave

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **o nome do suporte** é um caminho DBFS que representa onde será montado o recipiente de armazenamento Blob ou uma pasta no interior do recipiente (especificado na fonte).
   * **conf-key** pode `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` ser ou ou`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **o nome do âmbito** é o nome do âmbito secreto que criou na secção anterior. 
   * **nome chave** é o nome do segredo que criou para a chave da conta de armazenamento no seu cofre chave.

   ![Criar suporte de armazenamento blob em caderno](./media/store-secrets-azure-key-vault/command1.png)

6. Execute o seguinte comando para ler o ficheiro de texto no seu recipiente de armazenamento blob para um quadro de dados. Altere os valores no comando para combinar com o nome do seu suporte e nome de ficheiro.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Ler ficheiro sintetmente](./media/store-secrets-azure-key-vault/command2.png)

7. Utilize o seguinte comando para visualizar o conteúdo do seu ficheiro.

   ```python
   df.show()
   ```
   ![Mostrar o dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Para desmontar o seu armazenamento de blob, execute o seguinte comando:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Desmontar conta de armazenamento](./media/store-secrets-azure-key-vault/command4.png)

9. Note que uma vez que o monte esteja desmontado, já não pode ler a partir da sua conta de armazenamento blob.

   ![Desmontar erro da conta de armazenamento](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine todo o seu grupo de recursos com os seguintes passos:

1. A partir do menu à esquerda no portal Azure, selecione **grupos de Recursos** e navegue para o seu grupo de recursos.

2. **Selecione Eliminar o grupo de recursos** e digite o nome do grupo de recursos. Em seguida, selecione **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a implementar um ambiente de Databricks injetado VNet com um ponto final de serviço habilitado para cosmos DB.
> [!div class="nextstepaction"]
> [Tutorial: Implementar tijolos de dados Azure com um ponto final cosmos DB](service-endpoint-cosmosdb.md)
