---
title: Executar scripts U-SQL do Lago de Dados Azure na sua máquina local
description: Saiba como usar o Azure Data Lake Tools para o Visual Studio para executar trabalhos U-SQL na sua máquina local.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018992"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Executar scripts U-SQL na sua máquina local

Quando desenvolves scripts U-SQL, podes poupar tempo e despesas executando os scripts localmente. A azure Data Lake Tools for Visual Studio suporta a execução de scripts U-SQL na sua máquina local. 

## <a name="basic-concepts-for-local-runs"></a>Conceitos básicos para corridas locais

O gráfico seguinte mostra os componentes para execução local e como estes componentes mapeiam para a nuvem executado.

|Componente|Execução local|Corrida de nuvens|
|---------|---------|---------|
|Armazenamento|Pasta de raiz de dados locais|Conta padrão da Loja de Dados Azure|
|Computação|Motor de corrida local U-SQL|Serviço Azure Data Lake Analytics|
|Ambiente de execução|Diretório de trabalho na máquina local|Aglomerado Azure Data Lake Analytics|

As secções que se seguem fornecem mais informações sobre os componentes de execução local.

### <a name="local-data-root-folders"></a>Pastas de raiz de dados locais

Uma pasta de raiz de dados local é uma **loja local** para a conta de computação local. Qualquer pasta no sistema de ficheiros local da sua máquina local pode ser uma pasta de raiz de dados local. É o mesmo que a conta padrão da Azure Data Lake Store de uma conta data lake analytics. Mudar para uma pasta de raiz de dados diferente é como mudar para uma conta de loja diferente. 

A pasta raiz de dados é utilizada da seguinte forma:
- Armazenar metadados. Exemplos são bases de dados, tabelas, funções de valor de tabela e conjuntos.
- Procure os caminhos de entrada e saída que são definidos como caminhos relativos em scripts U-SQL. Ao utilizar caminhos relativos, é mais fácil implementar os seus scripts U-SQL para Azure.

### <a name="u-sql-local-run-engines"></a>Motores de funcionação local U-SQL

