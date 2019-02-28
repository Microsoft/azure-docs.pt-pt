---
title: Trabalhar com erros transitórios - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como resolver problemas, diagnosticar e evitar um erro de ligação de SQL ou um erro transitório na base de dados do Azure SQL.
keywords: ligação de SQL, cadeia de ligação, problemas de conectividade, erro transitório, erro de ligação
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 11/14/2018
ms.openlocfilehash: 1a13f5f038afed914fd2b2a178974a0ba2e7255f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984225"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>Trabalhando com problemas de ligação de base de dados SQL e erros transitórios

Este artigo descreve como impedir, resolução de problemas, diagnosticar e mitigar erros de ligação e erros transitórios que seu aplicativo de cliente encontra quando interage com a base de dados do Azure SQL. Saiba como configurar a lógica de repetição, criar a cadeia de ligação e ajustar as outras definições de ligação.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Transitório erros (falhas transitórias)

Um erro transitório, também conhecido como uma falha transitória, tem uma causa que em breve é resolvido em si. Uma causa ocasional de erros transitórios é quando o sistema do Azure rapidamente muda recursos de hardware para balanceamento de carga melhor várias cargas de trabalho. A maioria desses eventos de reconfiguração concluída em menos de 60 segundos. Durante este intervalo de tempo de reconfiguração, poderá ter problemas de conectividade à base de dados SQL. Aplicações que se ligam à base de dados do SQL devem ser criadas para esperar estes erros transitórios. Para manipulá-las, implemente a lógica de repetição no seu código em vez disso-los aos utilizadores como erros de aplicação.

