---
title: Visão geral da segurança da empresa em Azure HDInsight
description: Aprenda os vários métodos para garantir a segurança da empresa no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 94823445e4f8e3f8d40a219dc23b40f8a5c267c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996317"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Visão geral da segurança da empresa em Azure HDInsight

O Azure HDInsight oferece uma série de métodos para responder às necessidades de segurança da empresa. A maioria destas soluções não são ativadas por padrão. Esta flexibilidade permite-lhe escolher as funcionalidades de segurança que são mais importantes para si. E ajuda-o a evitar pagar por funcionalidades que não quer. Esta flexibilidade também significa que é da sua responsabilidade garantir que as soluções corretas estão ativadas para a sua configuração e ambiente.

Este artigo analisa as soluções de segurança dividindo as soluções de segurança em quatro pilares de segurança tradicionais: segurança do perímetro, autenticação, autorização e encriptação.

Este artigo também introduz o Pacote de **Segurança Empresarial Azure HDInsight (ESP),** que fornece autenticação baseada em Diretório ativo, suporte multiutilizador e controlo de acesso baseado em papéis para clusters HDInsight.

## <a name="enterprise-security-pillars"></a>Pilares de segurança da empresa

Uma forma de olhar para a segurança das empresas divide as soluções de segurança em quatro grupos principais com base no tipo de controlo. Estes grupos também são chamados pilares de segurança e são os seguintes tipos: segurança do perímetro, autenticação, autorização e encriptação.

### <a name="perimeter-security"></a>Segurança do perímetro

A segurança do perímetro no HDInsight é conseguida através de [redes virtuais.](../hdinsight-plan-virtual-network-deployment.md) Um administrador da empresa pode criar um cluster dentro de uma rede virtual (VNET) e usar grupos de segurança de rede (NSG) para restringir o acesso à rede virtual. Apenas os endereços IP permitidos nas regras nsg de entrada podem comunicar com o cluster HDInsight. Esta configuração proporciona segurança no perímetro.

Todos os clusters implantados num VNET também terão um ponto final privado. O ponto final resolve um IP privado dentro do VNET para acesso privado http aos gateways do cluster.

### <a name="authentication"></a>Autenticação

[O Pacote](apache-domain-joined-architecture.md) de Segurança Empresarial da HDInsight fornece autenticação baseada em Diretório ativo, suporte multiutilizador e controlo de acesso baseado em papéis. A integração do Diretório Ativo é conseguida através da utilização de Serviços de [Domínio de Diretório Ativo Azure.](../../active-directory-domain-services/overview.md) Com estas capacidades, pode criar um cluster HDInsight unido a um domínio de Diretório Ativo. Em seguida, configurar uma lista de colaboradores da empresa que podem autenticar para o cluster.

Com esta configuração, os colaboradores da empresa podem iniciar sessão nos nós do cluster utilizando as suas credenciais de domínio. Também podem usar as suas credenciais de domínio para autenticar com outros pontos finais aprovados. Como Apache Ambari Views, ODBC, JDBC, PowerShell e REST APIs para interagir com o cluster.

### <a name="authorization"></a>Autorização

As melhores práticas que a maioria das empresas segue é garantir que nem todos os colaboradores tenham acesso total a todos os recursos empresariais. Da mesma forma, o administrador pode definir políticas de controlo de acesso baseadas em papéis para os recursos de cluster. Esta ação só está disponível nos clusters da PE.

O administrador hadoop pode configurar o controlo de acesso baseado em funções (RBAC). As configurações protegem a [Apache Hive,](apache-domain-joined-run-hive.md) [HBase](apache-domain-joined-run-hbase.md)e [Kafka](apache-domain-joined-run-kafka.md) com plugins Apache Ranger. Configurar as políticas rBAC permite-lhe associar permissões com um papel na organização. Esta camada de abstração facilita a garantia de que as pessoas têm apenas as permissões necessárias para cumprirem as suas responsabilidades de trabalho. A Ranger também lhe permite auditar o acesso de dados dos colaboradores e quaisquer alterações feitas para aceder às políticas de controlo.

