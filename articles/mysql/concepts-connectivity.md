---
title: Erros de conectividade transitórios - Base de Dados Azure para MySQL
description: Aprenda a lidar com erros de conectividade transitórios e ligue-se eficientemente à Base de Dados Azure para o MySQL.
keywords: conexão mysql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação,ligar eficientemente
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb5adb3787176e3bdbfb7897aa7d7deb9cc2dae7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100146"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Manuseie erros transitórios e ligue-se eficientemente à Base de Dados Azure para mySQL

Este artigo descreve como lidar com erros transitórios e ligar eficientemente à Base de Dados Azure para o MySQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que se resolverá sozinho. Normalmente, estes erros manifestam-se como uma ligação ao servidor de base de dados que está a ser eliminado. Também não podem ser abertas novas ligações a um servidor. Erros transitórios podem ocorrer, por exemplo, quando o hardware ou falha de rede acontece. Outra razão poderia ser uma nova versão de um serviço PaaS que está a ser lançado. A maioria destes eventos são automaticamente atenuados pelo sistema em menos de 60 segundos. Uma boa prática para conceber e desenvolver aplicações na nuvem é esperar erros transitórios. Assuma que podem acontecer em qualquer componente a qualquer momento e ter a lógica adequada para lidar com estas situações.

## <a name="handling-transient-errors"></a>Manuseamento de erros transitórios

Os erros transitórios devem ser tratados utilizando a lógica de retry. Situações que devem ser consideradas:

* Um erro ocorre quando se tenta abrir uma ligação
* Uma ligação inativa é deixada no lado do servidor. Quando se tenta emitir um comando, não pode ser executado.
* Uma ligação ativa que está atualmente a executar um comando é abandonada.

O primeiro e o segundo caso são bastante diretos para lidar. Tente abrir a ligação de novo. Quando se consegue, o erro transitório foi atenuado pelo sistema. Pode utilizar novamente a sua Base de Dados Azure para o MySQL. Recomendamos que tenha esperas antes de voltar a experimentar a ligação. Afaste-se se as tentativas iniciais falharem. Desta forma, o sistema pode utilizar todos os recursos disponíveis para ultrapassar a situação de erro. Um bom padrão a seguir é:

* Aguarde 5 segundos antes da primeira tentativa.
* Para cada tentativa seguinte, o aumento exponencial da espera, até 60 segundos.
* Detete um número máximo de repetições no momento em que a sua aplicação considera que a operação falhou.

Quando uma ligação com uma transação ativa falha, é mais difícil lidar corretamente com a recuperação. Há dois casos: Se a transação foi lida apenas na natureza, é seguro reabrir a ligação e voltar a tentar a transação. Se, no entanto, a transação também estava escrita na base de dados, deve determinar se a transação foi revertida ou se foi bem sucedida antes do erro transitório. Nesse caso, pode não ter recebido o reconhecimento do servidor de base de dados.

Uma maneira de fazer isto, é gerar uma identificação única no cliente que é usado para todas as tentativas. Passa este ID único como parte da transação para o servidor e armazena-o numa coluna com um constrangimento único. Desta forma, pode voltar a tentar a transação com segurança. Será bem sucedido se a transação anterior for revertida e o ID único gerado pelo cliente ainda não existir no sistema. Falhará indicando uma violação da chave duplicada se o ID único foi previamente armazenado porque a transação anterior foi concluída com sucesso.

Quando o seu programa comunicar com a Base de Dados Azure para mySQL através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de retry para erros transitórios.

Certifique-se de testar a lógica de novo. Por exemplo, tente executar o seu código enquanto escala para cima ou para baixo os recursos computacionais da sua Base de Dados Azure para o servidor MySQL. A sua aplicação deve tratar do breve tempo de paragem que se encontra durante esta operação sem qualquer problema.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Ligue-se eficientemente à Base de Dados Azure para o MySQL

