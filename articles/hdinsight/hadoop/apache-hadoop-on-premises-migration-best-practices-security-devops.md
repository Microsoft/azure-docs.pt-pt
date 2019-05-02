---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - segurança e as melhores práticas de DevOps
description: Saiba mais segurança e melhores práticas do DevOps para migrar clusters do Hadoop no local para Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1d13b52d253562a24946e6df2fc069f41b485fef
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707884"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - segurança e as melhores práticas de DevOps

Este artigo oferece recomendações de segurança e de DevOps nos sistemas do Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar a migrar sistemas de Apache Hadoop no local para Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Proteger e regular a cluster com o Enterprise Security Package

O pacote de segurança da empresa (ESP) suporta a autenticação baseada no Active Directory, o suporte de multiutilizador e o controlo de acesso baseado em funções. Com a opção de ESP escolhida, cluster do HDInsight está associado ao domínio do Active Directory e o administrador da empresa pode configurar o controlo de acesso baseado em funções (RBAC) para o Apache Hive security com o Apache Ranger. O administrador também pode auditar o acesso a dados por funcionários e quaisquer alterações efetuadas às políticas de controlo de acesso.

ESP está disponível nos seguintes tipos de cluster: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka e Interactive Query (LLAP de Hive). 

Utilize os seguintes passos para implementar o cluster do HDInsight associados a um domínio:

- Implemente o Azure Active Directory (AAD), passando o nome de domínio.
- Implemente serviços de domínio do Azure Active Directory (AAD DS).
- Crie a rede Virtual e sub-rede necessária.
- Implemente uma VM na rede Virtual para gerir DS do AAD.
- Junte-se a VM ao domínio.
- Instalar o AD e as ferramentas DNS.
- Ter o administrador de DS do AAD criar uma unidade organizacional (UO).
- Ative o LDAPS para DS do AAD.
- Crie uma conta de serviço no Azure Active Directory com o delegado de leitura e escrita permissão ao administrador para a UO, para que ele pode. Esta conta de serviço pode, em seguida, associar máquinas ao domínio e coloque principais de máquina na UO. Ele também pode criar principais de serviço na UO que especificou durante a criação do cluster.


    > [!Note]
    > A conta de serviço não tem de ser a conta de administrador de domínio do AD.


- Implemente o cluster de HDInsight ESP, definindo os seguintes parâmetros:
    - **Nome de domínio**: O nome de domínio que está associada com o Azure AD DS.
    - **Nome de utilizador de domínio**: A conta de serviço no domínio do Azure AD DS DC gerido que criou na secção anterior, por exemplo: `hdiadmin@contoso.onmicrosoft.com`. Este utilizador de domínio será o administrador deste cluster do HDInsight.
    - **Palavra-passe do domínio**: A palavra-passe da conta de serviço.
    - **Unidade organizacional**: O nome único da UO que pretende utilizar com o cluster do HDInsight, por exemplo: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Se essa UO não existir, o cluster de HDInsight tenta criar a UO usando os privilégios da conta de serviço.
    - **URL de LDAPS**: por exemplo, `ldaps://contoso.onmicrosoft.com:636`.
    - **Grupo de utilizadores de acesso**: Os grupos de segurança cujos usuários que quer sincronizar para o cluster, por exemplo: `HiveUsers`. Se pretender especificar múltiplos grupos de utilizador, separadas por ponto e vírgula ";". O grupo (s) tem de existir no diretório antes de criar o cluster do ESP.

Para obter mais informações, veja os artigos seguintes:

- [Uma introdução à segurança do Apache Hadoop com clusters do HDInsight associados a um domínio](../domain-joined/apache-domain-joined-introduction.md)

- [Planear clusters do Apache Hadoop associados a um domínio do Azure no HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Configurar um cluster do HDInsight associados a um domínio com o Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Sincronizar utilizadores do Azure Active Directory para um cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Configurar políticas de Apache Hive no HDInsight associado a um domínio](../domain-joined/apache-domain-joined-run-hive.md)
- [Execute o Apache Oozie no Hadoop do HDInsight associados a um domínio clusters](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementar a segurança de ponta a ponta enterprise

Ponto a segurança da empresa de ponto pode ser obtido usando os controles seguintes:

- **Pipeline de dados particulares e protegidos (segurança de nível de perímetro)**
    - Segurança em nível de perímetro pode ser alcançada através de redes virtuais do Azure, os grupos de segurança de rede e o serviço de Gateway.

- **Autenticação e autorização de acesso a dados**
    - Crie cluster do HDInsight associado a um domínio com o Azure Active Directory Domain Services. (Enterprise Security Package).
    - Utilize Ambari para fornecer acesso baseado em funções aos recursos do cluster para os utilizadores do AD.
    - Utilizar o Apache Ranger para definir políticas de controlo de acesso para o Hive na mesa / coluna / nível de linha.
    - Acesso SSH para o cluster pode ser restringido apenas ao administrador.

- **Auditoria**
    - Ver e comunicar todo o acesso aos dados e recursos de cluster do HDInsight.
    - Ver e comunicar todas as alterações para as políticas de controlo de acesso.

- **Encriptação**
    - Encriptação do lado do servidor transparente com chaves geridas pela Microsoft ou as chaves geridas pelo cliente.
    - Na encriptação de trânsito através de encriptação do lado do cliente, https e TLS.

Para obter mais informações, veja os artigos seguintes:

- [Descrição geral de redes virtuais do Azure](../../virtual-network/virtual-networks-overview.md)
- [Descrição geral de grupos de segurança de rede do Azure](../../virtual-network/security-overview.md)
- [Peering de rede Virtual do Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Guia de segurança do armazenamento do Azure](../../storage/common/storage-security-guide.md)
- [Encriptação do serviço de armazenamento do Azure em repouso](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Utilização de monitorização e alertas

Para obter mais informações, consulte o artigo:

[Descrição Geral do Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Atualizar clusters

Atualize regularmente para a versão mais recente do HDInsight para tirar partido das funcionalidades mais recentes. Os seguintes passos podem ser utilizados para atualizar o cluster para a versão mais recente:

1. Crie um novo cluster de HDInsight de teste com a versão de HDInsight mais recente disponível.
1. Teste no novo cluster para se certificar de que as cargas de trabalho e tarefas funcionam conforme esperado.
1. Modificar tarefas ou aplicações ou cargas de trabalho conforme necessário.
1. Fazer backup de todos os dados transitórios armazenados localmente em nós do cluster.
1. Elimine o cluster existente.
1. Crie um cluster do HDInsight versão mais recente na mesma sub-rede VNET, utilizar o mesmo arquivo de dados e metadados de predefinição que o cluster anterior.
1. Importe todos os dados transitórios que foi feitos backup.
1. Iniciar tarefas/continuam a utilizar o novo cluster de processamento.

Para obter mais informações, consulte o artigo: [Cluster de HDInsight atualização para uma nova versão](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Sistemas de operativos do cluster de patch

Como um serviço gerido do Hadoop HDInsight assume o controlo de aplicação de patches de SO das VMs utilizados pelos clusters do HDInsight.

Para obter mais informações, consulte o artigo: [Aplicação de patches de SO para o HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Pós-migração

1. **Correção dos aplicativos** - iterativamente efetue as alterações necessárias para os trabalhos, processos e scripts.
2. **Executar testes de** - iterativamente execução funcional e desempenho de testes.
3. **Otimizar** – resolva problemas de desempenho com base nos resultados de teste acima e, em seguida, testar novamente para confirmar as melhorias de desempenho.

## <a name="next-steps"></a>Passos Seguintes

- Leia mais sobre [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
