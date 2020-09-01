---
title: Compreenda o controlo de acesso baseado em funções da Kubernetes no dispositivo Azure Stack Edge Microsoft Docs
description: Descreve como o Controlo de Acesso baseado em funções kubernetes ocorre num dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 310fde15a850214aa1741c9cb587c0edcf570a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085383"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Kubernetes Controlo de Acesso baseado em funções no seu dispositivo Azure Stack Edge


No seu dispositivo Azure Stack Edge, quando configurar o papel de computação, é criado um cluster Kubernetes. Pode utilizar o controlo de acesso baseado em funções da Kubernetes (RBAC) para limitar o acesso aos recursos de cluster no seu dispositivo.

Estes artigos fornecem uma visão geral para o sistema RBAC fornecido pela Kubernetes, e como é que o RBAC de Kubernetes é implementado no seu dispositivo Azure Stack Edge. 

## <a name="rbac-for-kubernetes"></a>RBAC para Kubernetes

O AMCC da Kubernetes permite atribuir aos utilizadores, ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar recursos, ou ver registos de cargas de trabalho de aplicações. Estas permissões podem ser miradas para um único espaço de nome, ou concedidas em todo o cluster. 

Quando configura o cluster Kubernetes, é criado um único utilizador correspondente a este cluster e é chamado de utilizador de administração de clusters.  Um `kubeconfig` ficheiro está associado ao utilizador de administração de cluster. O `kubeconfig` ficheiro é um ficheiro de texto que contém todas as informações de configuração necessárias para ligar ao cluster para autenticar o utilizador. 

### <a name="namespaces-and-users"></a>Espaços de nome e utilizadores

No mundo real, é importante dividir o cluster em múltiplos espaços de nome. 

- **Vários utilizadores**: Se tiver vários utilizadores, vários espaços de nome permitirão que esses utilizadores implementem as suas aplicações e serviços nos seus espaços específicos de nomes, isoladamente. 
- **Utilizador único**: Mesmo que exista um único utilizador, vários espaços de nome permitiriam que o utilizador executasse várias versões das aplicações no mesmo cluster Kubernetes.

### <a name="roles-and-rolebindings"></a>Papéis e RoleBindings

Kubernetes tem o conceito de função e de encadernação de papéis que permite dar permissões ao utilizador ou recursos a nível de espaço de nome e a um nível de cluster. 

- **Funções**: Pode definir permissões para os utilizadores como uma **Função** e, em seguida, usar **Roles** para conceder permissões dentro de um espaço de nome. 
- **RoleBindings**: Uma vez definidos os papéis, pode utilizar **RoleBindings** para atribuir funções para um determinado espaço de nome. 

Esta abordagem permite-lhe, logicamente, segregar um único cluster Kubernetes, com os utilizadores apenas capazes de aceder aos recursos da aplicação no seu espaço de nome atribuído. 


## <a name="rbac-on-azure-stack-edge"></a>RBAC em Azure Stack Edge

Na implementação atual do RBAC, o Azure Stack Edge permite-lhe tomar as seguintes ações a partir de um espaço de execução restrito do PowerShell:

- Criar espaços para nomes.  
- Criar utilizadores adicionais.
- Conceda-lhe acesso administrativo aos espaços de nome que criou. Tenha em mente que não terá acesso ao papel de administrador de cluster ou a uma visão dos recursos de todo o cluster.
- Obtenha `kubeconfig` ficheiro com informações para aceder ao cluster Kubernetes.


O dispositivo Azure Stack Edge tem vários espaços de nome do sistema e pode criar espaços de nome de utilizador com `kubeconfig` ficheiros para aceder a esses espaços de nome. Os utilizadores têm controlo total sobre estes espaços de nome e podem criar ou modificar utilizadores, ou conceder acesso aos utilizadores. Apenas o administrador do cluster tem acesso total a espaços de nomes do sistema e recursos em todo o cluster. Um `aseuser` tem acesso apenas de leitura a espaços de nomes do sistema.

Aqui está um diagrama que retrata a implementação do RBAC no dispositivo Azure Stack Edge.

![RBAC no dispositivo Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Neste diagrama, Alice, Bob e Chuck têm acesso apenas a espaços de nome de utilizador atribuídos, que neste caso são `ns1` `ns2` , e `ns3` respectivamente. Dentro destes espaços de nome, eles têm acesso administrativo. O administrador de cluster, por outro lado, tem acesso administrativo a espaços de nome do sistema e recursos de cluster.

Pode utilizar `kubectl` comandos para criar espaços de nome, atribuir utilizadores, atribuir utilizadores ou descarregar `kubeconfig` ficheiros. Aqui está um fluxo de trabalho de alto nível:

1. Crie um espaço de nome e um utilizador.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Criar um utilizador.  

    `New-HcsKubernetesUser -UserName`  

3. Associe o espaço de nomes ao utilizador que criou.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Guarde a configuração do utilizador para `C:\Users\<username>\.kube` .  

5. Instale `kubectl` e comece a implementar aplicações em `kubectl` . 

Para obter instruções detalhadas passo a passo, aceda ao [cluster Access Kubernetes via kuebctl no seu Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ao trabalhar com espaços de nome e utilizadores nos seus dispositivos Azure Stack Edge, aplicam-se as seguintes ressalvas:

- Não está autorizado a realizar quaisquer operações como criar utilizadores, conceder ou revogar o acesso ao espaço de nome ao utilizador, para qualquer um dos espaços de nome do sistema. Exemplos de espaços de nome do sistema `kube-system` incluem, , , , , , . `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` `kube-public` . Os espaços de nome do sistema também incluem os espaços de nome reservados para tipos de implementação tais como `iotedge` (IoT Edge namespace) e `azure-arc` (Azure Arc namespace).
- Pode criar espaços com nomes de utilizador e dentro desses espaços de nome criam utilizadores adicionais e concedem ou revogam o acesso ao espaço de nome a esses utilizadores.
- Não está autorizado a criar espaços de nome com nomes idênticos aos de qualquer espaço de nome do sistema. Os nomes dos espaços de nome do sistema estão reservados.  
- Não está autorizado a criar espaços com nomes de utilizador com nomes que já estejam a ser utilizados por outros espaços com nomes de utilizador. Por exemplo, se tiver um `test-ns` que criou, não pode criar outro `test-ns` espaço de nome.
- Não está autorizado a criar utilizadores com nomes já reservados. Por exemplo, `aseuser` é um administrador de cluster reservado e não pode ser usado.

Para obter mais informações sobre os espaços de nomes do Azure Stack Edge, consulte [os tipos namespace](azure-stack-edge-gpu-kubernetes-workload-management.md#namespaces-types).


<!--To deploy applications on an Azure Stack Edge device, use the following :
 
- First, you will use the PowerShell runspace to create a user, create a namespace, and grant user access to that namespace.
- Next, you will use the Azure Stack Edge resource in the Azure portal to create persistent volumes using either static or dynamic provisioning for the stateful applications that you will deploy.
- Finally, you will use the services to expose applications externally and within the Kubernetes cluster.-->

## <a name="next-steps"></a>Passos seguintes

Para entender como pode criar um utilizador, criar um espaço de nomes e conceder acesso ao espaço de nomes, consulte [Access a Kubernetes cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