Um motor de funcionação local U-SQL é uma **conta de computação local** para trabalhos U-SQL. Os utilizadores podem executar trabalhos U-SQL localmente através de Azure Data Lake Tools for Visual Studio. As execuções locais também são suportadas através das interfaces de linha de comando e programação do Azure Data Lake U-SQL SDK. Saiba mais sobre o [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Diretórios de trabalho

Quando executa um script U-SQL, é necessária uma pasta de diretório de trabalho para cache resultados de compilação, executar registos e executar outras funções. Em Azure Data Lake Tools for Visual Studio, o diretório de trabalho é o diretório de trabalho do projeto U-SQL. Está localizado `<U-SQL project root path>/bin/debug>` sob. O diretório de trabalho é limpo sempre que uma nova execução é desencadeada.

## <a name="local-runs-in-microsoft-visual-studio"></a>Corridas locais no Microsoft Visual Studio

As ferramentas do Azure Data Lake para o Estúdio Visual têm um motor de funcionamento local incorporado. As ferramentas surfacem o motor como uma conta de computação local. Para executar um script U-SQL localmente, selecione a conta **local-máquina** ou **projeto local** no menu de entrega de margem de lançamento do editor do script. Em seguida, **selecione Enviar por isso .**

![Envie um script U-SQL para uma conta local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Local funciona com uma conta de máquina local

Uma conta **de máquina local** é uma conta de computação local partilhada com uma única pasta de raiz de dados local como conta de loja local. Por predefinição, a pasta raiz de dados está localizada em **C:\Utilizadores \<username> \AppData\Local\USQLDataRoot**. Também é configurável através de **opções** e configurações do  >  **Lago de**  >  **Dados de Ferramentas.**

![Configure uma pasta de raiz de dados local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
É necessário um projeto U-SQL para uma corrida local. O diretório de trabalho do projeto U-SQL é utilizado para o diretório de trabalho local U-SQL. Os resultados da compilação, registos de execução e outros ficheiros relacionados com a execução de trabalho são gerados e armazenados sob a pasta do diretório de trabalho durante a execução local. Sempre que reexameces o guião, todos os ficheiros do diretório de trabalho são limpos e regenerados.

## <a name="local-runs-with-a-local-project-account"></a>Corridas locais com uma conta de projeto local

Uma conta **de projeto local** é uma conta de computação local isolada do projeto para cada projeto com uma pasta de raiz de dados local isolada. Todos os projetos U-SQL ativos que abrem no Solution Explorer no Visual Studio têm uma `(Local-project: <project name>)` conta correspondente. As contas estão listadas tanto no Server Explorer em Visual Studio como na margem de editor de script u-SQL.  

A conta **do projeto local** proporciona um ambiente de desenvolvimento limpo e isolado. Uma conta **local-máquina** tem uma pasta de raiz de dados local partilhada que armazena metadados e dados de entrada e saída para todos os trabalhos locais. Mas uma conta **de projeto local** cria uma pasta de raiz de dados local temporária no âmbito de um diretório de funcionamento de projeto U-SQL sempre que um script U-SQL é executado. Esta pasta de raiz de dados temporários é limpa quando uma reconstrução ou repetição acontece. 

Um projeto U-SQL gere o ambiente de execução local isolado através de uma referência de projeto e propriedade. Pode configurar as fontes de dados de entrada para scripts U-SQL tanto no projeto como nos ambientes de base de dados referenciados.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Gerir a fonte de dados de entrada para uma conta de projeto local 

Um projeto U-SQL cria uma pasta de raiz de dados local e cria dados para uma conta **de projeto local.** Uma pasta de raiz de dados temporária é limpa e recriada no âmbito do diretório de funcionamento do projeto U-SQL sempre que acontece uma reconstrução e uma execução local. Todas as fontes de dados configuradas pelo projeto U-SQL são copiadas para esta pasta temporária de raiz de dados local antes do funcionação do trabalho local. 

Pode configurar a pasta raiz das suas fontes de dados. Clique à direita **no projeto U-SQL**  >  **Property**  >  **Property Test Data Source**. Quando executa um script U-SQL numa conta **de projeto local,** todos os ficheiros e sub-dobradizações na pasta **De Dados de Teste** são copiados para a pasta de raiz de dados locais temporários. Os ficheiros nas sub-dobradeiras estão incluídos. Após a execução de um trabalho local, os resultados de produção também podem ser encontrados sob a pasta temporária de raiz de dados locais no diretório de trabalho do projeto. Toda esta saída é apagada e limpa quando o projeto é reconstruído e limpo. 

![Configure a fonte de dados de teste de um projeto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Gerir um ambiente de base de dados referenciado para uma conta **de projeto local** 

Se uma consulta U-SQL utilizar ou consultar objetos de base de dados U-SQL, deve preparar os ambientes da base de dados localmente antes de executar o script U-SQL localmente. Para uma conta **de projeto local,** as dependências da base de dados U-SQL podem ser geridas por referências de projetos U-SQL. Pode adicionar referências de projeto de base de dados U-SQL ao seu projeto U-SQL. Antes de executar scripts U-SQL numa conta **de projeto local,** todas as bases de dados referenciadas são implantadas na pasta temporária de raiz de dados locais. E para cada execução, a pasta de raiz de dados temporários é limpa como um ambiente isolado fresco.

Consulte este artigo relacionado:
* Saiba como gerir definições e referências de base de dados [U-SQL em projetos de base de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A diferença entre as contas **local-máquina** e **local-projecto**

Uma conta **de máquina local** simula uma conta Azure Data Lake Analytics nas máquinas locais dos utilizadores. Partilha a mesma experiência com uma conta Azure Data Lake Analytics. Uma conta **de projeto local** proporciona um ambiente de desenvolvimento local fácil de utilizar. Este ambiente ajuda os utilizadores a implementar referências de base de dados e dados de entrada antes de executarem scripts localmente. Uma conta **local-máquina** proporciona um ambiente permanente partilhado que pode ser acedido através de todos os projetos. Uma conta **de projeto local** proporciona um ambiente de desenvolvimento isolado para cada projeto. É refrescado para cada corrida. Uma conta **de projeto local** oferece uma experiência de desenvolvimento mais rápida aplicando rapidamente novas mudanças.

No quadro seguinte são apresentadas mais diferenças entre as contas **de projeto local** e **local:**

|Ângulo de diferença|Máquina local|Projeto local|
|----------------|---------------|---------------|
|Acesso local|Pode ser acedido por todos os projetos.|Apenas o projeto correspondente pode aceder a esta conta.|
|Pasta de raiz de dados locais|Uma pasta local permanente. Configurado através de **opções**  >  e configurações do Lago **de**  >  **Dados de Ferramentas**.|Uma pasta temporária criada para cada execução local no âmbito do diretório de trabalho do projeto U-SQL. A pasta é limpa quando acontece uma reconstrução ou repetição.|
|Dados de entrada para um script U-SQL|O caminho relativo sob a pasta permanente de raiz de dados locais.|Configurar através **da propriedade do projeto U-SQL** Fonte de  >  **dados de teste.** Todos os ficheiros e sub-dobradores são copiados para a pasta de raiz de dados temporários antes de uma execução local.|
|Dados de saída para um script U-SQL|Percurso relativo sob a pasta permanente de raiz de dados locais.|Saída para a pasta de raiz de dados temporários. Os resultados são limpos quando uma reconstrução ou repetição acontece.|
|Implantação de base de dados referenciada|As bases de dados referenciadas não são implantadas automaticamente quando correm contra uma conta **de máquina local.** É o mesmo para submeter-se a uma conta Azure Data Lake Analytics.|As bases de dados referenciadas são implantadas automaticamente na conta **do projeto local** antes de uma execução local. Todos os ambientes da base de dados são limpos e redistribuídos quando uma reconstrução ou repetição acontece.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Uma corrida local com o U-SQL SDK

Você pode executar scripts U-SQL localmente em Visual Studio e também usar o Azure Data Lake U-SQL SDK para executar scripts U-SQL localmente com interfaces de linha de comando e programação. Através destas interfaces, pode automatizar corridas e testes locais U-SQL.

Saiba mais sobre o [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Passos seguintes

- [Como configurar um pipeline CI/CD para a Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Como testar o seu código Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
