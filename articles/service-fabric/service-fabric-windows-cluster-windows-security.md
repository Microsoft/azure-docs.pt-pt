---
title: Proteger um cluster em execução no Windows usando a segurança do Windows
description: Saiba como configurar a segurança de nó para nó e de cliente para nó em um cluster autônomo em execução no Windows usando a segurança do Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774450"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autônomo no Windows usando a segurança do Windows
Para impedir o acesso não autorizado a um Cluster Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança de nó para nó e de cliente para nó usando a segurança do Windows no arquivo *ClusterConfig. JSON* .  O processo corresponde à etapa configurar segurança de [criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como Service Fabric usa a segurança do Windows, consulte [cenários de segurança de cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Você deve considerar a seleção de segurança de nó para nó com cuidado porque não há nenhuma atualização de cluster de uma opção de segurança para outra. Para alterar a seleção de segurança, você precisa recompilar o cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows usando o gMSA  
O arquivo de configuração *ClusterConfig. gMSA. Windows. Multimachine. JSON* de exemplo baixado com o pacote de cluster autônomo [Microsoft. Azure. WindowsServer.\<versão >. zip](https://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para configurar a segurança do Windows usando a [conta de serviço gerenciado de grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Definição de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Defina como *Windows* para habilitar a segurança do Windows para comunicação nó a nó.  | 
| ServerCredentialType |Defina como *Windows* para habilitar a segurança do Windows para comunicação de cliente/nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClustergMSAIdentity |Configura a segurança de nó para nó. Uma conta de serviço gerenciado de grupo. |
| ClusterSPN |SPN registrado para a conta gMSA|
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário cliente. |
| Identidade |Adicione o usuário Domain, domínio \ nome_do_usuário, para a identidade do cliente. |
| IsAdmin |Defina como true para especificar que o usuário de domínio tenha acesso de cliente de administrador ou false para acesso de cliente de usuário. |

> [!NOTE]
> O valor clustergMSAIdentidade deve estar em formato "mysfgmsa@mydomain".

[A segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security) é configurada definindo **ClustergMSAIdentity** quando o Service Fabric precisa ser executado em gMSA. Para criar relações de confiança entre nós, eles devem estar cientes uns dos outros. Isso pode ser feito de duas maneiras diferentes: Especifique a conta de serviço gerenciado de grupo que inclui todos os nós no cluster ou especifique o grupo de computadores de domínio que inclui todos os nós no cluster. É altamente recomendável usar a abordagem [gMSA (conta de serviço gerenciado de grupo)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clusters maiores (mais de 10 nós) ou para clusters que provavelmente aumentarão ou diminuirão.  
Essa abordagem não requer a criação de um grupo de domínio para o qual os administradores de cluster receberam direitos de acesso para adicionar e remover membros. Essas contas também são úteis para o gerenciamento automático de senhas. Para obter mais informações, consulte [introdução com contas de serviço gerenciado de grupo](https://technet.microsoft.com/library/jj128431.aspx).  
 
[A segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deve configurar o cluster para saber quais identidades de cliente ele pode confiar. Isso pode ser feito de duas maneiras diferentes: Especifique os usuários do grupo de domínio que podem se conectar ou especificar os usuários do nó de domínio que podem se conectar. O Service Fabric dá suporte a dois tipos diferentes de controle de acesso para clientes que estão conectados a um Cluster Service Fabric: administrador e usuário. O controle de acesso fornece a capacidade de o administrador de cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços. Para obter mais informações sobre controles de acesso, consulte [controle de acesso baseado em função para clientes Service Fabric](service-fabric-cluster-security-roles.md).  
 
A seção de **segurança** de exemplo a seguir configura a segurança do Windows usando gMSA e especifica que as máquinas no *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e que *CONTOSO\usera* tem acesso de cliente de administrador:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configurar a segurança do Windows usando um grupo de computadores  
Este modelo está sendo preterido. A recomendação é usar o gMSA conforme detalhado acima. O arquivo de configuração *ClusterConfig. Windows. Multimachine. JSON* de exemplo baixado com o pacote de cluster autônomo [Microsoft. Azure. WindowsServer.\<versão >. zip](https://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para configurar a segurança do Windows.  A segurança do Windows está configurada na seção **Propriedades** : 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Definição de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Defina como *Windows* para habilitar a segurança do Windows para comunicação nó a nó.  |
| ServerCredentialType |Defina como *Windows* para habilitar a segurança do Windows para comunicação de cliente/nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClusterIdentity |Use um nome de grupo de computadores, domain\machinegroup, para configurar a segurança de nó para nó. |
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário cliente. |  
| Identidade |Adicione o usuário Domain, domínio \ nome_do_usuário, para a identidade do cliente. |  
| IsAdmin |Defina como true para especificar que o usuário de domínio tenha acesso de cliente de administrador ou false para acesso de cliente de usuário. |  

[A segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security) é definida por meio da configuração usando **ClusterIdentity** se você quiser usar um grupo de computadores dentro de um domínio do Active Directory. Para obter mais informações, consulte [criar um grupo de computadores em Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[A segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deve configurar o cluster para saber as identidades de cliente nas quais o cluster pode confiar. Você pode estabelecer confiança de duas maneiras diferentes:

- Especifique os usuários do grupo de domínio que podem se conectar.
- Especifique os usuários do nó de domínio que podem se conectar.

O Service Fabric dá suporte a dois tipos diferentes de controle de acesso para clientes que estão conectados a um Cluster Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinados tipos de operações de cluster para diferentes grupos de usuários, o que torna o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.  

A seção de **segurança** de exemplo a seguir configura a segurança do Windows, especifica que as máquinas em *imfabric/ClusterA. contoso. com* fazem parte do cluster e especifica que *CONTOSO\usera* tem acesso de cliente de administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric não deve ser implantado em um controlador de domínio. Verifique se o ClusterConfig. JSON não inclui o endereço IP do controlador de domínio ao usar um grupo de computadores ou uma conta de serviço gerenciado (gMSA).
>
>

## <a name="next-steps"></a>Passos seguintes
Depois de configurar a segurança do Windows no arquivo *ClusterConfig. JSON* , retome o processo de criação do cluster em [criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como a segurança de nó para nó, a segurança de cliente para nó e o controle de acesso baseado em função, consulte [cenários de segurança de cluster](service-fabric-cluster-security.md).

Consulte [conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de conexão usando o PowerShell ou o FabricClient.
