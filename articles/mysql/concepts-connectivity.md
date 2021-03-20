---
title: Erros de conectividade transitórios - Base de Dados Azure para o MySQL
description: Aprenda a lidar com erros de conectividade transitórios e conecte-se eficientemente à Base de Dados Azure para o MySQL.
keywords: conexão mysql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação,conecte-se eficientemente
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542614"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Lidar com erros transitórios e ligar eficientemente à Base de Dados Azure para o MySQL

Este artigo descreve como lidar com erros transitórios e ligar-se eficientemente à Base de Dados Azure para o MySQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como falha transitória, é um erro que se resolverá sozinho. Normalmente, estes erros manifestam-se como uma ligação ao servidor de base de dados que está a ser largada. Também não podem ser abertas novas ligações a um servidor. Podem ocorrer erros transitórios, por exemplo, quando o hardware ou falha de rede acontece. Outra razão poderia ser uma nova versão de um serviço PaaS que está a ser lançado. A maioria destes eventos são automaticamente atenuados pelo sistema em menos de 60 segundos. Uma das melhores práticas para conceber e desenvolver aplicações na nuvem é esperar erros transitórios. Assuma que podem acontecer em qualquer componente a qualquer momento e ter a lógica adequada para lidar com estas situações.

## <a name="handling-transient-errors"></a>Manipulação de erros transitórios

Os erros transitórios devem ser manuseados utilizando lógica de repetição. Situações que devem ser consideradas:

* Um erro ocorre quando se tenta abrir uma ligação
* Uma ligação ociosa é deixada no lado do servidor. Quando se tenta emitir um comando, não pode ser executado.
* Uma ligação ativa que está atualmente a executar um comando é largada.

O primeiro e o segundo caso são bastante diretos para a frente. Tente abrir a ligação de novo. Quando tiver sucesso, o erro transitório foi atenuado pelo sistema. Pode utilizar novamente a sua Base de Dados Azure para o MySQL. Recomendamos que tenha esperado antes de voltar a tentar a ligação. Afaste-se se as primeiras tentativas falharem. Desta forma, o sistema pode utilizar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a seguir é:

* Aguarde 5 segundos antes da primeira repetição.
* Para cada uma das seguintes, aumente a espera exponencialmente, até 60 segundos.
* Descoda um número máximo de recauchutagens e o seu pedido considera que a operação falhou.

Quando uma ligação com uma transação ativa falha, é mais difícil lidar corretamente com a recuperação. Há dois casos: Se a transação foi apenas de natureza lida, é seguro reabrir a ligação e voltar a tentar a transação. Se, no entanto, a transação também estava a escrever para a base de dados, deve determinar se a transação foi revertida ou se foi bem sucedida antes do erro transitório acontecer. Nesse caso, pode não ter recebido o reconhecimento do servidor de base de dados.

Uma maneira de fazer isso, é gerar uma identificação única no cliente que é usado para todas as retrórias. Você passa este ID único como parte da transação para o servidor e para armazená-lo em uma coluna com uma restrição única. Desta forma, pode voltar a tentar a transação com segurança. Será bem sucedido se a transação anterior for revertida e o ID único gerado pelo cliente ainda não existir no sistema. Falhará indicando uma violação de chave duplicada se o ID único foi previamente armazenado porque a transação anterior foi concluída com sucesso.

Quando o seu programa comunicar com a Base de Dados Azure para o MySQL através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Certifique-se de testar a sua lógica de relículo. Por exemplo, tente executar o seu código enquanto escala os recursos de computação da sua Base de Dados Azure para o servidor MySQL. A sua aplicação deve tratar do breve tempo de paragem que se encontra durante esta operação sem problemas.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Conecte-se eficientemente à Base de Dados Azure para o MySQL

