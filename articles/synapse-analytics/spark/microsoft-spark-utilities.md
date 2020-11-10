---
title: Introdução dos Utilitários Microsoft Spark
description: Tutorial de utilização de MSSparkutils em cadernos Azure Synapse Analytics.
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 648c5b75f125725ebda2966d3ebc4200ee76b98c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428683"
---
# <a name="introduction-of-microsoft-spark-utilities"></a>Introdução dos Utilitários Microsoft Spark
Microsoft Spark Utilities (MSSparkUtils) é um pacote de incorporado para ajudá-lo a fazer tarefas usadas comuns mais facilmente. Você pode usar MSSparkUtils para trabalhar com o sistema de arquivos de forma eficiente, para obter variáveis ambientais, e para trabalhar com segredos. Os MSSparkUtils estão disponíveis em `PySpark (Python)` `Scala` , e `.NET Spark (C#)` cadernos e oleodutos Synapse.

## <a name="pre-requisites"></a>Pré-requisitos
### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Configure acesso ao Azure Data Lake Storage Gen2 
Os cadernos synapse usam o diretório ativo Azure (Azure AD) para aceder às contas da ADLS Gen2. É necessário ser um **Contribuinte blob para** aceder à conta ADLS Gen2 (ou pasta). 

Os gasodutos synapse utilizam a identidade do espaço de trabalho (MSI) para aceder às contas de armazenamento. Para utilizar o MSSparkUtils nas suas atividades de pipeline, a sua identidade de espaço de trabalho precisa de ser um **Contribuinte blob de armazenamento** para aceder à conta ADLS Gen2 (ou pasta).

Siga estes passos para garantir que o seu AD Azure e o espaço de trabalho MSI tenham acesso à conta ADLS Gen2:
1. Abra o [portal Azure](https://portal.azure.com/) e a conta de armazenamento a que pretende aceder. Pode navegar para o recipiente específico a que pretende aceder.
2. Selecione o **controlo de acesso (IAM)** do painel esquerdo.
3. Para a função **de Contribuinte de Dados blob de armazenamento** na conta de armazenamento, atribua a sua conta **AZure AD** e **a sua identidade de espaço de trabalho** (o mesmo que o nome do seu espaço de trabalho) ou certifique-se de que já está atribuída. 
4. Clique em **Guardar**.

Pode aceder aos dados da ADLS Gen2 com a Synapse Spark através do seguinte URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Configure acesso ao armazenamento de blob Azure 

Sinaapse alavanca **assinatura de acesso compartilhado (SAS)** para aceder ao Azure Blob Storage. Para evitar expor as teclas SAS no código, recomendamos a criação de um novo serviço ligado no espaço de trabalho synapse para a conta Azure Blob Storage a que pretende aceder.

Siga estes passos para adicionar um novo serviço ligado para uma conta de armazenamento Azure Blob:

1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).
2. **Selecione Gerir** a partir do painel esquerdo e selecionar **serviços Linked** sob as **ligações Externas**.
3. Procure no **Azure Blob Storage** no novo painel **de serviço ligado** à direita.
4. Clique em **Continue** (Continuar).
5. Selecione a Conta de Armazenamento Azure Blob para aceder e configurar o nome de serviço ligado. Sugerir a utilização **da tecla conta** para o **método de autenticação.**
6. Clique **na ligação de teste** para validar as definições estão corretas.
7. Clique em **Criar** primeiro e clique em **Publicar tudo** para guardar as suas alterações. 

Pode aceder aos dados do Azure Blob Storage com a Faísca de Sinapse através do seguinte URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Aqui está um exemplo de código:


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linkedServiceName = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linkedServiceName)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>Configure acesso ao Cofre da Chave Azure

Pode adicionar um Cofre de Chaves Azure como um serviço ligado para gerir as suas credenciais em Synapse. Siga estes passos para adicionar um Cofre de Chaves Azure como um serviço ligado à Sinapse:
1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).
2. **Selecione Gerir** a partir do painel esquerdo e selecionar **serviços Linked** sob as **ligações Externas**.
3. Procure **a Azure Key Vault** no novo painel de serviço **ligado** à direita.
4. Selecione a Conta cofre chave Azure para aceder e configurar o nome de serviço ligado.
5. Clique **na ligação de teste** para validar as definições estão corretas.
6. Clique em **Criar** primeiro e clique em **Publicar tudo** para guardar o seu alterar. 

Os cadernos synapse usam o diretório ativo Azure (Azure AD) para aceder ao Cofre da Chave Azure. Os oleodutos synapse utilizam a identidade do espaço de trabalho (MSI) para aceder ao Cofre da Chave Azure. Para garantir que o seu código funciona tanto no portátil como no oleoduto Synapse, recomendamos a concessão de uma permissão de acesso secreto tanto para a sua conta AZure AD como para a identidade do espaço de trabalho.

