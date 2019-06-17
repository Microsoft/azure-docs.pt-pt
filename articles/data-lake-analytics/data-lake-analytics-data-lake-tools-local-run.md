---
title: Scripts de execução do Azure Data Lake U-SQL no seu computador local
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio para executar tarefas de U-SQL no seu computador local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113940"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Executar scripts U-SQL no seu computador local

Ao desenvolver scripts U-SQL, pode poupar tempo e dinheiro ao executar os scripts localmente. O Azure Data Lake Tools para Visual Studio oferece suporte a scripts do U-SQL em execução no seu computador local. 

## <a name="basic-concepts-for-local-runs"></a>Conceitos básicos para execuções de locais

O gráfico seguinte mostra os componentes para executar local e como esses componentes mapeiam para executar de cloud.

|Componente|Execução local|Executar de cloud|
|---------|---------|---------|
|Armazenamento|Pasta raiz de dados local|Conta padrão do Azure Data Lake Store|
|Computação|Motor de execução local do U-SQL|Serviço do Azure Data Lake Analytics|
|Ambiente de execução|Diretório de trabalho no computador local|Cluster do Azure Data Lake Analytics|

As secções que se seguem fornecem mais informações sobre componentes de execução locais.

### <a name="local-data-root-folders"></a>Pastas de raiz de dados locais

Uma pasta de raiz de dados local é uma **arquivo local** para o local de conta de computação. Qualquer pasta no sistema de arquivos local no seu computador local pode ser uma pasta de raiz de dados local. É o mesmo que a conta do Azure Data Lake Store predefinida de uma conta do Data Lake Analytics. Mudar para uma pasta de raiz de dados diferentes é igual a mudar para uma conta de armazenamento predefinido diferente. 

A pasta de raiz de dados é usada da seguinte forma:
- Store metadados. Os exemplos são bases de dados, tabelas, as funções com valor de tabela e assemblies.
- Procure os caminhos de entrada e saídos que são definidos como caminhos relativos em scripts U-SQL. Ao utilizar caminhos relativos, é mais fácil de implantar seus scripts U-SQL para o Azure.

### <a name="u-sql-local-run-engines"></a>Mecanismos de execução de local de U-SQL

