---
title: Implementar um serviço de divisão/intercalação
description: Utilize a fusão dividida também para mover dados entre bases de dados de fragmentos.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792077"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Implementar um serviço de fusão dividida para mover dados entre bases de dados de fragmentos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A ferramenta de fusão dividida permite mover dados entre bases de dados de fragmentos. Ver [dados em movimento entre bases de dados de nuvem escalonadas](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Descarregue os pacotes de Split-Merge

1. Descarregue a versão mais recente do NuGet a partir do [NuGet.](https://docs.nuget.org/docs/start-here/installing-nuget)

1. Abra um pedido de comando e navegue para o diretório onde descarregou nuget.exe. O download inclui comandos PowerShell.

1. Descarregue o mais recente pacote de Split-Merge para o diretório atual com o comando abaixo:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Os ficheiros são colocados num diretório chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** onde *x.x.xxx.x* reflete o número da versão. Encontre os ficheiros de serviço de fusão dividida no sub-directório **de serviço\splitmerge\service** e no Split-Merge scripts PowerShell (e dlls de clientes necessários) no sub-directório **de conteúdo\splitmerge\powershell.**

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma base de dados de base de dados Azure SQL que será usada como base de dados de estado de fusão dividida. Aceda ao [Portal do Azure](https://portal.azure.com). Criar uma nova **Base de Dados SQL.** Dê à base de dados um nome e crie um novo administrador e senha. Certifique-se de que grava o nome e a palavra-passe para posterior utilização.

1. Certifique-se de que o seu servidor permite que os Serviços Azure se conectem ao mesmo. No portal, nas **Definições** de Firewall , certifique-se de que o acesso à definição **de Serviços Azure** está definido para **On**. Clique no ícone "salvar".

1. Crie uma conta de Armazenamento Azure para a saída de diagnósticos.

1. Crie um Serviço Azure Cloud para o seu serviço Split-Merge.

## <a name="configure-your-split-merge-service"></a>Configure o seu serviço de Split-Merge

### <a name="split-merge-service-configuration"></a>Split-Merge configuração de serviço

1. Na pasta em que descarregou os conjuntos de Split-Merge, crie uma cópia do ficheiro *ServiceConfiguration.Template.cscfg* que enviou ao lado do *SplitMergeService.cspkg* e rebatizá-lo *ServiceConfiguration.cscfg*.

1. Open *ServiceConfiguration.cscfg* em um editor de texto como Visual Studio que valida entradas como o formato de impressões digitais de certificado.

1. Crie uma nova base de dados ou escolha uma base de dados existente para servir como base de dados de estado para operações de Split-Merge e recuperar a cadeia de ligação dessa base de dados.

   > [!IMPORTANT]
   > Neste momento, a base de dados de estado deve utilizar a colagem latina (SQL \_ Latin1 \_ General \_ CP1 \_ CI \_ AS). Para obter mais informações, consulte [o Nome de Colagem do Windows (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql).

   Com a Base de Dados Azure SQL, a cadeia de ligação normalmente é do formulário:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Introduza esta cadeia de ligação no ficheiro *.cscfg* nas secções de funções **SplitMergeWeb** e **SplitMergeWorker** na definição ElasticScaleMetadata.

1. Para a função **SplitMergeWorker,** introduza uma cadeia de ligação válida ao armazenamento Azure para a definição **WorkerRoleSynchronizationStorageAccountConnectionS.**

### <a name="configure-security"></a>Configurar a segurança

Para obter instruções detalhadas para configurar a segurança do serviço, consulte a [configuração de segurança Split-Merge](elastic-scale-split-merge-security-configuration.md).

Para efeitos de uma simples implantação de teste para este tutorial, será realizado um conjunto mínimo de etapas de configuração para pôr o serviço a funcionar. Estes passos permitem que apenas uma máquina/conta as execute para comunicar com o serviço.

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Crie um novo diretório e a partir deste diretório execute o seguinte comando utilizando uma [visão de comando do desenvolvedor para](/dotnet/framework/tools/developer-command-prompt-for-vs) a janela do Estúdio Visual:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Pede-se-lhe uma senha para proteger a chave privada. Introduza uma senha forte e confirme- a. Em seguida, é solicitado que a palavra-passe seja usada mais uma vez depois disso. Clique em **Sim** no final para importá-lo para a loja Root das Autoridades de Certificação Fidedigna.

### <a name="create-a-pfx-file"></a>Criar um ficheiro PFX

Executar o seguinte comando a partir da mesma janela onde o certificado foi executado; utilize a mesma palavra-passe que usou para criar o certificado:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importe o certificado de cliente na loja pessoal

1. No Windows Explorer, clique duas vezes *em MyCert.pfx*.
2. No **Certificado De Import Wizard** selecione **O Utilizador Atual** e clique em **Seguinte**.
3. Confirme o caminho do ficheiro e clique em **Seguinte**.
4. Digite a palavra-passe, **deixe Incluir todas as propriedades estendidas** verificadas e clique em **Seguinte**.
5. Deixe **selecionar automaticamente a loja de certificados[...]** verificada e clique em **Seguinte**.
6. Clique **em Terminar** e **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Faça o upload do ficheiro PFX para o serviço de nuvem

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Selecione **Serviços cloud**.
3. Selecione o serviço de nuvem que criou acima para o serviço Split/Merge.
4. Clique em **Certificados** no menu superior.
5. Clique em **Upload** na barra inferior.
6. Selecione o ficheiro PFX e introduza a mesma palavra-passe acima.
7. Uma vez concluído, copie a impressão digital do certificado da nova entrada na lista.

### <a name="update-the-service-configuration-file"></a>Atualizar o ficheiro de configuração do serviço

Cole a impressão digital do certificado copiada acima no atributo de impressão/valor destas definições.
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

Por favor, note que para implementações de produção devem ser utilizados certificados separados para a AC, para encriptação, certificado de Servidor e certificados de cliente. Para obter instruções detalhadas sobre este documento, consulte [a Configuração de Segurança](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Implemente o seu serviço

1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Selecione o serviço de nuvem que criou anteriormente.
3. Clique em **Descrição geral**.
4. Escolha o ambiente de encenação e, em seguida, clique em **Upload**.
5. Na caixa de diálogo, introduza uma etiqueta de implantação. Para 'Pacote' e 'Configuração', clique em 'From Local' e escolha o ficheiro *SplitMergeService.cspkg* e o seu ficheiro cscfg que configuraste anteriormente.
6. Certifique-se de que a caixa de verificação rotulada **Implementar mesmo que uma ou mais funções contenham uma única instância** seja verificada.
7. Acerte o botão de tique no canto inferior direito para iniciar a implantação. Espere que leve alguns minutos para completar.

## <a name="troubleshoot-the-deployment"></a>Resolução de problemas da implantação

Se a sua função web não estiver online, é provável que seja um problema com a configuração de segurança. Verifique se o TLS/SSL está configurado como descrito acima.

Se o seu papel de trabalhador não estiver online, mas o seu papel na web for bem sucedido, é provavelmente um problema de ligação à base de dados de estado que criou anteriormente.

- Certifique-se de que a cadeia de ligação do seu cscfg está correta.
- Verifique se o servidor e a base de dados existem e se o id do utilizador e a palavra-passe estão corretos.
- Para a Base de Dados Azure SQL, a cadeia de ligação deve ser do formulário:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Certifique-se de que o nome do servidor não começa com **https://**.
- Certifique-se de que o seu servidor permite que os Serviços Azure se conectem ao mesmo. Para isso, abra a sua base de dados no portal e certifique-se de que a definição de Permitir o **acesso aos Serviços Azure** está definida para **On*****

## <a name="test-the-service-deployment"></a>Testar a implementação do serviço

### <a name="connect-with-a-web-browser"></a>Conecte-se com um navegador web

Determine o ponto final web do seu serviço de Split-Merge. Pode encontrar isso no portal indo para a **visão geral** do seu serviço em nuvem e olhando para o URL do **site** no lado direito. Substitua **http://** por **https://** uma vez que as definições de segurança predefinidos desativam o ponto de terminação HTTP. Carregue a página para este URL no seu navegador.

### <a name="test-with-powershell-scripts"></a>Teste com scripts PowerShell

A implementação e o seu ambiente podem ser testados executando os scripts powerShell da amostra incluídos.

> [!IMPORTANT]
> Os scripts da amostra são executados no PowerShell 5.1. Atualmente, não funcionam no PowerShell 6 ou mais tarde.

Os ficheiros de script incluídos são:

1. *SetupSampleSplitMergeEnvironment.ps1* - estabelece um nível de dados de teste para split/merge (ver tabela abaixo para descrição detalhada)
2. *ExecuteSampleSplitMerge.ps1* - executa operações de ensaio no nível dos dados de ensaio (ver quadro abaixo para descrição detalhada)
3. *GetMappings.ps1* - script de amostra de nível superior que imprime o estado atual dos mapeamentos de fragmentos.
4. *ShardManagement.psm1*  - script de ajudante que envolve a API de ShardManagement
5. *SqlDatabaseHelpers.psm1* - script helper para criar e gerir bases de dados na Base de Dados SQL

   <table style="width:100%">
     <tr>
       <th>Ficheiro PowerShell</th>
       <th>Passos</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Cria uma base de dados de gestor de mapas de fragmentos</td>
     </tr>
     <tr>
       <td>2. Cria 2 bases de dados de fragmentos.
     </tr>
     <tr>
       <td>3. Cria um mapa de fragmentos para essas bases de dados (elimina quaisquer mapas de fragmentos existentes nessas bases de dados). </td>
     </tr>
     <tr>
       <td>4. Cria uma pequena mesa de amostra em ambos os fragmentos, e povoa a mesa em um dos fragmentos.</td>
     </tr>
     <tr>
       <td>5. Declara o SchemaInfo para a mesa de sharded.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Ficheiro PowerShell</th>
       <th>Passos</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Envia um pedido dividido para o frontend web do Serviço Split-Merge, que divide metade dos dados do primeiro fragmento para o segundo fragmento.</td>
     </tr>
     <tr>
       <td>2. Sondagem o frontend web para o estado do pedido de divisão e aguarda até que o pedido termine.</td>
     </tr>
     <tr>
       <td>3. Envia um pedido de fusão para o frontend web do Serviço Split-Merge, que move os dados do segundo fragmento de volta para o primeiro fragmento.</td>
     </tr>
     <tr>
       <td>4. Sondagem o frontend web para o estado do pedido de fusão e aguarda até que o pedido esteja concluído.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Utilize o PowerShell para verificar a sua implantação

1. Abra uma nova janela PowerShell e navegue para o diretório onde descarregou o pacote de Split-Merge e, em seguida, navegue no diretório "powershell".

2. Crie um servidor (ou escolha um servidor existente) onde o gestor de mapas de fragmentos e fragmentos serão criados.

   > [!NOTE]
   > O *SetupSampleSplitMergeEnvironment.ps1* script cria todas estas bases de dados no mesmo servidor por padrão para manter o script simples. Isto não é uma restrição do próprio Serviço de Split-Merge.

   Será necessário um login de autenticação SQL com acesso de leitura/escrita aos DBs para que o serviço Split-Merge mova dados e atualize o mapa de fragmentos. Uma vez que o Serviço Split-Merge funciona na nuvem, não suporta atualmente autenticação integrada.

   Certifique-se de que o servidor está configurado para permitir o acesso a partir do endereço IP da máquina que executa estes scripts. Pode encontrar esta definição no servidor SQL / Firewalls e redes virtuais / endereços IP do Cliente.

3. Execute *o* SetupSampleSplitMergeEnvironment.ps1script para criar o ambiente de amostragem.

   A execução deste script eliminará quaisquer estruturas de dados de gestão de mapas de fragmentos existentes na base de dados do gestor de mapas de fragmentos e nos fragmentos. Pode ser útil refazer o script se desejar re-inicializar o mapa de fragmentos ou fragmentos.

   Linha de comando de amostra:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Execute o Getmappings.ps1 script para ver os mapeamentos que existem atualmente no ambiente da amostra.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Execute o *ExecuteSampleSplitMerge.ps1* script para executar uma operação dividida (movendo metade dos dados do primeiro fragmento para o segundo fragmento) e, em seguida, uma operação de fusão (movendo os dados de volta para o primeiro fragmento). Se configurar o TLS e deixar o ponto final desativado, certifique-se de que utiliza o https:// ponto final.

    Linha de comando de amostra:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Se receber o erro abaixo, é provavelmente um problema com o certificado de ponta web da Web. Tente ligar-se ao ponto final da Web com o seu navegador Web favorito e verifique se existe um erro de certificado.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    Se tiver sucesso, a saída deve parecer abaixo:

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

6. Experimente outros tipos de dados! Todos estes scripts têm um parâmetro opcional -ShardKeyType que permite especificar o tipo de chave. O padrão é Int32, mas também pode especificar Int64, Guid ou Binário.

## <a name="create-requests"></a>Criar pedidos

O serviço pode ser utilizado através da UI web ou importando e utilizando o módulo SplitMerge.psm1 PowerShell que submeterá os seus pedidos através da função web.

O serviço pode mover dados em tabelas e tabelas de referência. Uma tabela de fragmentos tem uma coluna-chave de fragmentos e tem diferentes dados de linha em cada fragmento. Uma tabela de referência não é estalida, pelo que contém os mesmos dados de linha em cada fragmento. As tabelas de referência são úteis para dados que não mudam frequentemente e são usados para se juntar com tabelas de fragmentos em consultas.

Para realizar uma operação de fusão dividida, deve declarar as tabelas e tabelas de referência que deseja ter movido. Isto é realizado com a **API SchemaInfo.** Esta API encontra-se no espaço de **nomes Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. Para cada mesa em pedaços, crie um objeto **ShardedTableInfo** descrevendo o nome do esquema parental da tabela (opcional, predefinido a "dbo"), o nome da tabela e o nome da coluna nessa tabela que contém a chave de fragmentos.
2. Para cada tabela de referência, crie um objeto **ReferenceTableInfo** que descreva o nome do esquema principal da tabela (opcional, predefinição para "dbo") e o nome da tabela.
3. Adicione os objetos TableInfo acima a um novo objeto **SchemaInfo.**
4. Obtenha uma referência a um objeto **ShardMapManager** e ligue para **GetSchemaInfoCollection**.
5. Adicione o **SchemaInfo** à **SchemaInfoCollection,** fornecendo o nome do mapa de fragmentos.

Um exemplo disso pode ser visto no SetupSampleSplitMergeEnvironment.ps1 script.

O serviço Split-Merge não cria a base de dados-alvo (ou esquema para quaisquer tabelas na base de dados) para si. Devem ser pré-criados antes de enviar um pedido ao serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Pode ver a mensagem abaixo ao executar os scripts PowerShell da amostra:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Este erro significa que o seu certificado TLS/SSL não está configurado corretamente. Por favor, siga as instruções na secção 'Conectar-se com um navegador web'.

Se não conseguir submeter pedidos, poderá ver isto:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Neste caso, verifique o seu ficheiro de configuração, em particular a definição para **WorkerRoleSynchronizationStorageAccountConnectionString**. Este erro normalmente indica que a função do trabalhador não conseguiu inicializar com sucesso a base de dados de metadados na primeira utilização.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png