Por exemplo, o administrador pode configurar o [Apache Ranger](https://ranger.apache.org/) para definir políticas de controlo de acesso para o Hive. Esta funcionalidade garante a filtragem ao nível da linha e da coluna (máscara de dados). E filtra os dados sensíveis de utilizadores não autorizados.

### <a name="auditing"></a>Auditoria

A auditoria do acesso aos recursos do cluster é necessária para acompanhar o acesso não autorizado ou não intencional dos recursos. É tão importante como proteger os recursos do cluster de acesso não autorizado.

O administrador pode visualizar e reportar todo o acesso aos recursos e dados do cluster HDInsight. O administrador pode ver e reportar alterações às políticas de controlo de acesso.

Para aceder aos registos de auditoria apache Ranger e Ambari, e registos de acesso ssh, [enable Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing). E veja as tabelas que fornecem registos de auditoria.

### <a name="encryption"></a>Encriptação

A proteção dos dados é importante para satisfazer os requisitos de segurança organizacional e conformidade. Além de restringir o acesso a dados de funcionários não autorizados, deve engrafá-lo.

Armazenamento azure e armazenamento de data lake Gen1/Gen2, suporte [encriptação](../../storage/common/storage-service-encryption.md) transparente do lado do servidor de dados em repouso. Os clusters HDInsight seguros funcionarão perfeitamente com a encriptação do lado do servidor dos dados em repouso.

### <a name="compliance"></a>Conformidade

As ofertas de conformidade azure baseiam-se em vários tipos de garantias, incluindo certificações formais. Além disso, atestados, validações e autorizações. Avaliações efetuadas por empresas de auditoria independentes de terceiros. Alterações contratuais, autoavaliações e documentos de orientação do cliente produzidos pela Microsoft. Para obter informações sobre conformidade com o HDInsight, consulte o [Microsoft Trust Center](https://www.microsoft.com/trust-center) e a visão geral da conformidade do Microsoft [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade partilhada

A imagem que se segue resume as principais áreas de segurança do sistema e as soluções de segurança que estão disponíveis para si em cada uma. Também destaca quais as áreas de segurança que são da sua responsabilidade enquanto cliente. E quais as áreas que são da responsabilidade da HDInsight como prestadora de serviços.

![Diagrama de responsabilidades partilhadas HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

A tabela seguinte fornece ligações aos recursos para cada tipo de solução de segurança.

| Área de segurança | Soluções disponíveis | Responsável |
|---|---|---|
| Segurança de Acesso a Dados | Configure listas de [controlo de acesso SLs](../../storage/blobs/data-lake-storage-access-control.md) para Azure Data Lake Storage Gen1 e Gen2  | Cliente |
|  | Ativar a propriedade ["Transferência Segura necessária"](../../storage/common/storage-require-secure-transfer.md) nas contas de armazenamento. | Cliente |
|  | Configure firewalls de [armazenamento Azure](../../storage/common/storage-network-security.md) e redes virtuais | Cliente |
|  | Configure [Azure pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço de rede virtual para Cosmos DB e [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Cliente |
|  | Certifique-se de que a [encriptação TLS](../../storage/common/storage-security-tls.md) está ativada para dados em trânsito. | Cliente |
|  | Configure [as chaves geridas pelo cliente](../../storage/common/storage-encryption-keys-portal.md) para encriptação do Armazenamento Azure | Cliente |
| Aplicação e segurança de middleware | Integrar com AAD-DS e [Configurar a autenticação](apache-domain-joined-configure-using-azure-adds.md) | Cliente |
|  | Configure as políticas de [autorização do Apache Ranger](apache-domain-joined-run-hive.md) | Cliente |
|  | Utilize [os registos do Monitor Azure](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Cliente |
| Segurança do sistema operativo | Crie clusters com a mais recente imagem de base segura | Cliente |
|  | [Certifique-se de remendo de OS](../hdinsight-os-patching.md) em intervalos regulares | Cliente |
| Segurança da rede | Configurar uma [rede virtual](../hdinsight-plan-virtual-network-deployment.md) |
|  | Configure regras do grupo de [segurança de rede Inbound (NSG)](../control-network-traffic.md) | Cliente |
|  | Configure [restrição de tráfego de saída](../hdinsight-restrict-outbound-traffic.md) com Firewall | Cliente |
| Infraestrutura virtualizada | N/D | HDInsight (fornecedor de nuvem) |
| Segurança física das infraestruturas | N/D | HDInsight (fornecedor de nuvem) |

## <a name="next-steps"></a>Próximos passos

* [Plano para clusters HDInsight com ESP](apache-domain-joined-architecture.md)
* [Configure os clusters HDInsight com ESP](apache-domain-joined-configure.md)
* [Gerir clusters HDInsight com ESP](apache-domain-joined-manage.md)
