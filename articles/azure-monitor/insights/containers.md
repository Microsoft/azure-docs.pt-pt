---
title: Solução de Monitorização de Contentores no Monitor Azure Microsoft Docs
description: A solução de monitorização de contentores no Azure Monitor ajuda-o a visualizar e a gerir os seus anfitriões de contentores Docker e Windows num único local.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/06/2020
ms.openlocfilehash: a02ea022bedd92e9deaa0730cc1be051a9d20c88
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145689"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitorização de contentores no Monitor Azure

![Símbolo de contentores](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e utilizar a solução de monitorização do contentor no Azure Monitor, o que o ajuda a visualizar e gerir os anfitriões do seu contentor Docker e Windows num único local. Docker é um sistema de virtualização de software usado para criar contentores que automatizam a implementação de software para a sua infraestrutura de TI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra quais os contentores que estão a funcionar, que imagem de contentor estão a funcionar e onde os contentores estão a funcionar. Pode ver informações detalhadas de auditoria que mostram comandos utilizados com contentores. E, você pode resolver os problemas de recipientes visualizando e pesquisando registos centralizados sem ter que ver remotamente anfitriões docker ou Windows. Você pode encontrar recipientes que podem ser barulhentos e consumir recursos excedentários em um hospedeiro. E, você pode ver CPU centralizado, memória, armazenamento, e informações de utilização da rede e desempenho para recipientes. Nos computadores que executam o Windows, pode centralizar e comparar registos dos contentores Windows Server, Hyper-V e Docker. A solução suporta os seguintes orquestradores de contentores:

- Docker Swarm
- DC/OS
- Utilizar o Kubernetes
- Service Fabric
- Red Hat OpenShift

Se tiver contentores implantados no [Azure Service Fabric,](../../service-fabric/service-fabric-overview.md)recomendamos que a [solução de Tecido de Serviço](../../service-fabric/service-fabric-diagnostics-oms-setup.md) e esta solução incluam a monitorização de eventos de cluster. Antes de ativar a solução De Tecido de Serviço, [reveja utilizando a solução De Tecido de Serviço](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) para entender o que fornece e como usá-la.

Se estiver interessado em monitorizar o desempenho das suas cargas de trabalho implantadas em ambientes Kubernetes hospedados no Serviço Azure Kubernetes (AKS), consulte [o Serviço Monitor Azure Kubernetes](./container-insights-overview.md). A solução de monitorização do contentor não suporta a monitorização desta plataforma.  

O diagrama seguinte mostra as relações entre vários hospedeiros de contentores e agentes com o Azure Monitor.

![Diagrama de contentores](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos do sistema e plataformas apoiadas

Antes de começar, reveja os seguintes detalhes para verificar se encontra os pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Suporte de solução de monitorização de contentores para a plataforma Docker Orchestrator e OS

O quadro que se segue descreve o suporte de monitorização do sistema de monitorização do sistema de estivador e do sistema operativo com o Azure Monitor.   

|Orquestração Docker | ACS | Linux | Windows | Contentor<br>Inventário | Imagem<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registar | Contentor<br>Registar |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Utilizar o Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosfera<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Enxame | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Recursos de infraestrutura | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autónomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Servidor Linux<br>(autónomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Versões docker suportadas no Linux

- Estivador 1.11 a 1.13
- Estivador CE e EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 distribuição Linux suportadas como hospedeiros de contentores

- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS (estável)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Plataforma de recipiente aberto de chapéu vermelho (OCP) 3.4 e 3.5
- ACS Mesosfera DC/OS 1.7.3 a 1.8.8
- ACS Kubernetes 1.4.5 a 1.6
    - Eventos Kubernetes, inventário de Kubernetes e processos de contentores só são suportados com a versão 1.4.1-45 e mais tarde do agente Log Analytics para Linux
- Enxame ACS Docker

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows suportado

- Windows Server 2016
- Edição de Aniversário do Windows 10 (Profissional ou Empresa)

### <a name="docker-versions-supported-on-windows"></a>Versões docker suportadas no Windows

- Estivador 1.12 e 1.13
- Estivador 17.03.0 e mais tarde

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

Utilize as seguintes informações para instalar e configurar a solução.

1. Adicione a solução de monitorização do contentor ao seu espaço de trabalho Log Analytics a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou utilizando o processo descrito nas [soluções de monitorização add da Galeria solutions](./solutions.md).

2. Instale e utilize o Docker com um agente Log Analytics. Com base no seu sistema operativo e no orquestrador Docker, pode utilizar os seguintes métodos para configurar o seu agente.
   - Para anfitriões autónomos:
     - Nos sistemas operativos Linux suportados, instale e execute o Docker e, em seguida, instale e configuure o [agente Log Analytics para o Linux](../learn/quick-collect-linux-computer.md).  
     - No CoreOS, não é possível executar o agente Log Analytics para o Linux. Em vez disso, você tem uma versão contentorizada do agente Log Analytics para Linux. Reveja os anfitriões de contentores Linux, incluindo os anfitriões de contentores Do Governo de Azure, incluindo o CoreOS, se estiver a trabalhar com contentores na Nuvem do Governo de Azure.
     - No Windows Server 2016 e Windows 10, instale o Motor Docker e o cliente, em seguida, conecte um agente para recolher informações e enviá-la para o Azure Monitor. [Reveja instalar e configurar os anfitriões dos contentores do Windows](#install-and-configure-windows-container-hosts) se tiver um ambiente Windows.
   - Para a orquestração multi-anfitrião Docker:
     - Se tiver um ambiente Red Hat OpenShift, reveja o Configure um agente Log Analytics para Red Hat OpenShift.
     - Se tiver um cluster Kubernetes utilizando o Serviço de Contentores Azure:
       - [Reveja configurar um agente Do Log Analytics Linux para Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - [Reveja configurar um agente do Log Analytics Windows para Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Reveja o Leme para implantar o agente Log Analytics no Linux Kubernetes.
     - Se tiver um cluster de Serviço de Contentores Azure DC/OS, saiba mais no [Monitor um cluster de Serviço de Contentores Azure DC/OS com o Azure Monitor](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Se tiver um ambiente de modo Docker Swarm, saiba mais na Configure um agente Log Analytics para Docker Swarm.
     - Se tiver um cluster de tecido de serviço, saiba mais em [recipientes Monitor com Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Reveja o [artigo do Docker Engine no Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obter informações adicionais sobre como instalar e configurar os seus Motores Docker em computadores que executam o Windows.

> [!IMPORTANT]
> O Docker deve estar a funcionar **antes** de instalar o [agente Log Analytics para o Linux](../learn/quick-collect-linux-computer.md) nos anfitriões dos contentores. Se já instalou o agente antes de instalar o Docker, tem de reinstalar o agente Log Analytics para o Linux. Para mais informações sobre Docker, consulte o site do [Docker.](https://www.docker.com)

### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar os anfitriões dos contentores Linux

Depois de ter instalado o Docker, utilize as seguintes definições para o seu anfitrião do contentor para configurar o agente para utilização com o Docker. Primeiro precisa do seu ID e chave do espaço de trabalho Log Analytics, que pode encontrar no portal Azure. No seu espaço de trabalho, clique em **Computadores De Início Rápido** para ver o  >  **Computers** **ID** do espaço de trabalho e **a chave primária.**  Copie e cole ambos no seu editor favorito.

**Para todos os anfitriões de contentores Linux, exceto CoreOS:**

- Para obter mais informações e etapas sobre como instalar o agente Log Analytics para linux, consulte a [visão geral do agente Log Analytics](../platform/log-analytics-agent.md).

**Para todos os anfitriões de contentores Linux, incluindo CoreOS:**

Ligue o recipiente que pretende monitorizar. Modificar e utilizar o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os anfitriões de contentores do Governo Azure Linux, incluindo o CoreOS:**

Ligue o recipiente que pretende monitorizar. Modificar e utilizar o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Passar de usar um agente Linux instalado para um num recipiente**

Se usou previamente o agente instalado diretamente e pretender utilizar um agente que esteja a funcionar num contentor, deve primeiro remover o agente Log Analytics para o Linux. Consulte [desinstalar o agente Log Analytics para](../learn/quick-collect-linux-computer.md) que o Linux compreenda como desinstalar o agente com sucesso.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configure um agente log analytics para Docker Swarm

Pode executar o agente Log Analytics como um serviço global no Docker Swarm. Utilize as seguintes informações para criar um serviço de agente Log Analytics. Tem de fornecer o seu ID e Chave Primária do Espaço de Trabalho do Log Analytics.

- Executar o seguinte no nó mestre.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Segredos seguros para Docker Swarm

Para Docker Swarm, uma vez criado o segredo para o Workspace ID e Key Primary, use as seguintes informações para criar as suas informações secretas.

1. Executar o seguinte no nó mestre.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Verifique se os segredos foram criados corretamente.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Executar o seguinte comando para montar os segredos no agente de Log Analytics contentorizado.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Configure um agente log analytics para red hat openshift

Existem três formas de adicionar o agente Log Analytics ao Red Hat OpenShift para começar a recolher dados de monitorização de contentores.

* [Instale o agente Log Analytics para o Linux](../learn/quick-collect-linux-computer.md) diretamente em cada nó OpenShift  
* [Ativar a extensão VM do Log Analytics](../learn/quick-collect-azurevm.md) em cada nó OpenShift que reside no Azure  
* Instale o agente Log Analytics como um conjunto de daemon openshift  

Nesta secção cobrimos os passos necessários para instalar o agente Log Analytics como um conjunto de daemon openShift.  

1. Inscreva-se no nó principal OpenShift e copie o ficheiro yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o seu nó principal e modifique o valor com o seu ID do Espaço de Trabalho do Log Analytics e com a sua Chave Primária.
2. Executar os seguintes comandos para criar um projeto para o Azure Monitor e definir a conta do utilizador.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Para implantar o conjunto de daemon, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

4. Para verificar se está configurado e funcionando corretamente, digite o seguinte:

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

Se pretender utilizar segredos para proteger o ID e a Chave Primária do Log Analytics quando utilizar o ficheiro yaml do agente Log Analytics, execute os seguintes passos.

1. Inscreva-se no nó principal openShift e copie o ficheiro yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o script gerador secreto [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do GitHub.  Este script gerará o ficheiro secrets yaml para O ID do Espaço de Trabalho do Log Analytics e chave primária para proteger as informações secretas.  
2. Executar os seguintes comandos para criar um projeto para o Azure Monitor e definir a conta do utilizador. O script gerador secreto pede o seu ID de Workspace de Log Analytics `<WSID>` e Chave Primária `<KEY>` e, após a conclusão, cria o ficheiro ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Desloque o ficheiro secreto executando o seguinte:

    `oc create -f ocp-secret.yaml`

4. Verificar a implementação executando o seguinte:

    `oc describe secret omsagent-secret`  

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

5. Implementar o ficheiro yaml do agente Log Analytics, executando o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verificar a implementação executando o seguinte:

    `oc describe ds oms`

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configure um agente De Log Analytics Linux para Kubernetes

Para o Kubernetes, utiliza um script para gerar o ficheiro secrets yaml para o seu ID workspace e chave primária para instalar o agente Log Analytics para o Linux. Na página [do Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) existem ficheiros que pode utilizar com ou sem as suas informações secretas.

- O agente default Log Analytics para Linux DaemonSet não tem informações secretas (omsagent.yaml)
- O agente Log Analytics para linux DaemonSet yaml file usa informações secretas (omsagent-ds-secrets.yaml) com scripts de geração secreta para gerar os segredos yaml (omsagentsecret.yaml) arquivo.

Pode optar por criar DaemonSets omsagent com ou sem segredos.

**Arquivo padrão OMSagent DaemonSet yaml sem segredos**

- Para o ficheiro yaml do agente de Log Analytics padrão, substitua o `<WSID>` e para o seu `<KEY>` WSID e KEY. Copie o ficheiro para o seu nó mestre e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Arquivo padrão OMSagent DaemonSet yaml com segredos**

1. Para utilizar o agente DoemonSet do Log Analytics usando informações secretas, crie primeiro os segredos.
    1. Copie o script e o ficheiro de modelo secreto e certifique-se de que estão no mesmo diretório.
        - Roteiro gerador secreto - secret-gen.sh
        - modelo secreto - modelo secreto.yaml
    2. Executar o guião, como o exemplo seguinte. O script pede o ID do Espaço de Trabalho do Log Analytics e a Chave Primária e depois de os introduzir, o script cria um ficheiro yaml secreto para que possa executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie o casulo de segredos executando o seguinte:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Para verificar, execute o seguinte:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        A saída deve assemelhar-se:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        A saída deve assemelhar-se:

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

    5. Crie o seu conjunto de daemon omsagent correndo ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Verifique se o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Para Kubernetes, use um script para gerar os segredos yaml file para Workspace ID e Chave Primária para o agente Log Analytics para Linux. Utilize as seguintes informações de exemplo com o [ficheiro yaml omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger as suas informações secretas.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configurar um agente do Log Analytics Windows para Kubernetes

Para o Windows Kubernetes, utiliza um script para gerar o ficheiro Secrets Yaml para o seu ID workspace e chave primária para instalar o agente Log Analytics. Na página [do Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) existem ficheiros que pode utilizar com as suas informações secretas.  Tem de instalar o agente Log Analytics separadamente para os nós de mestre e agente.  

1. Para utilizar o agente DoemonSet do Log Analytics utilizando informações secretas no nó Master, inicie sessão e crie os segredos primeiro.
    1. Copie o script e o ficheiro de modelo secreto e certifique-se de que estão no mesmo diretório.
        - Roteiro gerador secreto - secret-gen.sh
        - modelo secreto - modelo secreto.yaml

    2. Executar o guião, como o exemplo seguinte. O script pede o ID do Espaço de Trabalho do Log Analytics e a Chave Primária e depois de os introduzir, o script cria um ficheiro yaml secreto para que possa executá-lo.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Crie o seu conjunto de daemon omsagent correndo ```kubectl create -f omsagentsecret.yaml```
    4. Para verificar, execute o seguinte:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        A saída deve assemelhar-se:

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

    5. Crie o seu conjunto de daemon omsagent correndo ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Verifique se o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no nó operário, que está a executar o Windows, siga os passos na instalação da secção [e configuure os anfitriões do contentor do Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Use o Helm para implantar o agente Log Analytics no Linux Kubernetes

Para utilizar o leme para implantar o agente Log Analytics no seu ambiente Linux Kubernetes, execute os seguintes passos.

1. Crie o seu conjunto de daemon omsagent correndo ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Os resultados serão semelhantes aos seguintes:

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

3. Pode verificar o estado do omsagent em execução: ```helm status "omsagent"``` e a saída será semelhante à seguinte:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Para mais informações, visite [o Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar os anfitriões dos contentores do Windows

Utilize as informações na secção para instalar e configurar os anfitriões dos contentores do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar agentes do Windows

Antes de instalar agentes em computadores que executam o Windows, tem de configurar o serviço Docker. A configuração permite que o agente Do Windows ou a extensão da máquina virtual do Azure Monitor utilizem a tomada Docker TCP para que os agentes possam aceder remotamente ao daemon do Docker e capturar dados para monitorização.

##### <a name="to-configure-the-docker-service"></a>Para configurar o serviço Docker  

Execute os seguintes comandos PowerShell para ativar o tubo TCP e o tubo nomeado para Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Para obter mais informações sobre a configuração do daemon do Docker utilizada com os recipientes do Windows, consulte [o Motor Docker no Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalar agentes do Windows

Para ativar a monitorização do computador Windows e Hiper-V, instale o Microsoft Monitoring Agent (MMA) em computadores Windows que são anfitriões de contentores. Para computadores que executam o Windows no ambiente no local, consulte [os computadores Connect Windows ao Azure Monitor](../platform/agent-windows.md). Para máquinas virtuais em funcionamento em Azure, conecte-as ao Azure Monitor utilizando a [extensão da máquina virtual](../learn/quick-collect-azurevm.md).

Pode monitorizar os recipientes do Windows em funcionamento no Service Fabric. No entanto, [apenas máquinas virtuais que executam em Azure](../learn/quick-collect-azurevm.md) e [computadores que executam o Windows no seu ambiente no local](../platform/agent-windows.md) são atualmente suportados para o Service Fabric.

Pode verificar se a solução de monitorização do contentor está corretamente definida para o Windows. Para verificar se o pacote de gestão foi descarregado corretamente, procure *ContainerManagement.xxx* . Os ficheiros devem estar na pasta C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.

## <a name="solution-components"></a>Componentes da solução

A partir do portal Azure, navegue até à *Galeria soluções* e adicione a **Solução de Monitorização de Contentores.** Se estiver a utilizar agentes windows, o seguinte pacote de gestão é instalado em cada computador com um agente quando adiciona esta solução. Não é necessária nenhuma configuração ou manutenção para o pacote de gestão.

- *ContainerManagement.xxx* instalado em C:\Ficheiros de programa\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Detalhes da recolha de dados de contentores

A solução de Monitorização do Contentor recolhe várias métricas de desempenho e regista dados de hospedeiros e contentores utilizando agentes que ativa.

Os dados são recolhidos a cada três minutos pelos seguintes tipos de agente.

- [Agente de Log Analytics para Linux](../learn/quick-collect-linux-computer.md)
- [Agente windows](../platform/agent-windows.md)
- [Extensão VM do Log Analytics](../learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Registos de contentores

A tabela que se segue mostra exemplos de registos recolhidos pela solução de monitorização do contentor e dos tipos de dados que aparecem nos resultados da pesquisa de registos.

| Tipo de dados | Tipo de dados na Pesquisa de Registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | Computador, Nome de Objeto, Contra-Natal &#40;%Tempo do processador, Disco lê MB, Disco escreve MB, Uso de Memória MB, Bytes de Receção de Rede, Bytes de Envio de Rede, Utilização de Processadores,&#41; de Rede, ContraValue,TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentores | `ContainerInventory` | TimeGenerated, Computador, nome do recipiente, Nome de Contentores, Imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventário de imagem de contentor | `ContainerImageInventory` | TempoGerado, Computador, Imagem, ImageTag, ImageSize, Virtualsize, Running, Pausa, Parado, Falhado, SourceSystem, ImageID, TotalContainer |
| Tronco de contentor | `ContainerLog` | TimeGenerated, Computador, ID de imagem, nome do recipiente, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Registo de serviço de contentores | `ContainerServiceLog`  | TempoGerado, Computador, TimeOfCommand, Imagem, Comando, SourceSystem, ContainerID |
| Inventário do nó do contentor | `ContainerNodeInventory_CL`| TempoGertado, Computador, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de Kubernetes | `KubePodInventory_CL` | TempoGertado, Computador, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo de contentor | `ContainerProcess_CL` | TempoGertado, Computador, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de Kubernetes | `KubeEvents_CL` | TempoGertado, Computador, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Mensagem |

As etiquetas anexadas aos tipos de dados *PodLabel* são as suas próprias etiquetas personalizadas. As etiquetas PodLabel anexadas mostradas na tabela são exemplos. Assim, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s` , irá `PodLabel_docker_registry_s` diferir no conjunto de dados do seu ambiente e genericamente se assemelhar `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Contentores de monitor
Depois de ter a solução ativada no portal Azure, o azulejo container mostra informações **sumárias** sobre os anfitriões dos contentores e os recipientes que estão a funcionar nos hospedeiros.

![Azulejos de contentores](./media/containers/containers-title.png)

O azulejo mostra uma visão geral de quantos contentores você tem no ambiente e se eles estão falhados, correndo ou parado.

### <a name="using-the-containers-dashboard"></a>Utilização do painel de contentores

Clique no **azulejo de Recipientes.** A partir daí, verá vistas organizadas por:

- **Eventos do Contentor** - Mostra o estado do contentor e os computadores com recipientes falhados.
- **Registos de Contentores** - Mostra um gráfico de ficheiros de registo de contentores gerados ao longo do tempo e uma lista de computadores com o maior número de ficheiros de registo.
- **Eventos Kubernetes** - Mostra um gráfico de eventos kubernetes gerados ao longo do tempo e uma lista das razões pelas quais as cápsulas geraram os eventos. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário de espaço de nomes Kubernetes** - Mostra o número de espaços de nome e pods e mostra a sua hierarquia. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário do nó do recipiente** - Mostra o número de tipos de orquestração utilizados nos nós/hospedeiros dos contentores. Os nós/anfitriões do computador também estão listados pelo número de contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário de Imagens de Contentores** - Mostra o número total de imagens de contentores utilizadas e o número de tipos de imagem. O número de imagens também está listado pela etiqueta de imagem.
- **Estado dos recipientes** - Mostra o número total de nós de contentores/computadores hospedeiros que têm recipientes em funcionamento. Os computadores também estão listados pelo número de anfitriões em execução.
- **Processo do Contentor** - Mostra um gráfico de linha de processos de contentores que se arrastam ao longo do tempo. Os contentores também são listados por comando/processo de execução dentro de contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Desempenho do CPU do contentor** - Mostra um gráfico de linha da utilização média do CPU ao longo do tempo para nós/anfitriões de computador. Também lista os nós/anfitriões do computador com base na utilização média do CPU.
- **Desempenho da memória do recipiente** - Mostra um gráfico de linha de utilização da memória ao longo do tempo. Também lista a utilização da memória do computador com base no nome da instância.
- **Desempenho do Computador** - Mostra gráficos de linha da percentagem do desempenho do CPU ao longo do tempo, por cento do uso da memória ao longo do tempo, e megabytes de espaço livre de disco ao longo do tempo. Você pode pairar sobre qualquer linha em um gráfico para ver mais detalhes.

Cada área do painel é uma representação visual de uma pesquisa que é executada em dados recolhidos.

![Screenshot que mostra um dashboard para visualizar os dados recolhidos. ](./media/containers/containers-dash01.png)

![Painel de contentores](./media/containers/containers-dash02.png)

Na área **do Estado do Contentor,** clique na área superior, como mostrado abaixo.

![Estado dos contentores](./media/containers/containers-status.png)

O Log Analytics abre, apresentando informações sobre o estado dos seus contentores.

![Log Analytics para contentores](./media/containers/containers-log-search.png)

A partir daqui, pode editar a consulta de pesquisa para modificá-la para encontrar a informação específica que lhe interessa. Para obter mais informações sobre consultas de registo, consulte [as consultas de registo no Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Resolução de problemas encontrando um recipiente falhado

Log Analytics marca um recipiente como **Falhado** se tiver saído com um código de saída não zero. Pode ver uma visão geral dos erros e falhas ambientais na área **dos Recipientes Falhados.**

### <a name="to-find-failed-containers"></a>Para encontrar recipientes falhados

1. Clique na área **de Estado do Contentor.**  
   ![estado dos contentores](./media/containers/containers-status.png)
2. O Log Analytics abre e exibe o estado dos seus recipientes, semelhante ao seguinte.  
   ![estado de contentores](./media/containers/containers-log-search.png)
3. Expandir a linha Falhada e clicar + para adicionar os seus critérios à consulta. Em seguida, comente a linha Resumo na consulta.
   ![Screenshot que mostra a linha que deve ser comentada.](./media/containers/containers-state-failed-select.png)  
1. Executar a consulta e, em seguida, expandir uma linha nos resultados para ver o ID de imagem.  
   ![Screenshot que mostra como ver o ID de imagem.](./media/containers/containers-state-failed.png)  
1. Digite o seguinte na consulta de registo. `ContainerImageInventory | where ImageID == <ImageID>` para ver detalhes sobre a imagem, como o tamanho da imagem e o número de imagens paradas e falhadas.  
   ![recipientes falhados](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Registos de consulta para dados de contentores

Quando está a resolver um erro específico, pode ajudar a ver onde está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para devolver as informações que deseja.

- **ContainerImageInventory** – Utilize este tipo quando estiver a tentar encontrar informações organizadas por imagem e ver informações de imagem, tais como IDs de imagem ou tamanhos.
- **ContainerInventory** – Use este tipo quando quiser informações sobre a localização do contentor, quais são os seus nomes e que imagens estão a ser executadas.
- **ContainerLog** – Utilize este tipo quando pretender encontrar informações e entradas específicas de registo de erros.
- **ContainerNodeInventory_CL**  Utilize este tipo quando quiser as informações sobre o hospedeiro/nó onde residem os recipientes. Fornece-lhe a versão Docker, tipo de orquestração, armazenamento e informações de rede.
- **ContainerProcess_CL** Utilize este tipo para ver rapidamente o processo a funcionar dentro do recipiente.
- **ContainerServiceLog** – Utilize este tipo quando estiver a tentar encontrar informações sobre o rasto de auditoria para o daemon do Docker, tais como iniciar, parar, excluir ou puxar comandos.
- **KubeEvents_CL**  Use este tipo para ver os eventos de Kubernetes.
- **KubePodInventory_CL**  Utilize este tipo quando quiser compreender a informação da hierarquia do cluster.

### <a name="to-query-logs-for-container-data"></a>Para consultar registos de dados de contentores

* Escolha uma imagem que sabe que falhou recentemente e encontre os registos de erro para a mesmo. Comece por encontrar um nome de recipiente que esteja a executar essa imagem com uma pesquisa **de ContainerInventory.** Por exemplo, procurar `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Pesquisa de recipientes Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha nos resultados para ver detalhes desse recipiente.

## <a name="example-log-queries"></a>Consultas de registo de exemplo

É frequentemente útil construir consultas começando com um ou dois exemplos e, em seguida, modificá-los para se adaptar ao seu ambiente. Como ponto de partida, pode experimentar a área **de PERGUNTAS A amostra** na extrema direita da página de solução, para ajudá-lo a construir consultas mais avançadas.

![Consultas de contentores](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Salvar consultas de registo

As consultas de poupança são uma característica padrão no Azure Monitor. Ao salvá-los, terá aqueles que achou úteis para uso futuro.

Depois de criar uma consulta que considera útil, guarde-a clicando em **Favoritos** no topo da página 'Procurar registo'. Em seguida, pode aceder-lhe facilmente mais tarde a partir da página **My Dashboard.**

## <a name="next-steps"></a>Passos seguintes

[Registos de consulta](../log-query/log-query-overview.md) para visualizar registos de dados detalhados do contentor.

