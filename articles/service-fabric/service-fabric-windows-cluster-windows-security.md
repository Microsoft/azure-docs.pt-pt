---
title: Garantir um cluster em execução no Windows utilizando a segurança do Windows
description: Aprenda a configurar a segurança nó-a-nó e a segurança entre cliente e nó num cluster autónomo em execução no Windows utilizando a segurança do Windows.
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: e97a951f6dc0a97b1cfa8f960ed762084c82d2ed
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839486"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autónomo no Windows utilizando a segurança do Windows
Para evitar o acesso não autorizado a um cluster de tecido de serviço, deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança nó-a-nó e o cliente-a-nó, utilizando a segurança do Windows no ficheiro *ONClusterConfig.JS.*  O processo corresponde ao passo de segurança de configuração de [Criar um cluster autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como o Service Fabric utiliza a segurança do Windows, consulte [os cenários de segurança do Cluster.](service-fabric-cluster-security.md)

> [!NOTE]
> Deve considerar cuidadosamente a seleção da segurança nó-a-nó, porque não existe uma atualização de cluster de uma escolha de segurança para outra. Para mudar a seleção de segurança, tem que reconstruir todo o cluster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configure a segurança do Windows utilizando o gMSA  
A amostraClusterConfig.gMSA.Windows.MultiMachine.JSficheiro de configuração * ON* descarregado com o [Microsoft.Azure.ServiceFabric.WindowsServer. \<version> pacote](https://go.microsoft.com/fwlink/?LinkId=730690) de cluster autónomo zip contém um modelo para configurar a segurança do Windows utilizando [a conta de serviço gerido do grupo (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)):  

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
| ClusterCredentialType |Configurar para *o Windows* para ativar a segurança do Windows para a comunicação do nó de nó.  | 
| ServerCredentialType |Configurar para *o Windows* para ativar a segurança do Windows para a comunicação de nó de cliente. |
| WindowsIdentities |Contém o cluster e as identidades dos clientes. |
| ClustergMSAIdentity |Configura a segurança do nó-a-nó. Um grupo geria a conta de serviço. |
| ClusterSPN |SPN registado para conta gMSA|
| Clientes |Configura a segurança entre cliente e nó. Uma série de contas de utilizadores de clientes. |
| Identidade |Adicione o utilizador de domínio, nome de utilizador domínio\username, para a identidade do cliente. |
| IsAdmin |Definido como verdadeiro para especificar que o utilizador de domínio tem acesso ao cliente administrador ou falso para acesso ao cliente do utilizador. |

> [!NOTE]
> O valor da clustergMSAIdentity deve estar em formato mysfgmsa@mydomain " "

[A segurança do nó para o nó](service-fabric-cluster-security.md#node-to-node-security) é configurada definindo **clustergMSAIdentity** quando o tecido de serviço precisa ser executado sob gMSA. Para construir relações de confiança entre nós, devem ser sensibilizados uns para os outros. Isto pode ser realizado de duas maneiras diferentes: Especifique a Conta de Serviço Gerida do Grupo que inclui todos os nós no cluster ou especifique o grupo de máquinas de domínio que inclui todos os nós no cluster. Recomendamos vivamente a utilização da abordagem da Conta de Serviço Gerida do [Grupo (gMSA),](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) especialmente para clusters maiores (mais de 10 nós) ou para clusters que são suscetíveis de crescer ou encolher.  
Esta abordagem não exige a criação de um grupo de domínio para o qual os administradores de agrupamentos tenham direitos de acesso para adicionar e remover membros. Estas contas também são úteis para a gestão automática de senhas. Para obter mais informações, consulte [Começar com contas de serviço geridas pelo grupo.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))  
 
[A segurança do cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClienteSIdentidades**. Para estabelecer a confiança entre um cliente e o cluster, deve configurar o cluster para saber em que identidades do cliente pode confiar. Isto pode ser feito de duas maneiras diferentes: Especifique os utilizadores do grupo de domínio que podem ligar ou especificar os utilizadores de nó de domínio que podem ligar. O Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes que estão ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acessos proporciona a capacidade de o administrador do cluster limitar o acesso a certos tipos de operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, apenas leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços. Para obter mais informações sobre os controlos de acesso, consulte [o controlo de acesso baseado em funções para clientes do Service Fabric](service-fabric-cluster-security-roles.md).  
 
A secção de **segurança** que se segue configura a segurança do Windows utilizando o gMSA e especifica que as máquinas em *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e que *o CONTOSO\usera* tem acesso a um cliente administrativo:  
  
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
Este modelo está a ser depreciado. A recomendação é utilizar o gMSA como descrito acima. A amostraClusterConfig.Windows.MultiMachine.JSficheiro de configuração * ON* descarregado com o [Microsoft.Azure.ServiceFabric.WindowsServer. \<version> pacote](https://go.microsoft.com/fwlink/?LinkId=730690) de cluster autónomo zip contém um modelo para configurar a segurança do Windows.  A segurança do Windows está configurada na secção **Propriedades:** 

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
| ClusterCredentialType |Configurar para *o Windows* para ativar a segurança do Windows para a comunicação do nó de nó.  |
| ServerCredentialType |Configurar para *o Windows* para ativar a segurança do Windows para a comunicação de nó de cliente. |
| WindowsIdentities |Contém o cluster e as identidades dos clientes. |
| ClusterIdentity |Utilize um nome de grupo de máquinas, grupo de domínio\machine, para configurar a segurança do nó-a-nó. |
| Clientes |Configura a segurança entre cliente e nó. Uma série de contas de utilizadores de clientes. |  
| Identidade |Adicione o utilizador de domínio, nome de utilizador domínio\username, para a identidade do cliente. |  
| IsAdmin |Definido como verdadeiro para especificar que o utilizador de domínio tem acesso ao cliente administrador ou falso para acesso ao cliente do utilizador. |  

[A segurança do nó para nó](service-fabric-cluster-security.md#node-to-node-security) é configurada através da configuração do **ClusterId se** pretender utilizar um grupo de máquinas dentro de um domínio de diretório ativo. Para obter mais informações, consulte [Criar um Grupo de Máquinas no Diretório Ativo](/previous-versions/commerce-server/aa545347(v=cs.70)).

[A segurança entre clientes](service-fabric-cluster-security.md#client-to-node-security) e nó é configurada utilizando **as identificações do Cliente.** Para estabelecer a confiança entre um cliente e o cluster, deve configurar o cluster para conhecer as identidades do cliente em que o cluster pode confiar. Pode estabelecer confiança de duas maneiras diferentes:

- Especifique os utilizadores do grupo de domínio que podem ligar.
- Especifique os utilizadores de nó de domínio que podem ligar.

O Service Fabric suporta dois tipos diferentes de controlo de acesso para clientes que estão ligados a um cluster de Tecido de Serviço: administrador e utilizador. O controlo de acesso permite ao administrador de cluster limitar o acesso a certos tipos de operações de cluster para diferentes grupos de utilizadores, o que torna o cluster mais seguro.  Os administradores têm acesso total às capacidades de gestão (incluindo capacidades de leitura/escrita). Os utilizadores, por padrão, apenas leram o acesso às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver aplicações e serviços.  

A secção de **segurança** que se segue configura a segurança do Windows, especifica que as máquinas em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster, e especifica que *o CONTOSO\usera* tem acesso a um cliente administrativo:

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
> O tecido de serviço não deve ser implantado num controlador de domínio. Certifique-se de que ClusterConfig.jsnão inclui o endereço IP do controlador de domínio quando utilizar um grupo de máquinas ou uma conta de serviço gerido em grupo (gMSA).
>
>

## <a name="next-steps"></a>Passos seguintes
Depois de configurar a segurança do Windows no ficheiro * ONClusterConfig.JS,* retomar o processo de criação de clusters em [Criar um cluster autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como a segurança do nó-a-nó, a segurança do cliente-a-nó e o controlo de acesso baseado em funções, consulte [cenários](service-fabric-cluster-security.md)de segurança do Cluster .

Consulte [Ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) por exemplos de ligação utilizando PowerShell ou FabricClient.
