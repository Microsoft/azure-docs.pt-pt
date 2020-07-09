---
title: Use ID Broker (pré-visualização) para gestão credencial- Azure HDInsight
description: Saiba mais sobre o HDInsight ID Broker para simplificar a autenticação para clusters Apache Hadoop unidos pelo domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: ff7cb3c03edf9b421347815311796896caaffd70
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086607"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Utilizar o Corretor de ID (pré-visualização) para gestão credencial

Este artigo descreve como configurar e usar a função de Corretor de ID em Azure HDInsight. Pode utilizar esta funcionalidade para iniciar singing no Apache Ambari através da Autenticação Multi-Factor Azure e obter os bilhetes Kerberos necessários sem precisar de hashes de password nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS).

## <a name="overview"></a>Descrição geral

O ID Broker simplifica configurações complexas de autenticação nos seguintes cenários:

* A sua organização conta com a federação para autenticar os utilizadores para aceder a recursos na nuvem. Anteriormente, para utilizar clusters HDInsight Enterprise Security Package (ESP), tinha de ativar a sincronização de haxixe de palavra-passe do seu ambiente no local para o Azure Ative Directory. Esta exigência pode ser difícil ou indesejável para algumas organizações.

* Está a construir soluções que utilizam tecnologias que dependem de diferentes mecanismos de autenticação. Por exemplo, Apache Hadoop e Apache Ranger dependem de Kerberos, enquanto o Azure Data Lake Storage depende da OAuth.

O ID Broker fornece uma infraestrutura de autenticação unificada e remove a exigência de sincronização de hashes de palavra-passe para Azure AD DS. O ID Broker consiste em componentes em execução num VM do Servidor do Windows (nó de corretor de ID), juntamente com nós de gateway de cluster. 

O diagrama que se segue mostra o fluxo de autenticação para todos os utilizadores, incluindo os utilizadores federados, após a ativação do Corretor de ID:

![Fluxo de autenticação com corretor de ID](./media/identity-broker/identity-broker-architecture.png)

O ID Broker permite-lhe iniciar sedutação nos clusters ESP utilizando a Autenticação Multi-Factor, sem fornecer quaisquer palavras-passe. Se já assinou contrato com outros serviços Azure, como o portal Azure, pode iniciar sômposição no seu cluster HDInsight com uma única experiência de sso de assinatura.

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

O plug-in HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) é atualizado para suportar o OAuth. Pode utilizar este plug-in para ligar ao cluster e apresentar empregos.

Também pode usar [Ferramentas de Colmeia Spark & para código VS](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) para alavancar o caderno e submeter empregos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem um hash de senha em Azure AD DS

Depois de o ID Broker estar ativado, ainda vai precisar de um hash de palavra-passe armazenado em Azure AD DS para cenários SSH com contas de domínio. Para o SSH a um VM de domínio ou para executar o `kinit` comando, precisa fornecer uma palavra-passe. 

A autenticação SSH requer que o haxixe esteja disponível em Azure AD DS. Se quiser usar o SSH apenas para cenários administrativos, pode criar uma conta apenas na nuvem e usá-la para SSH no cluster. Outros utilizadores ainda podem utilizar ferramentas Ambari ou HDInsight (como o plug-in IntelliJ) sem ter o hash de senha disponível em Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Clientes que usam OAuth para ligar ao gateway HDInsight com a configuração do Corretor de ID

Na configuração do corretor de ID, as aplicações personalizadas e os clientes que se conectam ao gateway podem ser atualizados para adquirir primeiro o token OAuth necessário. Pode seguir os passos deste [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) para adquirir o token com as seguintes informações:

*   OAu uri recurso uri:`https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Próximos passos

* [Configure um cluster HDInsight com pacote de segurança empresarial utilizando os serviços de domínio do diretório ativo Azure](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorizar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
