---
title: Compreenda o controlo de acesso baseado em funções da Kubernetes no dispositivo Azure Stack Edge Pro| Microsoft Docs
description: Descreve como o controlo de acesso baseado em funções da Kubernetes ocorre num dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443153"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes controlo de acesso baseado em funções no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

No seu dispositivo Azure Stack Edge Pro, quando configurar o papel de computação, é criado um cluster Kubernetes. Pode utilizar o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC) para limitar o acesso aos recursos de cluster no seu dispositivo.

Estes artigos fornecem uma visão geral para o sistema RBAC de Kubernetes fornecido pela Kubernetes, e como é implementado o RBAC da Kubernetes no seu dispositivo Azure Stack Edge Pro. 

## <a name="kubernetes-rbac"></a>RBAC do Kubernetes

O AMCC da Kubernetes permite atribuir aos utilizadores, ou grupos de utilizadores, permissão para fazer coisas como criar ou modificar recursos, ou ver registos de cargas de trabalho de aplicações. Estas permissões podem ser miradas para um único espaço de nome, ou concedidas em todo o cluster. 

Quando configura o cluster Kubernetes, é criado um único utilizador correspondente a este cluster e é chamado de utilizador de administração de clusters.  Um `kubeconfig` ficheiro está associado ao utilizador de administração de cluster. O `kubeconfig` ficheiro é um ficheiro de texto que contém todas as informações de configuração necessárias para ligar ao cluster para autenticar o utilizador.

## <a name="namespaces-types"></a>Tipos de espaços de nome

Os recursos de Kubernetes, tais como cápsulas e implementações, são logicamente agrupados num espaço de nome. Estes agrupamentos fornecem uma forma de dividir logicamente um cluster Kubernetes e restringir o acesso para criar, ver ou gerir recursos. Os utilizadores só podem interagir com recursos dentro dos espaços de nome atribuídos.

