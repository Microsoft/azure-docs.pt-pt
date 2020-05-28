---
title: Implementar um serviço de divisão/intercalação
description: Utilize também a fusão de divisão para mover dados entre bases de dados fiadas.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 200a6b1bc2f960555fae1d910dfebde66628d13a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045649"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Implementar um serviço de fusão de divisão para mover dados entre bases de dados fiadas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A ferramenta de fusão de divisão permite-lhe mover dados entre bases de dados fiadas. Ver [dados em movimento entre bases de dados em nuvem escalada](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Descarregue os pacotes Split-Merge

1. Descarregue a versão mais recente do NuGet a partir de [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Abra um pedido de comando e navegue para o diretório onde descarregou nuget.exe. O download inclui comandos PowerShell.

1. Descarregue o mais recente pacote Split-Merge para o diretório atual com o comando abaixo:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

The files are placed in a directory named **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** where *x.x.xxx.x* reflects the version number. Encontre os ficheiros de serviço de fusão split-merge no sub-directório do **serviço de conteúdo\splitmerge\service,** e os scripts PowerShell split-Merge (e dlls de cliente necessários) no sub-directório de **fusão de conteúdo\splitmerge\powershell.**

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma base de dados Azure SQL que será usada como base de dados de estado de fusão de divisões. Vá ao [portal Azure.](https://portal.azure.com) Criar uma nova Base de **Dados SQL**. Dê um nome à base de dados e crie um novo administrador e senha. Certifique-se de que regista o nome e a palavra-passe para posterior utilização.

1. Certifique-se de que o seu servidor permite que os Serviços Azure se conectem ao mesmo. No portal, nas **Definições de Firewall,** certifique-se de que a definição de Permitir o acesso à definição de **Serviços Azure** está definida para **ligar**. Clique no ícone "guardar".

1. Crie uma conta de Armazenamento Azure para a saída de diagnósticos.

1. Crie um serviço Azure Cloud para o seu serviço Split-Merge.

## <a name="configure-your-split-merge-service"></a>Configure o seu serviço Split-Merge

### <a name="split-merge-service-configuration"></a>Configuração do serviço Split-Merge

1. Na pasta em que descarregou os conjuntos Split-Merge, crie uma cópia do ficheiro *ServiceConfiguration.Template.cscfg* que foi enviado ao lado de *SplitMergeService.cspkg* e rebatizando-o *ServiceConfiguration.cscfg*.

1. Open *ServiceConfiguration.cscfg* num editor de texto como o Visual Studio que valida inputs como o formato de impressões digitais do certificado.

1. Crie uma nova base de dados ou escolha uma base de dados existente para servir como base de dados de estado para operações split-merge e recuperar a cadeia de ligação dessa base de dados.

   > [!IMPORTANT]
   > Neste momento, a base de dados de estado deve utilizar a colagem latina (SQL \_ Latin1 \_ General \_ CP1 \_ CI \_ AS). Para mais informações, consulte o Nome de [Colagem do Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

   Com base de dados Azure SQL, a cadeia de ligação normalmente é da forma:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Introduza esta cadeia de ligação no ficheiro *.cscfg* nas secções de função **SplitMergeWeb** e **SplitMergeWorker** na definição ElasticScaleMetadata.

1. Para a função **SplitMergeWorker,** introduza uma cadeia de ligação válida ao armazenamento Azure para a **definição DeArmazenamentoDeConexDeDo DeLineConnectionDo** Do Trabalho.

### <a name="configure-security"></a>Configurar a segurança

Para instruções detalhadas para configurar a segurança do serviço, consulte a [configuração de segurança Split-Merge](elastic-scale-split-merge-security-configuration.md).

Para efeitos de uma simples implementação de teste para este tutorial, será realizado um conjunto mínimo de etapas de configuração para que o serviço esteja a funcionar. Estes passos permitem que apenas uma máquina/conta que as executa se comunique com o serviço.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Crie um novo diretório e a partir deste diretório execute o seguinte comando usando um Pedido de Comando de Desenvolvimento para janela [de Estúdio Visual:](https://msdn.microsoft.com/library/ms229859.aspx)

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

É-lhe pedido uma senha para proteger a chave privada. Introduza uma senha forte e confirme-a. Em seguida, é-lhe solicitado que a palavra-passe seja usada mais uma vez depois disso. Clique **Yes** sim no final para importá-lo para a loja root das autoridades de certificação fidedigna.

### <a name="create-a-pfx-file"></a>Criar um ficheiro PFX

Executar o seguinte comando a partir da mesma janela onde o vicert foi executado; Utilize a mesma palavra-passe que usou para criar o certificado:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importar o certificado de cliente para a loja pessoal

1. No Windows Explorer, clique duas *vezes em MyCert.pfx*.
2. No Assistente de Importação de **Certificado,** selecione **Utilizador Atual** e clique **em Seguinte**.
3. Confirme o caminho do ficheiro e clique **em Seguinte**.
4. Digite a palavra-passe, deixe **Incluir todas as propriedades estendidas** verificadas e clique em **Next**.
5. Deixe **automaticamente selecione automaticamente a loja de certificados[...]** verificada e clique **em Seguinte**.
6. Clique em **Terminar** e **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Faça upload do ficheiro PFX para o serviço de nuvem

1. Vá ao [portal Azure.](https://portal.azure.com)
2. Selecione **Serviços cloud**.
3. Selecione o serviço de nuvem que criou acima para o serviço Split/Merge.
4. Clique em **Certificados** no menu superior.
5. Clique em **carregar** na barra inferior.
6. Selecione o ficheiro PFX e introduza a mesma palavra-passe acima.
7. Uma vez concluída, copie a impressão digital do certificado a partir da nova entrada na lista.

### <a name="update-the-service-configuration-file"></a>Atualizar o ficheiro de configuração do serviço

Colar a impressão digital do certificado copiada acima na impressão digital/atributo de valor destas definições.
Para o papel de trabalhador:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Para o papel web:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Por favor, note que para implementações de produção certificados separados devem ser usados para a AC, para encriptação, o certificado servidor e certificados de cliente. Para obter instruções detalhadas sobre isto, consulte a [Configuração de Segurança](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Implante o seu serviço

1. Vá ao [portal Azure](https://portal.azure.com)
2. Selecione o serviço de nuvem que criou anteriormente.
3. Clique em **Descrição geral**.
4. Escolha o ambiente de encenação e, em seguida, clique em **Carregar**.
5. Na caixa de diálogo, introduza uma etiqueta de implantação. Para 'Package' e 'Configuração', clique em 'From Local' e escolha o ficheiro *SplitMergeService.cspkg* e o ficheiro cscfg que configurau anteriormente.
6. Certifique-se de que a caixa de verificação rotulada **Desdobrar mesmo que uma ou mais funções contenham uma única instância.**
7. Acerte no botão de marcação na parte inferior direita para iniciar a implantação. Espere que leve alguns minutos para terminar.

## <a name="troubleshoot-the-deployment"></a>Problemas de resolução da implantação

Se o seu papel na web não ficar online, é provável que seja um problema com a configuração de segurança. Verifique se o TLS/SSL está configurado como descrito acima.

Se o seu papel de trabalhador não estiver online, mas o seu papel na web for bem sucedido, é provavelmente um problema de ligação à base de dados de estado que criou anteriormente.

- Certifique-se de que a cadeia de ligação no seu cscfg é exata.
- Verifique se o servidor e a base de dados existem e se o id e a palavra-passe do utilizador estão corretos.
- Para a Base de Dados Azure SQL, a cadeia de ligação deve ser do formulário:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Certifique-se de que o nome do servidor não começa com **https://**.
- Certifique-se de que o seu servidor permite que os Serviços Azure se conectem ao mesmo. Para isso, abra a sua base de dados no portal e certifique-se de que a definição de Permitir o acesso à definição de **Serviços Azure** está definida para **On*****.

## <a name="test-the-service-deployment"></a>Testar a implementação do serviço

### <a name="connect-with-a-web-browser"></a>Conecte-se com um navegador web

Determine o ponto final do seu serviço Split-Merge. Pode encontrá-lo no portal indo para a **visão geral** do seu serviço de nuvem e olhando para baixo do **Site URL** do lado direito. Substitua **http://** por **https://** uma vez que as definições de segurança predefinidas desativam o ponto final http. Carregue a página para este URL no seu navegador.

### <a name="test-with-powershell-scripts"></a>Teste com scripts PowerShell

A implementação e o seu ambiente podem ser testados executando os scripts powerShell da amostra incluídos.

Os ficheiros do script incluídos são:

1. *ConfiguraçãoSampleSplitMergeEnvironment.ps1* - estabelece um nível de dados de teste para Split/Merge (ver tabela abaixo para descrição detalhada)
2. *ExecuteSampleSplitMerge.ps1* - executa operações de teste no nível de dados de teste (ver tabela abaixo para descrição detalhada)
3. *GetMappings.ps1* - script de amostra de nível superior que imprime o estado atual dos mapeamentos de fragmentos.
4. *ShardManagement.psm1* - script de ajudante que envolve a API shardManagement
5. *SqlDatabaseHelpers.psm1* - script de ajuda para criar e gerir bases de dados na Base de Dados SQL

   <table style="width:100%">
     <tr>
       <th>Ficheiro PowerShell</th>
       <th>Passos</th>
     </tr>
     <tr>
       <th rowspan="5">ConfiguraçãoSampleSplitMergeEnvironment.ps1</th>
       <td>1. Cria uma base de dados de gestor de mapas de fragmentos</td>
     </tr>
     <tr>
       <td>2. Cria 2 bases de dados.
     </tr>
     <tr>
       <td>3. Cria um mapa de fragmentos para essas bases de dados (elimina quaisquer mapas de fragmentos existentes nessas bases de dados). </td>
     </tr>
     <tr>
       <td>4. Cria uma pequena mesa de amostras em ambos os fragmentos, e povoa a mesa em um dos fragmentos.</td>
     </tr>
     <tr>
       <td>5. Declara o SchemaInfo para a mesa escada.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Ficheiro PowerShell</th>
       <th>Passos</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Envia um pedido dividido para a web frontend do Serviço Split-Merge, que divide metade dos dados do primeiro fragmento para o segundo fragmento.</td>
     </tr>
     <tr>
       <td>2. Sondagens à frente da web para o estado de pedido dividido e aguarda até que o pedido termine.</td>
     </tr>
     <tr>
       <td>3. Envia um pedido de fusão para o web frontend do Serviço Split-Merge, que move os dados do segundo fragmento de volta para o primeiro fragmento.</td>
     </tr>
     <tr>
       <td>4. Sondagens à web frontend para o estado de pedido de fusão e aguarda até que o pedido esteja concluído.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Use powerShell para verificar a sua implementação

1. Abra uma nova janela PowerShell e navegue para o diretório onde descarregou o pacote Split-Merge e, em seguida, navegue para o diretório "powershell".

2. Crie um servidor (ou escolha um servidor existente) onde o gestor de mapas de fragmentos e fragmentos serão criados.

   > [!NOTE]
   > O script *ConfiguraçãoSampleSplitMergeEnvironment.ps1* cria todas estas bases de dados no mesmo servidor por padrão para manter o script simples. Esta não é uma restrição do próprio Serviço split-merge.

   Será necessário um login de autenticação SQL com acesso de leitura/escrita aos DBs para que o serviço Split-Merge mova dados e atualize o mapa do fragmento. Uma vez que o Serviço Split-Merge funciona na nuvem, não suporta atualmente a Autenticação Integrada.

   Certifique-se de que o servidor está configurado para permitir o acesso a partir do endereço IP da máquina que executa estes scripts. Pode encontrar esta definição em servidor /Firewalls e redes virtuais / endereços IP do cliente.

3. Execute o script *SetupSampleSplitMergeEnvironment.ps1* para criar o ambiente da amostra.

   Executar este script eliminará quaisquer estruturas de dados de gestão de mapas existentes na base de dados do gestor de mapas de fragmentos e nos fragmentos. Pode ser útil reexecutar o script se desejar reinicializar o mapa ou fragmentos.

   Linha de comando da amostra:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Execute o script Getmappings.ps1 para ver os mapeamentos que existem atualmente no ambiente da amostra.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Execute o script *ExecuteSampleSplitMerge.ps1* para executar uma operação dividida (movendo metade dos dados do primeiro fragmento para o segundo fragmento) e, em seguida, uma operação de fusão (movendo os dados de volta para o primeiro fragmento). Se configurar o TLS e deixar o ponto final desativado, certifique-se de que utiliza o ponto final https://.

    Linha de comando da amostra:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Se receber o erro abaixo, é provavelmente um problema com o certificado do seu ponto final da Web. Tente ligar-se ao ponto final da Web com o seu navegador Web favorito e verifique se existe um erro de certificado.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    Se for bem sucedido, a saída deve parecer a seguinte:

    ```output
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

6. Experimente outros tipos de dados! Todos estes scripts têm um parâmetro opcional -ShardKeyType que lhe permite especificar o tipo de chave. O padrão é Int32, mas também pode especificar Int64, Guid ou Binary.

## <a name="create-requests"></a>Criar pedidos

O serviço pode ser utilizado utilizando o UI web ou importando e utilizando o módulo PowerShell SplitMerge.psm1 que submeterá os seus pedidos através da função web.

O serviço pode mover dados em ambas as tabelas e tabelas de referência. Uma mesa espélada tem uma coluna chave de sharding e tem dados de linha diferentes em cada fragmento. Uma tabela de referência não é escada, pelo que contém os mesmos dados da linha em cada fragmento. As tabelas de referência são úteis para dados que não mudam frequentemente e são usados para se juntar em tabelas esfartos em consultas.

Para realizar uma operação de fusão dividida, deve declarar as mesas e as tabelas de referência que pretende ter movido. Isto é feito com a **API SchemaInfo.** Este API está no espaço de nome **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. Para cada mesa esfumada, crie um objeto **ShardedTableInfo** descrevendo o nome do esquema principal da tabela (opcional, predefinição de "dbo"), o nome da tabela e o nome da coluna nessa tabela que contém a chave sharding.
2. Para cada tabela de referência, crie um objeto **ReferenceTableInfo** descrevendo o nome do esquema principal da tabela (opcional, predefinições para "dbo") e o nome da tabela.
3. Adicione os objetos tableInfo acima a um novo objeto **SchemaInfo.**
4. Obtenha uma referência a um objeto **ShardMapManager** e ligue para **GetSchemaInfoCollection**.
5. Adicione o **SchemaInfo** à **SchemaInfoCollection,** fornecendo o nome do mapa do fragmento.

Um exemplo disso pode ser visto no script SetupSampleSplitMergeEnvironment.ps1.

O serviço Split-Merge não cria a base de dados de destino (ou esquema para tabelas na base de dados) para si. Devem ser pré-criados antes de enviar um pedido ao serviço.

## <a name="troubleshooting"></a>Resolução de Problemas

Pode ver a mensagem abaixo ao executar os scripts PowerShell da amostra:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Este erro significa que o seu certificado TLS/SSL não está configurado corretamente. Siga as instruções na secção 'Conectar-se com um navegador web'.

Se não puder submeter pedidos, poderá ver isto:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Neste caso, verifique o seu ficheiro de configuração, em particular a definição para **WorkerRoleSynchronizationStorageAccountConnectionString**. Este erro normalmente indica que a função do trabalhador não conseguiu inicializar com sucesso a base de dados de metadados na primeira utilização.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
