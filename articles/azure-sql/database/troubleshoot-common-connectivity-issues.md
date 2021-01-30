---
title: Trabalhar com erros transitórios
description: Aprenda a resolver problemas, diagnosticar e evitar um erro de ligação SQL ou erro transitório ao ligar-se à Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics.
keywords: conexão sql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093762"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Resolução de erros de conexão transitórios na Base de Dados SQL e na SQL Gestd instance

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo descreve como prevenir, resolver problemas, diagnosticar e mitigar erros de ligação e erros transitórios que a sua aplicação do cliente encontra quando interage com a Azure SQL Management Instance e Azure Synapse Analytics. Aprenda a configurar a lógica de retrip, construa a cadeia de ligação e ajuste outras definições de ligação.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erros transitórios (falhas transitórias)

Um erro transitório, também conhecido como uma falha transitória, tem uma causa subjacente que logo se resolve. Uma causa ocasional de erros transitórios é quando o sistema Azure rapidamente transfere recursos de hardware para um melhor equilíbrio de carga várias cargas de trabalho. A maioria destes eventos de reconfiguração terminam em menos de 60 segundos. Durante este período de tempo de reconfiguração, poderá ter problemas em ligar-se à sua base de dados na Base de Dados SQL. As aplicações que se ligam à sua base de dados devem ser construídas para esperar estes erros transitórios. Para lidar com os mesmos, implementar a lógica de reação no seu código em vez de os apresentar aos utilizadores como erros de aplicação.

Se o seu programa de clientes utilizar ADO.NET, o seu programa é informado sobre o erro transitório pelo lançamento da **SqlException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Conexão vs. comando

Recandidatar a ligação SQL Database e SQL Managed Instance ou restabelece-la novamente, dependendo do seguinte:

- **Um erro transitório ocorre durante uma tentativa de ligação**

Após um atraso de alguns segundos, recandiduça a ligação.

- **Um erro transitório ocorre durante uma base de dados SQL e comando de consulta de instância gerida SQL**

Não relemisse imediatamente o comando. Em vez disso, depois de um atraso, estabelecer recentemente a ligação. Então, recandidutar o comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Repita a lógica nos erros transitórios

Os programas de clientes que ocasionalmente encontram um erro transitório são mais robustos quando contêm lógica de retíduo. Quando o seu programa comunicar com a sua base de dados na BASE de Dados SQL através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Princípios para a repetição

- Se o erro for transitório, tentar abrir uma ligação.
- Não relembare diretamente uma base de dados SQL ou uma declaração de instância gerida sql `SELECT` que falhou com um erro transitório. Em vez disso, estabeleça uma nova ligação e, em seguida, retry o `SELECT` .
- Quando uma declaração de base de dados sql ou sql de instância gerida `UPDATE` falha com um erro transitório, estabeleça uma nova ligação antes de voltar a tentar a ATUALIZAÇÃO. A lógica de reequipamento deve garantir que toda a transação de base de dados tenha terminado ou que toda a transação seja revertida.

### <a name="other-considerations-for-retry"></a>Outras considerações para redaçar

- Um programa de lote que começa automaticamente após o horário de trabalho e termina antes da manhã pode dar-se ao luxo de ser muito paciente com intervalos de tempo longos entre as suas tentativas de reesporção.
- Um programa de interface de utilizador deve explicar a tendência humana de desistir após uma espera de muito tempo. A solução não deve voltar a tentar de poucos em poucos segundos, porque essa política pode inundar o sistema com pedidos.

### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre recaídas

