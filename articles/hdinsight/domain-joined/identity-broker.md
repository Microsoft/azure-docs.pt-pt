---
title: Corretor de ID Azure HDInsight (HIB)
description: Saiba mais sobre o Azure HDInsight ID Broker para simplificar a autenticação para clusters Apache Hadoop unidos pelo domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: c6bc5ca748a35b17c61d314e96f7284d30e7fc3b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338134"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Corretor de ID Azure HDInsight (HIB)

Este artigo descreve como configurar e usar a função Azure HDInsight ID Broker. Você pode usar esta funcionalidade para obter a autenticação moderna da OAuth para Apache Ambari enquanto tem aplicação de autenticação multifactor sem precisar de hashes de senha legado em Azure Ative Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Descrição geral

HdInsight ID Broker simplifica configurações de autenticação complexas nos seguintes cenários:

* A sua organização conta com a federação para autenticar os utilizadores para aceder a recursos na nuvem. Anteriormente, para utilizar clusters hdInsight Enterprise Security Package, tinha de ativar a sincronização de haxixe de palavra-passe do seu ambiente no local para o Azure Ative Directory (Azure AD). Esta exigência pode ser difícil ou indesejável para algumas organizações.
* A sua organização quer impor a autenticação multifactor para acesso baseado na Web ou HTTP ao Apache Ambari e outros recursos de cluster.

O HDInsight ID Broker fornece a infraestrutura de autenticação que permite a transição protocolar de OAuth (moderno) para Kerberos (legado) sem necessidade de sincronizar as hashes de palavra-passe para Azure AD DS. Esta infraestrutura consiste em componentes em execução numa máquina virtual do Windows Server (VM) com o nó hdInsight ID Broker ativado, juntamente com nós de gateway de cluster.

Utilize a seguinte tabela para determinar a melhor opção de autenticação com base nas necessidades da sua organização.

|Opções de autenticação |Configuração HDInsight | Fatores a ter em conta |
|---|---|---|
| Totalmente OAuth | Pacote de segurança da empresa + corretor de ID HDInsight | Opção mais segura. (A autenticação multifactor é suportada.) Não *é* necessária sincronização de hash de passe. Sem acesso ssh/kinit/keytab para contas no local, que não têm hash de senha em Azure AD DS. As contas só em nuvem ainda podem ser ssh/kinit/keytab. Acesso baseado na Web a Ambari através da OAuth. Requer a atualização de aplicações antigas (por exemplo, JDBC/ODBC) para suportar o OAuth.|
| OAuth + Auth Básico | Pacote de segurança da empresa + corretor de ID HDInsight | Acesso baseado na Web a Ambari através da OAuth. As aplicações antigas continuam a usar auth básica. A autenticação multifactor deve ser desativada para acesso básico. Não *é* necessária sincronização de hash de passe. Sem acesso ssh/kinit/keytab para contas no local, que não têm hash de senha em Azure AD DS. As contas só em nuvem ainda podem ser ssh/kinit. |
| Auth totalmente básico | Pacote de Segurança Enterprise | Mais semelhante às instalações no local. É necessária sincronização de haxixe de palavra-passe para Azure AD DS. As contas no local podem ser ssh/kinit ou usar keytab. A autenticação multifactor deve ser desativada se o armazenamento de suporte for Azure Data Lake Storage Gen2. |

O diagrama a seguir mostra o fluxo moderno de autenticação baseado em OAuth para todos os utilizadores, incluindo utilizadores federados, após a ativação do HdInsight ID Broker:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagrama que mostra fluxo de autenticação com corretor de ID HDInsight.":::

Neste diagrama, o cliente (isto é, um browser ou app) precisa adquirir primeiro o token OAuth. Em seguida, apresenta o símbolo ao portal num pedido HTTP. Se já assinou contrato com outros serviços Azure, como o portal Azure, pode iniciar sômposição no seu cluster HDInsight com uma única experiência de inscrição.

Ainda pode haver muitas aplicações antigas que apenas suportam a autenticação básica (isto é, nome de utilizador e senha). Para estes cenários, ainda pode utilizar a autenticação básica HTTP para ligar aos gateways de cluster. Nesta configuração, deve garantir a conectividade da rede desde os nós de gateway até ao ponto final dos Serviços da Federação de Diretório Ativo (AD FS) para garantir uma linha de visão direta a partir de nós de gateway.

O diagrama seguinte mostra o fluxo básico de autenticação para os utilizadores federados. Em primeiro lugar, o gateway tenta completar a autenticação utilizando o [fluxo ROPC](../../active-directory/develop/v2-oauth-ropc.md). Caso não existam hashes de palavra-passe sincronizados com a Azure AD, volta a descobrir o ponto final AD FS e completa a autenticação acedendo ao ponto final da AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagrama que mostra arquitetura com autenticação básica.":::


## <a name="enable-hdinsight-id-broker"></a>Ativar o corretor de ID HDInsight

Para criar um cluster de pacotes de segurança empresarial com corretor de ID HDInsight habilitado:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Siga os passos básicos de criação para um cluster de pacotes de segurança da empresa. Para obter mais informações, consulte [Criar um cluster HDInsight com Pacote de Segurança Empresarial.](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)
1. Selecione **Ative HdInsight ID Broker**.

