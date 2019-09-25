---
title: Visão geral da segurança corporativa no Azure HDInsight
description: Conheça os vários métodos para garantir a segurança corporativa no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 09/23/2019
ms.openlocfilehash: e1863cc54759f6cc2266073629093d4923260525
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240410"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Visão geral da segurança corporativa no Azure HDInsight

O Azure HDInsight oferece vários métodos para atender às suas necessidades de segurança corporativa. A maioria dessas soluções não é ativada por padrão. Essa flexibilidade permite que você escolha os recursos de segurança que são mais importantes para você e ajuda a evitar o pagamento de recursos que você não deseja. Isso também significa que é sua responsabilidade certificar-se de que as soluções corretas estejam habilitadas para sua instalação e seu ambiente.

Este artigo analisa as soluções de segurança dividindo as soluções de segurança ao longo das linhas de quatro pilares de segurança tradicionais: segurança de perímetro, autenticação, autorização e criptografia.

Este artigo também apresenta a **Enterprise Security Package do Azure HDInsight (ESP)** , que fornece autenticação baseada em Active Directory, suporte a vários usuários e controle de acesso baseado em função para clusters HDInsight.

## <a name="enterprise-security-pillars"></a>Pilares de segurança corporativa

Uma maneira de examinar a segurança corporativa divide as soluções de segurança em quatro grupos principais com base no tipo de controle. Esses grupos também são chamados de pilares de segurança e são os seguintes: segurança de perímetro, autenticação, autorização e criptografia.

### <a name="perimeter-security"></a>Segurança de perímetro

A segurança do perímetro no HDInsight é obtida por meio de [redes virtuais](../hdinsight-plan-virtual-network-deployment.md). Um administrador corporativo pode criar um cluster dentro de uma VNET (rede virtual) e usar NSG (grupos de segurança de rede) para restringir o acesso à rede virtual. Somente os endereços IP permitidos nas regras NSG de entrada poderão se comunicar com o cluster HDInsight. Essa configuração fornece segurança de perímetro.

Todos os clusters implantados em uma VNET também terão um ponto de extremidade privado que é resolvido para um IP privado dentro da VNET para acesso HTTP privado aos gateways de cluster.

### <a name="authentication"></a>Authentication

O [Enterprise Security Package](apache-domain-joined-architecture.md) do HDInsight fornece autenticação baseada em Active Directory, suporte a vários usuários e controle de acesso baseado em função. A integração de Active Directory é obtida com o uso de [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Com esses recursos, você pode criar um cluster HDInsight que tenha ingressado em um domínio Active Directory gerenciado. Em seguida, você pode configurar uma lista de funcionários da empresa que podem autenticar e entrar no cluster.

Com essa configuração, os funcionários da empresa podem entrar nos nós do cluster usando suas credenciais de domínio. Eles também podem usar suas credenciais de domínio para autenticar com outros pontos de extremidade aprovados, como modos de exibição Apache Ambari, ODBC, JDBC, PowerShell e APIs REST para interagir com o cluster. 

### <a name="authorization"></a>Autorização

Uma prática recomendada que a maioria das empresas segue é garantir que nem todos os funcionários tenham acesso a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controle de acesso baseado em função para os recursos de cluster. Isso só está disponível nos clusters ESP.

O administrador do Hadoop pode configurar o RBAC (controle de acesso baseado em função) para proteger o Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) e [Kafka](apache-domain-joined-run-kafka.md) usando esses plug-ins no Apache Ranger. A configuração de políticas de RBAC permite que você associe permissões a uma função na organização. Essa camada de abstração torna mais fácil garantir que as pessoas tenham apenas as permissões necessárias para realizar suas responsabilidades de trabalho. O Ranger também permite auditar o acesso a dados de funcionários e quaisquer alterações feitas nas políticas de controle de acesso.