Siga estes passos para garantir o acesso secreto à sua identidade de espaço de trabalho:
1. Abra o [portal Azure](https://portal.azure.com/) e o Cofre da Chave Azure a que pretende aceder. 
2. Selecione as **políticas de Acesso** a partir do painel esquerdo.
3. Clique **na política de acesso de adicionar:** 
    - Escolha **Key, Secret, & Certificate Management** como modelo config.
    - Selecione **a sua conta AD Azure** e **a sua identidade de espaço de trabalho** (o mesmo que o nome do seu espaço de trabalho) no principal selecionado ou certifique-se de que já está atribuída. 
4. Clique **em Selecionar** e **Adicionar**.
5. Clique no botão **Guardar** para cometer alterações.  

## <a name="file-system-utilities"></a>Utilitários de sistema de ficheiros

`mssparkutils.fs` fornece utilitários para trabalhar com vários sistemas de ficheiros, incluindo Azure Data Lake Storage Gen2 (ADLS Gen2) e Azure Blob Storage. Certifique-se de que configura o acesso ao [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) e [ao Azure Blob Storage](#configure-access-to-azure-blob-storage) adequadamente.

Corra o seguinte comando para obter uma visão geral sobre os métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Resultados em:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Listar ficheiros
Listar o conteúdo de um diretório.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Ver propriedades de ficheiros
Devolve propriedades de ficheiros, incluindo nome de ficheiro, caminho de arquivo, tamanho do ficheiro, se é um diretório e se é um ficheiro.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Criar novo diretório
Cria o diretório dado se não existir, criando também os directórios-mãe necessários.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Ficheiro de cópia
Copia um ficheiro ou diretório, suporta cópia através dos sistemas de ficheiros.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Pré-visualizar o conteúdo do ficheiro
Retorna até os primeiros bytes 'maxBytes' do ficheiro dado como uma String codificada em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Arquivo de movimento
Move um ficheiro ou diretório, o suporte move-se através dos sistemas de ficheiros.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Escrever arquivo
Escreve a corda dada para um ficheiro codificado em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Conteúdo do apêndice a um ficheiro
Anexa a corda dada a um ficheiro codificado em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Apagar ficheiro ou diretório
Remove um ficheiro ou um diretório.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Serviços de utilidade de credenciais

Você pode usar MSSparkUtils Credentials Utilities para obter os tokens de acesso de serviços ligados e gerir segredos em Azure Key Vault. 

Corra o seguinte comando para obter uma visão geral sobre os métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

Obtenha resultados:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Obter ficha
Devolve a Azure AD token para um dado público, nome (opcional). A tabela abaixo lista todos os tipos de audiências disponíveis: 

|Tipo de Público|Chave do público|
|--|--|
|Tipo de Resolução de Público|'Público'|
|Recurso do público de armazenamento|'Armazenamento'|
|Recurso de audiência do Armazém de Dados|'DW'|
|Recurso de audiência do lago de dados|'AzureManagement'|
|Recurso do público do cofre|'DataLakeStore'|
|Recurso de audiência Azure OSSDB|'AzureOSSDB'|
|Recurso Azure Synapse|'Sinapse'|
|Recurso de fábrica de dados Azure|'ADF'|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="validate-token"></a>Validar ficha
Devoluções verdadeiras se o símbolo não tiver expirado.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Obtenha cadeia de ligação ou credenciais para serviço ligado
Devolve a cadeia de ligação ou credenciais para o serviço ligado. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Obter segredo usando identidade do espaço de trabalho
Devolve o segredo do Azure Key Vault para um dado nome Azure Key Vault, nome secreto e nome de serviço ligado usando a identidade do espaço de trabalho. Certifique-se de que configura o acesso ao [Cofre da Chave Azure](#configure-access-to-azure-key-vault) adequadamente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Obter segredo usando credenciais de utilizador
Devolve o segredo do Azure Key Vault para um dado nome Azure Key Vault, nome secreto e nome de serviço ligado usando credenciais de utilizador. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>Colocar segredo usando identidade do espaço de trabalho
Coloca o cofre da chave Azure em segredo para um dado nome Azure Key Vault, nome secreto e nome de serviço ligado usando a identidade do espaço de trabalho. Certifique-se de que configura o acesso ao [Cofre da Chave Azure](#configure-access-to-azure-key-vault) adequadamente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>Colocar segredo usando credenciais de utilizador
Coloca o cofre da chave Azure em segredo para um dado nome Azure Key Vault, nome secreto e nome de serviço ligado usando credenciais de utilizador. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>Serviços de utilidade ambiental 

Corra o seguinte comando para obter uma visão geral sobre os métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

Obtenha resultados:
```
getUserName(): returns user name
getUserId(): returns unique user id
getJobId(): returns job id
getWorkspaceName(): returns workspace name
getPoolName(): returns Spark pool name
getClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Obtenha o nome do utilizador
Devolve o nome de utilizador atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-user-id"></a>Obtenha iD do utilizador
Devolve o ID do utilizador atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-job-id"></a>Obter iD de emprego
Devolve a identificação do emprego.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-workspace-name"></a>Obtenha o nome do espaço de trabalho
Devolve o nome do espaço de trabalho.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-pool-name"></a>Obtenha o nome da piscina
Devolve o nome da piscina Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

### <a name="get-cluster-id"></a>Obter iD de cluster
Devolve o ID do cluster atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end

## <a name="next-steps"></a>Passos seguintes
- [Confira os cadernos da amostra da Sinapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Criar uma piscina Apache Spark (pré-visualização) em Azure Synapse Analytics usando ferramentas web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark em Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)