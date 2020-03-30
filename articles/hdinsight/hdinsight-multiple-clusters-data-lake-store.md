---
title: Vários clusters HDInsight & uma conta de armazenamento de lago de dados Azure
description: Saiba como usar mais do que um cluster HDInsight com uma única conta de Armazenamento de Lago de Dados
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495760"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Utilize vários clusters HDInsight com uma conta de Armazenamento de Lago de Dados Azure

Começando com a versão 3.5 do HDInsight, pode criar clusters HDInsight com contas de Armazenamento de Lagos De Dados Azure como o sistema de ficheiros padrão.
Data Lake Storage suporta armazenamento ilimitado que o torna ideal não só para hospedar grandes quantidades de dados; mas também para hospedar vários clusters HDInsight que partilham uma única Conta de Armazenamento de Data Lake. Para obter instruções sobre como criar um cluster HDInsight com armazenamento de data lake como armazenamento de data lake como o armazenamento, consulte [Quickstart: Configurar clusters em HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Este artigo fornece recomendações ao administrador de armazenamento de data lake para a criação de uma única e partilhada Conta de Armazenamento de Data Lake que pode ser usada em vários clusters **hDInsight ativos.** Estas recomendações aplicam-se ao alojamento de múltiplos clusters seguros e não seguros apache Hadoop numa conta partilhada de Armazenamento de Lago de Dados.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Ficheirode armazenamento de data Lake e ACLs de nível de pastas

O resto deste artigo assume que tem um bom conhecimento de suporte e nível de pastas ACLs no Armazenamento de Lagos De Dados Azure, que é descrito em detalhe no controlo de acesso em Armazenamento de Lago de [Dados Azure.](../data-lake-store/data-lake-store-access-control.md)

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Configuração de armazenamento de data lake para vários clusters HDInsight

Tomemos uma hierarquia de pastas de dois níveis para explicar as recomendações para a utilização de vários clusters HDInsight com uma conta de Armazenamento de Data Lake. Considere que tem uma conta de Armazenamento de Data Lake com a estrutura de pastas **/clusters/finanças**. Com esta estrutura, todos os clusters exigidos pela organização Financeira podem usar /clusters/financiamento como local de armazenamento. No futuro, se outra organização, por exemplo, o Marketing, quiser criar clusters HDInsight usando a mesma conta data Lake Storage, eles podem criar /clusters/marketing. Por enquanto, vamos apenas usar **/clusters/finanças**.

Para permitir que esta estrutura de pasta seja eficazmente utilizada pelos clusters HDInsight, o administrador de armazenamento de data lake deve atribuir permissões adequadas, conforme descrito na tabela. As permissões mostradas na tabela correspondem ao Access-ACLs e não aos ACLs predefinidos.

|Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeadas | Grupo nomeado | Permissões de grupo nomeadas |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x-x  |admin |admin  |Service principal (Principal de serviço) |--x  |FINGRP   |r-x         |
|/aglomerados | rwxr-x-x |admin |admin |Service principal (Principal de serviço) |--x  |FINGRP |r-x         |
|/clusters/finanças | rwxr-x-t |admin |FINGRP  |Service principal (Principal de serviço) |rwx  |-  |-     |

Na mesa,

- **administrador** é o criador e administrador da conta data lake storage.
- **O diretor** de serviço é o principal serviço de Diretório Ativo Azure (AAD) associado à conta.
- **A FINGRP** é um grupo de utilizadores criado em AAD que contém utilizadores da organização Financeira.

Para obter instruções sobre como criar uma aplicação AAD (que também cria um Diretor de Serviço), consulte [Criar uma aplicação AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Para obter instruções sobre como criar um grupo de utilizadores em AAD, consulte [Managing groups in Azure Ative Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Alguns pontos-chave a ter em conta.

- A estrutura de duas pastas de dois níveis **(/clusters/finanças)** deve ser criada e aprovisionada com permissões adequadas pelo administrador de Armazenamento de Data Lake **antes** de utilizar a conta de armazenamento para clusters. Esta estrutura não é criada automaticamente enquanto cria clusters.
- O exemplo acima recomenda definir o grupo próprio de **/clusters/financiamento** como **FINGRP** e permitir o acesso **r-x** ao FINGRP a toda a hierarquia da pasta a partir da raiz. Isto garante que os membros da FINGRP podem navegar na estrutura da pasta a partir da raiz.
- No caso de diferentes Diretores de Serviço AAD poderem criar clusters em **/clusters/finanças,** o bit pegajoso (quando definido na pasta **de financiamento)** garante que as pastas criadas por um Diretor de Serviço não podem ser eliminadas pelo outro.
- Uma vez que a estrutura e permissões da pasta estão em vigor, o processo de criação de cluster HDInsight cria um local de armazenamento específico do cluster em **/clusters/finanças/**. Por exemplo, o armazenamento de um cluster com o nome fincluster01 pode ser **/clusters/finance/fincluster01**. A propriedade e permissões para as pastas criadas pelo cluster HDInsight é mostrada na tabela aqui.

    |Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeadas | Grupo nomeado | Permissões de grupo nomeadas |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Principal de Serviço |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Recomendações para dados de entrada de emprego e de saída

Recomendamos que os dados de entrada para um trabalho, e as saídas de um trabalho sejam armazenadas numa pasta fora **/clusters**. Isto garante que, mesmo que a pasta específica do cluster seja eliminada para recuperar algum espaço de armazenamento, as inputs e saídas de trabalho ainda estão disponíveis para utilização futura. Neste caso, certifique-se de que a hierarquia da pasta para armazenar as inputs e saídas de trabalho permite um nível de acesso adequado para o Diretor de Serviço.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite para clusters que partilham uma única conta de armazenamento

O limite do número de clusters que podem partilhar uma única conta de Armazenamento de Data Lake depende da carga de trabalho que está a ser executada nesses clusters. Ter demasiados clusters ou cargas de trabalho muito pesadas nos clusters que partilham uma conta de armazenamento pode fazer com que a conta de armazenamento ingress/egress seja estrangulada.

## <a name="support-for-default-acls"></a>Suporte para Os ACLs Predefinidos

Ao criar um Diretor de Serviço com acesso ao utilizador nomeado (como mostrado na tabela acima), recomendamos **que não** adicione o utilizador nomeado com um ACL padrão. O fornecimento de acesso ao utilizador nomeado utilizando ACLs predefinidos resulta na atribuição de 770 permissões para utilizador próprio, grupo de possuir e outros. Embora este valor padrão de 770 não retire permissões de utilizador próprio (7) ou grupo de possuir (7), retira todas as permissões para outros (0). Isto resulta numa questão conhecida com um caso de utilização específico que é discutido em pormenor na secção [questões e seleções conhecidas.](#known-issues-and-workarounds)

## <a name="known-issues-and-workarounds"></a>Questões conhecidas e suposições

Esta secção lista os problemas conhecidos para a utilização do HDInsight com o Armazenamento de Data Lake e as suas soluções.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Recursos yARN apache sioop localizados publicamente

Quando uma nova conta de Armazenamento de Lagos de Dados Azure é criada, o diretório raiz é automaticamente provisionado com bits de permissão Access-ACL definidos para 770. O utilizador próprio da pasta raiz está definido para o utilizador que criou a conta (o administrador de Armazenamento de Data Lake) e o grupo próprio está definido para o grupo primário do utilizador que criou a conta. Não é dado acesso aos "outros".

Estas definições são conhecidas por afetar um caso específico de utilização HDInsight capturado em [ARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). As submissões de emprego podem falhar com uma mensagem de erro semelhante a esta:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Tal como referido no YARN JIRA ligado anteriormente, ao mesmo tempo que se alocem aos recursos públicos, o localtista valida que todos os recursos solicitados são, de facto, públicos, verificando as suas permissões no sistema de ficheiros remoto. Qualquer Recurso Local que não se encaixe nessa condição é rejeitado para localização. O cheque de permissões, inclui acesso de leitura ao ficheiro para "outros". Este cenário não funciona fora da caixa ao hospedar clusters HDInsight no Lago de Dados Azure, uma vez que o Azure Data Lake nega todo o acesso a "outros" ao nível das pastas de raiz.

#### <a name="workaround"></a>Solução

Definir permissões de leitura-execução para **outros** **/** através da hierarquia, por exemplo, em , **/clusters** e **/clusters/financiamento,** como mostrado na tabela acima.

## <a name="see-also"></a>Consulte também

- [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