O motor de execução local é o U-SQL um **conta de computação local** para as tarefas de U-SQL. Os utilizadores podem executar tarefas U-SQL localmente através do Azure Data Lake Tools para Visual Studio. Execuções locais também são suportadas por meio de interfaces de programação e de linha de comandos do SDK do Azure Data Lake U-SQL. Saiba mais sobre o [SDK do Azure Data Lake U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Diretórios de trabalho

Quando executa um script de U-SQL, uma pasta de diretório de trabalho é necessário para colocar em cache os resultados de compilação, executar os registos e executar outras funções. No Azure Data Lake Tools para Visual Studio, o diretório de trabalho é o diretório de trabalho do projeto U-SQL. Ele está localizado em `<U-SQL project root path>/bin/debug>`. O diretório de trabalho sejam limpos sempre que uma nova execução é acionada.

## <a name="local-runs-in-microsoft-visual-studio"></a>Local é executado no Microsoft Visual Studio

O Azure Data Lake Tools para Visual Studio tem um motor de execução local incorporado. As ferramentas de superfície o mecanismo de como uma conta de computação local. Para executar um script de U-SQL localmente, selecione o **computador Local** ou **projeto Local** conta no menu de lista pendente de margem de editor do script. Em seguida, selecione **submeter**.

![Submeter um script de U-SQL para uma conta local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Local é executado com uma conta de computador Local

R **máquina Local** conta é a conta com uma pasta de raiz única de dados locais como a conta de armazenamento local de computação de um local compartilhado. Por predefinição, a pasta de raiz de dados está localizada em **C:\Users\<nome de utilizador > \AppData\Local\USQLDataRoot**. Também é configurável através de **ferramentas** > **Data Lake** > **opções e definições**.

![Configurar uma pasta de raiz de dados locais](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Um projeto U-SQL é necessário para um execução de local. Diretório de trabalho do projeto U-SQL é utilizado para o U-SQL execute diretório de trabalho local. Resultados de compilação, executar os registos e outros ficheiros relacionados com a execução de tarefa são gerados e armazenados na pasta de diretório de trabalho durante a execução local. Sempre que executar novamente o script, todos os ficheiros no diretório de trabalho são limpos e gerada novamente.

## <a name="local-runs-with-a-local-project-account"></a>Local é executado com uma conta do projeto Local

R **projeto Local** conta é a conta para cada projeto com uma pasta de raiz de dados local isolado de computação de um local isolado de projeto. Cada projeto U-SQL ativo que se abre no Explorador de soluções no Visual Studio tem um correspondente `(Local-project: <project name>)` conta. As contas são listadas no Server Explorer no Visual Studio e a margem de editor de script de U-SQL.  

O **projeto Local** conta fornece um ambiente de desenvolvimento limpo e isolada. R **máquina Local** conta tem uma pasta de raiz do local de dados partilhada que armazena dados de metadados e entrada e saída para todas as tarefas de locais. Mas um **projeto Local** conta cria uma pasta de raiz de dados local temporário num diretório de trabalho de projeto U-SQL sempre que um script de U-SQL é executado. Esta pasta de raiz de dados temporário é limpos quando uma reconstrução ou volte a executar acontece. 

Um projeto U-SQL gerencia o ambiente de execução local isolado através de uma referência de projeto e a propriedade. Pode configurar as origens de dados de entrada para scripts U-SQL no projeto e os ambientes de banco de dados referenciada.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Gerir a origem de dados de entrada para uma conta do projeto Local 

Um projeto U-SQL cria uma pasta de raiz de dados locais e conjuntos de cópias de segurança para um **projeto Local** conta. Uma pasta de raiz de dados temporário é limpo e recriada sob o diretório de trabalho de projeto U-SQL sempre que uma recompilação e a execução local acontece. Todas as origens de dados que estão configuradas pelo projeto U-SQL são copiadas para esta pasta de raiz do local de dados temporário antes da tarefa local é executada. 

Pode configurar a pasta raiz de origens de dados. Com o botão direito **projeto U-SQL** > **propriedade** > **origem de dados de teste**. Quando executa um script de U-SQL **projeto Local** conta, todos os ficheiros e subpastas na **origem de dados de teste** pasta são copiados para a pasta de raiz de dados local temporário. Ficheiros em subpastas são incluídos. Após a execução de uma tarefa local, resultados de saída também podem ser encontrados na pasta raiz de dados local temporário no diretório de trabalho do projeto. Todos os esta saída é eliminada e limpos quando o projeto obtém reconstruído e limpos. 

![Configurar origem de dados de teste de um projeto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Gerir um ambiente de base de dados referenciado para uma **projeto Local** conta 

Se utiliza ou consultas com objetos de banco de dados U-SQL de consulta U-SQL, tem de certificar os ambientes de banco de dados pronto localmente antes de executar o script de U-SQL localmente. Para uma **projeto Local** conta, base de dados U-SQL dependências podem ser geridas pelas referências de projeto U-SQL. Pode adicionar referências de projeto de banco de dados U-SQL ao seu projeto de U-SQL. Antes de executar scripts U-SQL **projeto Local** conta, referenciada todas as bases de dados são implementadas para a pasta de raiz de dados local temporário. E para cada execução, a pasta de raiz de dados temporário é limpa como um novo ambiente isolado.

Consulte este artigo relacionado:
* Saiba como gerir definições de base de dados de U-SQL e referências no [projetos de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A diferença entre **computador Local** e **projeto Local** contas

R **máquina Local** conta simula uma conta do Azure Data Lake Analytics nos computadores dos usuários de local. Ele compartilha a mesma experiência com uma conta do Azure Data Lake Analytics. R **projeto Local** conta fornece um ambiente de desenvolvimento local fácil de utilizar. Neste ambiente ajuda os utilizadores a implementar as referências de base de dados e dados de entrada antes de executar scripts localmente. R **máquina Local** conta fornece um ambiente compartilhado permanente que pode ser acedido através de todos os projetos. R **projeto Local** conta fornece um ambiente de desenvolvimento isolado para cada projeto. Ele é atualizado para cada execução. R **projeto Local** conta oferece uma experiência de desenvolvimento mais rápida através da aplicação rapidamente novas alterações.

Mais diferenças entre **computador Local** e **projeto Local** contas são mostradas na tabela a seguir:

|Ângulo de diferença|Máquina local|Projeto de local|
|----------------|---------------|---------------|
|Acesso local|Pode ser acedido por todos os projetos.|Apenas o projeto correspondente pode aceder a esta conta.|
|Pasta raiz de dados local|Uma pasta local permanente. Configurado através da **ferramentas** > **Data Lake** > **opções e definições**.|Uma pasta temporária criada para cada execução local sob o diretório de trabalho do projeto de U-SQL. A pasta removida quando uma reconstrução ou volte a executar acontece.|
|Dados de entrada para um script de U-SQL|O caminho relativo na pasta raiz de dados local permanente.|Definido por meio **propriedade de projeto U-SQL** > **origem de dados de teste**. Todos os ficheiros e subpastas são copiadas para a pasta de raiz de dados temporário antes da execução de um local.|
|Dados de saída para um script de U-SQL|Caminho relativo na pasta raiz de dados local permanente.|Saída para a pasta de raiz de dados temporário. Os resultados são limpos quando uma reconstrução ou volte a executar acontece.|
|Implementação de base de dados referenciada|Bases de dados referenciadas não são implementados automaticamente quando em execução em relação a um **máquina Local** conta. É o mesmo para submeter a uma conta do Azure Data Lake Analytics.|Bases de dados referenciadas são implementadas para o **projeto Local** conta automaticamente antes da execução de um local. Todos os ambientes de banco de dados são limpos e implantados novamente quando uma reconstrução ou volte a executar acontece.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Um execução com o SDK U-SQL de local

Pode executar scripts U-SQL localmente no Visual Studio e também utilizar o SDK do Azure Data Lake U-SQL para executar scripts U-SQL localmente com interfaces de programação e de linha de comandos. Através dessas interfaces, pode automatizar execuções locais do U-SQL e testes.

Saiba mais sobre o [SDK do Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Passos Seguintes

- [Como configurar um pipeline CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
