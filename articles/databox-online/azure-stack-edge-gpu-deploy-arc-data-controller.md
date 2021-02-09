---
title: Coloque um controlador de dados Azure Arc no seu dispositivo GPU Azure Stack Edge Pro| Microsoft Docs
description: Descreve como implementar um Controlador de Dados do Arco Azure e serviços de dados Azure no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: 9e56e37135c2ff73fb64d8afd5a852fd757f3e21
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989404"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Implementar serviços de dados Azure no seu dispositivo GPU Azure Stack Edge Pro


Este artigo descreve o processo de criação de um Controlador de Dados Azure Arc e, em seguida, a implementação de Serviços de Dados Azure No seu dispositivo GPU Azure Stack Edge Pro. 

O Azure Arc Data Controller é o avião de controlo local que permite serviços de dados Azure em ambientes geridos pelo cliente. Uma vez criado o Controlador de Dados do Arco Azure no cluster Kubernetes que funciona no seu dispositivo Azure Stack Edge Pro, pode implementar serviços de dados do Azure, como sql Managed Instance (Preview) nesse controlador de dados.

O procedimento para criar o Controlador de Dados e, em seguida, implementar uma SQL Managed Instance envolve a utilização de PowerShell e `kubectl` - uma ferramenta nativa que fornece acesso de linha de comando ao cluster Kubernetes no dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Tem acesso a um dispositivo Azure Stack Edge Pro e ativou o seu dispositivo como descrito no [Activate Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Ativou o papel de computação no dispositivo. Um cluster Kubernetes também foi criado no dispositivo quando configurava o cálculo no dispositivo de acordo com as instruções em [cálculo de Configuração no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Tem o ponto final da API de Kubernetes a partir da página **de Dispositivo** da web UI local. Para obter mais informações, consulte as instruções no [ponto de final da API da Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Tem acesso a um cliente que se ligará ao seu dispositivo. 
    1. Este artigo utiliza um sistema de clientes Windows que executa o PowerShell 5.0 ou mais tarde para aceder ao dispositivo. Pode utilizar qualquer outro cliente com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Instale `kubectl` no seu cliente. Para a versão do cliente:
        1. Identifique a versão do servidor Kubernetes instalada no dispositivo. Na UI local do dispositivo, aceda à página de **atualizações de Software.** Note a versão do **servidor Kubernetes** nesta página.
        1. Transfira um cliente com um desvio de, no máximo, uma versão secundária em relação ao mestre. A versão do cliente, mas pode liderar o mestre até uma versão menor. Por exemplo, um mestre v1.3 deve trabalhar com v1.1, v1.2 e v1.3 nós, e deve trabalhar com clientes v1.2, v1.3 e v1.4. Para obter mais informações sobre a versão do cliente Kubernetes, consulte [a versão e a versão de Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)
    
1. Opcionalmente, [Instale ferramentas de cliente para implantar e gerir os serviços de dados habilitados a Azure Arc.](../azure-arc/data/install-client-tools.md) Estas ferramentas não são necessárias, mas são recomendadas.  
1. Certifique-se de que dispõe de recursos suficientes no seu dispositivo para disponibilizar um controlador de dados e uma SQL Managed Instance. Para o controlador de dados e uma sql Managed Instance, você precisará de um mínimo de 16 GB de RAM e 4 núcleos de CPU. Para obter orientações detalhadas, aceda aos [requisitos mínimos para a implementação de serviços de dados habilitados a Azure Arc](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Configure Kubernetes iPs de serviço externo

1. Vá à uI web local do dispositivo e, em seguida, vá ao **Compute**.
1. Selecione a rede ativada para calcular. 

    ![Página de computação na UI local 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Certifique-se de que fornece três IPs de serviço externo Kubernetes adicionais (para além dos IPs que já configuraram para outros serviços ou contentores externos). O controlador de dados utilizará dois IPs de serviço e o terceiro IP é utilizado quando criar uma SqL Managed Instance. Necessitará de um IP para cada Serviço de Dados adicional que irá implementar. 

    ![Página de computação na UI local 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Aplique as definições e estes novos IPs entrarão imediatamente em vigor num cluster Kubernetes já existente. 


## <a name="deploy-azure-arc-data-controller"></a>Implementar controlador de dados do Arco azul

Antes de implementar um controlador de dados, terá de criar um espaço de nome.

### <a name="create-namespace"></a>Create namespace 

Crie um novo espaço de nome dedicado onde irá implantar o Controlador de Dados. Também irá criar um utilizador e, em seguida, conceder ao utilizador o acesso ao espaço de nomes que criou. 

> [!NOTE]
> Tanto para nomes como para nomes de utilizadores, aplicam-se as [convenções de nomeação do subdomínio DNS.](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)

1. [Ligue-se à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Criar um espaço de nome. Escreva:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Criar um utilizador. Escreva: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Um ficheiro config é apresentado em texto simples. Copie este ficheiro e guarde-o como um ficheiro *config.* 

    > [!IMPORTANT]
    > Não guarde o ficheiro config como *.txt* ficheiro, guarde o ficheiro sem qualquer extensão de ficheiro.

1. O ficheiro config deve viver na `.kube` pasta do seu perfil de utilizador na máquina local. Copie o ficheiro para essa pasta no seu perfil de utilizador.

    ![Localização do ficheiro config no cliente](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)
1. Conceda ao utilizador acesso ao espaço de nomes que criou. Escreva: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Aqui está uma amostra de saída dos comandos anteriores. Neste exemplo, criamos um `myadstest` espaço de nome, um `myadsuser` utilizador e concedemos ao utilizador acesso ao espaço de nomes.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Adicione uma entrada DNS no ficheiro hostes no seu sistema. 

    1. Executar o Bloco de Notas como administrador e abrir o `hosts` ficheiro localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Utilize as informações que guardou na página **dispositivo** na UI local (pré-requisito) para criar a entrada no ficheiro hostis. 

        Por exemplo, copie este ponto final `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` para criar a seguinte entrada com endereço IP do dispositivo e domínio DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Para verificar se pode ligar-se às cápsulas Kubernetes, inicie uma posição cmd ou uma sessão PowerShell. Escreva:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Agora pode implementar as suas aplicações de tratamento de dados e serviços de dados no espaço de nomes e, em seguida, ver as aplicações e os seus registos.

### <a name="create-data-controller"></a>Criar controlador de dados

O controlador de dados é uma coleção de cápsulas que são implantadas no seu cluster Kubernetes para fornecer uma API, o serviço de controlador, o bootstrapper, e as bases de dados de monitorização e dashboards. Siga estes passos para criar um controlador de dados no cluster Kubernetes que existe no seu dispositivo Azure Stack Edge no espaço de nomes que criou anteriormente.   

1. Reúna as seguintes informações que necessitará para criar um controlador de dados:

    
    |Coluna1  |Coluna2  |
    |---------|---------|
    |Nome do controlador de dados     |Um nome descritivo para o seu controlador de dados. Por exemplo, `arctestdatacontroller`.         |
    |Nome de utilizador do controlador de dados     |Qualquer nome de utilizador para o utilizador do administrador do controlador de dados. O nome de utilizador e a palavra-passe do controlador de dados são utilizados para autenticar a API do controlador de dados para executar funções administrativas.          |
    |Senha de controlador de dados     |Uma palavra-passe para o utilizador do administrador do controlador de dados. Escolha uma senha segura e partilhe-a apenas com aqueles que precisam de ter privilégios de administrador de cluster.         |
    |Nome do seu espaço de nome Kubernetes     |O nome do espaço de nomes Kubernetes em que pretende criar o controlador de dados.         |
    |ID de assinatura Azure     |A assinatura Azure GUID para onde pretende que o recurso de controlador de dados em Azure seja criado.         |
    |Nome do grupo de recursos do Azure     |O nome do grupo de recursos onde pretende que o recurso de controlador de dados em Azure seja criado.         |
    |Local azul     |A localização Azure onde os metadados de recursos do controlador de dados serão armazenados em Azure. Para obter uma lista das regiões disponíveis, consulte a Azure global infrastructure /Products by region.|


1. Ligue-se à interface do PowerShell. Para criar o controlador de dados, escreva: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Aqui está uma amostra de saída dos comandos anteriores.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    A colocação pode demorar aproximadamente 5 minutos a ser concluída.

    > [!NOTE]
    > O controlador de dados criado no cluster Kubernetes no seu dispositivo Azure Stack Edge Pro funciona apenas no modo desligado na versão atual.

### <a name="monitor-data-creation-status"></a>Monitorizar o estado da criação de dados

1. Abra outra janela PowerShell.
1. Utilize o seguinte `kubectl` comando para monitorizar o estado de criação do controlador de dados. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Quando o controlador é criado, o estado deve ser `Ready` .
    Aqui está uma amostra de saída do comando anterior:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Para identificar os IPs atribuídos aos serviços externos em execução no controlador de dados, utilize o `kubectl get svc -n <namespace>` comando. Eis uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Implementar exemplo gerido pelo SQL

Depois de ter criado com sucesso o controlador de dados, pode utilizar um modelo para implementar uma SqL Managed Instance no controlador de dados.

### <a name="deployment-template"></a>Modelo de implementação

Utilize o seguinte modelo de implementação para implementar uma sql Gestd Instance no controlador de dados no seu dispositivo.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Nome de metadados
    
O nome dos metadados é o nome da SQL Managed Instance. A cápsula associada no anterior será o `deployment.yaml` nome como ( é o número de `sqlex-n` `n` cápsulas associadas à aplicação). 
    
#### <a name="password-and-username-data"></a>Dados de palavra-passe e nome de utilizador

O nome de utilizador e a palavra-passe do controlador de dados são utilizados para autenticar a API do controlador de dados para executar funções administrativas. O segredo de Kubernetes para o nome de utilizador do controlador de dados e palavra-passe no modelo de implementação são cadeias codificadas base64. 

Pode utilizar uma ferramenta online para codificar o nome de utilizador e palavra-passe pretendidos ou pode utilizar ferramentas CLI construídas dependendo da sua plataforma. Ao utilizar uma ferramenta de codificação Base64 on-line, forneça o nome de utilizador e as cordas de senha (que introduziu enquanto cria o controlador de dados) na ferramenta e a ferramenta gerará as cadeias codificadas Base64 correspondentes.
    
#### <a name="service-type"></a>Tipo de serviço

O tipo de serviço deve ser definido para `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nome da classe de armazenamento

Pode identificar a classe de armazenamento no seu dispositivo Azure Stack Edge que a implementação utilizará para dados, cópias de segurança, registos de dados e registos. Utilize o  `kubectl get storageclass` comando para implantar a classe de armazenamento no seu dispositivo.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Na saída da amostra anterior, a classe de armazenamento `ase-node-local` do seu dispositivo deve ser especificada no modelo.
 
#### <a name="spec"></a>Especificação

Para criar uma ocorrência gerida sql no seu dispositivo Azure Stack Edge, pode especificar os requisitos de memória e CPU na secção de especificações do `deployment.yaml` . Cada instância gerida pelo SQL deve solicitar um mínimo de 2-GB de memória e 1 núcleo de CPU, como mostrado no exemplo seguinte. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Para orientações detalhadas de dimensionamento para o controlador de dados e 1 SQL Managed Instance, reveja [os detalhes de dimensionamento de instâncias geridas pela SQL](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Modelo de implementação de execução

Executar o `deployment.yaml` seguinte comando:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Aqui está uma amostra de saída do seguinte comando:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

A `sqlex-0` cápsula na saída da amostra indica o estado da SQL Managed Instance.

## <a name="remove-data-controller"></a>Remover o controlador de dados

Para remover o controlador de dados, elimine o espaço de nome dedicado no qual o implementou.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Passos seguintes

- [Implemente uma aplicação apátrida no seu Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
