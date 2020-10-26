---
title: Múltiplos clusters HDInsight & uma conta de armazenamento do Lago de Dados Azure
description: Saiba como usar mais do que um cluster HDInsight com uma única conta de Armazenamento do Lago de Dados
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: df28374d0f124ceb46d2f97d55218d428275deca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533092"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Use vários clusters HDInsight com uma conta de armazenamento do Lago de Dados Azure

Começando pela versão 3.5 do HDInsight, pode criar clusters HDInsight com contas de Armazenamento de Data Lake Azure como o sistema de ficheiros predefinido.
O Armazenamento do Lago de Dados suporta armazenamento ilimitado que o torna ideal não só para hospedar grandes quantidades de dados; mas também para hospedar vários clusters HDInsight que partilham uma única conta de armazenamento do Data Lake. Para obter instruções sobre como criar um cluster HDInsight com o Armazenamento do Lago de Dados como armazenamento, consulte [Quickstart: Configurar clusters em HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

Este artigo fornece recomendações ao administrador de armazenamento de data lake para a criação de uma única conta de armazenamento de data lake que pode ser usada em vários clusters **hdinsight ativos.** Estas recomendações aplicam-se ao alojamento de múltiplos aglomerados Apache Hadoop seguros e não seguros numa conta partilhada de Data Lake Storage.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Ficheiro de armazenamento de data lake e ACLs de nível de pasta

O resto deste artigo pressupõe que você tem um bom conhecimento de ACLs de nível de arquivo e pasta no Azure Data Lake Storage, que é descrito em detalhe no [controle access em Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Configuração de armazenamento do lago de dados para vários clusters HDInsight

Vamos tomar uma hierarquia de pasta de dois níveis para explicar as recomendações para usar vários clusters HDInsight com uma conta de Armazenamento de Data Lake. Considere que tem uma conta de Armazenamento do Lago de Dados com a estrutura de pasta **/clusters/finanças.** Com esta estrutura, todos os clusters exigidos pela organização das Finanças podem utilizar /clusters/finanças como local de armazenamento. No futuro, se outra organização, digamos Marketing, quiser criar clusters HDInsight usando a mesma conta de Armazenamento de Data Lake, eles podem criar /clusters/marketing. Por enquanto, vamos **apenas usar /clusters/finanças** .

Para permitir que esta estrutura de pasta seja eficazmente utilizada por clusters HDInsight, o administrador de armazenamento do Data Lake deve atribuir permissões apropriadas, conforme descrito na tabela. As permissões apresentadas na tabela correspondem a ACLs de acesso e não a ACLs predefinidos.

|Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeadas | Grupo nomeado | Permissões de grupo nomeadas |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x-x  |administração |administração  |Service principal (Principal de serviço) |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x-x |administração |administração |Service principal (Principal de serviço) |--x  |FINGRP |r-x         |
|/clusters/finanças | rwxr-x-t |administração |FINGRP  |Service principal (Principal de serviço) |rwx  |-  |-     |

Na mesa,

- **a administradora** é o criador e administrador da conta de Armazenamento do Lago de Dados.
- **O diretor de** serviço é o diretor de serviço azure ative (AAD) associado à conta.
- **FINGRP** é um grupo de utilizadores criado em AAD que contém utilizadores da organização Finanças.

Para obter instruções sobre como criar uma aplicação AAD (que também cria um Principal de Serviço), consulte [Criar uma aplicação AAD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Para obter instruções sobre como criar um grupo de utilizadores em AAD, consulte [grupos de gestão no Diretório Ativo Azure](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Alguns pontos-chave a considerar.

- A estrutura de pasta de dois níveis **(/clusters/finance)** deve ser criada e antevisão com permissões adequadas pelo administrador de armazenamento do Lago de Dados **antes** de utilizar a conta de armazenamento para clusters. Esta estrutura não é criada automaticamente enquanto cria clusters.
- O exemplo acima recomenda a definição do grupo de possuidor de **/clusters/finanças** como **FINGRP** e permitir o acesso **r-x** ao FINGRP a toda a hierarquia da pasta a partir da raiz. Isto garante que os membros do FINGRP podem navegar na estrutura da pasta a partir da raiz.
- No caso de diferentes diretores de serviços da AAD poderem criar clusters em **/clusters/finanças,** o bit pegajoso (quando definido na pasta **de finanças)** garante que as pastas criadas por um Diretor de Serviço não podem ser eliminadas pela outra.
- Uma vez que a estrutura e permissões da pasta estão em vigor, o processo de criação de cluster HDInsight cria um local de armazenamento específico do cluster em **/clusters/finanças/** . Por exemplo, o armazenamento de um cluster com o nome fincluster01 pode ser **/clusters/finance/fincluster01** . A propriedade e permissões para as pastas criadas pelo cluster HDInsight são mostradas na tabela aqui.

    |Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeadas | Grupo nomeado | Permissões de grupo nomeadas |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Principal de Serviço |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Recomendações para a entrada de emprego e dados de saída

Recomendamos que os dados de entrada para um trabalho e as saídas de um trabalho sejam armazenados numa pasta fora **/clusters** . Isto garante que mesmo que a pasta específica do cluster seja eliminada para recuperar algum espaço de armazenamento, as entradas e saídas de trabalho ainda estão disponíveis para uso futuro. Neste caso, certifique-se de que a hierarquia da pasta para armazenar as entradas e saídas de trabalho permite um nível de acesso adequado para o Diretor de Serviço.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite para os clusters que partilham uma única conta de armazenamento

O limite do número de clusters que podem partilhar uma única conta de Armazenamento do Lago de Dados depende da carga de trabalho que está a ser executada nesses clusters. Ter demasiados aglomerados ou cargas de trabalho muito pesadas nos clusters que partilham uma conta de armazenamento pode fazer com que a conta de armazenamento entre em estrangulamento.

## <a name="support-for-default-acls"></a>Apoio à Default-ACLs

Ao criar um Principal de Serviço com acesso a um utilizador nomeado (como mostrado na tabela acima), recomendamos **não** adicionar o utilizador nomeado com um ACL predefinido. O fornecimento de acesso a utilizadores nomeados utilizando ACLs predefinidos resulta na atribuição de 770 permissões para o utilizador próprio, grupo próprio e outros. Embora este valor predefinido de 770 não retire permissões de um utilizador próprio (7) ou de um grupo de proprietária (7), retira todas as permissões para outros (0). Isto resulta numa questão conhecida com um caso de utilização particular que é discutido em pormenor na secção [questões e soluções alternativas conhecidas.](#known-issues-and-workarounds)

## <a name="known-issues-and-workarounds"></a>Questões e soluções alternativas conhecidas

Esta secção lista os problemas conhecidos para a utilização de HDInsight com o Armazenamento do Lago de Dados, e as suas soluções alternativas.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Recursos de YARN Apache Hadoop localizados publicamente visíveis

Quando uma nova conta de armazenamento do Lago de Dados Azure é criada, o diretório de raiz é automaticamente a provisionado com bits de permissão Access-ACL definidos para 770. O utilizador da pasta raiz é definido para o utilizador que criou a conta (a administração de Armazenamento de Data Lake) e o grupo de propriedade está definido para o grupo principal do utilizador que criou a conta. Não há acesso a "outros".

Estas definições são conhecidas por afetar um caso de utilização HDInsight específico capturado em [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). As submissões de emprego podem falhar com uma mensagem de erro semelhante a esta:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Tal como afirmado no YARN JIRA, ligado anteriormente, enquanto se localizava recursos públicos, o localizador valida que todos os recursos solicitados são efetivamente públicos, verificando as suas permissões no sistema de ficheiros remotos. Qualquer Fonte Local que não se encaixe nessa condição é rejeitada para localização. O cheque para permissões, inclui acesso de leitura ao ficheiro para "outros". Este cenário não funciona fora da caixa quando hospeda clusters HDInsight no Azure Data Lake, uma vez que a Azure Data Lake nega todo o acesso a "outros" ao nível da pasta de raiz.

#### <a name="workaround"></a>Solução

Desempate as permissões de execução de leitura para **outros** através da hierarquia, por exemplo, em **/** , **/clusters** e **/clusters/finanças** como mostrado na tabela acima.

## <a name="see-also"></a>Ver também

- [Início rápido: Configurar clusters no HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)