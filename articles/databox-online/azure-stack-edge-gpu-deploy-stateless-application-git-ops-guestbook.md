---
title: Implemente a aplicação PHP `Guestbook` no Arc ativada kubernetes no dispositivo GPU Azure Stack Edge Pro| Microsoft Docs
description: Descreve como implementar uma `Guestbook` aplicação apátrida PHP com Redis usando GitOps num arco ativado pelo cluster Kubernetes do seu dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f47f795335204ecd0c74463b76583d7dd79f8532
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436435"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Implementar uma `Guestbook` aplicação php apátrida com Redis no Arc habilitado o cluster Kubernetes no GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra-lhe como construir e implementar uma aplicação web simples e multi-nível usando Kubernetes e Azure Arc. Este exemplo consiste nos seguintes componentes:

- Um mestre Redis de uma só instância para armazenar `guestbook` entradas
- Múltiplas instâncias replicadas do Redis para servir leituras
- Múltiplas instâncias frontend web

A implementação é feita utilizando GitOps no cluster Kubernetes ativado pelo Arco no seu dispositivo Azure Stack Edge Pro. 

Este procedimento destina-se a pessoas que tenham revisto as [cargas de trabalho de Kubernetes no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e estejam familiarizadas com os conceitos do [What is Azure Arc enabled Kubernetes (Preview)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Este artigo contém referências ao termo escravo, um termo que a Microsoft já não usa. Quando o termo for removido do software, vamos removê-lo deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder implementar a aplicação apátrida, certifique-se de que preencheu os seguintes pré-requisitos no seu dispositivo e no cliente que utilizará para aceder ao dispositivo:

> [!NOTE]
> Este artigo contém referências ao termo escravo, um termo que a Microsoft já não usa. Quando o termo for removido do software, vamos removê-lo deste artigo.

### <a name="for-device"></a>Para o dispositivo

1. Tem credenciais de entrada num dispositivo Azure Stack Edge Pro de 1 nó.
    1. O dispositivo está ativado. Ver [Ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. O dispositivo tem o papel computacional configurado através do portal Azure e tem um cluster Kubernetes. Consulte [o cálculo Configure](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Ativou o Azure Arc no cluster Kubernetes existente no seu dispositivo e tem um recurso Azure Arc correspondente no portal Azure. Para obter passos detalhados, consulte [Enable Azure Arc no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Para o cliente aceder ao dispositivo

1. Tem um sistema de clientes Windows que será utilizado para aceder ao dispositivo Azure Stack Edge Pro.
  
    - O cliente está a executar o Windows PowerShell 5.0 ou mais tarde. Para descarregar a versão mais recente do Windows PowerShell, aceda à [Instalação do Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7&preserve-view = true).
    
    - Também pode ter qualquer outro cliente com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) Este artigo descreve o procedimento quando se utiliza um cliente Windows. 
    
1. Completou o procedimento descrito no [Access the Kubernetes cluster no dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Tu:
    
    - Instalado `kubectl` no cliente. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Certifique-se de que a `kubectl` versão do cliente não é distorcida mais do que uma versão da versão master kubernetes em execução no seu dispositivo Azure Stack Edge Pro. 
      - Utilize `kubectl version` para verificar a versão do kubectl em execução no cliente. Tome nota da versão completa.
      - Na UI local do seu dispositivo Azure Stack Edge Pro, vá ao **Overview** e note o número de software Kubernetes. 
      - Verifique estas duas versões para compatibilidade a partir do mapeamento fornecido na versão Kubernetes suportada. <!--insert link-->

1. Tem uma [configuração GitOps que pode utilizar para executar uma implementação do Arco Azure](https://github.com/kagoyal/dbehaikudemo). Neste exemplo, utilizará os `yaml` seguintes ficheiros para implementar no seu dispositivo Azure Stack Edge Pro.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Implementar a configuração

Siga estes passos para configurar o recurso Azure Arc para implementar uma configuração GitOps através do portal Azure: 

1. No seu portal Azure, aceda ao recurso Azure Arc que criou quando ativou o Azure Arc no cluster Kubernetes no seu dispositivo. 

    ![Ir para o recurso Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Vá a **Configurações** e selecione **+ Adicionar configuração**.

    ![A screenshot mostra o cluster Azure Arc ativado kubernetes com configuração Add selecionada.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. Na **configuração Adicionar,** introduza os valores adequados para os campos e, em seguida, selecione **Aplicar**.

    |Parâmetro  |Descrição |
    |---------|---------|
    |Nome de configuração     | Nome para o recurso de configuração.        |
    |Nome da instância do operador     |Nome da instância do operador para identificar uma configuração específica. O nome é uma cadeia de 253 caracteres máximos que deve ser minúscula, alfanumérica, hífen e apenas período.         |
    |Espaço de nome do operador     | **Desmotestesser para** corresponder ao espaço de nome especificado na implantação `yaml` . <br> O campo define o espaço de nome onde o operador está instalado. O nome é uma cadeia de 253 caracteres máximos que deve ser minúscula, alfanumérica, hífen e apenas período.         |
    |URL do repositório     |<br>Caminho para o repositório de git em `http://github.com/username/repo` ou formato onde a sua `git://github.com/username/repo` configuração GitOps está localizada.         |
    |Âmbito do operador     | Selecione **Espaço de Nomes**. <br>Este parâmetro define o âmbito de instalação do operador. Selecione Namespace para instalar o seu operador no espaço de nome especificado nos ficheiros yaml de implementação.       |
    |Tipo de operadores     | Deixe à revelia. <br>Este parâmetro especifica o tipo de operador - por defeito, definido como fluxo.        |
    |Params de operador     | Deixe em branco. <br>Este parâmetro contém parâmetros para passar para o operador de fluxo.        |
    |Helm     | Desa esta definição deste parâmetro para **Desativar**. <br>Ative esta opção se fizer implementações baseadas em gráficos.        |


    ![Adicionar configuração](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. A implementação da configuração começa e o **estado do Operador** mostra como **pendente**. 

    ![A screenshot mostra o arco Azure ativado o cluster Kubernetes em um estado pendente à medida que se refresca.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. A mobilização leva alguns minutos. Quando a implantação estiver concluída, o **estado do operador** aparece como **instalado**.

    ![A screenshot mostra o arco Azure ativado o cluster Kubernetes em um estado instalado.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Verificar a implementação

A implementação através da configuração GitOps cria um `demotestguestbook` espaço de nome conforme especificado nos ficheiros de implantação `yaml` localizados no repo git.

1. Depois de ter aplicado a configuração GitOps, [Ligue-se à interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Executar o seguinte comando para listar as cápsulas em execução no `demotestguestbook` espaço de nome correspondente à implantação.

    `kubectl get pods -n <your-namespace>`
  
    Aqui está uma amostra de saída.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. Neste exemplo, o serviço frontend foi implantado como tipo:LoadBalancer. Você precisará encontrar o endereço IP deste serviço para ver o `guestbook` . Execute o seguinte comando.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. O serviço frontend `type:LoadBalancer` tem um endereço IP externo. Este IP é da gama de endereços IP que especificou para serviços externos ao configurar as definições da rede Compute no dispositivo. Utilize este endereço IP para visualizar o `guestbook` URL: `https://<external-IP-address>` .

    ![Ver livro de visitas](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Eliminar implantação

Para eliminar a implementação, pode eliminar a configuração do portal Azure. A eliminação da configuração eliminará os objetos que foram criados, incluindo implementações e serviços.

1. No portal Azure, vá ao recurso Azure Arc > Configurações. 
1. Localize a configuração que pretende eliminar. Selecione o ... para invocar o menu de contexto e selecione **Delete.**
    ![Eliminar configuração](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Pode levar vários minutos para a configuração ser eliminada.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar o [Painel kubernetes para monitorizar as implementações no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)