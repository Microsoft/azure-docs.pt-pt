---
title: Solução de monitorização de contentores no Azure Monitor | Documentos da Microsoft
description: A solução de monitorização de contentores no Azure Monitor ajuda-o a ver e gerir o Docker e o Windows anfitriões de contentor numa única localização.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: magoedte
ms.openlocfilehash: 6803e700a42a0d0279b48d38995c4fc299415075
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985500"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitorização de contentores no Azure Monitor

![Símbolo de contentores](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e utilizar a solução de monitorização de contentores no Azure Monitor, que lhe permite ver e gerir o Docker e o Windows anfitriões de contentor numa única localização. O docker é um sistema de Virtualização de software utilizado para criar contentores que automatizam a implementação de software para infra-estrutura de TI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra os contentores estão em execução, que imagem de contentor estarem a ser executados e, em que os contentores estão em execução. Pode ver informações detalhadas de auditoria que mostra os comandos utilizados com contentores. Além disso, pode resolver contentores ao visualizar e procurar registos centralizados sem ter de ver remotamente os anfitriões de Docker ou do Windows. Pode encontrar os contentores que podem ser desnecessárias e consumindo recursos em excesso num anfitrião. Além disso, pode ver centralizada de CPU, memória, armazenamento e informações de utilização e desempenho de rede para contentores. Em computadores com Windows, pode centralizar e comparar os registos do Windows Server, Hyper-V e contentores do Docker. A solução suporta orquestradores do contentor seguinte:

- Docker Swarm
- DC/OS
- Utilizar o Kubernetes
- Service Fabric
- Red Hat OpenShift

Se tem interesse em monitorizar o desempenho das cargas de trabalho implementadas nos ambientes do Kubernetes alojado no Azure Kubernetes Service (AKS), consulte [Monitor Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md). A solução de monitorização do contentor não inclui suporte para monitorizar essa plataforma.  

O diagrama seguinte mostra as relações entre vários anfitriões de contentor e agentes com o Azure Monitor.

![Diagrama de contentores](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos de sistema e as plataformas suportadas

Antes de começar, reveja os detalhes seguintes para verificar que cumpre os pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Solução de monitorização do contentor de suporte para a plataforma do Orchestrator do Docker e SO
A tabela seguinte descreve a orquestração do Docker e do sistema de operativo suporte de inventário de contentor, desempenho e registos de monitorização com o Azure Monitor.   

| | ACS | Linux | Windows | Contentor<br>Inventário | Imagem<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registar | Contentor<br>Registar |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Utilizar o Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Recursos de infraestrutura | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autónomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Servidor Linux<br>(autónomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Versões docker suportadas no Linux

- Docker 1.11 para 1.13
- V17.06 docker CE e EE

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 distribuições de Linux suportadas como anfitriões de contentor


- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE 42.2 dar um passo
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 e 3.5
- ACS o Mesosphere DC/OS 1.7.3 para 1.8.8
- Kubernetes no ACS 1.4.5 para 1.6
    - Eventos de Kubernetes, Kubernetes inventário e processos de contentor só são suportados com a versão 1.4.1-45 e mais tarde do agente do Log Analytics para Linux
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Sistema de operativo do Windows suportado

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional ou Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Versões docker suportadas no Windows

- Docker 1.12 e 1.13
- Docker 17.03.0 e posterior

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

1. Adicionar a solução de monitorização de contentores à sua área de trabalho do Log Analytics da [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou utilizando o processo descrito [adicionar soluções a partir da Galeria de soluções de monitorização](../../azure-monitor/insights/solutions.md).

2. Instalar e utilizar o Docker com um agente do Log Analytics. Com base no seu sistema operativo e o orchestrator do Docker, pode utilizar os seguintes métodos para configurar o agente.
  - Para anfitriões autónomos:
    - Em sistemas de operativos Linux suportados, instalar e executar o Docker e, em seguida, instalar e configurar o [agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
    - No CoreOS, não é possível executar o agente do Log Analytics para Linux. Em vez disso, executar uma versão em contentores do agente do Log Analytics para Linux. Reveja os anfitriões de contentor do Linux incluindo o CoreOS ou anfitriões de contentores de Linux do Azure Government incluindo CoreOS se estiver a trabalhar com contentores na Cloud do Azure Government.
    - No Windows Server 2016 e Windows 10, instalar o motor do Docker e o cliente, em seguida, ligue-se um agente reunir informações e enviá-lo para o Azure Monitor. Revisão [instalar e configurar os anfitriões de contentor do Windows](#install-and-configure-windows-container-hosts) se tiver um ambiente do Windows.
  - Para orquestração de vários anfitriões do Docker:
    - Se tiver um ambiente de Red Hat OpenShift, consulte Configurar um agente do Log Analytics para Red Hat OpenShift.
    - Se tiver um cluster do Kubernetes com o Azure Container Service:
       - Revisão [configurar um agente Linux do Log Analytics para o Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Revisão [configurar um agente do Windows do Log Analytics para Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Reveja a utilização Helm para implementar o agente do Log Analytics no Linux Kubernetes.
    - Se tiver um cluster do DC/OS do Azure Container Service, saiba mais em [monitorizar um cluster de DC/OS do Azure Container Service com o Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Se tiver um ambiente de modo Docker Swarm, saiba mais em configurar um agente de Log Analytics para o Docker Swarm.
    - Se tiver um cluster do Service Fabric, saiba mais em [monitorizar contentores com o Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Reveja os [motor do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artigo para obter mais informações sobre como instalar e configurar seus mecanismos de Docker em computadores que executam o Windows.

> [!IMPORTANT]
> Tem de executar o docker **antes de** instalar o [agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) em seus anfitriões de contentor. Se já tiver instalado o agente antes de instalar o Docker, terá de reinstalar o agente do Log Analytics para Linux. Para obter mais informações sobre o Docker, consulte a [Web site do Docker](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar os anfitriões de contentor do Linux

Depois de instalar o Docker, utilize as seguintes definições para o anfitrião de contentor para configurar o agente para utilização com o Docker. Primeiro precisa o ID de área de trabalho do Log Analytics e a chave, que pode ser encontrado no portal do Azure. Na área de trabalho, clique em **início rápido** > **computadores** para ver seu **ID da área de trabalho** e **chave primária**.  Copie e cole ambos no seu editor favorito.

**Para todos os anfitriões de contentor de Linux exceto CoreOS:**

- Para obter mais informações e passos sobre como instalar o agente do Log Analytics para Linux, consulte [descrição geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Para anfitriões de contentor do todos os Linux incluindo o CoreOS:**

Inicie o contentor que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para anfitriões de contentor do todas as do Azure Government Linux incluindo o CoreOS:**

Inicie o contentor que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Mudar de utilizar um agente de Linux instalado para um num contentor**

Se tiver utilizado o agente diretamente instalados e pretende utilizar em vez disso, um agente em execução num contentor, primeiro tem de remover o agente do Log Analytics para Linux. Ver [desinstalar o agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para compreender como com êxito a desinstalar o agente.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configurar um agente de Log Analytics para o Docker Swarm

Pode executar o agente Log Analytics como um serviço global no Docker Swarm. Utilize as seguintes informações para criar um serviço de agente do Log Analytics. Tem de fornecer o seu ID de área de trabalho do Log Analytics e a chave primária.

- Execute o seguinte no nó principal.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Proteger segredos para o Docker Swarm

Para o Docker Swarm, uma vez criado o segredo para ID de área de trabalho e a chave primária, utilize as seguintes informações para criar as suas informações secretas.

1. Execute o seguinte no nó principal.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Certifique-se de que segredos foram criados adequadamente.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Execute o comando seguinte para montar os segredos para o agente do Log Analytics em contentores.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Configurar um agente de Log Analytics para o Red Hat OpenShift
Existem três formas de adicionar o agente Log Analytics para Red Hat OpenShift comece a recolher dados de monitorização do contentor.

* [Instalar o agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) diretamente em cada nó do OpenShift  
* [Ativar a extensão de VM do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em cada nó do OpenShift que residem no Azure  
* Instalar o agente Log Analytics como um daemon-conjunto do OpenShift  

Nesta secção, vamos abordar os passos necessários para instalar o agente Log Analytics como um conjunto de daemon OpenShift.  

1. Início de sessão para o nó principal do OpenShift e copie o ficheiro yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o nó principal e modificar o valor com o seu ID de área de trabalho do Log Analytics e com a chave primária.
2. Execute os seguintes comandos para criar um projeto para o Azure Monitor e definir a conta de utilizador.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Para implementar o conjunto de daemon, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

5. Certifique-se de que está configurado e funcionar corretamente, escreva o seguinte:

    `oc describe daemonset omsagent`  

    e a saída deve assemelhar-se:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Se pretender utilizar segredos para proteger o seu ID de área de trabalho do Log Analytics e a chave primária ao utilizar o ficheiro de yaml de conjunto de daemon de agente do Log Analytics, execute os seguintes passos.

1. Início de sessão para o nó principal do OpenShift e copie o ficheiro yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o segredo gerar script [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do GitHub.  Este script irá gerar o ficheiro yaml de segredos para ID de área de trabalho do Log Analytics e a chave primária proteger a sua secrete informações.  
2. Execute os seguintes comandos para criar um projeto para o Azure Monitor e definir a conta de utilizador. O segredo de gerar o script pede-lhe para o seu ID de área de trabalho do Log Analytics <WSID> e a chave primária <KEY> e após a conclusão, ele cria o ficheiro de ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Implemente o ficheiro de segredo executando o seguinte:

    `oc create -f ocp-secret.yaml`

5. Verificar a implementação executando o seguinte:

    `oc describe secret omsagent-secret`  

    e a saída deve assemelhar-se:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Implemente o ficheiro de yaml de conjunto de daemon de agente do Log Analytics ao executar o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Verificar a implementação executando o seguinte:

    `oc describe ds oms`

    e a saída deve assemelhar-se:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configurar um agente Linux do Log Analytics para o Kubernetes

Para Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para o seu ID de área de trabalho e a chave primária instalar o agente do Log Analytics para Linux. Com o [GitHub de Kubernetes do Log Analytics Docker](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) página, existem ficheiros que podem ser usadas com ou sem seu informações secretas.

- O agente de Log Analytics predefinido para Linux DaemonSet não tem informações secretas (omsagent.yaml)
- O agente de Log Analytics para o ficheiro yaml de Linux DaemonSet utiliza informações secretas (omsagent-ds-secrets.yaml) com scripts de geração secreta para gerar o ficheiro yaml (omsagentsecret.yaml) de segredos.

Pode optar por criar omsagent DaemonSets com ou sem segredos.

**Ficheiro yaml de OMSagent DaemonSet de predefinição sem segredos**

- Para o arquivo de yaml DaemonSet agente do padrão do Log Analytics, substitua a `<WSID>` e `<KEY>` aos seus WSID e a chave. Copie o ficheiro para o nó principal e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Ficheiro yaml de OMSagent DaemonSet de padrão com segredos**

1. Para utilizar o Log Analytics agent DaemonSet com informações secretas, criar os segredos em primeiro lugar.
    1. Copie o script e o ficheiro de modelo secreta e certificar-se de que estão no mesmo diretório.
        - Gerar script - gen.sh segredo do segredo
        - modelo secreto - template.yaml do segredo
    2. Execute o script, semelhante ao seguinte exemplo. O script pede-lhe o ID de área de trabalho do Log Analytics e a chave primária e depois de introduzi-los, o script cria um ficheiro yaml secreta para que pode executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie o pod segredos ao executar o seguinte:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Para verificar, execute o seguinte:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Saída deve assemelhar-se:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Saída deve assemelhar-se:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Criar a sua omsagent daemon-set através da execução ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Certifique-se de que o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Para Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para ID de área de trabalho e a chave primária para o agente do Log Analytics para Linux. Utilize as seguintes informações de exemplo com o [ficheiro yaml de omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger as suas informações secretas.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configurar um agente de Windows do Log Analytics para o Kubernetes
Para Windows Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para o seu ID de área de trabalho e a chave primária instalar o agente do Log Analytics. Com o [GitHub de Kubernetes do Log Analytics Docker](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) página, existem ficheiros que pode utilizar com as suas informações secretas.  Tem de instalar o agente do Log Analytics separadamente para os nós de agente e mestras.  

1. Para utilizar o agente do Log Analytics DaemonSet com informações secretas no mestre de nó, inicie sessão e criar primeiro os segredos.
    1. Copie o script e o ficheiro de modelo secreta e certificar-se de que estão no mesmo diretório.
        - Gerar script - gen.sh segredo do segredo
        - modelo secreto - template.yaml do segredo

    2. Execute o script, semelhante ao seguinte exemplo. O script pede-lhe o ID de área de trabalho do Log Analytics e a chave primária e depois de introduzi-los, o script cria um ficheiro yaml secreta para que pode executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Criar a sua omsagent daemon-set através da execução ``` kubectl create -f omsagentsecret.yaml ```
    4. Para verificar, execute o seguinte:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Saída deve assemelhar-se:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Criar a sua omsagent daemon-set através da execução ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Certifique-se de que o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no nó de trabalho, o que estiver executando o Windows, siga os passos na secção [instalar e configurar os anfitriões de contentores do Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Utilizar o Helm para implementar o agente do Log Analytics no Linux Kubernetes
Para utilizar o helm para implementar o agente do Log Analytics no seu ambiente do Linux Kubernetes, execute os seguintes passos.

1. Criar a sua omsagent daemon-set através da execução ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Os resultados terá um aspeto semelhantes ao seguinte:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Pode verificar o estado do omsagent ao executar: ```helm status "omsagent"``` e a saída terá um aspeto semelhante ao seguinte:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Para obter mais informações, visite [gráfico do Helm de solução de contentor](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar os anfitriões de contentores do Windows

Utilize as informações na seção instalar e configurar os anfitriões de contentores do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar os agentes do Windows

Antes de instalar agentes em computadores que executam o Windows, terá de configurar o serviço de Docker. A configuração permite que o agente do Windows ou a extensão de máquina virtual do Azure Monitor para utilizar o soquete TCP de Docker para que os agentes podem aceder remotamente o daemon do Docker e para capturar dados para monitorização.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Para iniciar o Docker e verificar a respetiva configuração

Existem passos necessários para configurar o TCP, pipe nomeado para o Windows Server:

1. No Windows PowerShell, ative o pipe TCP e o pipe nomeado.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Configurar Docker com o ficheiro de configuração para o pipe TCP e pipe nomeado. O ficheiro de configuração está localizado em C:\ProgramData\docker\config\daemon.json.

    No ficheiro daemon JSON, terá de fazer o seguinte:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Para obter mais informações sobre a configuração do daemon Docker utilizada com contentores do Windows, consulte [motor do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Instale os agentes do Windows

Para ativar a monitorização de contentores do Windows e o Hyper-V, instale o Microsoft Monitoring Agent (MMA) em computadores Windows que são anfitriões de contentor. Para computadores com Windows no seu ambiente no local, consulte [computadores Windows ligar para o Azure Monitor](../../azure-monitor/platform/agent-windows.md). Para máquinas virtuais em execução no Azure, ligue-os para o Azure Monitor utilizando a [extensão da máquina virtual](../../azure-monitor/learn/quick-collect-azurevm.md).

Pode monitorizar contentores do Windows em execução no Service Fabric. No entanto, apenas [máquinas virtuais em execução no Azure](../../azure-monitor/learn/quick-collect-azurevm.md) e [computadores que executam o Windows no seu ambiente no local](../../azure-monitor/platform/agent-windows.md) são atualmente suportadas para o Service Fabric.

Pode verificar que a solução de monitorização de contentores está corretamente definida para Windows. Para verificar se o pacote de gestão foi download corretamente, procure *ContainerManagement.xxx*. Os ficheiros devem ser na pasta C:\Program Files\Microsoft Monitoring Agent\Agent\Health serviço State\Management Packs.


## <a name="solution-components"></a>Componentes da solução

A partir do portal do Azure, navegue para o *Galeria de soluções* e adicione o **solução de monitorização de contentores**. Se estiver a utilizar agentes do Windows, o seguinte pacote de gestão é instalado em cada computador com um agente quando adicionar esta solução. Nenhuma configuração ou manutenção é necessária para o pacote de gestão.

- *ContainerManagement.xxx* instalados em C:\Program Files\Microsoft Monitoring Agent\Agent\Health serviço State\Management pacotes

## <a name="container-data-collection-details"></a>Detalhes de recolha de dados de contentor
A solução de monitorização de contentores recolhe diversos dados de registo e métricas de desempenho de anfitriões de contentor e contentores com os agentes que ativar.

Dados são recolhidos a cada três minutos pelos seguintes tipos de agente.

- [Agente do log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agente do Windows](../../azure-monitor/platform/agent-windows.md)
- [Extensão de VM de análise de registo](../../azure-monitor/learn/quick-collect-azurevm.md)


### <a name="container-records"></a>Registos de contentor

A tabela seguinte mostra exemplos de registos recolhidos pela solução de monitorização de contentores e os tipos de dados que aparecem nos resultados de pesquisa de registo.

| Tipo de dados | Tipo de dados na pesquisa de registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | CounterName de computador, ObjectName, &#40;% de tempo do processador, o disco lê MB, disco escreve MB, MB de utilização de memória, rede receba Bytes, rede enviar Bytes, o processador de seg de utilização, rede&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Inventário de imagens de contentor | `ContainerImageInventory` | TimeGenerated, computador, imagem, ImageTag, ImageSize, VirtualSize, em execução, em pausa, parado, falha, SourceSystem, ImageID, TotalContainer |
| Registo de contentor | `ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Registo do serviço de contentor | `ContainerServiceLog`  | TimeGenerated, computador, TimeOfCommand, imagem, comando, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário do Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo de contentor | `ContainerProcess_CL` | TimeGenerated, computador, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, computador, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s SourceComponent_s, SourceSystem, da mensagem |

Etiquetas anexado ao *PodLabel* tipos de dados são suas próprias etiquetas personalizadas. As etiquetas de PodLabel anexadas, mostradas na tabela são exemplos. Por isso, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` irá ser diferente no conjunto de dados do seu ambiente e genericamente assemelhar-se `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Contentores de monitor
Depois de ter a solução ativada no portal do Azure, o **contentores** mosaico mostra informações de resumo sobre os seus anfitriões de contentor e os contentores em execução em anfitriões.


![Mosaico de contentores](./media/containers/containers-title.png)

O mosaico mostra uma visão geral dos contentores de quantos tiver no ambiente e se estiver falha, em execução ou parado.

### <a name="using-the-containers-dashboard"></a>Utilizar o dashboard de contentores
Clique nas **contentores** mosaico. A partir daí, verá os modos de exibição organizados por:

- **Eventos de contentor** -mostra o estado do contentor e de computadores com contentores falhados.
- **Registos de contentor** -mostra um gráfico dos ficheiros de registo de contentor gerado ao longo do tempo e uma lista de computadores com o maior número de ficheiros de registo.
- **Eventos de Kubernetes** -mostra um gráfico de eventos de Kubernetes gerado ao longo do tempo e uma lista dos motivos por que motivo pods gerados os eventos. *Este conjunto de dados é usado apenas em ambientes de Linux.*
- **Inventário de espaço de nomes do Kubernetes** – mostra o número de espaços de nomes e pods e mostra a respetiva hierarquia. *Este conjunto de dados é usado apenas em ambientes de Linux.*
- **Inventário de nó do contentor** -mostra o número de tipos de orquestração usados em nós/anfitriões de contentor. Os nós de computador/anfitriões são também apresentados pelo número de contentores. *Este conjunto de dados é usado apenas em ambientes de Linux.*
- **Inventário de imagens de contentor** -mostra o número total de imagens de contentores utilizado e o número de tipos de imagem. O número de imagens também está listado pela tag de imagem.
- **Estado dos contentores** -mostra o número total do contentor de computadores de nós/anfitriões que têm de contentores em execução. Computadores também são listados pelo número de anfitriões a executar.
- **Processo de contentor** -mostra um gráfico de linhas de processos de contentor em execução ao longo do tempo. Contentores são também apresentados ao executar o comando/processo dentro de contentores. *Este conjunto de dados é usado apenas em ambientes de Linux.*
- **Desempenho de CPU do contentor** -mostra um gráfico de linhas a utilização média da CPU ao longo do tempo para nós/anfitriões de computador. Também listas os nós de computador/anfitriões com base em média de utilização da CPU.
- **Desempenho de memória do contentor** -mostra um gráfico de linhas do uso da memória ao longo do tempo. Também apresenta a utilização com base no nome de instância de memória do computador.
- **Desempenho do computador** -mostra os gráficos de linhas de % de desempenho de CPU ao longo do tempo, percentagem de utilização de memória ao longo do tempo e quantidade de megabytes de espaço livre em disco ao longo do tempo. Pode pairar o rato sobre qualquer linha num gráfico para ver mais detalhes.


Cada área do dashboard é uma representação visual de uma pesquisa que é executada sobre os dados recolhidos.

![Dashboard de contentores](./media/containers/containers-dash01.png)

![Dashboard de contentores](./media/containers/containers-dash02.png)

Na **estado do contentor** área, clique na área de principal, conforme mostrado abaixo.

![Estado dos contentores](./media/containers/containers-status.png)

O log Analytics é aberta, exibindo informações sobre o estado dos seus contentores.

![O log Analytics para contentores](./media/containers/containers-log-search.png)

A partir daqui, pode editar a consulta de pesquisa de modificá-lo para encontrar as informações específicas que está interessado. Para obter mais informações sobre consultas de registo, consulte [registar as consultas no Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Resolver problemas ao detetar um contentor com falhas

O log Analytics marca um contentor como **falhada** se saiu com um código de saída diferente de zero. Pode ver uma descrição geral dos erros e falhas no ambiente no **contentores falha** área.

### <a name="to-find-failed-containers"></a>Para localizar os contentores falhados
1. Clique nas **estado do contentor** área.  
   ![Estado dos contentores](./media/containers/containers-status.png)
2. O log Analytics abre e apresenta o estado dos seus contentores, semelhantes ao seguinte.  
   ![Estado de contentores](./media/containers/containers-log-search.png)
3. Expanda a linha de falha e clique em + para adicionar os seus critérios à consulta. Em seguida, comente a linha de resumir na consulta.
   ![contentores falhados](./media/containers/containers-state-failed-select.png)  
1. Execute a consulta e, em seguida, expanda uma linha nos resultados para ver o ID de imagem.  
   ![contentores falhados](./media/containers/containers-state-failed.png)  
1. Escreva o seguinte na consulta de registo. `ContainerImageInventory | where ImageID == <ImageID>` Para ver detalhes sobre a imagem como o tamanho da imagem e o número de imagens com falha e paradas.  
   ![contentores falhados](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Registos de consulta para os dados de contentor
Quando estiver a resolver problemas um erro específico, pode ajudar a verificar em que está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para retornar as informações que pretende.


- **ContainerImageInventory** – Utilize este tipo quando está a tentar encontrar informações organizadas por imagem e para ver informações de imagem como imagem IDs ou tamanhos.
- **ContainerInventory** – Utilize este tipo quando pretender obter informações sobre a localização do contentor, quais são seus nomes e o que imagens de estarem a ser executados.
- **ContainerLog** – Utilize este tipo quando pretende localizar entradas e informações de registo de erro específico.
- **ContainerNodeInventory_CL** usar esse tipo quando pretender que as informações sobre/nó de anfitrião em que os contentores são que residem. Ele fornece Docker versão, tipo de orquestração, armazenamento e informações de rede.
- **ContainerProcess_CL** usar esse tipo para ver rapidamente o processo em execução dentro do contentor.
- **ContainerServiceLog** – Utilize este tipo quando está a tentar encontrar as informações de trilha de auditoria para o daemon do Docker, tais como iniciar, parar, eliminar ou comandos de solicitação.
- **KubeEvents_CL** usar esse tipo para ver os eventos de Kubernetes.
- **KubePodInventory_CL** usar esse tipo para compreender as informações da hierarquia de cluster.


### <a name="to-query-logs-for-container-data"></a>Para registos de consulta para os dados de contentor
* Escolha uma imagem que sabe que falhou recentemente e localizar os registos de erros para ele. Comece localizando um nome de contentor que está a executar essa imagem com um **ContainerInventory** pesquisa. Por exemplo, pesquisar por `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Procurar contentores do Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha de resultados para ver os detalhes para esse contentor.


## <a name="example-log-queries"></a>Consultas de registo de exemplo
Muitas vezes é útil criar consultas, começando com um ou dois exemplos e, em seguida, modificá-los de acordo com seu ambiente. Como ponto de partida, pode experimentar com o **consultas de exemplo** área para ajudar a criar consultas mais avançadas.

![Consultas de contentores](./media/containers/containers-queries.png)


## <a name="saving-log-queries"></a>A guardar consultas de registo
A guardar consultas é uma funcionalidade padrão do Azure Monitor. Salvando-los, terá que encontrar útil útil para utilização futura.

Depois de criar uma consulta que ser úteis, guarde-o clicando **Favoritos** na parte superior da página pesquisa de registos. Em seguida, pode aceder facilmente mais tarde a partir da **meu painel** página.

## <a name="next-steps"></a>Passos Seguintes
* [Consultar os registos](../log-query/log-query-overview.md) para ver os registos de dados de contentor detalhadas.