Os espaços de nome destinam-se a ser utilizados em ambientes com muitos utilizadores espalhados por várias equipas ou projetos. Para mais informações, consulte [os espaços de nomes de Kubernetes.](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

O seu dispositivo Azure Stack Edge Pro tem os seguintes espaços de nome:

- **Espaço de nome do sistema** - Este espaço de nome é onde existem recursos fundamentais, tais como funcionalidades de rede como DNS e proxy, ou o dashboard Kubernetes. Normalmente, não se implanta as suas próprias aplicações neste espaço de nome. Use este espaço de nome para depurar quaisquer problemas de cluster Kubernetes. 

    Existem vários espaços de nome do sistema no seu dispositivo e os nomes correspondentes a estes espaços de nome do sistema são reservados. Aqui está uma lista dos espaços reservados para o sistema: 
    - kube-system
    - metallb-sistema
    - espaço dbe-nome
    - predefinição
    - kubernetes-dashboard
    - kube-node-arrendamento
    - kube-public


    Certifique-se de que não utiliza nomes reservados para espaços com nomes de utilizador que criar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Espaço de nome do utilizador** - Estes são os espaços de nome que pode criar através do **kubectl** ou através da interface PowerShell do dispositivo para implementar aplicações locais.
 
- **IoT Edge namespace** - Você conecta-se a este `iotedge` espaço de nomes para gerir aplicações implementadas via IoT Edge.

- **Azure Arc namespace** - Você conecta-se a este `azure-arc` espaço de nome para gerir aplicações implementadas via Azure Arc. Com o Azure Arc, também pode implementar aplicações em outros espaços com nomes de utilizador. 

## <a name="namespaces-and-users"></a>Espaços de nome e utilizadores

No mundo real, é importante dividir o cluster em múltiplos espaços de nome. 

- **Vários utilizadores**: Se tiver vários utilizadores, vários espaços de nome permitirão que esses utilizadores implementem as suas aplicações e serviços nos seus espaços específicos de nomes, isoladamente. 
- **Utilizador único**: Mesmo que exista um único utilizador, vários espaços de nome permitiriam que o utilizador executasse várias versões das aplicações no mesmo cluster Kubernetes.

### <a name="roles-and-rolebindings"></a>Papéis e RoleBindings

Kubernetes tem o conceito de função e de encadernação de papéis que permite dar permissões ao utilizador ou recursos a nível de espaço de nome e a um nível de cluster. 

- **Funções**: Pode definir permissões para os utilizadores como uma **Função** e, em seguida, usar **Roles** para conceder permissões dentro de um espaço de nome. 
- **RoleBindings**: Uma vez definidos os papéis, pode utilizar **RoleBindings** para atribuir funções para um determinado espaço de nome. 

Esta abordagem permite-lhe, logicamente, segregar um único cluster Kubernetes, com os utilizadores apenas capazes de aceder aos recursos da aplicação no seu espaço de nome atribuído. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC no Azure Stack Edge Pro

Na implementação atual do Kubernetes RBAC, o Azure Stack Edge Pro permite-lhe tomar as seguintes ações a partir de um espaço de execução restrito do PowerShell:

- Criar espaços para nomes.  
- Criar utilizadores adicionais.
- Conceda-lhe acesso administrativo aos espaços de nome que criou. Tenha em mente que não terá acesso ao papel de administrador de cluster ou a uma visão dos recursos de todo o cluster.
- Obtenha `kubeconfig` ficheiro com informações para aceder ao cluster Kubernetes.


O dispositivo Azure Stack Edge Pro tem vários espaços de nome do sistema e pode criar espaços de nome de utilizador com `kubeconfig` ficheiros para aceder a esses espaços de nome. Os utilizadores têm controlo total sobre estes espaços de nome e podem criar ou modificar utilizadores, ou conceder acesso aos utilizadores. Apenas o administrador do cluster tem acesso total a espaços de nomes do sistema e recursos em todo o cluster. Um `aseuser` tem acesso apenas de leitura a espaços de nomes do sistema.

Aqui está um diagrama que retrata a implementação do RBAC de Kubernetes no dispositivo Azure Stack Edge Pro.

![Kubernetes RBAC no dispositivo Azure Stack Edge Pro](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Neste diagrama, Alice, Bob e Chuck têm acesso apenas a espaços de nome de utilizador atribuídos, que neste caso são `ns1` `ns2` , e `ns3` respectivamente. Dentro destes espaços de nome, eles têm acesso administrativo. O administrador de cluster, por outro lado, tem acesso administrativo a espaços de nome do sistema e recursos de cluster.

Como utilizador, pode criar espaços de nome e utilizadores, atribuir aos utilizadores espaços de nome ou descarregar `kubeconfig` ficheiros. Para obter instruções detalhadas passo a passo, aceda ao [cluster Access Kubernetes via kuebctl no seu Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ao trabalhar com espaços de nome e utilizadores nos seus dispositivos Azure Stack Edge Pro, aplicam-se as seguintes ressalvas:

- Não está autorizado a realizar quaisquer operações como criar utilizadores, conceder ou revogar o acesso ao espaço de nome ao utilizador, para qualquer um dos espaços de nome do sistema. Exemplos de espaços de nome do sistema `kube-system` incluem, , , , , , . `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` `kube-public` . Os espaços de nome do sistema também incluem os espaços de nome reservados para tipos de implementação tais como `iotedge` (IoT Edge namespace) e `azure-arc` (Azure Arc namespace).
- Pode criar espaços com nomes de utilizador e dentro desses espaços de nome criam utilizadores adicionais e concedem ou revogam o acesso ao espaço de nome a esses utilizadores.
- Não está autorizado a criar espaços de nome com nomes idênticos aos de qualquer espaço de nome do sistema. Os nomes dos espaços de nome do sistema estão reservados.  
- Não está autorizado a criar espaços com nomes de utilizador com nomes que já estejam a ser utilizados por outros espaços com nomes de utilizador. Por exemplo, se tiver um `test-ns` que criou, não pode criar outro `test-ns` espaço de nome.
- Não está autorizado a criar utilizadores com nomes já reservados. Por exemplo, `aseuser` é um utilizador reservado e não pode ser utilizado.


## <a name="next-steps"></a>Passos seguintes

Para entender como pode criar um utilizador, criar um espaço de nomes e conceder acesso ao espaço de nomes, consulte [Access a Kubernetes cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

