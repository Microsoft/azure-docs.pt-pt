---
title: 'Segurança: Migrar no local Apache Hadoop para Azure HDInsight'
description: Aprenda segurança e DevOps as melhores práticas para migrar no local aglomerados Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75974411"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight - segurança e DevOps boas práticas

Este artigo dá recomendações para segurança e DevOps em sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar na migração dos sistemas Apache Hadoop para o Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Seguro e governar cluster com pacote de segurança empresarial

O Pacote de Segurança Empresarial (ESP) suporta a autenticação baseada no Diretório Ativo, suporte multiutilizador e controlo de acesso baseado em funções. Com a opção ESP escolhida, o cluster HDInsight é unido ao domínio Ative Directory e o administrador da empresa pode configurar o controlo de acesso baseado em funções (RBAC) para a segurança da Apache Hive utilizando o Apache Ranger. O administrador também pode auditar o acesso de dados pelos colaboradores e quaisquer alterações feitas para aceder às políticas de controlo.

EsP está disponível nos seguintes tipos de cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e Interactive Query (Hive LLAP).

Utilize os seguintes passos para implementar o cluster HDInsight, filiado no domínio:

- Implementar o Diretório Ativo Azure (AAD) passando o nome de Domínio.
- Implemente serviços de domínio de diretório ativo azure (AAD DS).
- Crie a rede virtual e a sub-rede necessárias.
- Implemente um VM na Rede Virtual para gerir o AAD DS.
- Junte-se ao VM ao domínio.
- Instale ferramentas AD e DNS.
- Mande o Administrador AAD DS criar uma Unidade Organizacional (OU).
- Ativar o LDAPS para AAD DS.
- Crie uma conta de serviço no Azure Ative Directory com leitura delegada & escrever permissão de administração para a U, para que possa. Esta conta de serviço pode então juntar máquinas ao domínio e colocar os diretores da máquina dentro da U. Também pode criar diretores de serviço dentro da Ou que especifica durante a criação de clusters.

    > [!Note]
    > A conta de serviço não precisa de ser uma conta de administração de domínio AD.

- Implementar o cluster ESP HDInsight definindo os seguintes parâmetros:

    |Parâmetro |Descrição |
    |---|---|
    |Nome de domínio|O nome de domínio que está associado ao Azure AD DS.|
    |Nome do utilizador do domínio|A conta de serviço no domínio gerido por Azure AD DS DC `hdiadmin@contoso.onmicrosoft.com`que criou na secção anterior, por exemplo: . Este utilizador de domínio será o administrador deste cluster HDInsight.|
    |Senha de domínio|A senha da conta de serviço.|
    |Unidade organizacional|O nome distinto da Ou que pretende utilizar com o cluster `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`HDInsight, por exemplo: . Se este OU não existir, o cluster HDInsight tenta criar a Ou usando os privilégios da conta de serviço.|
    |LDAPS URL|por exemplo, `ldaps://contoso.onmicrosoft.com:636`.|
    |Grupo de utilizadores de acesso|Os grupos de segurança cujos utilizadores pretende sincronizar com o cluster, por exemplo: `HiveUsers`. Se pretender especificar vários grupos de utilizadores, separe-os por ponto e vírgula ';'. O(s) grupo(s) deve existir no diretório antes de criar o cluster ESP.|

Para obter mais informações, veja os artigos seguintes:

- [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](../domain-joined/hdinsight-security-overview.md)
- [Grupos Apache Hadoop unidos pelo domínio do Plan Azure em HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configure um cluster HDInsight associado ao domínio utilizando serviços de domínio de diretório ativo Azure](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configure as políticas da Hive Apache no HDInsight filiado em domínio](../domain-joined/apache-domain-joined-run-hive.md)
- [Executar Apache Oozie em clusters Hadoop HDInsight unidos pelo domínio](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementar fim para acabar com a segurança da empresa

A segurança da empresa final pode ser alcançada utilizando os seguintes controlos:

**Gasoduto de dados privado e protegido (segurança do nível do perímetro)**
    - Nível de perímetro A segurança pode ser alcançada através de Redes Virtuais Azure, Grupos de Segurança de Rede e serviço Gateway.

**Autenticação e autorização para acesso a dados**
    - Crie um cluster HDInsight associado ao domínio utilizando serviços de domínio de diretório ativo Azure. (Pacote de Segurança Empresarial).
    - Utilize a Ambari para fornecer acesso baseado em funções aos recursos de cluster para utilizadores de Anúncios.
    - Utilize o Apache Ranger para definir as políticas de controlo de acesso para a Colmeia no nível de mesa/coluna/linha.
    - O acesso sSH ao cluster só pode ser restringido ao administrador.

**Auditoria**
    - Ver e reportar todo o acesso aos recursos e dados do cluster HDInsight.
    - Ver e reportar todas as alterações às políticas de controlo de acesso.

**Encriptação**
    - Encriptação transparente do Lado do Servidor utilizando chaves geridas pela Microsoft ou chaves geridas pelo cliente.
    - Na encriptação transit usando encriptação do lado do cliente, https e TLS.

Para obter mais informações, veja os artigos seguintes:

- [Visão geral das Redes Virtuais Azure](../../virtual-network/virtual-networks-overview.md)
- [Visão geral dos Grupos de Segurança da Rede Azure](../../virtual-network/security-overview.md)
- [Peering da Rede Virtual Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guia de segurança do Armazenamento do Azure](../../storage/blobs/security-recommendations.md)
- [Encriptação do serviço de armazenamento Azure em repouso](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Utilizar alerta de monitorização &

Para mais informações, consulte o artigo:

[Descrição Geral do Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Agrupamentos de upgrade

Atualize regularmente para a mais recente versão HDInsight para tirar partido das funcionalidades mais recentes. Os seguintes passos podem ser usados para atualizar o cluster para a versão mais recente:

1. Crie um novo cluster TEST HDInsight utilizando a mais recente versão HDInsight disponível.
1. Teste o novo cluster para garantir que os empregos e as cargas de trabalho funcionem como esperado.
1. Modificar empregos ou aplicações ou cargas de trabalho conforme necessário.
1. Recue quaisquer dados transitórios armazenados localmente nos nós do cluster.
1. Elimine o aglomerado existente.
1. Crie um cluster da mais recente versão HDInsight na mesma subnet de rede virtual, utilizando os mesmos dados padrão e metaloja do cluster anterior.
1. Importar quaisquer dados transitórios que foram apoiados.
1. Inicie empregos/continue a processar usando o novo cluster.

Para mais informações, consulte o artigo: Atualize o [cluster HDInsight para uma nova versão](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Sistemas operativos de cluster de patch

Como um serviço Hadoop gerido, o HDInsight cuida de corrigir o Os dos VMs usados pelos clusters HDInsight.

Para mais informações, consulte o artigo: [Os patching para HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Pós-Migração

1. **Remediar as aplicações** - Iterativamente fazer as alterações necessárias aos empregos, processos e scripts.
2. **Realizar Testes** - Realizar testes funcionais e de desempenho iterativamente.
3. **Otimizar** - Abordar quaisquer problemas de desempenho com base nos resultados dos testes acima e, em seguida, testar novamente para confirmar as melhorias de desempenho.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre [hDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
