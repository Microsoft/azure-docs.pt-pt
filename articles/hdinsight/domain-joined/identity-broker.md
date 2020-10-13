---
title: Use ID Broker (pré-visualização) para gestão credencial- Azure HDInsight
description: Saiba mais sobre o HDInsight ID Broker para simplificar a autenticação para clusters Apache Hadoop unidos pelo domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 24f15b8a4d5a5afd3a2794fe686d3acb0036cdd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565331"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (pré-visualização)

Este artigo descreve como configurar e usar a funcionalidade HDInsight ID Broker (HIB) em Azure HDInsight. Pode utilizar esta funcionalidade para obter a autenticação moderna da OAuth para o Apache Ambari, ao mesmo tempo que tem a aplicação de autenticação multi-factor (MFA) sem precisar de hashes de senha legado nos Serviços de Domínio do Diretório Ativo (AAD-DS).

## <a name="overview"></a>Descrição geral

O HIB simplifica configurações complexas de autenticação nos seguintes cenários:

* A sua organização conta com a federação para autenticar os utilizadores para aceder a recursos na nuvem. Anteriormente, para utilizar clusters HDInsight Enterprise Security Package (ESP), tinha de ativar a sincronização de haxixe de palavra-passe do seu ambiente no local para o Azure Ative Directory (Azure AD). Esta exigência pode ser difícil ou indesejável para algumas organizações.

* A sua organização gostaria de impor o MFA para acesso baseado na web/HTTP ao Apache Ambari e outros recursos de cluster.

A HIB fornece a infraestrutura de autenticação que permite a transição protocolar de OAuth (moderno) para Kerberos (legado) sem necessidade de sincronizar as hashes de palavra-passe para AAD-DS. Esta infraestrutura consiste em componentes em execução num VM do Servidor do Windows (nó de ID Broker), juntamente com nós de gateway de cluster.

Utilize a seguinte tabela para determinar a melhor opção de autenticação com base nas necessidades da sua organização:

|Opções de autenticação |Configuração HDInsight | Fatores a ter em conta |
|---|---|---|
| Totalmente OAuth | ESP + HIB | 1. Opção mais segura (MFA é suportado) 2.    Não é necessária sincronização de hash de passe. 3.  Sem acesso a ssh/kinit/keytab para contas on-prem, que não têm hash de senha em AAD-DS. 4.   As contas de nuvem só podem ser ssh/kinit/keytab. 5. Acesso à Ambari através da Oauth 6.  Requer a atualização de aplicações antigas (JDBC/ODBC, etc.) para apoiar o OAuth.|
| OAuth + Auth Básico | ESP + HIB | 1. Acesso à Ambari através da Oauth 2. As aplicações antigas continuam a usar auth. 3 básico. O MFA deve ser desativado para o acesso básico. 4. Não é necessária sincronização de hash de passe. 5. Sem acesso a ssh/kinit/keytab para contas on-prem, que não têm hash de senha em AAD-DS. 6. As contas de nuvem só podem ser ssh/kinit. |
| Auth totalmente básico | ESP | 1. Mais semelhante às configurações on-prem. 2. É necessária sincronização de haxixe de palavra-passe para AAD-DS. 3. As contas on-prem podem ser ssh/kinit ou usar keytab. 4. O MFA deve ser desativado se o armazenamento de suporte for ADLS Gen2 |

O seguinte diagrama mostra o fluxo moderno de autenticação baseada em OAuth para todos os utilizadores, incluindo utilizadores federados, após o ID Broker ser ativado:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Fluxo de autenticação com corretor de ID":::

Neste diagrama, o cliente (ou seja, navegador ou apps) precisa adquirir primeiro o token OAuth e, em seguida, apresentar o token para gateway num pedido HTTP. Se já assinou contrato com outros serviços Azure, como o portal Azure, pode iniciar sômposição no seu cluster HDInsight com uma única experiência de sso de assinatura.

Ainda pode haver muitas aplicações antigas que apenas suportam a autenticação básica (isto é, nome de utilizador/senha). Para estes cenários, ainda pode utilizar a autenticação básica HTTP para ligar aos gateways de cluster. Nesta configuração, deve garantir a conectividade da rede desde os nós de gateway até ao ponto final da federação (ponto final AD FS) para garantir uma linha de visão direta a partir dos nós de gateway. 

