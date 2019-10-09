---
title: Solucionar problemas Azure Data Factory fluxos de dados | Microsoft Docs
description: Saiba como solucionar problemas de fluxo de dados no Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178621"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Solucionar problemas Azure Data Factory fluxos de dados

Este artigo explora métodos comuns de solução de problemas para fluxos de dados em Azure Data Factory.

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Mensagem de erro: DF-SYS-01: shaded. databricks. org. Apache. Hadoop. FS. Azure. Azureexception: com. Microsoft. Azure. Storage. StorageException: O contêiner especificado não existe.

- **Sintomas**: A execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Motivo**: Quando o conjunto de conteúdo contém um contêiner que não existe no armazenamento

- **Resolução**: Certifique-se de que o contêiner que você está referenciando no conjunto de seus exista

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Mensagem de erro: DF-SYS-01: Java. lang. AssertionError: falha na asserção: Estruturas de diretório conflitantes detectadas. Caminhos suspeitos

- **Sintomas**: Ao usar curingas na transformação de origem com arquivos parquet

- **Motivo**: Sintaxe de curinga incorreta ou inválida

- **Resolução**: Verifique a sintaxe curinga que você está usando em suas opções de transformação de origem

## <a name="error-message-df-src-002-container-container-name-is-required"></a>Mensagem de erro: DF-SRC-002: ' container ' (nome do contêiner) é necessário

- **Sintomas**: A execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Motivo**: Quando o conjunto de conteúdo contém um contêiner que não existe no armazenamento

- **Resolução**: Certifique-se de que o contêiner que você está referenciando no conjunto de seus exista

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Mensagem de erro: DF-UNI-001: PrimaryKeyvalue tem tipos incompatíveis IntegerType e StringType

- **Sintomas**: A execução de fluxo de dados de visualização de dados, depuração e pipeline falha porque o contêiner não existe

- **Motivo**: Ocorre ao tentar inserir o tipo de chave primária incorreta nos coletores de banco de dados

- **Resolução**: Use uma coluna derivada para converter a coluna que você está usando para a chave primária em seu fluxo de dados para corresponder ao tipo de dados do seu banco de dado de destino

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Mensagem de erro: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Falha na conexão TCP/IP com a porta do host xxxxx.database.windows.net 1433. Erro: "xxxx.database.windows.net. Verifique as propriedades da conexão. Certifique-se de que uma instância do SQL Server está em execução no host e aceitando conexões TCP/IP na porta. Verifique se as conexões TCP com a porta não estão bloqueadas por um firewall. "

- **Sintomas**: Não é possível visualizar dados ou executar o pipeline com a origem ou o coletor do banco

- **Motivo**: O banco de dados é protegido pelo firewall

- **Resolução**: Abrir o acesso do firewall ao banco de dados

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Mensagem de erro: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Já existe um objeto chamado ' XXXXXX ' no banco de dados.

- **Sintomas**: Falha do coletor ao criar a tabela

- **Motivo**: Já existe um nome de tabela existente no banco de dados de destino com o mesmo nome definido em sua origem ou no DataSet

- **Resolução**: Alterar o nome da tabela que você está tentando criar



## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
