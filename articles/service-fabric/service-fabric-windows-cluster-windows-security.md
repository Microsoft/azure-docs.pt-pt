---
title: Proteja um cluster que funciona no Windows utilizando a segurança do Windows
description: Aprenda a configurar a segurança de nó ao nó e cliente-a-nó num cluster autónomo que funciona no Windows utilizando a segurança do Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774450"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteja um cluster autónomo no Windows utilizando a segurança do Windows
Para evitar o acesso não autorizado a um cluster de Tecido de Serviço, deve fixar o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança nó-a-nó e cliente-a-nó utilizando a segurança do Windows no ficheiro *ClusterConfig.JSON.*  O processo corresponde ao passo de segurança configurado de [Criar um cluster autónomo que funciona no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como o Tecido de Serviço utiliza a segurança do Windows, consulte [cenários](service-fabric-cluster-security.md)de segurança do Cluster .

> [!NOTE]
> Deve considerar cuidadosamente a seleção da segurança nó-a-nó porque não existe um upgrade de cluster de uma escolha de segurança para outra. Para mudar a seleção de segurança, tem que reconstruir todo o cluster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configure a segurança do Windows utilizando gMSA  
O ficheiro de configuração *ClusterConfig.gMSA.Windows.MultiMachine.JSON* descarregou com o [Microsoft.Azure.ServiceFabric.WindowsServer.\< versão>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) modo de cluster contém um modelo para configurar a segurança do Windows usando [a Conta de Serviço Gerida pelo Grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Configurar para *o Windows* para permitir a segurança do Windows para a comunicação do nó nonó.  | 
| ServerCredentialType |Configurar para *o Windows* para permitir a segurança do Windows para a comunicação cliente-nó. |
| Identidades do Windows |Contém o cluster e as identidades dos clientes. |
| ClustergMSAIdentity |Configures segurança nó-a-nó. Uma conta de serviço gerida pelo grupo. |
| ClusterSPN |SPN registado para conta gMSA|
| Identidades do Cliente |Configura a segurança cliente-a-nó. Uma série de contas de utilizador de clientes. |
| Identidade |Adicione o utilizador de domínio, nome de utilizador de domínio, para a identidade do cliente. |
| IsAdmin |Deacordo para especificar que o utilizador de domínio tem acesso ao cliente do administrador ou falso para o acesso ao cliente do utilizador. |

> [!NOTE]
> O valor clustergMSAIdentidade devemysfgmsa@mydomainestar em formato " ".

[A segurança](service-fabric-cluster-security.md#node-to-node-security) do nó ao nó é configurada através da definição de **ClustergMSAIdentity** quando o tecido de serviço precisa de ser executado sob gMSA. Para construir relações de confiança entre nós, devem ser sensibilizados uns dos outros. Isto pode ser realizado de duas maneiras diferentes: Especifique a Conta de Serviço Gerida pelo Grupo que inclui todos os nós do cluster ou especifique o grupo de máquinas de domínio que inclui todos os nós do cluster. Recomendamos vivamente a utilização da abordagem da Conta de Serviço gerida pelo [grupo (gMSA),](https://technet.microsoft.com/library/hh831782.aspx) particularmente para clusters maiores (mais de 10 nós) ou para clusters que são suscetíveis de crescer ou encolher.  
Esta abordagem não exige a criação de um grupo de domínio para o qual os administradores de cluster stiveram direitos de acesso para adicionar e remover membros. Estas contas também são úteis para a gestão automática de passwords. Para mais informações, consulte [Iniciar-se com Contas de Serviço Geridas](https://technet.microsoft.com/library/jj128431.aspx)pelo Grupo .  
 
[A segurança do cliente ao nó](service-fabric-cluster-security.md#client-to-node-security) está configurada usando **identidades do Cliente.** Para estabelecer a confiança entre um cliente e o cluster, deve configurar o cluster para saber em que identidades do cliente pode confiar. Isto pode ser feito de duas maneiras diferentes: Especifique os utilizadores do grupo de domínio que podem ligar ou especificar os utilizadores do nó de domínio que podem ligar. O Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso proporciona a capacidade de o administrador do cluster limitar o acesso a certos tipos de operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, só leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços. Para obter mais informações sobre os controlos de acesso, consulte [o controlo de acesso baseado em Funções para os clientes do Service Fabric](service-fabric-cluster-security-roles.md).  
 
A secção de **segurança** de exemplo seguinte configura a segurança do Windows utilizando gMSA e especifica que as máquinas em *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e que *CONTOSO\usera* tem acesso ao cliente administrativo:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configure a segurança do Windows utilizando um grupo de máquinas  
Este modelo está a ser depreciado. A recomendação é utilizar o GMSA como descrito acima. O ficheiro de configuração *ClusterConfig.Windows.MultiMachine.JSON* descarregado com o [Microsoft.Azure.ServiceFabric.WindowsServer.\< versão>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) pacote de cluster autónomo contém um modelo para configurar a segurança do Windows.  A segurança do Windows está configurada na secção **Propriedades:** 

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
| ClusterCredentialType |Configurar para *o Windows* para permitir a segurança do Windows para a comunicação do nó nonó.  |
| ServerCredentialType |Configurar para *o Windows* para permitir a segurança do Windows para a comunicação cliente-nó. |
| Identidades do Windows |Contém o cluster e as identidades dos clientes. |
| Identidade cluster |Utilize um nome de grupo de máquinas, domain\machinegroup, para configurar a segurança do nó ao nó. |
| Identidades do Cliente |Configura a segurança cliente-a-nó. Uma série de contas de utilizador de clientes. |  
| Identidade |Adicione o utilizador de domínio, nome de utilizador de domínio, para a identidade do cliente. |  
| IsAdmin |Deacordo para especificar que o utilizador de domínio tem acesso ao cliente do administrador ou falso para o acesso ao cliente do utilizador. |  

A segurança do [nó ao nó](service-fabric-cluster-security.md#node-to-node-security) é configurada através da definição utilizando **clusterIdentity** se pretender utilizar um grupo de máquinas dentro de um Domínio de Diretório Ativo. Para mais informações, consulte [Criar um Grupo de Máquinas em Diretório Ativo](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[A segurança cliente-a-nó](service-fabric-cluster-security.md#client-to-node-security) é configurada utilizando **identidades do Cliente**. Para estabelecer a confiança entre um cliente e o cluster, deve configurar o cluster para conhecer as identidades do cliente em que o cluster pode confiar. Pode estabelecer confiança de duas maneiras diferentes:

- Especifique os utilizadores do grupo de domínio que podem ligar.
- Especifique os utilizadores do nó de domínio que podem ligar.

O Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso permite ao administrador do cluster limitar o acesso a certos tipos de operações de cluster para diferentes grupos de utilizadores, o que torna o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, só leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços.  

A secção de **segurança** de exemplo seguinte configura a segurança do Windows, especifica que as máquinas em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster, e especifica que *CONTOSO\usera* tem acesso ao cliente administrativo:

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
> O tecido de serviço não deve ser implantado num controlador de domínio. Certifique-se de que clusterConfig.json não inclui o endereço IP do controlador de domínio quando utilizar um grupo de máquinas ou uma conta de serviço gerida por grupo (gMSA).
>
>

## <a name="next-steps"></a>Passos seguintes
Depois de configurar a segurança do Windows no ficheiro *ClusterConfig.JSON,* retome o processo de criação do cluster em [Criar um cluster autónomo que corre no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como a segurança do nó ao nó, a segurança cliente-a-nó e o controlo de acesso baseado em papéis, consulte [os cenários](service-fabric-cluster-security.md)de segurança do Cluster .

Consulte [a Connect a um cluster seguro,](service-fabric-connect-to-secure-cluster.md) por exemplo, de ligação utilizando powerShell ou FabricClient.