Por exemplo, o administrador pode configurar o [Apache Ranger](https://ranger.apache.org/) para definir políticas de controlo de acesso para o Hive. Essa funcionalidade garante a filtragem em nível de linha e de coluna (mascaramento de dados) e filtra os dados confidenciais de usuários não autorizados.

### <a name="auditing"></a>Auditoria

A auditoria de todo o acesso aos recursos de cluster e dos dados é necessária para controlar o acesso não autorizado ou não intencional dos recursos. É tão importante quanto proteger os recursos do cluster HDInsight contra usuários não autorizados e proteger os dados.

O administrador pode exibir e relatar todo o acesso aos dados e recursos do cluster HDInsight. O administrador também pode exibir e relatar todas as alterações nas políticas de controle de acesso criadas em pontos de extremidade com suporte do Apache Ranger. 

Para acessar os logs de auditoria do Apache Ranger e do Ambari, bem como os logs de acesso SSH, [habilite Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) e exiba as tabelas que fornecem registros de auditoria.

### <a name="encryption"></a>Encriptação

A proteção de dados é importante para atender aos requisitos de conformidade e segurança organizacional. Além de restringir o acesso a dados de funcionários não autorizados, você deve criptografá-los.

Ambos os armazenamentos de dados para clusters HDInsight, armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen1/Gen2, dão suporte à [criptografia transparente do servidor de dados](../../storage/common/storage-service-encryption.md) em repouso. Os clusters HDInsight seguros trabalharão diretamente com esse recurso de criptografia de dados no lado do servidor em repouso.

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade compartilhada

A imagem a seguir resume as principais áreas de segurança do sistema e as soluções de segurança que estão disponíveis para você em cada uma delas. Ele também realça quais áreas de segurança são sua responsabilidade como um cliente e quais áreas são responsabilidade do HDInsight como o provedor de serviços.

![Diagrama de responsabilidades compartilhadas do HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

A tabela a seguir fornece links para recursos para cada tipo de solução de segurança.

| Área de segurança | Soluções disponíveis | Parte responsável |
|---|---|---|
| Segurança de acesso a dados | Configurar [ACLs de listas de controle de acesso](../../storage/blobs/data-lake-storage-access-control.md) para Azure data Lake Storage Gen1 e Gen2  | Cliente |
|  | Habilite a propriedade ["transferência segura obrigatória"](../../storage/common/storage-require-secure-transfer.md) em contas de armazenamento. | Cliente |
|  | Configurar redes virtuais e [firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md) | Cliente |
|  | Configurar [pontos de extremidade de serviço de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) para Cosmos DB e [BD SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Cliente |
|  | Verifique se a [criptografia TLS](../../storage/common/storage-security-tls.md) está habilitada para os dados em trânsito. | Cliente |
|  | Configurar [chaves gerenciadas pelo cliente](../../storage/common/storage-encryption-keys-portal.md) para a criptografia de armazenamento do Azure | Cliente |
| Segurança de aplicativo e middleware | Integrar com o AAD-DS e [Configurar a autenticação](apache-domain-joined-configure-using-azure-adds.md) | Cliente |
|  | Configurar políticas de [autorização do Apache Ranger](apache-domain-joined-run-hive.md) | Cliente |
|  | Usar [logs de Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Cliente |
| Segurança do sistema operacional | Criar clusters com a imagem de base segura mais recente | Cliente |
|  | Garantir a [aplicação de patch do sistema operacional](../hdinsight-os-patching.md) em intervalos regulares | Cliente |
| Segurança da rede | Configurar uma [rede virtual](../hdinsight-plan-virtual-network-deployment.md) |
|  | Configurar [regras do NSG (grupo de segurança de rede) de entrada](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Cliente |
|  | Configurar a [restrição de tráfego de saída](../hdinsight-restrict-outbound-traffic.md) com o firewall (versão prévia) | Cliente |
| Infraestrutura virtualizada | N/A | HDInsight (provedor de nuvem) |
| Segurança de infraestrutura física | N/A | HDInsight (provedor de nuvem) |

## <a name="next-steps"></a>Passos seguintes

* [Planejar clusters HDInsight com ESP](apache-domain-joined-architecture.md)
* [Configurar clusters HDInsight com o ESP](apache-domain-joined-configure.md)
* [Gerenciar clusters HDInsight com o ESP](apache-domain-joined-manage.md)