Recomendamos que aguarde 5 segundos antes da primeira repetição. Tentar novamente depois de um atraso inferior a 5 segundos corre o risco de sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Para uma discussão sobre o período de bloqueio para clientes que utilizam ADO.NET, consulte [Connection pooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

Também é melhor definir um número máximo de retrações antes do programa se auto-terminar.

### <a name="code-samples-with-retry-logic"></a>Amostras de código com lógica de retíria

Exemplos de código com lógica de retíria estão disponíveis em:

- [Ligue resilientemente ao Azure SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Conecte-se resilientemente ao Azure SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Teste a sua lógica de retículo

Para testar a sua lógica de reagem, tem de simular ou causar um erro que possa ser corrigido enquanto o programa ainda está em funcionamento.

#### <a name="test-by-disconnecting-from-the-network"></a>Teste desligando-se da rede

Uma forma de testar a sua lógica de repetição é desligar o computador cliente da rede enquanto o programa está em execução. O erro é:

- **SqlException.Number** = 11001
- Mensagem: "Nenhum anfitrião deste tipo é conhecido"

Como parte da primeira tentativa de repetição, pode voltar a ligar o computador cliente à rede e, em seguida, tentar ligar.

Para tornar este teste prático, desligue o computador da rede antes de iniciar o seu programa. Em seguida, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 11001 à sua lista de erros a considerar como transitórios.
- Tente a sua primeira ligação como de costume.
- Depois de o erro ser apanhado, retire o 11001 da lista.
- Apresente uma mensagem que diga ao utilizador para ligar o computador à rede.
- Faça uma pausa na execução utilizando o método **Console.ReadLine** ou um diálogo com um botão OK. O utilizador pressiona a tecla Enter depois de o computador estar ligado à rede.
- Tentar novamente ligar-se, esperando sucesso.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Teste, soletrando mal o nome de utilizador ao ligar

O seu programa pode soletrar mal o nome de utilizador antes da primeira tentativa de ligação. O erro é:

- **SqlException.Number** = 18456
- Mensagem: "O login falhou para o utilizador 'WRONG_MyUserName'."

Como parte da primeira tentativa de relagem, o seu programa pode corrigir o erro ortográfico e, em seguida, tentar ligar.

Para tornar este teste prático, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 18456 à sua lista de erros a considerar como transitórios.
- Adicione propositadamente "WRONG_" ao nome de utilizador.
- Depois de o erro ser apanhado, retire 18456 da lista.
- Remova 'WRONG_' do nome de utilizador.
- Tentar novamente ligar-se, esperando sucesso.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection parâmetros para retíria de conexão

Se o seu programa de clientes ligar à sua base de dados na Base de Dados SQL utilizando o Sistema de Classe Quadro **.NET.Data.SqlClient.SqlConnection,** utilize .NET 4.6.1 ou mais tarde (ou .NET Core) para que possa utilizar a sua função de relemtivo de ligação. Para obter mais informações sobre a funcionalidade, consulte [SqlConnection.ConnectionString Property](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Quando construir a [cadeia de ligação](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) para o seu objeto **SqlConnection,** coordene os valores entre os seguintes parâmetros:

- **ConnectRetryCount**: &nbsp; &nbsp; O padrão é 1. O intervalo é de 0 a 255.
- **ConnectRetryInterval:** &nbsp; &nbsp; O padrão é de 10 segundos. O intervalo é de 1 a 60.
- **Intervalo de ligação**: &nbsp; &nbsp; O predefinido é de 15 segundos. O intervalo é de 0 a 2147483647.

Especificamente, os seus valores escolhidos devem tornar a igualdade verdadeira: Intervalo de ligação = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo for igual a 10 segundos, um intervalo de apenas 29 segundos não dá ao sistema tempo suficiente para a sua terceira e última repetição de ligação: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Conexão vs. comando

Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** permitem que o seu objeto **SqlConnection** volte a tentar a operação de ligação sem avisar ou incomodar o seu programa, tal como devolver o controlo ao seu programa. As retrações podem ocorrer nas seguintes situações:

- SqlConnection.Chamada de método aberto
- SqlConnection.Exechamada de método bonito

Há uma subtileza. Se ocorrer um erro transitório durante *a execução da sua consulta,* o seu objeto **SqlConnection** não se requalificação da operação de ligação. Certamente não relemca a sua pergunta. No entanto, **a SqlConnection** verifica rapidamente a ligação antes de enviar a sua consulta para execução. Se a verificação rápida detetar um problema de ligação, **a SqlConnection** retira a operação de ligação. Se a repetição for bem sucedida, a sua consulta será enviada para execução.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Se ConnectRetryCount ser combinado com a lógica de relagem de aplicações

Suponha que a sua aplicação tenha uma lógica de relíduo personalizada robusta. Pode voltar a tentar a operação de ligação quatro vezes. Se adicionar **ConnectRetryInterval** e **ConnectRetryCount** =3 à sua cadeia de ligação, aumentará a contagem de repetição para 4 * 3 = 12 retrílias. Pode não pretender um número tão elevado de recauchutagens.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Ligações à sua base de dados na Base de Dados SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Ligação: Cadeia de ligação

A cadeia de ligação necessária para ligar à sua base de dados é ligeiramente diferente da cadeia utilizada para ligar ao SQL Server. Pode copiar a cadeia de ligação da sua base de dados a partir do [portal Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Ligação: endereço IP

Tem de configurar a Base de Dados SQL para aceitar a comunicação a partir do endereço IP do computador que acolhe o seu programa de cliente. Para configurar esta configuração, edite as definições de firewall através do [portal Azure](https://portal.azure.com/).

Se se esquecer de configurar o endereço IP, o seu programa falha com uma mensagem de erro útil que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para obter mais informações, consulte [as definições de firewall configure na Base de Dados SQL](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Ligação: Portas

Normalmente, é necessário garantir que apenas a porta 1433 está aberta para comunicação de saída no computador que acolhe o seu programa de clientes.

Por exemplo, quando o programa do seu cliente está hospedado num computador Windows, pode utilizar o Windows Firewall no anfitrião para abrir a porta 1433.

1. Abra o Painel de Controlo.
2. Selecione **todos os itens do painel de controlo** Windows  >  **Firewall**  >  **Definições avançadas**  >  **Regras de saída**  >  **Ações** nova regra  >  **.**

Se o seu programa de clientes estiver hospedado numa máquina virtual Azure (VM), leia [portas para além de 1433 para ADO.NET 4.5 e SQL Database](adonet-v12-develop-direct-route-ports.md).

Para obter informações sobre a configuração de portas e endereços IP na sua base de dados, consulte [a firewall da Base de Dados Azure SQL](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Ligação: ADO.NET 4.6.2 ou posterior

Se o seu programa utilizar ADO.NET aulas como **System.Data.SqlClient.SqlConnection** para ligar à Base de Dados SQL, recomendamos que utilize a versão 4.6.2 ou posterior do Quadro NET.

#### <a name="starting-with-adonet-462"></a>Começando com ADO.NET 4.6.2

- A ligação aberta tenta ser novamente julgada para o Azure SQL, melhorando assim o desempenho de aplicações ativadas pela nuvem.

#### <a name="starting-with-adonet-461"></a>Começando com ADO.NET 4.6.1

- Para a Base de Dados SQL, a fiabilidade é melhorada quando abre uma ligação utilizando o método **SqlConnection.Open.** O método **Open** incorpora agora mecanismos de reorientação de melhor esforço em resposta a falhas transitórias para determinados erros dentro do período de tempo de ligação.
- O agrupamento de ligação é suportado, o que inclui uma verificação eficiente de que o objeto de ligação que dá ao seu programa está funcionando.

Quando utilizar um objeto de ligação a partir de uma piscina de ligação, recomendamos que o seu programa feche temporariamente a ligação quando esta não estiver imediatamente a ser utilizada. Não é caro reabrir uma ligação, mas é para criar uma nova ligação.

Se utilizar ADO.NET 4.0 ou mais cedo, recomendamos que faça upgrade para as ADO.NET mais recentes. A partir de agosto de 2018, pode [baixar ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnóstico

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnósticos: Teste se os utilitários podem ligar-se

Se o seu programa não conseguir ligar-se à sua base de dados na Base de Dados SQL, uma opção de diagnóstico é tentar ligar-se a um programa de utilidade. Idealmente, o utilitário conecta-se utilizando a mesma biblioteca que o seu programa utiliza.

Em qualquer computador Windows, pode experimentar estes utilitários:

- SQL Server Management Studio (ssms.exe), que se conecta utilizando ADO.NET
- `sqlcmd.exe`, que se conecta utilizando [o ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

Depois de o seu programa estar ligado, teste se funciona uma consulta CURTA SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Verifique as portas abertas

Se suspeitar que as tentativas de ligação falham devido a problemas de porta, pode executar um utilitário no seu computador que reporta sobre as configurações da porta.

No Linux, os seguintes utilitários podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Altere o valor de exemplo para ser o seu endereço IP.

No Windows, o [ utilitárioPortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) pode ser útil. Aqui está uma execução de exemplo que questionou a situação do porto numa base de dados na Base de Dados SQL e que foi executada num computador portátil:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnósticos: Registar os seus erros

Um problema intermitente é por vezes melhor diagnosticado pela deteção de um padrão geral ao longo de dias ou semanas.

O seu cliente pode ajudar num diagnóstico registando todos os erros que encontrar. Poderá correlacionar as entradas de registo com dados de erro que a SQL Database regista internamente.

A Enterprise Library 6 (EntLib60) oferece aulas geridas .NET para ajudar na exploração madeireira. Para obter mais informações, consulte [5 - Tão fácil como cair de um tronco: Utilize o Bloco de Aplicação de Registo](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examinar registos do sistema por erros

Aqui estão algumas declarações Do Transt-SQL SELECT que consultam registos de erros e outras informações.

| Consulta de log | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A [sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) visão oferece informações sobre eventos individuais, que inclui alguns que podem causar erros transitórios ou falhas de conectividade.<br/><br/>Idealmente, pode correlacionar os valores **start_time** ou **end_time** com informações sobre quando o seu programa de cliente sofre problemas.<br/><br/>Tem de se ligar à base de *dados principal* para executar esta consulta. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A [visão sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) oferece contagens agregadas de tipos de eventos para diagnósticos adicionais.<br/><br/>Tem de se ligar à base de *dados principal* para executar esta consulta. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnósticos: Pesquisa de eventos problemáticos no registo de base de dados SQL

Pode pesquisar por entradas sobre eventos problemáticos no registo sql Database. Experimente a seguinte declaração De Seleção Transact-SQL na base de dados *principal:*

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Algumas filas devolvidas de sys.fn_xe_telemetry_blob_target_read_file

O exemplo que se segue mostra como pode ser uma linha retornado. Os valores nulos apresentados muitas vezes não são nulos noutras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca Empresarial 6

A Enterprise Library 6 (EntLib60) é um quadro de classes .NET que o ajuda a implementar clientes robustos de serviços na nuvem, um dos quais é a SQL Database. Para localizar tópicos dedicados a cada área em que o EntLib60 pode ajudar, consulte [a Enterprise Library 6 - abril 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)).

A lógica de recandidúz para lidar com erros transitórios é uma área em que o EntLib60 pode ajudar. Para mais informações, consulte [4 - Perseverança, segredo de todos os triunfos: Utilize o Bloco de Aplicação de Tratamento de Avarias Transitórios](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> O código fonte do EntLib60 está disponível para download público a partir do [Centro de Descarregamento.](https://github.com/MicrosoftArchive/enterprise-library) A Microsoft não tem planos para fazer mais atualizações de funcionalidades ou atualizações de manutenção para o EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Aulas de EntLib60 para erros transitórios e redandimento

As seguintes aulas de EntLib60 são particularmente úteis para a lógica de relcurá-lo. Todas estas classes encontram-se dentro ou sob o espaço de nome **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

No espaço de **nomes Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling:**

- **Aula de RetryPolicy**
  - **Método executaracção**
- **Aula de Backoff Exponencial**
- **SqlDatabaseTransientErrorDetectionStrategy**
- **Aula de ReliableSqlConnection**
  - **Executar Método decomedição**

No espaço de **nomes Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport:**

- **Aula detrategia de sempre TransienteErrorDetectionStrategy**
- **Classe NeverTransientErrorDetectionStrategy**

Aqui estão algumas ligações com informações sobre o EntLib60:

- Download gratuito de livros: [Guia do Desenvolvedor para a Microsoft Enterprise Library, 2ª edição](https://www.microsoft.com/download/details.aspx?id=41145).
- Boas práticas: [A orientação geral da retry](/azure/architecture/best-practices/transient-faults) tem uma excelente discussão aprofundada sobre a lógica de retíria.
- Descarregamento nuGet: [Enterprise Library - Transiente Falha no Bloco de Aplicação 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de registo

- O bloco de madeira é uma solução altamente flexível e configurável que pode utilizar para:
  - Crie e guarde as mensagens de registo numa grande variedade de locais.
  - Categorize e filtre mensagens.
  - Recolher informações contextuais que são úteis para depurar e rastrear, bem como para os requisitos de auditoria e registo geral.
- O bloco de registo abstrata a funcionalidade de registo a partir do destino de registo para que o código de aplicação seja consistente, independentemente da localização e tipo da loja de registo sonoro alvo.

Para obter mais informações, consulte [5 - Tão fácil como cair de um tronco: Utilize o Bloco de Aplicação de Registo](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código fonte do método EntLib60 IsTransient

Em seguida, da classe **SqlDataTransientErrorDetectionStrategy,** é o código fonte C# para o método **IsTransient.** O código fonte esclarece quais os erros considerados transitórios e dignos de novo, a partir de abril de 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de conexão para Base de Dados SQL e Servidor SQL](connect-query-content-reference-guide.md#libraries)
- [Agrupamento de conexão (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Retrying* é uma biblioteca de retíria licenciada apache 2.0, escrita em Python,](https://pypi.python.org/pypi/retrying) para simplificar a tarefa de adicionar o comportamento de retagem a qualquer coisa.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php