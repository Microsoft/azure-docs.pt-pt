---
title: 'Segurança: migrar Apache Hadoop locais para o Azure HDInsight'
description: Aprenda as práticas recomendadas de segurança e DevOps para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974411"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrar clusters de Apache Hadoop locais para as práticas recomendadas do Azure HDInsight-Security e DevOps

Este artigo fornece recomendações de segurança e DevOps em sistemas do Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar na migração de sistemas locais Apache Hadoop para o Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Proteger e controlar o cluster com o Enterprise Security Package

O Enterprise Security Package (ESP) dá suporte à autenticação baseada em Active Directory, ao suporte multiusuário e ao controle de acesso baseado em função. Com a opção ESP escolhida, o cluster HDInsight é Unido ao domínio de Active Directory e o administrador corporativo pode configurar o RBAC (controle de acesso baseado em função) para segurança de Apache Hive usando o Apache Ranger. O administrador também pode auditar o acesso a dados por funcionários e quaisquer alterações feitas nas políticas de controle de acesso.

O ESP está disponível nos seguintes tipos de cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e consulta interativa (Hive LLAP).

Use as etapas a seguir para implantar o cluster HDInsight ingressado no domínio:

- Implante Azure Active Directory (AAD) passando o nome de domínio.
- Implantar Azure Active Directory Domain Services (AAD DS).
- Crie a rede virtual e a sub-rede necessárias.
- Implante uma VM na rede virtual para gerenciar o AAD DS.
- Ingresse a VM no domínio.
- Instale as ferramentas de AD e DNS.
- Faça com que o administrador do AAD DS crie uma UO (unidade organizacional).
- Habilite LDAPs para o AAD DS.
- Crie uma conta de serviço no Azure Active Directory com permissão de administrador de gravação & de leitura delegada para a UO, para que ela possa. Essa conta de serviço pode então unir computadores ao domínio e posicionar entidades de segurança na UO. Ele também pode criar entidades de serviço dentro da UO que você especificar durante a criação do cluster.

    > [!Note]
    > A conta de serviço não precisa ser uma conta de administrador de domínio do AD.

- Implante o cluster do HDInsight ESP definindo os seguintes parâmetros:

    |Parâmetro |Descrição |
    |---|---|
    |Nome de domínio|O nome de domínio associado ao AD DS do Azure.|
    |Nome de usuário de domínio|A conta de serviço no domínio gerenciado pelo DC AD DS do Azure que você criou na seção anterior, por exemplo: `hdiadmin@contoso.onmicrosoft.com`. Esse usuário de domínio será o administrador deste cluster HDInsight.|
    |Senha do domínio|A senha da conta de serviço.|
    |Unidade organizacional|O nome distinto da UO que você deseja usar com o cluster HDInsight, por exemplo: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Se essa UO não existir, o cluster HDInsight tentará criar a UO usando os privilégios da conta de serviço.|
    |URL DE LDAPS|por exemplo, `ldaps://contoso.onmicrosoft.com:636`.|
    |Acessar grupo de usuários|Os grupos de segurança cujos usuários você deseja sincronizar com o cluster, por exemplo: `HiveUsers`. Se você quiser especificar vários grupos de usuários, separe-os por ponto e vírgula '; '. Os grupos devem existir no diretório antes da criação do cluster ESP.|

Para obter mais informações, veja os artigos seguintes:

- [Uma introdução à segurança de Apache Hadoop com clusters HDInsight ingressados no domínio](../domain-joined/hdinsight-security-overview.md)
- [Planejar clusters de Apache Hadoop ingressados no domínio do Azure no HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configurar um cluster HDInsight ingressado no domínio usando Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizar Azure Active Directory usuários com um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configurar políticas de Apache Hive no HDInsight ingressado no domínio](../domain-joined/apache-domain-joined-run-hive.md)
- [Executar o Apache Oozie em clusters Hadoop do HDInsight ingressados no domínio](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implemente segurança empresarial de ponta a ponta

A segurança empresarial de ponta a ponta pode ser obtida usando os seguintes controles:

**Pipeline de dados particular e protegido (segurança em nível de perímetro)**
    - A segurança de nível de perímetro pode ser obtida por meio de redes virtuais do Azure, grupos de segurança de rede e serviço de gateway.

**Autenticação e autorização para acesso a dados**
    - Crie um cluster HDInsight ingressado no domínio usando Azure Active Directory Domain Services. (Enterprise Security Package).
    - Use o Ambari para fornecer acesso baseado em função aos recursos de cluster para usuários do AD.
    - Use o Apache Ranger para definir políticas de controle de acesso para o hive no nível de tabela/coluna/linha.
    - O acesso do SSH ao cluster pode ser restrito apenas ao administrador.

**Auditoria**
    - Exibir e relatar todo o acesso aos dados e recursos do cluster HDInsight.
    - Exibir e relatar todas as alterações para as políticas de controle de acesso.

**Encriptação**
    - Criptografia transparente do lado do servidor usando chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente.
    - Criptografia em trânsito usando criptografia do lado do cliente, HTTPS e TLS.

Para obter mais informações, veja os artigos seguintes:

- [Visão geral das redes virtuais do Azure](../../virtual-network/virtual-networks-overview.md)
- [Visão geral dos grupos de segurança de rede do Azure](../../virtual-network/security-overview.md)
- [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guia de segurança do Armazenamento do Azure](../../storage/blobs/security-recommendations.md)
- [Criptografia do Serviço de Armazenamento do Azure em repouso](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Usar monitoramento & alertas

Para obter mais informações, consulte o artigo:

[Descrição Geral do Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Atualizar clusters

Atualize regularmente para a versão mais recente do HDInsight para aproveitar os recursos mais recentes. As etapas a seguir podem ser usadas para atualizar o cluster para a versão mais recente:

1. Crie um novo cluster de teste do HDInsight usando a versão mais recente do HDInsight disponível.
1. Teste no novo cluster para certificar-se de que os trabalhos e as cargas de trabalho funcionam conforme o esperado.
1. Modifique trabalhos ou aplicativos ou cargas de trabalho conforme necessário.
1. Faça backup de todos os dados transitórios armazenados localmente nos nós do cluster.
1. Exclua o cluster existente.
1. Crie um cluster da versão mais recente do HDInsight na mesma sub-rede de rede virtual, usando os mesmos dados padrão e meta Store que o cluster anterior.
1. Importe os dados transitórios dos quais foi feito backup.
1. Iniciar trabalhos/continuar o processamento usando o novo cluster.

Para obter mais informações, consulte o artigo: [atualizar o cluster HDInsight para uma nova versão](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Sistemas operacionais de cluster de patch

Como um serviço Hadoop gerenciado, o HDInsight cuida da aplicação de patch no sistema operacional das VMs usadas por clusters HDInsight.

Para obter mais informações, consulte o artigo: [aplicação de patch de so para HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Pós-migração

1. **Corrigir aplicativos** – faça as alterações necessárias de forma iterativa nos trabalhos, processos e scripts.
2. **Executar testes** -execução iterativa de testes funcionais e de desempenho.
3. **Otimize** -resolva os problemas de desempenho com base nos resultados do teste acima e, em seguida, teste novamente para confirmar as melhorias de desempenho.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre o [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
