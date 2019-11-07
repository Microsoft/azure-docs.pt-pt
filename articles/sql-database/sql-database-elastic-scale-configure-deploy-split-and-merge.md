---
title: Implementar um serviço de divisão/intercalação
description: Use a divisão e mesclagem também para mover dados entre bancos de dado fragmentados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 009fb4be61aad5c700c7520764e9414ed9422721
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690308"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Implantar um serviço de divisão/mesclagem para mover dados entre bancos de dado fragmentados

A ferramenta de divisão/mesclagem permite que você mova dados entre bancos de dado fragmentados. Consulte [movendo dados entre bancos de dados de nuvem escalados](sql-database-elastic-scale-overview-split-and-merge.md) horizontalmente

## <a name="download-the-split-merge-packages"></a>Baixar os pacotes de divisão/mesclagem
1. Baixe a versão mais recente do NuGet do [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).
2. Abra um prompt de comando e navegue até o diretório em que você baixou o NuGet. exe. O download inclui comandos do PowerShell.
3. Baixe o pacote de divisão/mesclagem mais recente no diretório atual com o comando abaixo:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Os arquivos são colocados em um diretório chamado **Microsoft. Azure. SQLDatabase. ElasticScale. Service. SplitMerge. x. x. xxx. x** , onde *x. x. xxx. x* reflete o número de versão. Localize os arquivos de serviço de divisão/mesclagem no subdiretório **content\splitmerge\service** e os scripts do PowerShell de divisão/mesclagem (e DLLs de cliente necessárias) no subdiretório **content\splitmerge\powershell** .

