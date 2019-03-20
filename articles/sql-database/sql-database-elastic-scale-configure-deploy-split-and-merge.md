---
title: Implementar um serviço de divisão / intercalação | Documentos da Microsoft
description: Utilize a dividir / unir demasiado para mover dados entre bases de dados em partição horizontal.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 5aff7e93dcfaa5320be0d6f7d427abcdc88c69e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995820"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Implementar um serviço de divisão / intercalação para mover dados entre bases de dados em partição horizontal

A ferramenta de dividir / unir permite mover dados entre bases de dados em partição horizontal. Consulte [mover dados entre bases de dados de cloud escaladas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Transfira os pacotes de dividir / unir
1. Baixe a versão mais recente do NuGet partir [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).
2. Abra uma linha de comandos e navegue para o diretório onde transferiu nuget.exe. O download inclui comandos do PowerShell.
3. Transferir o pacote de dividir / unir mais recente para o diretório atual com o comando abaixo:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Os ficheiros são colocados num diretório chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** onde *x.x.xxx.x* reflete o número de versão. Localizar os ficheiros do serviço de divisão / intercalação no **content\splitmerge\service** subdiretório e os scripts do PowerShell de dividir / unir (e dlls de cliente necessário) no **content\splitmerge\powershell** subdiretório.

## <a name="prerequisites"></a>Pré-requisitos
1. Crie uma base de dados de BD SQL do Azure que será utilizada como a base de dados de estado de divisão / intercalação. Aceda ao [Portal do Azure](https://portal.azure.com). Criar uma nova **base de dados SQL**. Dê um nome de base de dados e criar um novo administrador e a palavra-passe. Certifique-se de que registe o nome e a palavra-passe para utilizar mais tarde.
2. Certifique-se de que o servidor de BD SQL do Azure permite que os serviços do Azure ligar ao mesmo. No portal, no **as definições da Firewall**, certifique-se a **permitir acesso aos serviços do Azure** definição está definida como **no**. Clique no ícone de "Guardar".
3. Crie uma conta de armazenamento do Azure para a saída de diagnóstico.
4. Crie um serviço de nuvem do Azure para o seu serviço de divisão / intercalação.

## <a name="configure-your-split-merge-service"></a>Configurar o seu serviço de dividir / unir
### <a name="split-merge-service-configuration"></a>Configuração do serviço de dividir / unir
1. Na pasta na qual transferiu os assemblies de dividir / unir, crie uma cópia dos **ServiceConfiguration.Template.cscfg** ficheiro fornecida juntamente com **SplitMergeService.cspkg** e mude o nome **Serviceconfiguration. Cscfg**.
2. Open **serviceconfiguration. Cscfg** num editor de texto como o Visual Studio que valida as entradas, como o formato de thumbprints de certificado.
3. Criar uma nova base de dados ou escolha uma base de dados existente para servir como a base de dados de estado para operações de dividir / Unir e obter a cadeia de ligação dessa base de dados. 
   
   > [!IMPORTANT]
   > Neste momento, a base de dados de estado tem de utilizar o agrupamento Latino (SQL\_Latin1\_gerais\_CP1\_CI\_AS). Para obter mais informações, consulte [nome de agrupamento do Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Com a BD do SQL do Azure, a cadeia de ligação é, normalmente, o formato:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Introduza esta cadeia de ligação no ficheiro cscfg em ambos os **SplitMergeWeb** e **SplitMergeWorker** secções de função na definição de ElasticScaleMetadata.
5. Para o **SplitMergeWorker** função, introduza uma cadeia de ligação válida para o armazenamento do Azure para o **WorkerRoleSynchronizationStorageAccountConnectionString** definição.

### <a name="configure-security"></a>Configurar a segurança
Para obter instruções detalhadas configurar a segurança do serviço, consulte a [configuração de segurança de dividir / unir](sql-database-elastic-scale-split-merge-security-configuration.md).

Para efeitos de uma implementação de teste simples para este tutorial, um conjunto mínimo de passos de configuração será executada para obter o serviço de cópia de segurança e em execução. Essas etapas habilitam a apenas uma conta de computador/executando-os para comunicar com o serviço.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Criar um novo diretório e deste diretório, execute o seguinte comando com uma [linha de comandos do programador para o Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) janela:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

É-lhe perguntado para uma palavra-passe proteger a chave privada. Introduza uma palavra-passe forte e confirme-lo. Em seguida, é-lhe pedido para a palavra-passe a ser utilizado mais uma vez depois disso. Clique em **Sim** no final para importá-lo para o arquivo de raiz de autoridades de certificação fidedigna.

### <a name="create-a-pfx-file"></a>Criar um ficheiro PFX
Execute o seguinte comando na mesma janela onde o makecert foi executado; Utilize a mesma palavra-passe que utilizou para criar o certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importar o certificado de cliente para o arquivo pessoal
1. No Explorador do Windows, faça duplo clique em **MyCert**.
2. Na **Assistente para importar certificados** selecionar **utilizador atual** e clique em **seguinte**.
3. Confirme o caminho do ficheiro e clique em **seguinte**.
4. Escreva a palavra-passe, deixe **incluem propriedades expandidas tudo** marcado e clique em **próxima**.
5. Deixe **selecionar automaticamente o arquivo de certificados [...]**  marcado e clique em **próxima**.
6. Clique em **Finish** e **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Carregar o ficheiro PFX para o serviço em nuvem
1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Selecione **serviços Cloud**.
3. Selecione o serviço de nuvem que criou acima para o serviço de divisão/intercalação.
4. Clique em **certificados** no menu superior.
5. Clique em **carregar** na barra inferior.
6. Selecione o ficheiro PFX e introduza a mesma palavra-passe anteriores.
7. Depois de concluído, copie o thumbprint do certificado da nova entrada na lista.

### <a name="update-the-service-configuration-file"></a>Atualizar o ficheiro de configuração de serviço
Cole o thumbprint do certificado copiado acima para o atributo de valor do thumbprint dessas configurações.
Para a função de trabalho:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Para a função da web:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

. Tenha em atenção que, para produção, implementações de separam certificados deve ser utilizada para a AC, para criptografia, o certificado de servidor e certificados de cliente. Para obter instruções detalhadas sobre isso, consulte [configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Implementar o seu serviço
1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Selecione o serviço de nuvem que criou anteriormente.
3. Clique em **Descrição geral**.
4. Escolha o ambiente de teste, em seguida, clique em **carregar**.
5. Na caixa de diálogo, introduza uma etiqueta de implementação. Para "Package" e "Configuração", clique em 'Do Local' e escolher o **SplitMergeService.cspkg** de ficheiros e o ficheiro cscfg que configurou anteriormente.
6. Certifique-se de que a caixa de seleção rotulada **implementar mesmo que uma ou mais funções contenham uma única instância** está marcada.
7. Pressiono o botão de escala em inferior direito para iniciar a implementação. Esperava demorar alguns minutos a concluir.


## <a name="troubleshoot-the-deployment"></a>Resolver problemas relacionados com a implementação
Se a função da web falhar a ficar online, é provável que um problema com a configuração de segurança. Verifique se o SSL está configurado conforme descrito acima.

Se a função de trabalho falha a ficar online, mas a função da web for concluída com êxito, é mais provável que um problema ao ligar à base de dados de estado que criou anteriormente.

* Certifique-se de que a cadeia de ligação em sua cscfg está correta.
* Verifique se o servidor e base de dados existem e que o id de utilizador e palavra-passe estão corretos.
* Para o Azure SQL DB, a cadeia de ligação deve ter o formato:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Certifique-se de que o nome do servidor não começa com **https://**.
* Certifique-se de que o servidor de BD SQL do Azure permite que os serviços do Azure ligar ao mesmo. Para tal, abra a sua base de dados no portal e certifique-se de que o **permitir acesso aos serviços do Azure** definição está definida como **no**\*\*.

## <a name="test-the-service-deployment"></a>Testar a implementação do serviço
### <a name="connect-with-a-web-browser"></a>Conecte-se um navegador da web
Determine o ponto de extremidade de web do seu serviço de divisão / intercalação. Pode encontrá-lo no portal ao aceder a **descrição geral** do seu serviço de nuvem e observar sendo **URL do Site** no lado direito. Substitua **http://** com **https://** , uma vez que as configurações de segurança padrão desativar ponto de extremidade HTTP. Carregar a página para este URL no seu browser.

### <a name="test-with-powershell-scripts"></a>Testar com scripts do PowerShell
A implementação e o seu ambiente podem ser testados através da execução de scripts do PowerShell de exemplo incluídos.

Os arquivos de script incluídos são:

1. **SetupSampleSplitMergeEnvironment.ps1** -configura uma camada de dados de teste para dividir/unir (consulte a tabela abaixo para descrição detalhada)
2. **ExecuteSampleSplitMerge.ps1** -executa operações de teste no teste (veja a tabela abaixo para uma descrição detalhada) de camada de dados
3. **GetMappings.ps1** – nível superior script que imprime o estado atual dos mapeamentos de partição horizontal de exemplo.
4. **ShardManagement.psm1** -script de programa auxiliar que encapsula a API de ShardManagement
5. **SqlDatabaseHelpers.psm1** -script de programa auxiliar para criar e gerir bases de dados SQL
   
   <table style="width:100%">
     <tr>
       <th>Ficheiro do PowerShell</th>
       <th>Passos</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Cria uma base de dados de Gestor de mapas de partições horizontais</td>
     </tr>
     <tr>
       <td>2.    Cria 2 bases de dados de partição horizontal.
     </tr>
     <tr>
       <td>3.    Cria um mapa de partições horizontais para essas bases de dados (eliminações qualquer partição horizontal existente mapeia nessas bases de dados). </td>
     </tr>
     <tr>
       <td>4.    Cria uma tabela de exemplo pequeno em ambas as partições horizontais e preenche a tabela de uma das partições horizontais.</td>
     </tr>
     <tr>
       <td>5.    Declara SchemaInfo para a tabela em partição horizontal.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Ficheiro do PowerShell</th>
       <th>Passos</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Envia um pedido de divisão para o serviço de divisão / intercalação front-end web, que divide metade os dados a partir da primeira partição horizontal à segunda partição horizontal.</td>
     </tr>
     <tr>
       <td>2.    Consulta o front-end da web para o estado do pedido de divisão e aguarda até que a solicitação for concluída.</td>
     </tr>
     <tr>
       <td>3.    Envia um pedido de intercalação para o serviço de divisão / intercalação front-end web, que move os dados da segunda partição horizontal de volta para a partição horizontal primeiro.</td>
     </tr>
     <tr>
       <td>4.    Consulta o front-end da web para o estado do pedido de intercalação e aguarda até que a solicitação for concluída.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Utilize o PowerShell para verificar a implementação
1. Abra uma nova janela do PowerShell e navegue para o diretório onde transferiu o pacote de dividir / Unir e, em seguida, navegue para o diretório de "powershell".
2. Criar um servidor de base de dados do Azure SQL (ou escolha um servidor existente) onde serão criadas o Gestor de mapas de partições horizontais e as partições horizontais.
   
   > [!NOTE]
   > O script de SetupSampleSplitMergeEnvironment.ps1 cria todos esses bancos de dados no mesmo servidor, por predefinição, para manter o script simples. Isso não é uma restrição do serviço de divisão / intercalação em si.
   >
   
   Será necessário um início de sessão de autenticação de SQL com acesso de leitura/gravação para as bases de dados para o serviço de divisão / intercalação mover dados e atualizar o mapa de partições horizontais. Uma vez que o serviço de divisão / intercalação é executado na cloud, não suporta atualmente autenticação integrada.
   
   Certifique-se de que o servidor de SQL do Azure está configurado para permitir o acesso do endereço IP da máquina que executa estes scripts. Pode encontrar esta definição sob o servidor SQL do Azure / configuração / endereços IP permitidos.
3. Execute o script de SetupSampleSplitMergeEnvironment.ps1 para criar o ambiente de exemplo.
   
   Executar este script irá eliminar quaisquer dados de gestão existentes de mapa de partições horizontais estruturas sobre a base de dados de Gestor de mapas de partições horizontais e as partições horizontais. Poderá ser útil voltar a executar o script, se pretender reinicializar o mapa de partições horizontais ou shards.
   
   Linha de comandos de exemplo:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Execute o script de Getmappings.ps1 para ver os mapeamentos de que existem atualmente no ambiente de exemplo.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Execute o script de ExecuteSampleSplitMerge.ps1 para executar uma operação de divisão (mover metade os dados em partição horizontal primeiro para a partição horizontal segundo) e, em seguida, uma operação de intercalação (mover os dados de volta para a primeira partição de horizontal). Se configurou o SSL e à esquerda do ponto de extremidade http desabilitado, certifique-se de que utiliza o ponto final https://.
   
   Linha de comandos de exemplo:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Se receber o erro, abaixo provavelmente é um problema com certificado o ponto final Web. Tente ligar-se para o ponto final Web com o seu browser favorito e verifique se existe um erro de certificado.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Se tiver êxito, o resultado deverá ser semelhante a abaixo:
   
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
6. Experimentar outros tipos de dados! Todos esses scripts demorar um parâmetro - ShardKeyType opcional que permite-lhe especificar o tipo de chave. A predefinição é Int32, mas também pode especificar Int64, Guid ou binário.

## <a name="create-requests"></a>Pedidos de criação
O serviço pode ser utilizado, utilizando a IU da web ou ao importar e utilizar o módulo de SplitMerge.psm1 PowerShell que irá submeter pedidos através da função da web.

O serviço pode mover dados em tabelas em partição horizontal e tabelas de referência. Uma tabela em partição horizontal tem uma coluna de chave de fragmentação e tem diferentes de linha de dados em cada partição horizontal. Não é uma tabela de referência em partição horizontal para que ele contém os mesmos dados de linha em cada partição horizontal. As tabelas de referência são úteis para dados que não são alterados frequentemente e é utilizada a associação com tabelas em partição horizontal em consultas.

Para realizar uma operação de divisão / intercalação, deve declarar as tabelas de referência que deseja terem sido movidos e tabelas em partição horizontal. Isto é conseguido com o **SchemaInfo** API. Esta API está no **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** espaço de nomes.

1. Para cada tabela em partição horizontal, criar um **ShardedTableInfo** objeto que descreve o nome do esquema da tabela pai (opcional, predefinições para "dbo"), o nome da tabela e o nome da coluna dessa tabela que contém a chave de fragmentação.
2. Para cada tabela de referência, criar um **ReferenceTableInfo** objeto que descreve o nome do esquema da tabela pai (opcional, predefinições para "dbo") e o nome da tabela.
3. Adicionar os objetos de TableInfo acima para um novo **SchemaInfo** objeto.
4. Obter uma referência para um **ShardMapManager** objeto e chamar **GetSchemaInfoCollection**.
5. Adicionar a **SchemaInfo** para o **SchemaInfoCollection**, fornecendo o nome do mapa de partições horizontais.

Um exemplo disso pode ser visto no SetupSampleSplitMergeEnvironment.ps1 script.

O serviço de divisão / intercalação não cria a base de dados de destino (ou o esquema para todas as tabelas na base de dados) para. Têm de ser previamente criadas antes de enviar um pedido para o serviço.

## <a name="troubleshooting"></a>Resolução de problemas
Pode ver a abaixo da mensagem durante a execução de scripts do powershell de exemplo:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Este erro significa que o certificado SSL não está configurado corretamente. Siga as instruções na secção "A ligar com um navegador da web".

Se não podem submeter pedidos verá isto:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

Neste caso, de verificação do ficheiro de configuração, em particular a definição para **WorkerRoleSynchronizationStorageAccountConnectionString**. Normalmente, este erro indica que a função de trabalho não foi possível com êxito inicializar a base de dados de metadados na primeira utilização. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

