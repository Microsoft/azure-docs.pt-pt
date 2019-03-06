---
title: Utilizar vários clusters do HDInsight com uma conta de armazenamento do Azure Data Lake - Azure
description: Saiba como utilizar mais de um cluster do HDInsight com uma única conta de armazenamento do Data Lake
keywords: armazenamento do hdinsight, hdfs, dados estruturados, dados não estruturados, data lake store
services: hdinsight,storage
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 0d57c65c93ffcd6c4c5249a1e5effeb457ed1736
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440901"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Utilizar vários clusters do HDInsight com uma conta de armazenamento do Azure Data Lake

A partir do HDInsight versão 3.5, pode criar clusters do HDInsight com contas de armazenamento do Azure Data Lake como o sistema de ficheiros predefinido.
Armazenamento do Data Lake suporta armazenamento ilimitado, que o torna ideal não apenas para o alojamento de grandes quantidades de dados. mas também para o alojamento de vários HDInsight clusters que a partilha de uma única conta de armazenamento do Data Lake. Para obter instruções sobre como criar um cluster do HDInsight com o armazenamento do Data Lake, como o armazenamento, consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Este artigo fornece recomendações para o administrador de armazenamento do Data Lake para configurar um único e partilhado armazenamento de conta do Data Lake que podem ser utilizadas em vários **Active Directory** clusters do HDInsight. Estas recomendações aplicam-se ao alojamento de vários clusters do Apache Hadoop seguros, bem como não segura numa conta de armazenamento do Data Lake partilhada.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>ACLs de nível de ficheiros de armazenamento do Data Lake e de pastas