As ligações de base de dados são um recurso limitado, pelo que fazer uso eficaz do pool de ligação para aceder à Base de Dados Azure para mySQL otimiza o desempenho. A secção abaixo explica como utilizar o agrupamento de ligações ou ligações persistentes para aceder mais eficazmente à Base de Dados Azure para o MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Aceder às bases de dados utilizando o pooling de ligação (recomendado)

Gerir as ligações de base de dados pode ter um impacto significativo no desempenho da aplicação como um todo. Para otimizar o desempenho da sua aplicação, o objetivo deve ser reduzir o número de vezes estabelecidas as ligações e tempo para estabelecer ligações em caminhos-chave de código. Recomendamos vivamente a utilização de ligações de ligação à base de dados ou ligações persistentes para ligar à Base de Dados Azure para o MySQL. O agrupamento de ligação à base de dados trata da criação, gestão e atribuição de ligações à base de dados. Quando um programa solicita uma ligação à base de dados, prioriza a atribuição de ligações de bases de dados ociosas existentes, em vez da criação de uma nova ligação. Depois de o programa ter terminado a utilização da ligação à base de dados, a ligação é recuperada em preparação para uma utilização posterior, em vez de simplesmente ser encerrada.

Para uma melhor ilustração, este artigo fornece [um pedaço de código de amostra](./sample-scripts-java-connection-pooling.md) que usa java como exemplo. Para mais informações, consulte [o Apache Common DBCP.](https://commons.apache.org/proper/commons-dbcp/)

> [!NOTE]
> O servidor configura um mecanismo de timeout para fechar uma ligação que está em estado de ocioso há algum tempo para libertar recursos. Certifique-se de que configura o sistema de verificação para garantir a eficácia das ligações persistentes quando as utilizar. Para mais informações, consulte os sistemas de [verificação Configure do lado do cliente para garantir a eficácia das ligações persistentes](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Bases de dados de acesso utilizando ligações persistentes (recomendadas)

O conceito de conexões persistentes é semelhante ao do agrupamento de ligação. A substituição de ligações curtas por ligações persistentes requer apenas pequenas alterações ao código, mas tem um grande efeito em termos de melhoria do desempenho em muitos cenários típicos de aplicação.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Aceder às bases de dados utilizando mecanismo de espera e de retry com ligações curtas

Se tiver limitações de recursos, recomendamos vivamente que utilize bases de dados ou ligações persistentes para aceder a bases de dados. Se a sua aplicação utilizar ligações curtas e experimentar falhas de ligação quando se aproximar do limite superior do número de ligações simultâneas, pode tentar esperar e retentar o mecanismo. Pode definir um tempo de espera adequado, com um tempo de espera mais curto após a primeira tentativa. Depois disso, pode tentar esperar por eventos várias vezes.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configure mecanismos de verificação nos clientes para confirmar a eficácia das ligações persistentes

O servidor configura um mecanismo de tempo para fechar uma ligação que está em estado de ocioso há algum tempo para libertar recursos. Quando o cliente volta a aceder à base de dados, é equivalente a criar um novo pedido de ligação entre o cliente e o servidor. Para garantir a eficácia das ligações durante o processo de utilização das mesmas, configure um mecanismo de verificação no cliente. Como mostra o seguinte exemplo, pode utilizar a ligação Tomcat JDBC para configurar este mecanismo de verificação.

Ao definir o parâmetro TestOnBorrow, quando há um novo pedido, o conjunto de ligação verifica automaticamente a eficácia de quaisquer ligações ociosas disponíveis. Se tal ligação for eficaz, a sua piscina de ligação devolvida diretamente retira a ligação. A piscina de ligação cria então uma nova ligação eficaz e devolve-a. Este processo garante que a base de dados seja acedida de forma eficiente. 

Para obter informações sobre as definições específicas, consulte o documento oficial de introdução do [pool de ligação JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). É necessário definir principalmente os seguintes três parâmetros: TestOnBorrow (definido para verdade), ValidaçãoQuery (definido para SELECT 1) e ValidaçãoQuetempo de Tempo (definido para 1). O código de amostra específico é mostrado abaixo:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de ligação à Base de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)
