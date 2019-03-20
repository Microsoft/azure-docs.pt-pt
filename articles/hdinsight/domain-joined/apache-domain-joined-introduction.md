---
title: Uma introdução à segurança do Apache Hadoop com o Enterprise Security Package
description: Saiba como o Enterprise Security Package suporta os quatro pilares da segurança corporativa.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f3d9a2447bdbc2a1a5ce930ffa161d5a9e30069b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225064"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Uma introdução à segurança do Apache Hadoop com o Enterprise Security Package

No passado, o Azure HDInsight suportado apenas um único utilizador: administrador local. Isto resultava muito bem para equipas ou departamentos de aplicações mais pequenos. Como cargas de trabalho baseadas em Apache Hadoop ganhavam mais popularidade no setor empresarial, a necessidade para suportam capacidades de nível empresarial, como a autenticação baseada no Active Directory, multiusuário e controlo de acesso baseado em funções se tornou cada vez mais importante. 

Pode criar um cluster do HDInsight com o pacote de segurança empresarial (ESP), que está associado a um domínio do Active Directory. Em seguida, pode configurar uma lista de funcionários da empresa que podem autenticar através do Azure Active Directory para iniciar sessão para o cluster do HDInsight. Ninguém de fora da empresa pode iniciar sessão ou aceder ao cluster do HDInsight. 

O administrador da empresa, pode configurar o controlo de acesso baseado em funções (RBAC) para o Apache Hive security utilizando [Apache Ranger](https://hortonworks.com/apache/ranger/). Configurar o RBAC restringe o acesso a dados a apenas o que é necessário. Por fim, o administrador pode auditar o acesso a dados por funcionários e quaisquer alterações efetuadas às políticas de controlo de acesso. O administrador, em seguida, pode obter um alto grau de governação de recursos da sua empresa.

> [!NOTE]  
> Apache Oozie está agora ativada nos clusters do ESP. Para aceder à IU da web de Oozie, devem ativar os utilizadores [túnel](../hdinsight-linux-ambari-ssh-tunnel.md).

Segurança empresarial contém quatro pilares principais: segurança de perímetro, autenticação, autorização e encriptação.

![Vantagens de clusters do HDInsight de pacote de segurança empresarial nos quatro pilares da segurança empresarial](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Segurança de perímetro
Segurança de perímetro no HDInsight é alcançada através de redes virtuais e o serviço de Gateway de VPN do Azure. Um administrador empresarial pode criar um cluster do ESP numa rede virtual e utilizar grupos de segurança de rede (regras de firewall) para restringir o acesso à rede virtual. Apenas os endereços IP definidos nas regras de firewall de entrada será capazes de comunicar com o cluster do HDInsight. Esta configuração fornece segurança de perímetro.

Outra camada de segurança de perímetro é obtida através do serviço de Gateway de VPN. O gateway funciona como primeira linha de defesa para qualquer pedido de entrada para o cluster do HDInsight. Aceita o pedido, valida- e apenas, em seguida, permite que o pedido passar para outros nós no cluster. Dessa forma, o gateway fornece segurança de perímetro para outros nós de dados e o nome do cluster.

## <a name="authentication"></a>Authentication
Um administrador empresarial pode criar um cluster do HDInsight com ESP num [rede virtual](https://azure.microsoft.com/services/virtual-network/). Todos os nós do cluster do HDInsight são associados ao domínio gerido pela empresa. Isto é conseguido através da utilização de [do Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Com esta configuração, empregados da empresa podem iniciar sessão nós do cluster através das respetivas credenciais de domínio. Também podem utilizar as credenciais de domínio para autenticar noutros pontos finais aprovados, como o Apache Ambari Views, ODBC, JDBC, PowerShell e REST APIs para interagir com o cluster. O administrador tem controlo total sobre a limitar o número de utilizadores que interagem com o cluster através destes pontos finais.

## <a name="authorization"></a>Autorização
Uma prática recomendada que se seguem a maioria das empresas é garantir que nem todos os empregados têm acesso a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controlo de acesso baseado em funções para os recursos do cluster. 

Por exemplo, o administrador pode configurar o [Apache Ranger](https://hortonworks.com/apache/ranger/) para definir políticas de controlo de acesso para o Hive. Esta funcionalidade garante que os empregados podem aceder apenas quantidade de dados que precisam para ter êxito nas suas tarefas. Acesso SSH ao cluster também está limitado a apenas o administrador.

## <a name="auditing"></a>Auditoria
A auditoria de acesso de todos os recursos de cluster e os dados, é necessário para controlar o acesso não autorizado ou não intencional dos recursos. É tão importante quanto a proteger os recursos de cluster do HDInsight contra utilizadores não autorizados e proteção dos dados. 

O administrador pode ver e comunicar todo o acesso aos dados e recursos de cluster do HDInsight. O administrador também pode ver e comunicar todas as alterações para as políticas de controlo de acesso criadas nos pontos finais suportados do Ranger Apache. 

Um cluster do HDInsight com ESP usa a IU familiar do Ranger Apache para procurar registos de auditoria. No back-end, o Ranger utiliza [Apache Solr](https://hortonworks.com/apache/solr/) para armazenar e procurar os registos.

## <a name="encryption"></a>Encriptação
Proteção de dados é importante para os requisitos de segurança e conformidade organizacionais no reunião. Além de restringir o acesso aos dados de empregados não autorizados, deve criptografá-lo. 

Ambos os arquivos de dados para os clusters do HDInsight – armazenamento de Blobs do Azure e Azure Data Lake Storage Gen1/Gen2 – suporte transparente lado do servidor [encriptação de dados](../../storage/common/storage-service-encryption.md) inativos. Clusters seguros do HDInsight funcionará sem problemas com esta capacidade de encriptação do lado do servidor de dados em repouso.

## <a name="next-steps"></a>Passos Seguintes

* [Planear clusters do HDInsight com o ESP](apache-domain-joined-architecture.md)
* [Configurar clusters do HDInsight com ESP](apache-domain-joined-configure.md)
* [Gerir clusters do HDInsight com o ESP](apache-domain-joined-manage.md)
* [Configurar políticas do Apache Hive para clusters do HDInsight com o ESP](apache-domain-joined-run-hive.md)
* [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

