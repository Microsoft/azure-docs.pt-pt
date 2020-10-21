---
title: 'Segurança: Migrar para as instalações apache Hadoop para Azure HDInsight'
description: Aprenda segurança e devOps as melhores práticas para migrar no local os clusters Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 7deaca67212146881754f785e6dceaa064bde6e7
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329428"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrar para os centros apache Hadoop para Azure HDInsight - segurança e devOps melhores práticas

Este artigo dá recomendações para segurança e DevOps em sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas Apache Hadoop para Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Secure and govern cluster with Enterprise Security Package

O Pacote de Segurança Empresarial (ESP) suporta a autenticação baseada no Diretório Ativo, suporte multiuser e controlo de acesso baseado em funções. Com a opção ESP escolhida, o cluster HDInsight é unido ao domínio ative directory e o administrador da empresa pode configurar o controlo de acesso baseado em funções (RBAC) para a segurança da Hive Apache usando o Apache Ranger. O administrador também pode auditar o acesso de dados por parte dos colaboradores e quaisquer alterações feitas para aceder às políticas de controlo de acesso.

O ESP está disponível nos seguintes tipos de cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e Interactive Query (Hive LLAP).

Utilize os seguintes passos para implantar o cluster HDInsight de domínio:

- Implementar o Azure Ative Directory (AAD) passando o nome de Domínio.
- Implementar serviços de domínio de diretório ativo Azure (AAD DS).
- Crie a rede virtual e a sub-rede necessárias.
- Implementar um VM na Rede Virtual para gerir o AAD DS.
- Junte-se ao VM ao domínio.
- Instale ferramentas AD e DNS.
- Que o Administrador DS da AAD crie uma Unidade Organizacional (OU).
- Ativar LDAPS para AAD DS.
- Crie uma conta de serviço no Azure Ative Directory com leitura delegada & escrever permissão de administração para a U, para que possa. Esta conta de serviço pode então juntar máquinas ao domínio e colocar os principais da máquina dentro da U. Também pode criar princípios de serviço dentro da UO que especifique durante a criação do cluster.

    > [!Note]
    > A conta de serviço não precisa de ser conta de administração de domínio AD.

- Implementar o cluster HDInsight ESP definindo os seguintes parâmetros:

    |Parâmetro |Descrição |
    |---|---|
    |Nome de domínio|O nome de domínio que está associado ao Azure AD DS.|
    |Nome do utilizador do domínio|A conta de serviço no domínio gerido pela Azure AD DS DC que criou na secção anterior, por exemplo: `hdiadmin@contoso.onmicrosoft.com` . Este utilizador de domínio será o administrador deste cluster HDInsight.|
    |Senha de domínio|A senha da conta de serviço.|
    |Unidade organizacional|O nome distinto do UO que pretende utilizar com o cluster HDInsight, por exemplo: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` . Se este OU não existir, o cluster HDInsight tenta criar o OU utilizando os privilégios da conta de serviço.|
    |LDAPS URL|por exemplo, `ldaps://contoso.onmicrosoft.com:636` . .|
    |Aceder ao grupo de utilizadores|Os grupos de segurança cujos utilizadores pretende sincronizar com o cluster, por exemplo: `HiveUsers` . Se quiser especificar vários grupos de utilizadores, separe-os por pontos e vírgula ';'. O(s) grupo(s) deve existir no diretório antes de criar o cluster ESP.|

Para obter mais informações, veja os seguintes artigos:

- [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](../domain-joined/hdinsight-security-overview.md)
- [Plan Azure juntam-se a aglomerados Apache Hadoop em HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configure um cluster HDInsight de domínio, utilizando os Serviços de Domínio do Diretório Ativo Azure](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configure as políticas de Colmeia Apache em HDInsight de domínio](../domain-joined/apache-domain-joined-run-hive.md)
- [Executar Apache Oozie em clusters HDInsight Hadoop unidos pelo domínio](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementar fim para acabar com a segurança da empresa

A segurança final da empresa pode ser alcançada utilizando os seguintes controlos:

**Gasoduto de dados privado e protegido (segurança ao nível do perímetro)**
    - Nível de perímetro A segurança pode ser alcançada através de Redes Virtuais Azure, Grupos de Segurança de Rede e serviço Gateway.

**Autenticação e autorização para acesso a dados**
    - Criar cluster HDInsight unidos ao domínio utilizando serviços de domínio do diretório ativo Azure. (Pacote de Segurança Empresarial).
    - Utilize o Ambari para fornecer acesso baseado em funções aos recursos de cluster para utilizadores de AD.
    - Utilize o Apache Ranger para definir as políticas de controlo de acesso para a Colmeia ao nível da tabela / coluna/linha.
    - O acesso do SSH ao cluster só pode ser restringido ao administrador.

**Auditoria**
    - Ver e reportar todo o acesso aos recursos e dados do cluster HDInsight.
    - Ver e reportar todas as alterações às políticas de controlo de acesso.

**Encriptação**
    - A encriptação Server-Side transparente utilizando chaves geridas pela Microsoft ou chaves geridas pelo cliente.
    - Na encriptação de Trânsito utilizando Client-Side encriptação, https e TLS.

Para obter mais informações, veja os seguintes artigos:

- [Visão geral das Redes Virtuais Azure](../../virtual-network/virtual-networks-overview.md)
- [Visão geral dos grupos de segurança da rede Azure](../../virtual-network/security-overview.md)
- [Peering de Rede Virtual do Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guia de segurança de Armazenamento do Azure](../../storage/blobs/security-recommendations.md)
- [Encriptação do serviço de armazenamento Azure em repouso](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Utilizar & de monitorização alertando

Para mais informações, consulte o artigo:

[Visão geral do Monitor Azure](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Upgrade de clusters

Atualização regular para a versão HDInsight mais recente para tirar partido das funcionalidades mais recentes. Os seguintes passos podem ser usados para atualizar o cluster para a versão mais recente:

1. Crie um novo cluster TEST HDInsight utilizando a versão HDInsight mais recente disponível.
1. Teste no novo cluster para garantir que os empregos e as cargas de trabalho funcionem como esperado.
1. Modificar empregos ou aplicações ou cargas de trabalho conforme necessário.
1. Ressari quaisquer dados transitórios armazenados localmente nos nós do cluster.
1. Elimine o cluster existente.
1. Crie um cluster da versão HDInsight mais recente na mesma sub-rede de rede virtual, utilizando os mesmos dados predefinidos e meta-loja que o cluster anterior.
1. Importe quaisquer dados transitórios que foram apoiados.
1. Iniciar trabalhos/continuar a processar utilizando o novo cluster.

Para obter mais informações, consulte o artigo: [Upgrade o cluster HDInsight para uma nova versão](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Sistemas operativos de cluster de remendação

Para obter mais informações, consulte o artigo: [correção de SISTEMAS para HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Pós-Migração

1. **Aplicações corretivas** - Iterativamente escolho as alterações necessárias aos trabalhos, processos e scripts.
2. **Realizar Testes** - Executar iterativamente testes funcionais e de desempenho.
3. **Otimize** - Aborde quaisquer problemas de desempenho com base nos resultados dos testes acima e, em seguida, reteste para confirmar as melhorias de desempenho.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