A função hdInsight ID Broker adiciona um VM extra ao cluster. Este VM é o nó de corretor de id de id de aparência HD, e inclui componentes do servidor para suportar a autenticação. O nó de corretor de ID HDInsight é um domínio associado ao domínio Azure AD DS.

![Diagrama que mostra a opção para ativar o HdInsight ID Broker.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Use Azure Resource Manager templates (Utilizar modelos do Azure Resource Manager)

Se adicionar uma nova função chamada `idbrokernode` com os seguintes atributos ao perfil de computação do seu modelo, o cluster será criado com o nó de Corretor de ID HDInsight ativado:

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
            "targetInstanceCount": 2,
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

Para ver uma amostra completa de um modelo ARM, consulte o modelo publicado [aqui.](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)


## <a name="tool-integration"></a>Integração de ferramentas

As ferramentas HDInsight são atualizadas para suportar de forma nativa o OAuth. Use estas ferramentas para o acesso moderno baseado em OAuth aos clusters. O plug-in HDInsight [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) pode ser utilizado para aplicações baseadas em Java, como o Scala. [As ferramentas de faísca e colmeia para código de estúdio visual](../hdinsight-for-vscode.md) podem ser usadas para trabalhos PySpark e Hive. As ferramentas suportam trabalhos de lote e interativos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha em Azure AD DS

|Opções SSH |Fatores a ter em conta |
|---|---|
| Conta VM local (por exemplo, sshuser) | Forneceu esta conta na hora da criação do cluster. Não há autenticação kerberos para esta conta. |
| Conta apenas na nuvem (por exemplo, alice@contoso.onmicrosoft.com ) | O hash de senha está disponível em Azure AD DS. A autenticação kerberos é possível através da SSH Kerberos. |
| Conta no local (por alice@contoso.com exemplo,) | A autenticação SSH Kerberos só é possível se um hash de senha estiver disponível em Azure AD DS. Caso contrário, este utilizador não pode SSH para o cluster. |

Para o SSH a um VM de domínio ou para executar o `kinit` comando, tem de fornecer uma palavra-passe. A autenticação SSH Kerberos requer que o haxixe esteja disponível em Azure AD DS. Se quiser usar o SSH apenas para cenários administrativos, pode criar uma conta apenas na nuvem e usá-la para SSH para o cluster. Outros utilizadores no local ainda podem usar ferramentas Ambari ou HDInsight ou auth básico HTTP sem ter o hash de senha disponível em Azure AD DS.

Se a sua organização não sincronizar as hashes de password para Azure AD DS, como uma boa prática, crie um utilizador apenas em nuvem em Azure AD. Em seguida, atribua-o como administrador de cluster quando criar o cluster, e use-o para fins de administração. Pode usá-lo para obter acesso à raiz aos VMs via SSH.

Para resolver problemas de autenticação, consulte [este guia](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Clientes que usam OAuth para ligar a uma porta de entrada HDInsight com hdInsight ID Broker

Na configuração do HDInsight ID Broker, as aplicações personalizadas e os clientes que se conectam ao gateway podem ser atualizados para adquirir primeiro o token OAuth necessário. Siga os passos [neste documento](../../storage/common/storage-auth-aad-app.md) para adquirir o token com as seguintes informações:

*   OAu uri recurso uri: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Depois de adquirir o token OAuth, utilize-o no cabeçalho de autorização do pedido HTTP para o gateway de cluster (por exemplo, https:// <clustername> -int.azurehdinsight.net). Um comando de caracóis de amostra para Apache Livy API pode parecer este exemplo:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Para a utilização da Beeline e da Livy, também pode seguir os códigos de amostras fornecidos [aqui](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) para configurar o seu cliente para usar o OAuth e ligar-se ao cluster.

## <a name="faq"></a>FAQ
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Que aplicação é criada pela HDInsight em AAD?
Para cada cluster, uma aplicação de terceiros será registada em AAD com o cluster uri como o identifierUri `https://clustername.azurehdinsight.net` (como).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Por que os utilizadores são solicitados a pedir consentimento antes de usar clusters hib habilitados?
No AAD, é necessário o consentimento para todas as aplicações de terceiros antes de poder autenticar os utilizadores ou aceder aos dados.

### <a name="can-the-consent-be-approved-programatically"></a>O consentimento pode ser aprovado de forma programaticamente?
Microsoft Graph api permite automatizar o consentimento, ver a documentação da [API](/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0) A sequência para automatizar o consentimento é:

* Registe uma aplicação e conceda a Aplicação.ReadWrite.Todas as permissões à aplicação, para aceder ao Microsoft Graph
* Após a criação de um cluster, consulta para a app cluster com base no identificador uri
* Registar consentimento para a app

Quando o cluster é eliminado, o HDInsight elimina a aplicação e não há necessidade de limpar qualquer consentimento.

 


## <a name="next-steps"></a>Passos seguintes

* [Configure um cluster HDInsight com pacote de segurança empresarial utilizando os serviços de domínio do diretório ativo Azure](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorizar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)