O diagrama seguinte mostra o fluxo básico de autenticação para os utilizadores federados. Em primeiro lugar, o gateway tenta completar a autenticação utilizando o [fluxo ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) e no caso de não haver hashes de senha sincronizadas com Azure AD, depois volta a descobrir o ponto final AD FS e completar a autenticação acedendo ao ponto final AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Fluxo de autenticação com corretor de ID":::


## <a name="enable-hdinsight-id-broker"></a>Ativar o corretor de ID HDInsight

Para criar um cluster ESP com corretor de ID habilitado, tome os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Siga os passos básicos de criação para um cluster ESP. Para obter mais informações, consulte [Criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione **Ative HdInsight ID Broker**.

A função ID Broker adicionará um VM extra ao cluster. Este VM é o nó de Corretor de ID e inclui componentes do servidor para suportar a autenticação. O nó de corretor de ID é domínio associado ao domínio Azure AD DS.

![Opção para ativar o Corretor de ID](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager
Se adicionar uma nova função chamada `idbrokernode` com os seguintes atributos ao perfil de computação do seu modelo, então o cluster será criado com o nó de corretor de ID ativado:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Integração de ferramentas

As ferramentas HDIsngith são atualizadas para suportar o OAuth de forma nativa. Recomendamos vivamente a utilização destas ferramentas para o acesso moderno baseado em OAuth aos clusters. O plug-in HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) pode ser utilizado para aplicações baseadas em JAVA, como o Scala. [As ferramentas de & de faíscas para o código VS](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) podem ser utilizadas em trabalhos de PySpark e Hive. Apoiam tanto os trabalhos em lote como os trabalhos interativos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha em Azure AD DS

|Opções SSH |Fatores a ter em conta |
|---|---|
| Conta VM local (por exemplo, sshuser) | 1. Forneceu esta conta na hora da criação do cluster. 2.  Não há authication kerberos para esta conta |
| Conta Cloud Only (por alice@contoso.onmicrosoft.com exemplo) | 1. O hash da palavra-passe está disponível em AAD-DS 2. A autenticação de Kerberos é possível através de kerberos SSH |
| Conta on-prem (por alice@contoso.com exemplo) | 1. A autenticação da SSH Kerberos só é possível se o hash de palavra-passe estiver disponível em AAD-DS, caso contrário, este utilizador não pode SSH para o cluster |

Para o SSH a um VM de domínio ou para executar o `kinit` comando, precisa fornecer uma palavra-passe. A autenticação da SSH Kerberos requer que o haxixe esteja disponível em AAD-DS. Se quiser usar o SSH apenas para cenários administrativos, pode criar uma conta apenas na nuvem e usá-la para SSH no cluster. Outros utilizadores on-prem ainda podem usar ferramentas Ambari ou HDInsight ou auth básico HTTP sem ter o hash de senha disponível em AAD-DS.

Se a sua organização não estiver a sincronizar as hashes de palavra-passe para a AAD-DS, como uma boa prática, crie um único utilizador em nuvem no Azure AD e atribua-o como administrador de cluster ao criar o cluster e use-o para fins administrativos, o que inclui o acesso à raiz aos VMs via SSH.

Para resolver problemas de autenticação, consulte este [guia.](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Clientes que usam OAuth para ligar ao gateway HDInsight com HIB

Na configuração HIB, as aplicações personalizadas e os clientes que se conectam ao gateway podem ser atualizados para adquirir primeiro o token OAuth necessário. Pode seguir os passos deste [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) para adquirir o token com as seguintes informações:

*   OAu uri recurso uri: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Depois de adquirir o token OAuth, pode usá-lo no cabeçalho de autorização do pedido HTTP para o gateway de cluster (por exemplo, https:// <clustername> -int.azurehdinsight.net). Por exemplo, um comando de caracóis de amostra para Apache Livy API pode ser assim:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Passos seguintes

* [Configure um cluster HDInsight com pacote de segurança empresarial utilizando os serviços de domínio do diretório ativo Azure](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorizar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
