---
title: Vários clusters HDInsight & uma conta de Azure Data Lake Storage
description: Saiba como usar mais de um cluster HDInsight com uma única conta de Data Lake Storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495760"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Usar vários clusters HDInsight com uma conta de Azure Data Lake Storage

A partir do HDInsight versão 3,5, você pode criar clusters HDInsight com contas de Azure Data Lake Storage como o sistema de arquivos padrão.
O Data Lake Storage dá suporte ao armazenamento ilimitado que o torna ideal não apenas para hospedar grandes quantidades de dados; Mas também para hospedar vários clusters HDInsight que compartilham uma única conta de Data Lake Storage. Para obter instruções sobre como criar um cluster HDInsight com Data Lake Storage como armazenamento, consulte [início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Este artigo fornece recomendações para o administrador de Data Lake Storage para configurar uma conta de Data Lake Storage única e compartilhada que pode ser usada em vários clusters HDInsight **ativos** . Essas recomendações se aplicam para hospedar vários clusters de Apache Hadoop seguros e não seguros em uma conta de Data Lake Storage compartilhada.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage ACLs de nível de arquivo e pasta

O restante deste artigo pressupõe que você tenha um bom conhecimento de ACLs de nível de arquivo e pasta no Azure Data Lake Storage, que é descrito em detalhes no [controle de acesso no Azure data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Configuração de Data Lake Storage para vários clusters HDInsight

Vamos pegar uma hierarquia de pastas de dois níveis para explicar as recomendações para o uso de vários clusters HDInsight com uma conta de Data Lake Storage. Considere que você tem uma conta de Data Lake Storage com a estrutura de pasta **/clusters/Finance**. Com essa estrutura, todos os clusters exigidos pela organização de finanças podem usar/clusters/Finance como o local de armazenamento. No futuro, se outra organização, por exemplo, marketing, quiser criar clusters HDInsight usando a mesma conta de Data Lake Storage, poderá criar/clusters/marketing. Por enquanto, vamos usar apenas **/clusters/Finance**.

Para permitir que essa estrutura de pastas seja efetivamente usada por clusters HDInsight, o administrador de Data Lake Storage deve atribuir as permissões apropriadas, conforme descrito na tabela. As permissões mostradas na tabela correspondem a Access-ACLs, e não a default-ACLs.

|Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Usuário nomeado | Permissões de usuário nomeado | Grupo nomeado | Permissões de grupo nomeado |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administrador |administrador  |Principal de serviço |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |administrador |administrador |Principal de serviço |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |administrador |FINGRP  |Principal de serviço |rwx  |-  |-     |

Na tabela,

- **admin** é o criador e o administrador da conta de data Lake Storage.
- A **entidade de serviço** é a entidade de serviço Azure Active Directory (AAD) associada à conta.
- **FINGRP** é um grupo de usuários criado no AAD que contém usuários da organização de finanças.

Para obter instruções sobre como criar um aplicativo do AAD (que também cria uma entidade de serviço), consulte [criar um aplicativo do AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Para obter instruções sobre como criar um grupo de usuários no AAD, consulte [Managing groups in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Alguns pontos importantes a serem considerados.

- A estrutura de pastas de dois níveis ( **/clusters/Finance/** ) deve ser criada e provisionada com as permissões apropriadas pelo administrador de data Lake Storage **antes** de usar a conta de armazenamento para clusters. Essa estrutura não é criada automaticamente durante a criação de clusters.
- O exemplo acima recomenda definir o grupo proprietário de **/clusters/Finance** como **FINGRP** e permitir o acesso **r-x** a FINGRP para toda a hierarquia de pastas, começando da raiz. Isso garante que os membros de FINGRP possam navegar na estrutura de pastas iniciando na raiz.
- No caso em que diferentes entidades de serviço do AAD podem criar clusters em **/clusters/Finance**, o bit de aderência (quando definido na pasta **Finance** ) garante que as pastas criadas por uma entidade de serviço não possam ser excluídas pelo outro.
- Depois que a estrutura de pastas e as permissões estiverem em vigor, o processo de criação de cluster do HDInsight criará um local de armazenamento específico do cluster em **/clusters/Finance/** . Por exemplo, o armazenamento de um cluster com o nome fincluster01 poderia ser **/clusters/Finance/fincluster01**. A propriedade e as permissões para as pastas criadas pelo cluster HDInsight são mostradas na tabela aqui.

    |Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Usuário nomeado | Permissões de usuário nomeado | Grupo nomeado | Permissões de grupo nomeado |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Principal de Serviço |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Recomendações para dados de entrada e saída do trabalho

É recomendável que os dados de entrada para um trabalho e as saídas de um trabalho sejam armazenados em uma pasta fora de **/clusters**. Isso garante que, mesmo que a pasta específica do cluster seja excluída para recuperar algum espaço de armazenamento, as entradas e saídas do trabalho ainda estarão disponíveis para uso futuro. Nesse caso, verifique se a hierarquia de pastas para armazenar as entradas e saídas de trabalho permite o nível apropriado de acesso para a entidade de serviço.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite de clusters que compartilham uma única conta de armazenamento

O limite do número de clusters que podem compartilhar uma única conta de Data Lake Storage depende da carga de trabalho que está sendo executada nesses clusters. Ter um número excessivo de clusters ou cargas de trabalho muito pesadas nos clusters que compartilham uma conta de armazenamento pode fazer com que a entrada/saída da conta de armazenamento seja limitada.

## <a name="support-for-default-acls"></a>Suporte para ACLs padrão

Ao criar uma entidade de serviço com acesso de usuário nomeado (como mostrado na tabela acima), recomendamos **não** adicionar o usuário nomeado com uma ACL padrão. O provisionamento de acesso de usuário nomeado usando ACLs padrão resulta na atribuição de 770 permissões para propriedade de usuário proprietário, grupo de propriedade e outros. Embora esse valor padrão de 770 não remova as permissões do usuário proprietário (7) ou do grupo proprietário (7), ele retira todas as permissões para outros (0). Isso resulta em um problema conhecido com um caso de uso específico que é discutido em detalhes na seção [problemas conhecidos e soluções alternativas](#known-issues-and-workarounds) .

## <a name="known-issues-and-workarounds"></a>Problemas conhecidos e soluções alternativas

Esta seção lista os problemas conhecidos para usar o HDInsight com o Data Lake Storage e suas soluções alternativas.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Recursos YARN localizados publicamente visíveis Apache Hadoop

Quando uma nova conta de Azure Data Lake Storage é criada, o diretório raiz é provisionado automaticamente com os bits de permissão Access-ACL definidos como 770. O usuário proprietário da pasta raiz é definido como o usuário que criou a conta (o administrador de Data Lake Storage) e o grupo proprietário é definido como o grupo primário do usuário que criou a conta. Nenhum acesso é fornecido para "outros".

Essas configurações são conhecidas por afetar um caso de uso específico do HDInsight capturado no [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Os envios de trabalhos podem falhar com uma mensagem de erro semelhante a esta:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Conforme mencionado no YARN JIRA vinculado anteriormente, ao localizar recursos públicos, o localizador valida que todos os recursos solicitados são realmente públicos, verificando suas permissões no sistema de arquivos remoto. Qualquer LocalResource que não caiba nessa condição será rejeitado para localização. A verificação de permissões, inclui acesso de leitura ao arquivo para "outros". Esse cenário não funciona pronto para uso ao hospedar clusters HDInsight em Azure Data Lake, já que Azure Data Lake nega todo o acesso a "outros" no nível da pasta raiz.

#### <a name="workaround"></a>Solução

Defina as permissões de leitura-execução para **outros** por meio da hierarquia, por exemplo, em **/** , **/clusters** e **/clusters/Finance** , conforme mostrado na tabela acima.

## <a name="see-also"></a>Ver também

- [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
