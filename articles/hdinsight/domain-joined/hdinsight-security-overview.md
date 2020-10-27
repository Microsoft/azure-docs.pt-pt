---
title: Visão geral da segurança da empresa em Azure HDInsight
description: Aprenda os vários métodos para garantir a segurança da empresa em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 7f450d54a0039f591178ae839fbb404f31d80671
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537274"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Visão geral da segurança da empresa em Azure HDInsight

A Azure HDInsight oferece uma série de métodos para responder às necessidades de segurança da sua empresa. A maioria destas soluções não são ativadas por defeito. Esta flexibilidade permite-lhe escolher as funcionalidades de segurança que são mais importantes para si e ajuda-o a evitar pagar por funcionalidades que não deseja. Esta flexibilidade também significa que é sua responsabilidade garantir que as soluções corretas são ativadas para a sua configuração e ambiente.

Este artigo analisa soluções de segurança dividindo soluções de segurança em quatro pilares de segurança tradicionais: segurança do perímetro, autenticação, autorização e encriptação.

Este artigo também introduz o **Azure HDInsight Enterprise Security Package (ESP),** que fornece autenticação baseada em diretório ativo, suporte a vários utilizadores e controlo de acesso baseado em funções para clusters HDInsight.

## <a name="enterprise-security-pillars"></a>Pilares de segurança da empresa

Uma forma de olhar para a segurança da empresa divide as soluções de segurança em quatro grupos principais com base no tipo de controlo. Estes grupos também são chamados de pilares de segurança e são os seguintes tipos: segurança do perímetro, autenticação, autorização e encriptação.

### <a name="perimeter-security"></a>Segurança do perímetro

A segurança do perímetro em HDInsight é conseguida através de [redes virtuais.](../hdinsight-plan-virtual-network-deployment.md) Um administrador da empresa pode criar um cluster dentro de uma rede virtual (VNET) e usar grupos de segurança de rede (NSG) para restringir o acesso à rede virtual. Apenas os endereços IP permitidos nas regras NSG de entrada podem comunicar com o cluster HDInsight. Esta configuração proporciona segurança no perímetro.

Todos os clusters implantados num VNET também terão um ponto final privado. O ponto final resolve-se para um IP privado dentro do VNET para acesso HTTP privado aos gateways de cluster.

### <a name="authentication"></a>Autenticação

[O Pacote](apache-domain-joined-architecture.md) de Segurança Empresarial da HDInsight fornece autenticação baseada em diretório ativo, suporte a vários utilizadores e controlo de acesso baseado em funções. A integração do Ative Directory é conseguida através da utilização dos Serviços de [Domínio do Diretório Ativo Azure.](../../active-directory-domain-services/overview.md) Com estas capacidades, pode criar um cluster HDInsight ligado a um domínio ative Directory. Em seguida, configurar uma lista de funcionários da empresa que podem autenticar para o cluster.

Com esta configuração, os colaboradores da empresa podem inscrever-se nos nós do cluster usando as suas credenciais de domínio. Também podem usar as suas credenciais de domínio para autenticar com outros pontos finais aprovados. Como Apache Ambari Views, ODBC, JDBC, PowerShell e REST APIs para interagir com o cluster.

### <a name="authorization"></a>Autorização

Uma das melhores práticas que a maioria das empresas segue é garantir que nem todos os funcionários tenham acesso total a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controlo de acesso baseadas em funções para os recursos de cluster. Esta ação só está disponível nos clusters esp.

O administrador Hadoop pode configurar o controlo de acesso baseado em funções (RBAC). As configurações asseguram [a](apache-domain-joined-run-hive.md)Colmeia Apache, [HBase](apache-domain-joined-run-hbase.md)e [Kafka](apache-domain-joined-run-kafka.md) com plugins Apache Ranger. Configurar as políticas do RBAC permite-lhe associar permissões com um papel na organização. Esta camada de abstração facilita a garantia de que as pessoas têm apenas as permissões necessárias para fazer as suas responsabilidades de trabalho. O Ranger também permite que você audite o acesso de dados dos colaboradores e quaisquer alterações feitas para aceder às políticas de controlo de acesso.

