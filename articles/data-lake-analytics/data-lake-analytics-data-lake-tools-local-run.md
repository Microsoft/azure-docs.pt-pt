---
title: Executar scripts U-SQL do Lago de Dados Azure na sua máquina local
description: Aprenda a usar ferramentas de lago de dados Azure para o Estúdio Visual para executar trabalhos U-SQL na sua máquina local.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "62113940"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Executar scripts U-SQL na sua máquina local

Quando desenvolve scripts U-SQL, pode economizar tempo e despesas executando os scripts localmente. As ferramentas do Lago de Dados Azure para o Estúdio Visual suportam a execução de scripts U-SQL na sua máquina local. 

## <a name="basic-concepts-for-local-runs"></a>Conceitos básicos para corridas locais

O gráfico seguinte mostra os componentes para o funcionamento local e como estes componentes mapeiam para o funcionamento da nuvem.

|Componente|Execução local|Corrida de nuvens|
|---------|---------|---------|
|Armazenamento|Pasta de raiz de dados locais|Conta Default Azure Data Lake Store|
|Computação|Motor de corrida local U-SQL|Serviço Azure Data Lake Analytics|
|Ambiente de corrida|Diretório de trabalho na máquina local|Cluster Azure Data Lake Analytics|

As secções que se seguem fornecem mais informações sobre os componentes locais de execução.

### <a name="local-data-root-folders"></a>Pastas de raiz de dados locais

Uma pasta de raiz de dados local é uma **loja local** para a conta de cálculo local. Qualquer pasta no sistema de ficheiros local na sua máquina local pode ser uma pasta de raiz de dados local. É o mesmo que a conta padrão da Loja de Lagos De Dados do Lago De dados de uma conta data lake analytics. Mudar para uma pasta de raiz de dados diferente é como mudar para uma conta de loja padrão diferente. 

A pasta raiz de dados é utilizada da seguinte forma:
- Armazenar metadados. Exemplos são bases de dados, tabelas, funções de valor de mesa e conjuntos.
- Procure os caminhos de entrada e saída que são definidos como caminhos relativos nos scripts U-SQL. Ao utilizar caminhos relativos, é mais fácil implantar os seus scripts U-SQL para o Azure.

### <a name="u-sql-local-run-engines"></a>Motores de corrida locais U-SQL