O restante deste artigo parte do princípio de que tem um bom conhecimento do nível de arquivo e pasta ACLs de armazenamento do Azure Data Lake, que está descrito em detalhe em [controlo de acesso no armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Configuração de armazenamento do Data Lake para múltiplos clusters do HDInsight
Vamos ver mais uma hierarquia de pastas de dois níveis para explicar as recomendações para a utilização de vários clusters do HDInsight com uma conta de armazenamento do Data Lake. Considere a tem uma conta de armazenamento do Data Lake com a estrutura de pastas **/clusters/finance**. Com esta estrutura, todos os clusters necessários para a organização do departamento financeiro podem utilizar /clusters/finance como localização de armazenamento. No futura, se outra organização, digamos que pretende criar o HDInsight clusters com a mesma conta de armazenamento do Data Lake, foi possível criar/clusters/marketing, de Marketing. Por enquanto, vamos simplesmente usar **/clusters/finance**.

Para ativar esta estrutura de pastas para ser usado com eficiência pelos clusters do HDInsight, o administrador de armazenamento do Data Lake tem de atribuir as permissões apropriadas, conforme descrito na tabela. As permissões mostradas na tabela correspondem às ACLs de acesso e não de ACLs padrão. 


|Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeado | Grupo nomeado | Permissões de grupo nomeado |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Principal de serviço |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |Principal de serviço |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |Principal de serviço |rwx  |-  |-     |

Na tabela,

- **administrador** é o criador e o administrador da conta de armazenamento do Data Lake.
- **Principal de serviço** é o principal de serviço do Azure Active Directory (AAD) associado à conta.
- **FINGRP** é um grupo de utilizadores criado neste que contenha os utilizadores da organização do departamento financeiro.

Para obter instruções sobre como criar uma aplicação do AAD (que também cria um Principal de serviço), consulte [criar uma aplicação AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Para obter instruções sobre como criar um grupo de utilizadores no AAD, consulte [gerir grupos no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Alguns pontos importantes a considerar.

- Estrutura de pastas de nível de dois (**/clusters/Finanças/**) tem de ser criado e provisionado com permissões adequadas pelo administrador de armazenamento do Data Lake **antes de** com a conta de armazenamento para clusters. Essa estrutura não é criada automaticamente durante a criação de clusters.
- O exemplo acima recomenda a configuração do grupo proprietário da **/clusters/finance** como **FINGRP** e permitindo **r-x** acesso a FINGRP para a hierarquia de toda a pasta a partir de raiz. Isto garante que os membros de FINGRP podem navegar a estrutura de pastas a partir de raiz.
- No caso de quando os principais de serviço do AAD diferente pode criar clusters **/clusters/finance**, o sticky bit (quando definidos na **Finanças** pasta) garante que as pastas criadas por um Principal de serviço Não é possível eliminar por outros.
- Assim que a estrutura de pastas e as permissões estiverem em vigor, o processo de criação de cluster do HDInsight cria uma localização de armazenamento de cluster específicos em **/clusters/Finanças/**. Por exemplo, poderia ser o armazenamento para um cluster com o nome fincluster01 **/clusters/finance/fincluster01**. A propriedade e permissões para as pastas criadas pelo cluster do HDInsight é mostrado na tabela aqui.

    |Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeado | Grupo nomeado | Permissões de grupo nomeado |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/fincluster01 | rwxr-x---  |Principal de Serviço |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Recomendações para dados de entrada e saídos da tarefa

Recomendamos que os dados de entrada para uma tarefa e os resultados de uma tarefa ser armazenados numa pasta fora **/clusters**. Isto garante que, mesmo que a pasta de específicos do cluster é eliminada para recuperar algum espaço de armazenamento, as tarefas entradas e saídas ainda estão disponíveis para utilização futura. Nesse caso, certifique-se de que a hierarquia de pastas para armazenar as tarefa entradas e saídas permite um nível apropriado de acesso para o Principal de serviço.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite para o compartilhamento de uma única conta de armazenamento de clusters

O limite no número de clusters que pode partilhar uma única conta de armazenamento do Data Lake depende da carga de trabalho a ser executada nesses clusters. Ter demasiadas clusters ou cargas de trabalho muito pesadas nos clusters que partilham uma conta de armazenamento pode fazer com que a conta entrada/saída de armazenamento ficar bloqueados.

## <a name="support-for-default-acls"></a>Suporte para ACLs padrão

Ao criar um Principal de serviço com acesso de utilizador com o nome (conforme mostrado na tabela acima), recomendamos **não** adicionar o utilizador com o nome com uma ACL predefinida. Aprovisionamento de utilizador com o nome de acesso usando resultados de ACLs padrão na atribuição de 770 permissões para o utilizador proprietário, grupo proprietário e outros. Apesar deste valor predefinido de 770 não distância permissões de proprietário-utilizador (7) ou o grupo de proprietário (7), ele faz com que todas as permissões para outras pessoas (0). Isso resulta num problema conhecido com um uso-caso específico que é abordado em detalhe no [conhecidos de problemas e soluções alternativas](#known-issues-and-workarounds) secção.

## <a name="known-issues-and-workarounds"></a>Problemas conhecidos e soluções alternativas

Esta secção lista os problemas conhecidos para utilizar o HDInsight com o armazenamento do Data Lake e suas soluções alternativas.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Visíveis publicamente recursos localizados do Apache Hadoop YARN

Quando é criada uma nova conta de armazenamento do Azure Data Lake, o diretório de raiz é automaticamente aprovisionado com o conjunto de bits de permissões de ACL de acesso para 770. A pasta de raiz ao proprietário utilizador está definido para o utilizador que criou a conta (o administrador de armazenamento do Data Lake) e o grupo proprietário está definido para o grupo principal do utilizador que criou a conta. Sem acesso é fornecido para "outros".

Estas definições são conhecidas a afetar um específico HDInsight casos de utilização capturado na [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Submissões de tarefas poderá falhar com uma mensagem de erro semelhante a este:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Conforme indicado no JIRA YARN ligado anteriormente, durante a localização de recursos de público, o localizador valida que todos os recursos de pedido são públicos, de fato, verificando as respetivas permissões no sistema de ficheiros remoto. Qualquer LocalResource que não se ajusta a condição for rejeitado para a localização. A verificação de permissões, inclui o acesso de leitura para o ficheiro para "outros". Este cenário não funciona out-of-the-box quando alojar os clusters do HDInsight no Azure Data Lake, uma vez que o Azure Data Lake nega todo o acesso a "outros" no nível de pasta de raiz.

#### <a name="workaround"></a>Solução
Conjunto de leitura-permissões de execução para **outras pessoas** através da hierarquia, por exemplo, na **/**, **/clusters** e   **/clusters/Finanças** conforme mostrado na tabela acima.

## <a name="see-also"></a>Consulte também

* [Quickstart: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md)