Por exemplo, o administrador pode configurar o [Apache Ranger](https://ranger.apache.org/) para definir políticas de controlo de acesso para o Hive. Esta funcionalidade garante a filtragem ao nível da linha e do nível da coluna (mascaramento de dados). E filtra os dados sensíveis de utilizadores não autorizados.

### <a name="auditing"></a>Auditoria

A auditoria do acesso a recursos de cluster é necessária para rastrear o acesso não autorizado ou não intencional dos recursos. É tão importante como proteger os recursos do cluster do acesso não autorizado.

O administrador pode ver e reportar todo o acesso aos recursos e dados do cluster HDInsight. O administrador pode ver e reportar alterações às políticas de controlo de acesso.

Para aceder aos registos de auditoria do Apache Ranger e Ambari e aos registos de acesso ssh, ativar o [Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) e ver as tabelas que fornecem registos de auditoria.

### <a name="encryption"></a>Encriptação

A proteção dos dados é importante para satisfazer os requisitos de segurança organizacional e de conformidade. Além de restringir o acesso a dados de funcionários não autorizados, deve criptografá-lo.

O HDInsight suporta a encriptação de dados em repouso com as chaves geridas pela plataforma e [geridas pelo cliente.](../disk-encryption.md) A encriptação de dados em trânsito é tratada tanto com tLS como IPSec. Consulte [a Encriptação em trânsito para Azure HDInsight](encryption-in-transit.md) para obter mais informações.

### <a name="compliance"></a>Conformidade

As ofertas de conformidade da Azure baseiam-se em vários tipos de garantias, incluindo certificações formais. Além disso, atestados, validações e autorizações. Avaliações produzidas por empresas de auditoria independentes de terceiros. Alterações contratuais, autoavaliações e documentos de orientação do cliente produzidos pela Microsoft. Para obter informações de conformidade com o HDInsight, consulte o [Microsoft Trust Center](https://www.microsoft.com/trust-center) e a [visão geral da conformidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade partilhada

A imagem a seguir resume as principais áreas de segurança do sistema e as soluções de segurança que estão disponíveis para si em cada uma. Também destaca quais as áreas de segurança que são da sua responsabilidade enquanto cliente. E quais as áreas que são da responsabilidade da HDInsight como prestador de serviços.

![Diagrama de responsabilidades partilhadas HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

A tabela a seguir fornece ligações aos recursos para cada tipo de solução de segurança.

| Área de segurança | Soluções disponíveis | Parte responsável |
|---|---|---|
| Segurança de Acesso a Dados | Configure [as listas de controlo de acesso ACLs](../../storage/blobs/data-lake-storage-access-control.md) para Azure Data Lake Storage Gen1 e Gen2  | Cliente |
|  | Ativar a propriedade ["Secure transfer required"](../../storage/common/storage-require-secure-transfer.md) nas contas de armazenamento. | Cliente |
|  | Configure [Firewalls de armazenamento Azure](../../storage/common/storage-network-security.md) e redes virtuais | Cliente |
|  | Configurar [pontos finais de serviço de rede virtual Azure](../../virtual-network/virtual-network-service-endpoints-overview.md) para Cosmos DB e [Azure SQL DB](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) | Cliente |
|  | Certifique-se de que a [funcionalidade de encriptação em trânsito](./encryption-in-transit.md) está ativada para utilizar TLS e IPSec para comunicação intra-cluster. | Cliente |
|  | Configure [as chaves geridas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md) para encriptação de armazenamento Azure | Cliente |
|  | Controle o acesso aos seus dados através do suporte Azure utilizando [o cofre do Cliente](../../security/fundamentals/customer-lockbox-overview.md) | Cliente |
| Segurança de aplicação e middleware | Integre com AAD-DS e [Configure ESP](apache-domain-joined-configure-using-azure-adds.md) ou utilize [HIB para autenticação OAuth](identity-broker.md)| Cliente |
|  | Configure as políticas [de autorização do Apache Ranger](apache-domain-joined-run-hive.md) | Cliente |
|  | Utilize [registos do Monitor Azure](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Cliente |
| Segurança do sistema operativo | Criar clusters com a mais recente imagem de base segura | Cliente |
|  | Certifique-se [de que o SISTEMA remenda em](../hdinsight-os-patching.md) intervalos regulares | Cliente |
|  | Garantir [encriptação de disco CMK para VMs](../disk-encryption.md) | Cliente |
| Segurança da rede | Configurar uma [rede virtual](../hdinsight-plan-virtual-network-deployment.md) |
|  | Configurar [regras do grupo de segurança da rede de entrada (NSG)](../control-network-traffic.md) ou [ligação privada](../hdinsight-private-link.md) | Cliente |
|  | Configure [restrição de tráfego de saída](../hdinsight-restrict-outbound-traffic.md) com firewall | Cliente |
|  | Configurar [encriptação IPSec em trânsito](encryption-in-transit.md) entre nós de cluster | Cliente |
| Infraestrutura virtualizada | N/D | HDInsight (Fornecedor de nuvem) |
| Segurança da infraestrutura física | N/D | HDInsight (fornecedor de nuvem) |

## <a name="next-steps"></a>Passos seguintes

* [Plano para clusters HDInsight com ESP](apache-domain-joined-architecture.md)
* [Configurar clusters HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md)
* [Gerir clusters HDInsight com ESP](apache-domain-joined-manage.md)