## <a name="prerequisites"></a>Pré-requisitos
1. Crie um banco de dados do BD SQL do Azure que será usado como o banco de dados de status de divisão/mesclagem. Aceda ao [Portal do Azure](https://portal.azure.com). Crie um novo **banco de dados SQL**. Dê um nome ao banco de dados e crie um novo administrador e senha. Certifique-se de registrar o nome e a senha para uso posterior.
2. Verifique se o servidor de banco de BD SQL do Azure permite que os serviços do Azure se conectem a ele. No portal, nas configurações do **Firewall**, verifique se a configuração **permitir acesso aos serviços do Azure** está definida como **ativada**. Clique no ícone "salvar".
3. Crie uma conta de armazenamento do Azure para a saída de diagnóstico.
4. Crie um serviço de nuvem do Azure para seu serviço de divisão/mesclagem.

## <a name="configure-your-split-merge-service"></a>Configurar seu serviço de divisão/mesclagem
### <a name="split-merge-service-configuration"></a>Configuração do serviço de divisão/mesclagem
1. Na pasta para a qual você baixou os assemblies de divisão/mesclagem, crie uma cópia do arquivo de SplitMergeService. **cscfg** que foi enviado junto com o **. cspkg** e renomeie-o como o **. cscfg**.
2. Abra o **inconfiguration. cscfg** em um editor de texto como o Visual Studio que valida entradas como o formato de impressões digitais do certificado.
3. Crie um novo banco de dados ou escolha um banco de dados existente para servir como o banco de dados de status para operações de divisão e mesclagem e recupere a cadeia de conexão desse banco de dados. 
   
   > [!IMPORTANT]
   > Neste momento, o banco de dados de status deve usar o agrupamento latino (SQL\_Latino1\_geral\_CP1\_CI\_AS). Para obter mais informações, consulte [nome do agrupamento do Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Com o BD SQL do Azure, a cadeia de conexão normalmente está no formato:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Insira essa cadeia de conexão no arquivo cscfg nas seções de função **SplitMergeWeb** e **SplitMergeWorker** na configuração ElasticScaleMetadata.
5. Para a função **SplitMergeWorker** , insira uma cadeia de conexão válida para o armazenamento do Azure para a configuração **WorkerRoleSynchronizationStorageAccountConnectionString** .

### <a name="configure-security"></a>Configurar a segurança
Para obter instruções detalhadas sobre como configurar a segurança do serviço, consulte a [configuração de segurança de divisão/mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md).

Para fins de uma implantação de teste simples para este tutorial, um conjunto mínimo de etapas de configuração será executado para colocar o serviço em funcionamento. Essas etapas permitem que apenas uma máquina/conta que as execute para se comunicar com o serviço.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Crie um novo diretório e, nesse diretório, execute o seguinte comando usando um [prompt de comando do desenvolvedor para a janela do Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) :

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Você será solicitado a fornecer uma senha para proteger a chave privada. Insira uma senha forte e confirme-a. Em seguida, você será solicitado a usar a senha mais uma vez. Clique em **Sim** no final para importá-lo para o repositório raiz de autoridades de certificação confiáveis.

### <a name="create-a-pfx-file"></a>Criar um arquivo PFX
Execute o seguinte comando na mesma janela em que o MakeCert foi executado; Use a mesma senha que você usou para criar o certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importar o certificado do cliente para o repositório pessoal
1. No Windows Explorer, clique duas vezes em **MyCert. pfx**.
2. No **Assistente para importação de certificados** , selecione **usuário atual** e clique em **Avançar**.
3. Confirme o caminho do arquivo e clique em **Avançar**.
4. Digite a senha, deixe **incluir todas as propriedades estendidas** marcadas e clique em **Avançar**.
5. Deixe **selecionar automaticamente o repositório de certificados [...]** marcado e clique em **Avançar**.
6. Clique em **concluir** e em **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Carregar o arquivo PFX no serviço de nuvem
1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Selecione **serviços de nuvem**.
3. Selecione o serviço de nuvem que você criou acima para o serviço de divisão/mesclagem.
4. Clique em **certificados** no menu superior.
5. Clique em **carregar** na barra inferior.
6. Selecione o arquivo PFX e insira a mesma senha acima.
7. Depois de concluído, copie a impressão digital do certificado da nova entrada na lista.

### <a name="update-the-service-configuration-file"></a>Atualizar o arquivo de configuração de serviço
Cole a impressão digital do certificado copiada acima no atributo impressão digital/valor dessas configurações.
Para a função de trabalho:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Para a função Web:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Observe que, para implantações de produção, os certificados separados devem ser usados para a AC, para criptografia, o certificado do servidor e certificados do cliente. Para obter instruções detalhadas sobre isso, consulte [configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Implantar seu serviço
1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Selecione o serviço de nuvem que você criou anteriormente.
3. Clique em **Descrição geral**.
4. Escolha o ambiente de preparo e clique em **carregar**.
5. Na caixa de diálogo, insira um rótulo de implantação. Para ' Package ' e ' Configuration ', clique em ' from local ' e escolha o arquivo **SplitMergeService. cspkg** e o arquivo cscfg que você configurou anteriormente.
6. Certifique-se de que a caixa de seleção seja rotulada como **implantar mesmo se uma ou mais funções contiverem uma única instância** estiver marcada.
7. Pressione o botão de tique no canto inferior direito para iniciar a implantação. Espere alguns minutos para concluir.


## <a name="troubleshoot-the-deployment"></a>Solucionar problemas de implantação
Se sua função Web não ficar online, provavelmente será um problema com a configuração de segurança. Verifique se o SSL está configurado conforme descrito acima.

Se sua função de trabalho não ficar online, mas sua função Web for bem-sucedida, provavelmente será um problema ao se conectar ao banco de dados de status que você criou anteriormente.

* Certifique-se de que a cadeia de conexão no cscfg seja precisa.
* Verifique se o servidor e o banco de dados existem e se a ID de usuário e a senha estão corretas.
* Para o BD SQL do Azure, a cadeia de conexão deve estar no formato:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Verifique se o nome do servidor não começa com **https://** .
* Verifique se o servidor de banco de BD SQL do Azure permite que os serviços do Azure se conectem a ele. Para fazer isso, abra seu banco de dados no portal e verifique se a configuração **permitir acesso aos serviços do Azure** está definida como * * em * * * * *.

## <a name="test-the-service-deployment"></a>Testar a implantação do serviço
### <a name="connect-with-a-web-browser"></a>Conectar-se a um navegador da Web
Determine o ponto de extremidade da Web do seu serviço de divisão/mesclagem. Você pode encontrar isso no portal acessando a **visão geral** do seu serviço de nuvem e procurando na **URL do site** no lado direito. Substitua **http://** por **https://** , já que as configurações de segurança padrão desabilitam o ponto de extremidade http. Carregue a página para esta URL em seu navegador.

### <a name="test-with-powershell-scripts"></a>Testar com scripts do PowerShell
A implantação e o ambiente podem ser testados executando-se os scripts do PowerShell de exemplo incluídos.

Os arquivos de script incluídos são:

1. **SetupSampleSplitMergeEnvironment. ps1** -configura uma camada de dados de teste para divisão/mesclagem (consulte a tabela abaixo para obter uma descrição detalhada)
2. **ExecuteSampleSplitMerge. ps1** -executa operações de teste na camada de dados de teste (consulte a tabela abaixo para obter uma descrição detalhada)
3. **Getmappings. ps1** -script de exemplo de nível superior que imprime o estado atual dos mapeamentos de fragmento.
4. **ShardManagement. psm1** -o script auxiliar que ENCAPSULA a API ShardManagement
5. **SqlDatabaseHelpers. psm1** -script auxiliar para criar e gerenciar bancos de dados SQL
   
   <table style="width:100%">
     <tr>
       <th>Arquivo do PowerShell</th>
       <th>Passos</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment. ps1</th>
       <td>1.    Cria um banco de dados do Gerenciador de mapa de fragmentos</td>
     </tr>
     <tr>
       <td>2.    Cria dois bancos de dados de fragmentos.
     </tr>
     <tr>
       <td>3.    Cria um mapa de fragmentos para esses bancos de dados (exclui todos os mapas de fragmentos existentes nesses bancos de dados). </td>
     </tr>
     <tr>
       <td>4.    Cria uma pequena tabela de exemplo em ambos os fragmentos e popula a tabela em um dos fragmentos.</td>
     </tr>
     <tr>
       <td>5.    Declara o SchemaInfo para a tabela fragmentada.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Arquivo do PowerShell</th>
       <th>Passos</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge. ps1 </th>
       <td>1.    Envia uma solicitação Split para o front-end da Web do serviço de divisão/mesclagem, que divide metade dos dados do primeiro fragmento para o segundo fragmento.</td>
     </tr>
     <tr>
       <td>2.    Sonda o front-end da Web para o status da solicitação de divisão e aguarda até que a solicitação seja concluída.</td>
     </tr>
     <tr>
       <td>3.    Envia uma solicitação de mesclagem para o front-end da Web do serviço de divisão/mesclagem, que move os dados do segundo fragmento de volta para o primeiro fragmento.</td>
     </tr>
     <tr>
       <td>4.    Sonda o front-end da Web para o status da solicitação de mesclagem e aguarda até que a solicitação seja concluída.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Usar o PowerShell para verificar sua implantação
1. Abra uma nova janela do PowerShell e navegue até o diretório em que você baixou o pacote de divisão e mesclagem e, em seguida, navegue até o diretório "PowerShell".
2. Crie um servidor de banco de dados SQL do Azure (ou escolha um servidor existente) no qual o Gerenciador de mapa de fragmentos e os fragmentos serão criados.
   
   > [!NOTE]
   > O script SetupSampleSplitMergeEnvironment. ps1 cria todos esses bancos de dados no mesmo servidor por padrão para manter o script simples. Isso não é uma restrição do serviço de divisão/mesclagem.
   >
   
   Um logon de autenticação do SQL com acesso de leitura/gravação para os bancos de dados será necessário para que o serviço de divisão/mesclagem mova e atualize o mapa de fragmentos. Como o serviço de divisão/mesclagem é executado na nuvem, atualmente ele não dá suporte à autenticação integrada.
   
   Verifique se o SQL Server do Azure está configurado para permitir o acesso do endereço IP do computador que executa esses scripts. Você pode encontrar essa configuração em Azure SQL Server/configuração/endereços IP permitidos.
3. Execute o script SetupSampleSplitMergeEnvironment. ps1 para criar o ambiente de exemplo.
   
   A execução desse script apagará todas as estruturas de dados de gerenciamento do mapa de fragmentos existentes no banco de dado do Gerenciador de mapa de fragmentos e nos fragmentos. Pode ser útil executar novamente o script se você quiser reinicializar o mapa do fragmento ou os fragmentos.
   
   Linha de comando de exemplo:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Execute o script getmappings. ps1 para exibir os mapeamentos que existem atualmente no ambiente de exemplo.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Execute o script ExecuteSampleSplitMerge. ps1 para executar uma operação de divisão (movendo metade dos dados no primeiro fragmento para o segundo fragmento) e, em seguida, uma operação de mesclagem (movendo os dados de volta para o primeiro fragmento). Se você configurou o SSL e saiu do ponto de extremidade http desabilitado, certifique-se de usar o ponto de extremidade https://em vez disso.
   
   Linha de comando de exemplo:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Se você receber o erro abaixo, provavelmente será um problema com o certificado do ponto de extremidade da Web. Tente se conectar ao ponto de extremidade da Web com seu navegador da Web favorito e verifique se há um erro de certificado.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Se tiver êxito, a saída deverá ser semelhante à seguinte:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Experimente outros tipos de dados! Todos esses scripts usam um parâmetro opcional-ShardKeyType que permite especificar o tipo de chave. O padrão é Int32, mas você também pode especificar Int64, GUID ou Binary.

## <a name="create-requests"></a>Pedidos de criação
O serviço pode ser usado usando a interface do usuário da Web ou importando e usando o módulo do PowerShell SplitMerge. psm1 que enviará suas solicitações por meio da função Web.

O serviço pode mover dados em tabelas fragmentadas e tabelas de referência. Uma tabela fragmentada tem uma coluna de chave de fragmentação e tem dados de linha diferentes em cada fragmento. Uma tabela de referência não é fragmentada, então ela contém os mesmos dados de linha em cada fragmento. As tabelas de referência são úteis para dados que não são alterados com frequência e são usadas para INGRESSAr com tabelas fragmentadas em consultas.

Para executar uma operação de divisão/mesclagem, você deve declarar as tabelas fragmentadas e as tabelas de referência que deseja que tenham sido movidas. Isso é feito com a API **SchemaInfo** . Essa API está no namespace **Microsoft. Azure. SQLDatabase. ElasticScale. ShardManagement. Schema** .

1. Para cada tabela fragmentada, crie um objeto **ShardedTableInfo** descrevendo o nome do esquema pai da tabela (opcional, o padrão é "dbo"), o nome da tabela e o nome da coluna nessa tabela que contém a chave de fragmentação.
2. Para cada tabela de referência, crie um objeto **ReferenceTableInfo** descrevendo o nome do esquema pai da tabela (opcional, o padrão é "dbo") e o nome da tabela.
3. Adicione os objetos TableInfo acima a um novo objeto **SchemaInfo** .
4. Obtenha uma referência a um objeto **ShardMapManager** e chame **GetSchemaInfoCollection**.
5. Adicione o **SchemaInfo** ao **SchemaInfoCollection**, fornecendo o nome do mapa de fragmentos.

Um exemplo disso pode ser visto no script SetupSampleSplitMergeEnvironment. ps1.

O serviço de divisão/mesclagem não cria o banco de dados de destino (ou o esquema para todas as tabelas no banco de dados) para você. Eles devem ser criados previamente antes de enviar uma solicitação ao serviço.

## <a name="troubleshooting"></a>Resolução de problemas
Você pode ver a mensagem abaixo ao executar os scripts do PowerShell de exemplo:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Esse erro significa que o certificado SSL não está configurado corretamente. Siga as instruções na seção ' conectando-se a um navegador da Web '.

Se você não puder enviar solicitações, poderá ver isto:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Nesse caso, verifique o arquivo de configuração, em particular, a configuração de **WorkerRoleSynchronizationStorageAccountConnectionString**. Esse erro normalmente indica que a função de trabalho não pôde inicializar com êxito o banco de dados de metadados no primeiro uso. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