Um motor de corrida local U-SQL é uma **conta de cálculo local** para trabalhos U-SQL. Os utilizadores podem executar trabalhos U-SQL localmente através de Ferramentas de Lago de Dados Azure para estúdio visual. As corridas locais também são suportadas através das interfaces de linha de comando e programação Do Lago de Dados Azure U-SQL SDK. Saiba mais sobre o [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Diretórios de trabalho

Quando executa um script U-SQL, é necessária uma pasta de diretório funcional para cache resultados de compilação, executar registos e executar outras funções. Em Azure Data Lake Tools for Visual Studio, o diretório de trabalho é o diretório de trabalho do projeto U-SQL. Está localizado debaixo `<U-SQL project root path>/bin/debug>`de. O diretório de trabalho é limpo cada vez que uma nova execução é desencadeada.

## <a name="local-runs-in-microsoft-visual-studio"></a>Corridas locais no Microsoft Visual Studio

As Ferramentas do Lago De Dados Azure para o Estúdio Visual têm um motor de corrida local incorporado. As ferramentas surgem no motor como uma conta de cálculo local. Para executar um script U-SQL localmente, selecione a conta **local-máquina** ou **projeto local** no menu de entrega de margem do script. Em seguida, selecione **Submeter**.

![Envie um script U-SQL para uma conta local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Local corre com uma conta de máquina local

Uma conta **de máquina local** é uma conta de cálculo local partilhada com uma única pasta de raiz de dados local como conta de loja local. Por padrão, a pasta da raiz de dados está localizada no **nome de utilizador c:\Utilizadores\<>\AppData\Local\USQLDataRoot**. Também é configurável através de **Ferramentas** > **Data Lake** > **Options and Settings**.

![Configure uma pasta de raiz de dados local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Um projeto U-SQL é necessário para uma execução local. O diretório de trabalho do projeto U-SQL é utilizado para o diretório de trabalho local U-SQL. Os resultados da compilação, registos de execução e outros ficheiros relacionados com o funcionamento são gerados e armazenados sob a pasta de diretório de trabalho durante a execução local. Sempre que reexecutas o guião, todos os ficheiros do diretório de trabalho são limpos e regenerados.

## <a name="local-runs-with-a-local-project-account"></a>Local corre com uma conta de projeto local

Uma conta **de projeto local** é uma conta de cálculo local isolada por projetos para cada projeto com uma pasta de raiz de dados local isolada. Todos os projetos u-SQL ativos que se abrem `(Local-project: <project name>)` no Solution Explorer no Visual Studio têm uma conta correspondente. As contas estão listadas tanto no Server Explorer no Visual Studio como na margem de editor de scripts U-SQL.  

A conta **do projeto local** proporciona um ambiente de desenvolvimento limpo e isolado. Uma conta **local-máquina** tem uma pasta de raiz de dados local partilhada que armazena metadados e dados de entrada e saída para todos os trabalhos locais. Mas uma conta **de projeto local** cria uma pasta de raiz de dados local temporária sob um diretório de trabalho de projeto U-SQL cada vez que um script U-SQL é executado. Esta pasta de raiz de dados temporária saem limpas quando uma reconstrução ou reexecução acontece. 

Um projeto U-SQL gere o ambiente de execução local isolado através de uma referência e propriedade do projeto. Pode configurar as fontes de dados de entrada para scripts U-SQL tanto no projeto como nos ambientes de base de dados referenciados.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Gerir a fonte de dados de entrada para uma conta de projeto local 

Um projeto U-SQL cria uma pasta de raiz de dados local e configura dados para uma conta **de projeto local.** Uma pasta de raiz de dados temporária é limpa e recriada no âmbito do diretório de trabalho do projeto U-SQL sempre que uma reconstrução e execução local acontecem. Todas as fontes de dados configuradas pelo projeto U-SQL são copiadas para esta pasta de raiz de dados local temporária antes do funcionar do trabalho local. 

Pode configurar a pasta raiz das suas fontes de dados. Clique direito **U-SQL projeto** > **Propriedade** > **Test Source**. Quando executa um script U-SQL numa conta **de projeto local,** todos os ficheiros e subpastas da pasta Fonte de Dados de **Teste** são copiados para a pasta de raiz de dados local temporário. Os ficheiros em subpastas estão incluídos. Após uma corrida de trabalho local, os resultados da produção também podem ser encontrados sob a pasta de raiz de dados local temporária no diretório de trabalho do projeto. Toda esta saída é eliminada e limpa quando o projeto é reconstruído e limpo. 

![Configure a fonte de dados de teste de um projeto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Gerir um ambiente de base de dados referenciado para uma conta **de projeto local** 

Se uma consulta U-SQL utilizar ou consultar objetos de base de dados U-SQL, deve tornar os ambientes de base de dados prontos localmente antes de executar o script U-SQL localmente. Para uma conta **de projeto local,** as dependências da base de dados U-SQL podem ser geridas por referências de projetoU-SQL. Pode adicionar referências de projeto de projeto de base de dados U-SQL ao seu projeto U-SQL. Antes de executar scripts U-SQL numa conta **de projeto local,** todas as bases de dados referenciadas são implementadas para a pasta de raiz de dados local temporário. E para cada execução, a pasta de raiz de dados temporários é limpa como um ambiente isolado fresco.

Consulte este artigo relacionado:
* Saiba como gerir definições e referências de bases de dados U-SQL em [projetos de base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A diferença entre as contas **locais-máquina** e **projeto local**

Uma conta **de máquina local** simula uma conta Azure Data Lake Analytics nas máquinas locais dos utilizadores. Partilha a mesma experiência com uma conta Azure Data Lake Analytics. Uma conta **de projeto local** proporciona um ambiente de desenvolvimento local amigável para o utilizador. Este ambiente ajuda os utilizadores a implementar referências de base de dados e dados de entrada antes de executarem scripts localmente. Uma conta **de máquina local** proporciona um ambiente permanente partilhado que pode ser acedido através de todos os projetos. Uma conta **de projeto local** proporciona um ambiente de desenvolvimento isolado para cada projeto. É refrescado para cada corrida. Uma conta **de projeto local** oferece uma experiência de desenvolvimento mais rápida aplicando rapidamente novas mudanças.

Na tabela a seguinte são mostradas mais diferenças entre as contas **locais-máquina** si e as contas **do projeto local:**

|Ângulo de diferença|Máquina local|Projeto local|
|----------------|---------------|---------------|
|Acesso local|Pode ser acedido por todos os projetos.|Só o projeto correspondente pode aceder a esta conta.|
|Pasta de raiz de dados locais|Uma pasta local permanente. Configurado através de **ferramentas** > **Data Lake** > **Options and Settings**.|Uma pasta temporária criada para cada execução local sob o diretório de trabalho do projeto U-SQL. A pasta é limpa quando uma reconstrução ou recandidatura acontece.|
|Dados de entrada para um script U-SQL|O caminho relativo sob a pasta de raiz de dados local permanente.|Configurado através da >  **propriedade do projeto U-SQL**Test Data**Source**. Todos os ficheiros e subpastas são copiados para a pasta de raiz de dados temporárioantes de uma execução local.|
|Dados de saída para um script U-SQL|Caminho relativo sob a pasta de raiz de dados local permanente.|Saída para a pasta de raiz de dados temporário. Os resultados são limpos quando uma reconstrução ou recandidatura acontece.|
|Implantação de base de dados referenciada|As bases de dados referenciadas não são implantadas automaticamente quando se passa contra uma conta **de máquina local.** É o mesmo para submeter-se a uma conta Azure Data Lake Analytics.|As bases de dados referenciadas são implantadas automaticamente na conta **do projeto local** antes de uma execução local. Todos os ambientes de base de dados são limpos e redistribuídos quando uma reconstrução ou reexecução acontece.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Uma corrida local com o U-SQL SDK

Você pode executar scripts U-SQL localmente no Estúdio Visual e também usar o Azure Data Lake U-SQL SDK para executar scripts U-SQL localmente com interfaces de linha de comando e programação. Através destas interfaces, pode automatizar corridas e testes locais U-SQL.

Saiba mais sobre o [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Passos seguintes

- [Como configurar um oleoduto CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
