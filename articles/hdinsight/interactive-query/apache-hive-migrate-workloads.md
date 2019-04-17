---
title: Migrar cargas de trabalho do Azure HDInsight 3.6 do Hive do HDInsight 4.0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3.6 4.0 do HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610269"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho do Azure HDInsight 3.6 do Hive do HDInsight 4.0

Este documento mostra como migrar cargas de trabalho Apache Hive e do LLAP no HDInsight 3.6 4.0 do HDInsight. HDInsight 4.0 fornece as funcionalidades mais recentes sobre o Hive e o LLAP, como as vistas materializadas e colocação em cache de resultado de consulta. Quando migra as cargas de trabalho para o HDInsight 4.0, pode utilizar várias funcionalidades mais recentes do Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo abrange os seguintes assuntos:

* Migração dos metadados do Hive do HDInsight 4.0
* Migração de segura de ACID e no ACID não tabelas
* Preservação de políticas de segurança do Hive em todas as versões do HDInsight
* Execução da consulta e a depuração no HDInsight 3.6 4.0 do HDInsight

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrar o Apache Hive metadados para o HDInsight 4.0

Uma das vantagens de Hive é a capacidade de exportar metadados para uma base de dados externo (referido como o Metastore Hive). O **Hive Metastore** é responsável por armazenar as estatísticas de tabela, incluindo a localização de armazenamento de tabela, os nomes das colunas e informações de índice da tabela. O esquema de banco de dados da metastore difere entre versões do Hive. Faça o seguinte para atualizar um Metastore de Hive do HDInsight 3.6, para que seja compatível com o HDInsight 4.0.