As ligações de base de dados são um recurso limitado, pelo que fazer uma utilização eficaz do pooling de conexão para aceder à Base de Dados Azure para o MySQL otimiza o desempenho. A secção abaixo explica como utilizar o pooling de ligação ou ligações persistentes para aceder mais eficazmente à Base de Dados Azure para o MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Bases de dados de acesso utilizando o pooling de ligação (recomendado)

A gestão de ligações de base de dados pode ter um impacto significativo no desempenho da aplicação como um todo. Para otimizar o desempenho da sua aplicação, o objetivo deve ser reduzir o número de vezes que as ligações são estabelecidas e tempo para estabelecer ligações em caminhos-chave de código. Recomendamos vivamente a utilização de ligações de base de dados ou ligações persistentes para ligar à Base de Dados Azure para o MySQL. A ligação de base de dados trata da criação, gestão e alocação de ligações de base de dados. Quando um programa solicita uma ligação de base de dados, prioriza a atribuição de ligações de base de dados ociosas existentes, em vez da criação de uma nova ligação. Depois de o programa ter terminado a utilização da ligação de base de dados, a ligação é recuperada em preparação para uma utilização posterior, em vez de simplesmente ser encerrada.

Para uma melhor ilustração, este artigo fornece [um pedaço de código de amostra](./sample-scripts-java-connection-pooling.md) que usa JAVA como exemplo. Para mais informações, consulte [a Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> O servidor configura um mecanismo de timeout para fechar uma ligação que está em estado de inatividade há algum tempo para libertar recursos. Certifique-se de que configura o sistema de verificação para garantir a eficácia das ligações persistentes quando as estiver a utilizar. Para obter mais informações, consulte [os sistemas de verificação Configure do lado do cliente para garantir a eficácia das ligações persistentes](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Bases de dados de acesso utilizando ligações persistentes (recomendadas)

O conceito de conexões persistentes é semelhante ao do agrupamento de ligações. A substituição de ligações curtas por ligações persistentes requer apenas pequenas alterações ao código, mas tem um grande efeito em termos de melhoria do desempenho em muitos cenários típicos de aplicação.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Bases de dados de acesso utilizando mecanismo de espera e de relemcragens com ligações curtas

Se tiver limitações de recursos, recomendamos vivamente que utilize o pooling de bases de dados ou ligações persistentes para aceder a bases de dados. Se a sua aplicação utilizar ligações curtas e experimentar falhas de ligação quando se aproximar do limite superior do número de ligações simultâneas, pode tentar aguardar e voltar a tentar o mecanismo. Pode definir um tempo de espera adequado, com um tempo de espera mais curto após a primeira tentativa. Depois disso, pode tentar esperar por eventos várias vezes.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configure mecanismos de verificação nos clientes para confirmar a eficácia das ligações persistentes

O servidor configura um mecanismo de tempo limite para fechar uma ligação que está em estado de ocioso há algum tempo para libertar recursos. Quando o cliente volta a aceder à base de dados, é equivalente a criar um novo pedido de ligação entre o cliente e o servidor. Para garantir a eficácia das ligações durante o processo de utilização das mesmos, configurar um mecanismo de verificação no cliente. Como mostrado no exemplo seguinte, pode utilizar a ligação Tomcat JDBC para configurar este mecanismo de verificação.

Ao definir o parâmetro TestOnBorrow, quando há um novo pedido, o conjunto de ligação verifica automaticamente a eficácia de quaisquer ligações inativas disponíveis. Se tal ligação for eficaz, o seu pool de ligação diretamente devolvido retira a ligação. O pool de ligação cria então uma nova ligação eficaz e devolve-a. Este processo garante que a base de dados é acedida de forma eficiente. 

Para obter informações sobre as definições específicas, consulte o [documento oficial de introdução do pool de ligação JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). É necessário definir principalmente os seguintes três parâmetros: TestOnBorrow (definido como verdadeiro), ValidaationQuery (definido para SELECT 1) e ValidaationQueryTimeout (definido para 1). O código de amostra específico é apresentado abaixo:

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
