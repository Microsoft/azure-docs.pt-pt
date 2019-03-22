---
title: Proteger um cluster em execução no Windows através de segurança do Windows | Documentos da Microsoft
description: Saiba como configurar a segurança de nó para nó e o nó de cliente num cluster autónomo no Windows através de segurança do Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 681ee66ca165ece170dd2a2ce2736cf55a44f1f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104085"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autónomo no Windows com segurança do Windows
Para impedir acesso não autorizado ao cluster do Service Fabric, tem de proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança de nó para nó e o nó de cliente através de segurança do Windows no *ClusterConfig.JSON* ficheiro.  O processo correspondente para o passo de segurança de configuração do [criar um cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como o Service Fabric utiliza a segurança do Windows, consulte [cenários de segurança do Cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Deve considerar cuidadosamente a seleção de segurança de nó para nó porque não existe nenhuma atualização de cluster da opção de segurança para outro. Para alterar a seleção de segurança, terá de recriar o cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows utilizam a gMSA  
O exemplo *ClusterConfig.gMSA.Windows.MultiMachine.JSON* ficheiro de configuração transferido com o [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. Zip](https://go.microsoft.com/fwlink/?LinkId=730690) pacote de cluster autónomo contém um modelo para a configuração através de segurança do Windows [conta de serviço gerida da grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Defina como *Windows* para ativar a segurança do Windows para comunicação entre nós.  | 
| ServerCredentialType |Defina como *Windows* para ativar a segurança do Windows para a comunicação de nó de cliente. |
| WindowsIdentities |Contém as identidades de cliente e de cluster. |
| ClustergMSAIdentity |Configura a segurança de nó para nó. Um grupo de conta de serviço gerida. |
| ClusterSPN |SPN registrado para a conta gMSA|
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de utilizador do cliente. |
| Identidade |Adicione o utilizador de domínio, domínio \ nomedeutilizador, para a identidade do cliente. |
| IsAdmin |Defina como verdadeiro para especificar que o utilizador de domínio tem acesso de cliente de administrador ou FALSO para acesso de cliente do utilizador. |

> [!NOTE]
> Valor de ClustergMSAIdentity não pode incluir o nome de domínio e só pode ser o nome de conta de serviço gerida de grupo. I.E. "mysfgmsa" está correto e ambos "mydomain / / mysfgmsa" ou "mysfgmsa@mydomain" são inválidas; como o domínio é exigido pelo computador anfitrião.

[O nó para segurança de nó](service-fabric-cluster-security.md#node-to-node-security) está configurado, definindo **ClustergMSAIdentity** quando o service fabric tem de ser executado sob a gMSA. Para criar relações de confiança entre os nós, eles devem ser conscientizados de entre si. Isso pode ser feito de duas formas diferentes: Especificar o grupo de conta de serviço gerida que inclui todos os nós no cluster ou o grupo de máquina de domínio que inclui todos os nós do cluster. Recomendamos vivamente a utilização a [conta de serviço gerida da grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) abordagem, especialmente para clusters maiores (mais de 10 nós) ou para os clusters que são provável que aumentar ou diminuir.  
Essa abordagem não requer a criação de um grupo de domínio para o qual os administradores de cluster concedeu direitos de acesso para adicionar e remover membros. Estas contas também são úteis para gestão de palavra-passe automática. Para obter mais informações, consulte [guia de introdução contas de serviço geridas de grupo](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Cliente para segurança de nó](service-fabric-cluster-security.md#client-to-node-security) é configurado usando **ClientIdentities**. Para estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster para saber qual cliente identidades que pode confiar. Isso pode ser feito de duas formas diferentes: Especifique os utilizadores do grupo de domínio que podem ligar ou especificar os utilizadores de nó de domínio que podem ligar-se. Service Fabric suporta dois tipos de controle de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e usuário. Controlo de acesso fornece a capacidade para o administrador de cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de usuários, fazendo com que o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo recursos de leitura/gravação). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços. Para obter mais informações sobre controlos de acesso, consulte [baseado em funções controlo de acesso para clientes do Service Fabric](service-fabric-cluster-security-roles.md).  
 
O exemplo a seguir **security** secção configura a segurança do Windows utilizam a gMSA e especifica que as máquinas na *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e esse  *CONTOSO\usera* tem acesso de cliente do administrador:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configurar a segurança do Windows através de um grupo de máquina  
Esse modelo está a ser preterido. A recomendação é usar a gMSA conforme detalhado acima. O exemplo *ClusterConfig.Windows.MultiMachine.JSON* ficheiro de configuração transferido com o [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. Zip](https://go.microsoft.com/fwlink/?LinkId=730690) pacote de cluster autónomo contém um modelo para configurar a segurança do Windows.  Segurança do Windows está configurada no **propriedades** secção: 

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
| ClusterCredentialType |Defina como *Windows* para ativar a segurança do Windows para comunicação entre nós.  |
| ServerCredentialType |Defina como *Windows* para ativar a segurança do Windows para a comunicação de nó de cliente. |
| WindowsIdentities |Contém as identidades de cliente e de cluster. |
| ClusterIdentity |Utilize um nome de grupo do computador, domain\machinegroup, para configurar a segurança de nó para nó. |
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de utilizador do cliente. |  
| Identidade |Adicione o utilizador de domínio, domínio \ nomedeutilizador, para a identidade do cliente. |  
| IsAdmin |Defina como verdadeiro para especificar que o utilizador de domínio tem acesso de cliente de administrador ou FALSO para acesso de cliente do utilizador. |  

[O nó para segurança de nó](service-fabric-cluster-security.md#node-to-node-security) está definido como a definição **ClusterIdentity** se pretender utilizar um grupo de computador dentro de um domínio do Active Directory. Para obter mais informações, consulte [criar um grupo de computador no Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada utilizando **ClientIdentities**. Para estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster de saber o cliente identidades que o cluster pode confiar. Pode estabelecer confiança de duas formas diferentes:

- Especifique os utilizadores do grupo de domínio que podem ligar-se.
- Especifique os utilizadores de nó de domínio podem ligar-se.

Service Fabric suporta dois tipos de controle de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e usuário. Controlo de acesso permite que o administrador de cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de utilizadores, que faz com que o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo recursos de leitura/gravação). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços.  

O exemplo a seguir **security** secção configura a segurança do Windows, especifica que as máquinas na *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e especifica esse *CONTOSO\usera* tem acesso de cliente do administrador:

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
> Service Fabric não devem ser implementado num controlador de domínio. Certifique-se de que ClusterConfig.json não incluem o endereço IP do controlador de domínio, ao utilizar um grupo de computador ou grupo a conta de serviço gerida (gMSA).
>
>

## <a name="next-steps"></a>Passos Seguintes
Depois de configurar a segurança do Windows no *ClusterConfig.JSON* de ficheiros, retomar o processo de criação do cluster na [criar um cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como nó para nó segurança, segurança de cliente para nó e controlo de acesso baseado em funções, veja [cenários de segurança do Cluster](service-fabric-cluster-security.md).

Ver [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de ligar com o PowerShell ou FabricClient.