1. Crie uma nova cópia do seu metastore externo. HDInsight 3.6 e o HDInsight 4.0 requerem metastore diferentes esquemas, não é possível partilhar um metastore único.
1. Anexe a nova cópia do metastore para a) um cluster de HDInsight 4.0 existente ou b) num cluster que está a criar pela primeira vez. Ver [utilizar arquivos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre a ligação de um metastore externo a um cluster do HDInsight. Depois do Metastore é anexada, será automaticamente convertida num metastore compatível com o 4.0.

> [!Warning]
> A atualização que converte o esquema de metadados 3.6 do HDInsight para o esquema de HDInsight 4.0, não pode ser revertida.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar as tabelas do Hive para o HDInsight 4.0

Após concluir o conjunto anterior de passos para migrar o Metastore Hive HDInsight 4.0, as tabelas e bases de dados registadas no metastore será visíveis a partir do cluster de HDInsight 4.0, executando `show tables` ou `show databases` de dentro do cluster . Ver [execução da consulta em todas as versões do HDInsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução da consulta em clusters do HDInsight 4.0.

Os dados reais das tabelas, no entanto, não não acessíveis até que o cluster tem acesso às contas de armazenamento necessário. Para certificar-se de que o cluster de HDInsight 4.0 pode acessar os mesmos dados que o antigo cluster do HDInsight 3.6, execute os seguintes passos:

1. Determinar a conta de armazenamento do Azure da sua tabela ou base de dados com descrevem formatado.
2. Se o seu cluster do HDInsight 4.0 já está em execução, anexe a conta de armazenamento do Azure para o cluster através do Ambari. Se ainda não criou o cluster de HDInsight 4.0, certifique-se de que a conta de armazenamento do Azure é especificada como principal ou uma conta de armazenamento de cluster secundário. Para obter mais informações sobre como adicionar contas de armazenamento para clusters do HDInsight, consulte [adicionar mais contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabelas são tratadas de forma diferente no HDInsight 3.6 e 4.0 do HDInsight. Por esse motivo, não é possível partilhar as mesmo tabelas para clusters com versões diferentes. Se pretender utilizar o HDInsight 3.6 ao mesmo tempo que 4.0 do HDInsight, tem de ter cópias separadas dos dados para cada versão.

A carga de trabalho do Hive pode incluir uma mistura de transações ACID e no ACID não tabelas. Uma das principais diferenças entre o Hive no HDInsight 3.6 (Hive 2) e ao Hive no HDInsight 4.0 (Hive 3) é a conformidade de ACID para tabelas. No HDInsight 3.6, ativar a conformidade de ACID do Hive requer configuração adicional, mas no HDInsight 4.0 tabelas estão em conformidade ACID por predefinição. A única ação necessária antes de migração é executada uma compactação de principais com base na tabela ACID no 3.6 cluster. Na vista do Hive ou da Beeline, execute a seguinte consulta:

```bash
alter table myacidtable compact 'major';
```

Esta compactação é necessária porque as tabelas de HDInsight 3.6 e o HDInsight 4.0 ACID compreender deltas ACID diferentes. Compactação impõe uma ficha limpa que garante a consistência da tabela. Depois de concluída a compactação, os passos anteriores para a migração de metastore e tabela serão suficientes para utilizar quaisquer tabelas de HDInsight 3.6 ACID no HDInsight 4.0.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteja o Hive no HDInsight versões

Desde o HDInsight 3.6, o HDInsight integra-se com o Azure Active Directory com o pacote de segurança de Enterprise do HDInsight (ESP). ESP usa Kerberos e do Apache Ranger para gerir as permissões de recursos específicos dentro do cluster. As políticas de Ranger implementadas contra Hive no HDInsight 3.6 podem ser migradas para o HDInsight 4.0 com os seguintes passos:

1. Navegue para o painel de Ranger Service Manager no seu cluster do HDInsight 3.6.
2. Navegue para a política com o nome **HIVE** e exportar a política para um ficheiro json.
3. Certifique-se de que todos os utilizadores referidos no json exportado política existem no novo cluster. Se um utilizador é referido no json de política, mas não existe no novo cluster, adicione o utilizador para o novo cluster ou remova a referência da política.
4. Navegue para o **Ranger Service Manager** painel no seu cluster do HDInsight 4.0.
5. Navegue para a política com o nome **HIVE** e importar o json de políticas do ranger do passo 2.

## <a name="query-execution-across-hdinsight-versions"></a>Execução da consulta em todas as versões do HDInsight

Existem duas formas de executar e depurar consultas do Hive/LLAP dentro de um cluster do HDInsight 3.6. HiveCLI fornece uma experiência de linha de comando e a vista de exibição/Hive no Tez fornece um fluxo de trabalho baseados em GUI.

No HDInsight 4.0, HiveCLI foi substituído com o Beeline. HiveCLI é um cliente thrift para Hiveserver 1 e Beeline é um cliente JDBC, que fornece acesso ao Hiveserver 2. Beeline também pode ser utilizado para ligar a qualquer outro ponto de final de base de dados de compatíveis com o JDBC. Beeline está disponível-prontos no HDInsight 4.0 sem nenhuma instalação necessária.

No HDInsight 3.6, o cliente de GUI para interagir com o servidor do Hive é a exibição do Ambari Hive. HDInsight 4.0 substitui a vista do Hive com o Studio de análise de dados do Hortonworks (DAS). DAS não são enviados juntamente com clusters de HDInsight out-of-box e não é um pacote de suporte oficial. No entanto, DAS pode ser instaladas no cluster da seguinte forma:

1. Transfira o [script de instalação do pacote DAS](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh) e executá-lo em ambos os principais do cluster. Não execute este script como uma ação de script.
2. Transfira o [script de instalação do serviço DAS](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh) e executá-lo como uma ação de script. Selecione **nós principais** como o tipo de nó à escolha a partir da interface de ação de script.
3. Quando a ação de script for concluída, navegue para Ambari e selecione **Estúdio de análise de dados** na lista de serviços. Todos os serviços DAS são parados. No canto superior direito, selecione **ações** e **iniciar**. Pode agora executar e depurar as consultas com DAS.

Assim que o DAS é instalada, se não vir as consultas que já executou no Visualizador de consultas, execute os seguintes passos:

1. Definir as configurações para Hive no Tez e DAS conforme descrito em [este guia de resolução de problemas de instalação DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Certifique-se de que as seguintes configurações de diretório de armazenamento do Azure são blobs de páginas e de que estes estão listados em `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie o HDFS, Hive, Tez e em ambos os principais.

## <a name="next-steps"></a>Passos Seguintes

* [Anúncio de HDInsight 4.0](../hdinsight-version-release.md)
* [Aprofunde-se 4.0 do HDInsight](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 tabelas ACID do Hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)