Se o seu programa cliente utiliza o ADO.NET, o seu programa é informado sobre o erro transitório, o lançamento da **SqlException**. Compare os **número** propriedade face à lista de erros transitórios que encontram-se junto à parte superior do artigo [códigos de erro SQL para aplicações de cliente da base de dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Ligação vs. o comando

A tentar a ligação de SQL ou estabelecê-lo novamente, dependendo do seguinte:

- **Ocorre um erro transitório durante uma tentativa de ligação**

Após um atraso de alguns segundos, voltar a ligar.

- **Ocorre um erro transitório durante um comando de consulta SQL**

Não repita imediatamente o comando. Em vez disso, após um atraso, recentemente estabelece a ligação. Em seguida, repita o comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Repita a lógica nos erros transitórios

Programas de cliente que, ocasionalmente, encontram um erro transitório são mais robustos, quando estas contêm lógica de repetição. Quando o seu programa se comunica com a base de dados SQL por meio de middleware de terceiros, solicitar ao fornecedor se o middleware contém a lógica de repetição para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Princípios para repetição

- Se o erro é transitório, repita para abrir uma ligação.
- Não repita diretamente um SQL `SELECT` instrução que falhou com um erro transitório. Em vez disso, estabelecer uma ligação nova e, em seguida, repita o `SELECT`.
- Quando um SQL `UPDATE` instrução de falha com um erro transitório, estabelecer uma ligação nova, antes de repetir a ATUALIZAÇÃO. A lógica de repetição tem de garantir que a transação de base de dados completa foi concluída ou que toda a transação é revertida.

### <a name="other-considerations-for-retry"></a>Outras considerações para repetição

- Um programa de lote que é iniciado após as horas de trabalho e é concluída antes de manhã puder ser muito doente com longos intervalos de tempo entre suas tentativas de repetição automaticamente.
- Um programa de interface do usuário deve considerar a tendência humana de depois demasiado longa uma espera. A solução não tem uma nova tentativa cada segundo, porque essa política pode inundar o sistema com solicitações.

### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre repetições

Recomendamos que aguarde 5 segundos antes de sua primeira repetição. Repetir após um atraso menor do que os riscos de 5 segundos sobrecarregar o serviço em nuvem. Para cada repetição posterior, o atraso se deve aumentar exponencialmente, até um máximo de 60 segundos.

Para uma discussão sobre o período de bloqueio de clientes que usam o ADO.NET, consulte [pool (ADO.NET) de conexão do SQL Server](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Também pode desejar definir um número máximo de tentativas antes de Self-é encerrado.

### <a name="code-samples-with-retry-logic"></a>Exemplos de código com a lógica de repetição

Exemplos de código com a lógica de repetição estão disponíveis em:

- [Ligar de forma resiliente ao SQL Server com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Ligar de forma resiliente ao SQL Server com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testar a sua lógica de repetição

Para testar a sua lógica de repetição, tem de simular ou fazer com que um erro que pode ser corrigido enquanto seu programa ainda está em execução.

#### <a name="test-by-disconnecting-from-the-network"></a>Testar através da rede

Uma forma que pode testar a sua lógica de repetição é desligar o computador de cliente a partir da rede, enquanto o programa está em execução. O erro é:

- **SqlException.Number** = 11001
- Mensagem: "O sistema anfitrião não é conhecido"

Como parte da primeira tentativa de repetição, o seu programa pode corrigir o problema de ortografia e, em seguida, tentar estabelecer ligação.

Para fazer este teste prático, desligue o computador a partir da rede antes de começar o seu programa. Em seguida, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa para:

- Adicione temporariamente 11001 à sua lista de erros de considerar como transitório.
- Tente estabelecer ligação primeiro como de costume.
- Depois do erro é capturado, remova 11001 da lista.
- Exiba uma mensagem que informa ao usuário para conectar o computador na rede.
- Colocar em pausa ainda mais execução utilizando o **Console. readline** método ou uma caixa de diálogo com um botão OK. O usuário pressiona a tecla Enter depois do computador está ligado à rede.
- Tente novamente estabelecer ligação, era esperado o sucesso.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Teste ao misspelling o nome de base de dados ao ligar

Seu programa pode propositadamente errar o nome de utilizador antes da primeira tentativa de ligação. O erro é:

- **SqlException.Number** = 18456
- Mensagem: "Falha de início de sessão para o utilizador"WRONG_MyUserName"."

Como parte da primeira tentativa de repetição, o seu programa pode corrigir o problema de ortografia e, em seguida, tentar estabelecer ligação.

Para fazer este teste prático, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa para:

- Adicione temporariamente 18456 à sua lista de erros de considerar como transitório.
- Adicione propositadamente 'WRONG_' para o nome de utilizador.
- Depois do erro é capturado, remova 18456 da lista.
- Remova 'WRONG_' o nome de utilizador.
- Tente novamente estabelecer ligação, era esperado o sucesso.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros de .NET SqlConnection para tentativas de ligação

Se o seu programa cliente se liga a base de dados SQL utilizando a classe do .NET Framework **System.Data.SqlClient.SqlConnection**, use o .NET 4.6.1 ou posterior (ou .NET Core) para que possa utilizar a sua funcionalidade de repetição da ligação. Para obter mais informações sobre a funcionalidade, consulte [esta página da Web](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Quando está a desenvolver o [cadeia de ligação](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para a sua **SqlConnection** de objeto, coordenar os valores entre os seguintes parâmetros:

- **ConnectRetryCount**:&nbsp;&nbsp;a predefinição é 1. O intervalo é de 0 a 255.
- **ConnectRetryInterval**:&nbsp;&nbsp;predefinido é de 1 segundo. O intervalo é de 1 a 60.
- **Tempo limite da ligação**:&nbsp;&nbsp;a predefinição é 15 segundos. O intervalo é 0 e 2147483647.

Especificamente, seus valores escolhidas devem tornar o verdadeiro de igualdade seguintes: Tempo limite da ligação = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo é igual a 10 segundos, um tempo limite de apenas alguns segundos 29 não dar ao sistema tempo suficiente para sua terceira e última novamente estabelecer ligação: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Ligação vs. o comando

O **ConnectRetryCount** e **ConnectRetryInterval** parâmetros permitem que seu **SqlConnection** objeto repita a operação do connect sem informando ou incomodar sua programa, como retornar o controle para o seu programa. As repetições podem ocorrer nas seguintes situações:

- chamada de método mySqlConnection.Open
- chamada de método mySqlConnection.Execute

Há das sutilezas. Se ocorrer um erro transitório, enquanto sua *consulta* está a ser executada, sua **SqlConnection** objeto não repita a operação de connect. Certamente, não repita a consulta. No entanto, **SqlConnection** muito rapidamente verifica a ligação antes de enviar a sua consulta para execução. Se a verificação rápida detectar um problema de ligação **SqlConnection** repetem a operação do connect. Se a nova tentativa for bem sucedida, a consulta é enviada para a execução.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinada com a lógica de repetição do aplicativo

Suponha que seu aplicativo tem a lógica de repetição personalizada robusta. Ele poderá repita a operação de ligar a quatro vezes. Se adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 para a cadeia de ligação, aumentará a contagem de repetições para 4 * 3 = 12 tentativas. Que talvez não pretenda um número elevado de tentativas.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Ligações à base de dados SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Ligação: Cadeia de ligação

A cadeia de ligação que é necessária para ligar à base de dados SQL é ligeiramente diferente da cadeia utilizada para ligar ao SQL Server. Pode copiar a cadeia de ligação da base de dados a partir da [portal do Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Ligação: Endereço IP

Tem de configurar o servidor de base de dados SQL para aceitar comunicações do endereço IP do computador que aloja o seu programa cliente. Para configurar esta configuração, edite as definições de firewall através da [portal do Azure](https://portal.azure.com/).

Caso se esqueça de configurar o endereço IP, o seu programa falha com uma mensagem de erro útil que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para obter mais informações, consulte [configurar definições de firewall na base de dados SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Ligação: Portas

Normalmente, precisa garantir que apenas a porta 1433 está aberta para comunicações de saída no computador que aloja o seu programa cliente.

Por exemplo, quando o seu programa cliente é alojado num computador Windows, pode utilizar o Windows Firewall no anfitrião para abrir a porta 1433.

1. Abra o painel de controle.
2. Selecione **todos os itens do painel de controlo** > **Windows Firewall** > **definições avançadas** > **regras de saída**   >  **Ações** > **nova regra**.

Se o seu programa cliente estiver alojado numa máquina virtual do Azure (VM), leia [portas para além do 1433 para ADO.NET 4.5 e base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter informações gerais sobre a configuração de portas e endereços IP, consulte [firewall de base de dados do Azure SQL](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Ligação: ADO.NET 4.6.2 ou posterior

Se o programa usa classes do ADO.NET, como **System.Data.SqlClient.SqlConnection** para ligar à base de dados SQL, recomendamos que utilize o .NET Framework versão 4.6.2 ou posterior.

#### <a name="starting-with-adonet-462"></a>A partir do ADO.NET 4.6.2

- A tentativa de abrir ligação seja repetida imediatamente para bases de dados SQL do Azure, melhorando assim o desempenho de aplicações com o cloud.

#### <a name="starting-with-adonet-461"></a>A partir do ADO.NET 4.6.1

- Base de dados SQL, confiabilidade foi melhorada ao abrir uma ligação ao utilizar o **SqlConnection. Open** método. O **aberto** método incorpora agora mecanismos de repetição de melhor esforço em resposta a falhas transitórias para determinados erros durante o período de tempo limite de ligação.
- Agrupamento de ligações é suportado, que inclui uma verificação de eficiente que o objeto de conexão dá seu programa está a funcionar.

Quando utiliza um objeto de conexão de um pool de conexões, recomendamos que o seu programa temporariamente fecha a conexão quando não é imediatamente em utilização. Não é Caro reabrir uma ligação, mas é criar uma nova ligação.

Se usar o ADO.NET 4.0 ou anterior, recomendamos que Atualize para o ADO.NET mais recente. A partir de Agosto de 2018, pode [transferir ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/2018/04/30/announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnóstico

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnósticos: Testar se podem ligar-se utilitários

Se o seu programa não conseguir ligar à base de dados SQL, uma opção de diagnóstico é tentando estabelecer ligação com um programa utilitário. O ideal é que o utilitário liga-se utilizando a mesma biblioteca, que utiliza o seu programa.

Em qualquer computador Windows, pode experimentar esses utilitários:

- SQL Server Management Studio (ssms.exe), que se liga através de ADO.NET
- `sqlcmd.exe`, que liga ao utilizar [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Depois de ligar o seu programa, teste se funciona de uma consulta SQL SELECT curta.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Verifique as portas abertas

Se suspeitar de que as tentativas de ligação falharem devido a problemas de porta, pode executar um utilitário no seu computador que relate as configurações da porta.

No Linux, os seguintes utilitários podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Altere o valor de exemplo para ser o seu endereço IP.

No Windows, o [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) utilitário poderá ser útil. Aqui está uma execução de exemplo que consultados a situação de porta num servidor de base de dados SQL e que foi executado num computador portátil:

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

### <a name="diagnostics-log-your-errors"></a>Diagnósticos: Registar os erros

Um problema intermitente, às vezes, melhor é diagnosticado pela deteção de um padrão geral ao longo de dias ou semanas.

O cliente pode ajudar um diagnóstico através do registo de todos os erros que encontrar. Poderá conseguir correlacionar as entradas de registo com dados de erro que registos de base de dados SQL em si internamente.

Biblioteca de Enterprise 6 (EntLib60) oferece as classes do .NET gerida para ajudá-lo com o registo. Para obter mais informações, consulte [5 - tão fácil como cair de um registo: Utilizar o Logging Application Block](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examine os registos de sistema para erros

Aqui estão algumas instruções de Transact-SQL SELECIONE consultar os registos de erros e outras informações.

| Consulta de registo | Descrição |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |O [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) exibição traz informações sobre eventos individuais, incluindo algumas que podem causar erros transitórios ou falhas de conectividade.<br/><br/>O ideal é que pode correlacionar os **start_time** ou **end_time** valores com informações sobre o quando o seu programa cliente experimentado problemas.<br/><br/>Tem de ligar para o *mestre* base de dados para executar esta consulta. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |O [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) vista oferece agregadas contagens de tipos de eventos para diagnósticos adicionais.<br/><br/>Tem de ligar para o *mestre* base de dados para executar esta consulta. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnósticos: Procurar eventos de problema no registo de base de dados SQL

Pode procurar entradas sobre eventos de problema no registo de base de dados SQL. Experimente a seguinte instrução Transact-SQL SELECT no *mestre* base de dados:

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

#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Algumas linhas retornadas da sys.fn_xe_telemetry_blob_target_read_file

O exemplo seguinte mostra uma linha retornada possível aparência. Os valores nulos mostrados, muitas vezes, não são nulos em outras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>A Enterprise Library 6

Biblioteca de Enterprise 6 (EntLib60) é uma estrutura de classes do .NET que o ajuda a implementar clientes robustos de serviços cloud, é um dos quais o serviço de base de dados SQL. Para localizar tópicos dedicados para cada área na qual EntLib60 podem ajudá-lo, consulte [Enterprise Library 6 - Abril de 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

A lógica de repetição para lidar com erros transitórios é uma área na qual EntLib60 podem ajudá-lo. Para obter mais informações, consulte [4 - Perseverance, segredo de todos os triumphs: Utilizar o bloco de aplicativo de processamento de falhas transitórias](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> O código-fonte para EntLib60 está disponível para download público a partir da [Centro de transferências](https://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft não tem planos para fazer mais atualizações de funcionalidades ou atualizações de manutenção para EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes de EntLib60 para erros transitórios e tente novamente

As seguintes classes de EntLib60 são particularmente úteis para a lógica de repetição. Todas essas classes encontram-se no ou sob o espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

No espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **RetryPolicy** classe
  - **ExecuteAction** método
- **ExponentialBackoff** classe
- **SqlDatabaseTransientErrorDetectionStrategy** class
- **ReliableSqlConnection** classe
  - **ExecuteCommand** método

No espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** classe
- **NeverTransientErrorDetectionStrategy** class

Seguem-se algumas ligações para informações sobre EntLib60:

- Download gratuito do livro: [Guia do programador para o Microsoft Enterprise Library, 2ª edição](https://www.microsoft.com/download/details.aspx?id=41145).
- Melhores práticas: [Repita a orientação geral](../best-practices-retry-general.md) tem uma excelente visão detalhada de lógica de repetição.
- Download do NuGet: [A Enterprise Library - bloco de aplicativo de processamento de falhas transitórias 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de registo

- O bloco de registo é uma solução altamente flexível e configurável, que pode utilizar para:
  - Criar e armazenar as mensagens do registo numa ampla variedade de locais.
  - Categorizar e filtrar as mensagens.
  - Recolha informações contextuais que são os requisitos de registo útil para depuração e rastreamento, bem como para auditoria e geral.
- O bloco de registo abstrai a funcionalidade de registo do destino de registo para que o código do aplicativo é consistente, independentemente da localização e o tipo de arquivo de log de destino.

Para obter mais informações, consulte [5 - tão fácil como cair de um registo: Utilizar o Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código de origem do método EntLib60 IsTransient

Em seguida, a partir da **SqlDatabaseTransientErrorDetectionStrategy** de classe, é o código-fonte c# para o **IsTransient** método. O código-fonte esclarece que erros foram considerados transitório e digna de repetição, a partir de Abril de 2013.

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

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre como resolver outros problemas comuns de ligação de base de dados SQL, consulte [resolver problemas de ligação à base de dados do Azure SQL](sql-database-troubleshoot-common-connection-issues.md).
- [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md)
- [Pool (ADO.NET) de conexão do SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Repetir a operação* é um 2.0 Apache licenciado para fins gerais de repetir a operação de biblioteca, escrita em Python,](https://pypi.python.org/pypi/retrying) para simplificar a tarefa de adicionar o comportamento de repetição para qualquer coisa.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
