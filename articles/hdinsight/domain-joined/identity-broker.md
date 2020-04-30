---
title: Utilize o ID Broker (pré-visualização) para gestão credencial- Azure HDInsight
description: Saiba mais sobre o HDInsight ID Broker para simplificar a autenticação para clusters Apache Hadoop unidos pelo domínio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1e7eaf49fb8b62259b8c619c89edffd629dfde7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685519"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Utilize o ID Broker (pré-visualização) para gestão da credencial

Este artigo descreve como configurar e utilizar a funcionalidade ID Broker no Azure HDInsight. Pode utilizar esta funcionalidade para iniciar sessão no Apache Ambari através da Autenticação Multi-Factor Azure e obter os bilhetes kerberos necessários sem precisar de hashes de senha nos Serviços de Domínio do Diretório Ativo azure (Azure AD DS).

## <a name="overview"></a>Descrição geral

O ID Broker simplifica configurações complexas de autenticação nos seguintes cenários:

* A sua organização conta com a federação para autenticar os utilizadores para aceder em recursos na nuvem. Anteriormente, para utilizar os clusters do Pacote de Segurança Empresarial HDInsight (ESP), teve de ativar a sincronização de hash de palavra-passe do seu ambiente no local para o Diretório Ativo Azure. Esta exigência pode ser difícil ou indesejável para algumas organizações.

* Está a construir soluções que utilizam tecnologias que dependem de diferentes mecanismos de autenticação. Por exemplo, Apache Hadoop e Apache Ranger dependem de Kerberos, enquanto o Armazenamento do Lago De Dados Azure depende da OAuth.

O ID Broker fornece uma infraestrutura de autenticação unificada e remove a exigência de sincronizar hashes de senha para O DS Azure. O ID Broker consiste em componentes em execução num VM do Windows Server (nó id broker), juntamente com nós de gateway de cluster. 

O diagrama seguinte mostra o fluxo de autenticação para todos os utilizadores, incluindo utilizadores federados, após a ativação do ID Broker:

![Fluxo de autenticação com corretor de ID](./media/identity-broker/identity-broker-architecture.png)

O ID Broker permite-lhe iniciar sessão nos clusters ESP utilizando a Autenticação Multi-Factor, sem fornecer quaisquer palavras-passe. Se já assinou outros serviços do Azure, como o portal Azure, pode iniciar sessão no seu cluster HDInsight com uma única experiência de inscrição (SSO).

## <a name="enable-hdinsight-id-broker"></a>Ativar o HDInsight ID Broker

Para criar um cluster ESP com id broker habilitado, tome os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Siga os passos básicos de criação para um cluster ESP. Para mais informações, consulte [Criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Selecione Ativar o corretor de **ID HDInsight**.

A função ID Broker adicionará um VM extra ao cluster. Este VM é o nó de ID Broker e inclui componentes do servidor para suportar a autenticação. O nó id broker é domínio unido ao domínio Azure AD DS.

![Opção para ativar corretor de ID](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager
Se adicionar uma nova `idbrokernode` função chamada com os seguintes atributos ao perfil computacional do seu modelo, então o cluster será criado com o nó de corretor de ID ativado:

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

O plug-in HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) é atualizado para suportar o OAuth. Pode utilizar este plug-in para se ligar ao cluster e apresentar trabalhos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acesso SSH sem senha hash em Azure AD DS

Depois de ativado o ID Broker, ainda vai precisar de um hash de senha armazenado em DS Azure AD para cenários SSH com contas de domínio. Para o SSH a um VM de `kinit` domínio, ou para executar o comando, precisa de fornecer uma senha. 

A autenticação SSH requer que o hash esteja disponível em Azure AD DS. Se quiser utilizar o SSH apenas para cenários administrativos, pode criar uma conta apenas na nuvem e usá-lo para SSH para o cluster. Outros utilizadores ainda podem utilizar ferramentas Ambari ou HDInsight (como o plug-in IntelliJ) sem ter o hash de senha disponível no Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Clientes que usam a OAuth para se conectarem ao gateway HDInsight com a configuração do ID Broker

Na configuração do corretor de ID, aplicações personalizadas e clientes que se conectam ao gateway podem ser atualizados para adquirir primeiro o token OAuth necessário. Pode seguir os passos deste [documento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) para adquirir o símbolo com as seguintes informações:

*   OAuth resource uri:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Permissão: (nome: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Passos seguintes

* [Configure um cluster HDInsight com pacote de segurança empresarial utilizando serviços de domínio de diretório ativo Azure](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorizar o desempenho do cluster](../hdinsight-key-scenarios-to-monitor.md)
