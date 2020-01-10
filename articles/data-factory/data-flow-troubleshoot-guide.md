---
title: Solucionar problemas de fluxos de dados
description: Saiba como solucionar problemas de fluxo de dados no Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 06746cfc3b39a242c16a6b4f4c95b3c212a9abd5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443939"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Solucionar problemas Azure Data Factory fluxos de dados

Este artigo explora métodos comuns de solução de problemas para fluxos de dados em Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Mensagem de erro: DF-SYS-01: shaded. databricks. org. Apache. Hadoop. FS. Azure. Azureexception: com. Microsoft. Azure. Storage. StorageException: o contêiner especificado não existe.

- **Sintomas**: a execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Causa**: quando o conjunto de conteúdo contém um contêiner que não existe no armazenamento

- **Resolução**: Certifique-se de que o contêiner que você está referenciando no conjunto de seus já existe

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Mensagem de erro: DF-SYS-01: Java. lang. AssertionError: falha de asserção: estruturas de diretório conflitantes detectadas. Caminhos suspeitos

- **Sintomas**: ao usar curingas na transformação de origem com arquivos parquet

- **Causa**: sintaxe incorreta ou curinga inválida

- **Resolução**: Verifique a sintaxe curinga que você está usando em suas opções de transformação de origem

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Mensagem de erro: DF-SRC-002: ' container ' (nome do contêiner) é necessário

- **Sintomas**: a execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Causa**: quando o conjunto de conteúdo contém um contêiner que não existe no armazenamento

- **Resolução**: Certifique-se de que o contêiner que você está referenciando no conjunto de seus já existe

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Mensagem de erro: DF-UNI-001: PrimaryKeyvalue tem tipos incompatíveis IntegerType e StringType

- **Sintomas**: a execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Causa**: ocorre ao tentar inserir o tipo de chave primária incorreta nos coletores de banco de dados

- **Resolução**: Use uma coluna derivada para converter a coluna que você está usando para a chave primária em seu fluxo de dados para corresponder ao tipo de dados do seu banco de dado de destino

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Mensagem de erro: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: falha na conexão TCP/IP com o host xxxxx.database.windows.net porta 1433. Erro: "xxxx.database.windows.net. Verifique as propriedades da conexão. Certifique-se de que uma instância do SQL Server está em execução no host e aceitando conexões TCP/IP na porta. Verifique se as conexões TCP com a porta não estão bloqueadas por um firewall. "

- **Sintomas**: não é possível visualizar dados ou executar o pipeline com a origem ou o coletor do banco

- **Causa**: o banco de dados é protegido pelo firewall

- **Resolução**: Abra o acesso do firewall ao banco de dados

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Mensagem de erro: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: já existe um objeto chamado ' XXXXXX ' no banco de dados.

- **Sintomas**: falha do coletor ao criar a tabela

- **Causa**: já existe um nome de tabela existente no banco de dados de destino com o mesmo nome definido em sua origem ou no conjunto

- **Resolução**: altere o nome da tabela que você está tentando criar

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Mensagem de erro: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: a cadeia de caracteres ou os dados binários seriam truncados. 

- **Sintomas**: ao gravar dados em um coletor SQL, o fluxo de dados falha na execução do pipeline com possível erro de truncamento.

- **Causa**: um campo do fluxo de dados é mapeado para uma coluna em seu banco de dado SQL não é grande o suficiente para armazenar o valor, fazendo com que o driver SQL gere esse erro

- **Resolução**: você pode reduzir o comprimento dos dados para colunas de cadeia de caracteres usando ```left()``` em uma coluna derivada ou implementar o [padrão de "linha de erro".](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Mensagem de erro: desde o Spark 2,3, as consultas de arquivos JSON/CSV brutos não são permitidas quando as colunas referenciadas incluem apenas a coluna de registro corrompida interna. 

- **Sintomas**: falha na leitura de uma origem JSON

- **Causa**: ao ler de uma origem JSON com um único documento em muitas linhas aninhadas, o ADF, por meio do Spark, não consegue determinar onde um novo documento começa e o documento anterior termina.

- **Resolução**: na transformação de origem que está usando um conjunto de um DataSet JSON, expanda "configurações de JSON" e ative "documento único".

### <a name="error-message-duplicate-columns-found-in-join"></a>Mensagem de erro: colunas duplicadas encontradas em Join

- **Sintomas**: a transformação de junção resultou em colunas da esquerda e do lado direito que incluem nomes de coluna duplicados

- **Causa**: os fluxos que estão sendo ingressados têm nomes de coluna comuns

- **Resolução**: Adicione uma transformação selecionar após a junção e selecione "remover colunas duplicadas" para a entrada e saída.

### <a name="error-message-possible-cartesian-product"></a>Mensagem de erro: possível produto cartesiano

- **Sintomas**: a transformação de junção ou pesquisa detectou um possível produto cartesiano na execução do fluxo de dados

- **Causa**: se você não direcionou explicitamente o ADF para usar uma junção cruzada, o fluxo de dados pode falhar

- **Resolução**: altere sua transformação de pesquisa ou junção para uma junção usando junção cruzada personalizada e insira sua condição de pesquisa ou junção no editor de expressão. Se você quiser produzir explicitamente um produto cartesiano completo, use a transformação coluna derivada em cada um dos dois fluxos independentes antes da junção para criar uma chave sintética na qual corresponder. Por exemplo, crie uma nova coluna na coluna derivada em cada fluxo chamado ```SyntheticKey``` e defina-a como ```1```. Em seguida, use ```a.SyntheticKey == b.SyntheticKey``` como sua expressão de junção personalizada.

> [!NOTE]
> Certifique-se de incluir pelo menos uma coluna de cada lado da relação esquerda e direita em uma junção cruzada personalizada. A execução de Junções cruzadas com valores estáticos em vez de colunas de cada lado resultará em verificações completas de todo o conjunto de dados, fazendo com que o fluxo do seu data de desempenho seja insatisfatório.

## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas

1. Verifique o status das suas conexões de conjunto de conexão. Em cada transformação de origem e coletor, visite o serviço vinculado para cada conjunto de um que você está usando e teste as conexões.
2. Verifique o status das conexões de arquivo e de tabela do designer de fluxo de dados. Ative a depuração e clique em visualização de dados em suas transformações de origem para garantir que você possa acessar seus dados.
3. Se tudo estiver correto na visualização de dados, vá para o designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)
