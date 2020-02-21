---
title: Solução de monitorização de contentores no Monitor Azure [ Monitor De contentores] Microsoft Docs
description: A solução de monitorização de contentores no Monitor Azure ajuda-o a visualizar e gerir os seus anfitriões de contentores Docker e Windows num único local.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 7dab80f901304a727b75c7861c5d865fee03bec3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482995"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitorização de contentores no Monitor Azure

![Símbolo de contentores](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e utilizar a solução de monitorização de contentores no Monitor Azure, que o ajuda a visualizar e gerir os anfitriões de contentores Docker e Windows num único local. O docker é um sistema de Virtualização de software utilizado para criar contentores que automatizam a implementação de software para infra-estrutura de TI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra os contentores estão em execução, que imagem de contentor estarem a ser executados e, em que os contentores estão em execução. Pode ver informações detalhadas de auditoria que mostra os comandos utilizados com contentores. Além disso, pode resolver contentores ao visualizar e procurar registos centralizados sem ter de ver remotamente os anfitriões de Docker ou do Windows. Pode encontrar os contentores que podem ser desnecessárias e consumindo recursos em excesso num anfitrião. Além disso, pode ver centralizada de CPU, memória, armazenamento e informações de utilização e desempenho de rede para contentores. Em computadores com Windows, pode centralizar e comparar os registos do Windows Server, Hyper-V e contentores do Docker. A solução suporta orquestradores do contentor seguinte:

- Docker Swarm
- DC/OS
- Utilizar o Kubernetes
- Service Fabric
- Red Hat OpenShift

Se tiver contentores implantados em Tecido de [Serviço Azure,](../../service-fabric/service-fabric-overview.md)recomendamos que a [solução Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) e esta solução incluam a monitorização de eventos de cluster. Antes de ativar a solução Service Fabric, reveja [Utilizando a solução Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) para compreender o que fornece e como utilizá-la.

Se estiver interessado em monitorizar o desempenho das suas cargas de trabalho implantadas em ambientes Kubernetes hospedados no Serviço Azure Kubernetes (AKS), consulte [monitor Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md). A solução de monitorização do contentor não suporta a monitorização dessa plataforma.  

O diagrama seguinte mostra as relações entre vários hospedeiros de contentores e agentes com o Monitor Azure.

![Diagrama de contentores](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos de sistema e as plataformas suportadas

Antes de começar, reveja os detalhes seguintes para verificar que cumpre os pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Solução de monitorização do contentor de suporte para a plataforma do Orchestrator do Docker e SO

A tabela seguinte descreve a orquestração docker e o suporte de monitorização do sistema operativo de inventário de contentores, desempenho e registos com o Monitor Azure.   

| | ACS | Linux | Windows | Contentor<br>Inventário | Image<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registar | Contentor<br>Registar |
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

1. Adicione a solução de monitorização de contentores ao seu espaço de trabalho Log Analytics a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou utilizando o processo descrito em [Adicionar soluções de monitorização a partir da Galeria soluções](../../azure-monitor/insights/solutions.md).

2. Instalar e utilizar o Docker com um agente do Log Analytics. Com base no seu sistema operativo e o orchestrator do Docker, pode utilizar os seguintes métodos para configurar o agente.
   - Para anfitriões autónomos:
     - Nos sistemas operativos Linux suportados, instale e execute o Docker e, em seguida, instale e configure o [agente Log Analytics para o Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - No CoreOS, não é possível executar o agente do Log Analytics para Linux. Em vez disso, executar uma versão em contentores do agente do Log Analytics para Linux. Reveja os anfitriões de contentores Linux, incluindo os anfitriões de contentores CoreOS ou Azure Government Linux, incluindo o CoreOS, se estiver a trabalhar com contentores em Azure Government Cloud.
     - No Windows Server 2016 e Windows 10, instale o Motor E cliente Docker e, em seguida, conecte um agente para recolher informações e enviá-lo para o Monitor Azure. Reveja [Instalar e configurar os anfitriões](#install-and-configure-windows-container-hosts) do recipiente Windows se tiver um ambiente Windows.
   - Para orquestração de vários anfitriões do Docker:
     - Se tiver um ambiente OpenShift de chapéu vermelho, reveja o Configure um agente de Log Analytics para o Red Hat OpenShift.
     - Se tiver um cluster do Kubernetes com o Azure Container Service:
       - Reveja [um agente Linux de Log Analytics para Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Reveja [um agente do Windows de Log Analytics para Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Reveja o Helm para implantar o agente Log Analytics em Linux Kubernetes.
     - Se tiver um cluster azure container Service DC/OS, saiba mais no Monitor um cluster de Serviço de [Contentores Azure DC/OS com o Monitor Azure](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Se tiver um ambiente de modo Docker Swarm, saiba mais na Configure um agente de Log Analytics para Docker Swarm.
     - Se tiver um cluster de Tecido de Serviço, saiba mais em [recipientes monitor com o Monitor Azure](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Reveja o artigo do [Docker Engine no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obter informações adicionais sobre como instalar e configurar os seus Motores Docker em computadores que executam o Windows.

> [!IMPORTANT]
> O Docker deve estar a funcionar **antes** de instalar o [agente Log Analytics para o Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) nos seus anfitriões de contentores. Se já tiver instalado o agente antes de instalar o Docker, terá de reinstalar o agente do Log Analytics para Linux. Para mais informações sobre o Docker, consulte o site do [Docker.](https://www.docker.com)

### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar os anfitriões de contentor do Linux

Depois de instalar o Docker, utilize as seguintes definições para o anfitrião de contentor para configurar o agente para utilização com o Docker. Primeiro precisa o ID de área de trabalho do Log Analytics e a chave, que pode ser encontrado no portal do Azure. No seu espaço de trabalho, clique em **Quick Start** > **Computadores** para visualizar o seu **ID workspace** e **chave primária**.  Copie e cole ambos no seu editor favorito.

**Para todos os anfitriões de contentores Linux, exceto o CoreOS:**

- Para obter mais informações e passos sobre como instalar o agente Log Analytics para o Linux, consulte a visão geral do [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Para todos os anfitriões de contentores Linux, incluindo o CoreOS:**

Inicie o contentor que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os anfitriões de contentores do Governo Azure Linux, incluindo o CoreOS:**

Inicie o contentor que pretende monitorizar. Modificar e utilize o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Mudar de usar um agente Linux instalado para um num recipiente**

Se tiver utilizado o agente diretamente instalados e pretende utilizar em vez disso, um agente em execução num contentor, primeiro tem de remover o agente do Log Analytics para Linux. Consulte [desinstalar o agente Log Analytics para](../../azure-monitor/learn/quick-collect-linux-computer.md) o Linux para perceber como desinstalar o agente com sucesso.  

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

* [Instale o agente Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) diretamente em cada nó OpenShift  
* [Ativar a extensão VM de Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em cada nó OpenShift residente em Azure  
* Instale o agente Log Analytics como um conjunto de daemon OpenShift  

Nesta secção, vamos abordar os passos necessários para instalar o agente Log Analytics como um conjunto de daemon OpenShift.  

1. Inscreva-se no nó master OpenShift e copie o ficheiro yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o seu nó principal e modifique o valor com o seu ID de log analytics workspace e com a sua Chave Primária.
2. Executar os seguintes comandos para criar um projeto para o Monitor Azure e definir a conta de utilizador.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Para implementar o conjunto de daemon, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

4. Certifique-se de que está configurado e funcionar corretamente, escreva o seguinte:

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

1. Inscreva-se no nó principal openShift e copie o ficheiro yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o script gerador secreto [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do GitHub.  Este script irá gerar o ficheiro yaml de segredos para ID de área de trabalho do Log Analytics e a chave primária proteger a sua secrete informações.  
2. Executar os seguintes comandos para criar um projeto para o Monitor Azure e definir a conta de utilizador. O script gerador secreto pede o seu ID de workspace de Log Analytics `<WSID>` e chave primária `<KEY>` e após a conclusão, cria o ficheiro ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implemente o ficheiro de segredo executando o seguinte:

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

5. Implemente o ficheiro de yaml de conjunto de daemon de agente do Log Analytics ao executar o seguinte:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configurar um agente Linux do Log Analytics para o Kubernetes

Para Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para o seu ID de área de trabalho e a chave primária instalar o agente do Log Analytics para Linux. Na página [Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) existem ficheiros que pode utilizar com ou sem as suas informações secretas.

- O agente de Log Analytics predefinido para Linux DaemonSet não tem informações secretas (omsagent.yaml)
- O agente de Log Analytics para o ficheiro yaml de Linux DaemonSet utiliza informações secretas (omsagent-ds-secrets.yaml) com scripts de geração secreta para gerar o ficheiro yaml (omsagentsecret.yaml) de segredos.

Pode optar por criar omsagent DaemonSets com ou sem segredos.

**Ficheiro yaml padrão OMSagent DaemonSet sem segredos**

- Para o ficheiro yaml do agente de Log Analytics predefinido DaemonSet, substitua a `<WSID>` e `<KEY>` ao seu WSID e KEY. Copie o ficheiro para o nó principal e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Ficheiro yaml padrão OMSagent DaemonSet com segredos**

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

    5. Crie o seu conjunto de daemon omsagent executando ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Certifique-se de que o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Para Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para ID de área de trabalho e a chave primária para o agente do Log Analytics para Linux. Utilize as seguintes informações de exemplo com o [ficheiro yaml omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger as suas informações secretas.

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

Para Windows Kubernetes, utilize um script para gerar o ficheiro yaml de segredos para o seu ID de área de trabalho e a chave primária instalar o agente do Log Analytics. Na página [Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) existem ficheiros que pode utilizar com as suas informações secretas.  Tem de instalar o agente do Log Analytics separadamente para os nós de agente e mestras.  

1. Para utilizar o agente do Log Analytics DaemonSet com informações secretas no mestre de nó, inicie sessão e criar primeiro os segredos.
    1. Copie o script e o ficheiro de modelo secreta e certificar-se de que estão no mesmo diretório.
        - Gerar script - gen.sh segredo do segredo
        - modelo secreto - template.yaml do segredo

    2. Execute o script, semelhante ao seguinte exemplo. O script pede-lhe o ID de área de trabalho do Log Analytics e a chave primária e depois de introduzi-los, o script cria um ficheiro yaml secreta para que pode executá-lo.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Crie o seu conjunto de daemon omsagent executando ```kubectl create -f omsagentsecret.yaml```
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

    5. Crie o seu conjunto de daemon omsagent executando ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Certifique-se de que o agente do Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no Nó de Trabalhador, que está a executar o Windows, siga os passos na secção [instalar e configurar](#install-and-configure-windows-container-hosts)os anfitriões do recipiente Windows .

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Utilizar o Helm para implementar o agente do Log Analytics no Linux Kubernetes

Para utilizar o helm para implementar o agente do Log Analytics no seu ambiente do Linux Kubernetes, execute os seguintes passos.

1. Crie o seu conjunto de daemon omsagent executando ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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

3. Pode verificar o estado do agente omsagent executando: ```helm status "omsagent"``` e a saída será semelhante à seguinte:

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
   
    Para mais informações, visite o [Gráfico de Leme da Solução de Contentores](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar os anfitriões de contentores do Windows

Utilize as informações na seção instalar e configurar os anfitriões de contentores do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar os agentes do Windows

Antes de instalar agentes em computadores que executam o Windows, terá de configurar o serviço de Docker. A configuração permite ao agente Windows ou à extensão da máquina virtual Do Monitor Azure utilizar a tomada Docker TCP para que os agentes possam aceder remotamente ao daemon do Docker e capturar dados para monitorização.

##### <a name="to-configure-the-docker-service"></a>Para configurar o serviço Docker  

Execute os seguintes comandos PowerShell para ativar o tubo TCP e o tubo nomeado para o Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Para obter mais informações sobre a configuração do daemon Dono Docker utilizada com os Recipientes windows, consulte [o Motor Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instale os agentes do Windows

Para ativar a monitorização de contentores do Windows e o Hyper-V, instale o Microsoft Monitoring Agent (MMA) em computadores Windows que são anfitriões de contentor. Para computadores que executem o Windows no seu ambiente no local, consulte [connect Windows computers to Azure Monitor](../../azure-monitor/platform/agent-windows.md). Para máquinas virtuais em funcionamento em Azure, ligue-as ao Monitor Azure utilizando a [extensão](../../azure-monitor/learn/quick-collect-azurevm.md)da máquina virtual .

Pode monitorizar contentores do Windows em execução no Service Fabric. No entanto, apenas [as máquinas virtuais em funcionamento em Azure](../../azure-monitor/learn/quick-collect-azurevm.md) e [computadores que executam o Windows no seu ambiente no local](../../azure-monitor/platform/agent-windows.md) estão atualmente suportadas para o Tecido de Serviço.

Pode verificar que a solução de monitorização de contentores está corretamente definida para Windows. Para verificar se o pacote de gestão foi descarregado corretamente, procure *ContainerManagement.xxx*. Os ficheiros devem ser na pasta C:\Program Files\Microsoft Monitoring Agent\Agent\Health serviço State\Management Packs.

## <a name="solution-components"></a>Componentes da solução

A partir do portal Azure, navegue até à *Galeria soluções* e adicione a Solução de Monitorização de **Contentores.** Se estiver a utilizar agentes do Windows, o seguinte pacote de gestão é instalado em cada computador com um agente quando adicionar esta solução. Nenhuma configuração ou manutenção é necessária para o pacote de gestão.

- *ContainerManagement.xxx* instalado si em C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Detalhes de recolha de dados de contentor

A solução de monitorização de contentores recolhe diversos dados de registo e métricas de desempenho de anfitriões de contentor e contentores com os agentes que ativar.

Dados são recolhidos a cada três minutos pelos seguintes tipos de agente.

- [Agente de Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agente windows](../../azure-monitor/platform/agent-windows.md)
- [Extensão VM de Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

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

As etiquetas anexadas aos tipos de dados *PodLabel* são as suas próprias etiquetas personalizadas. As etiquetas de PodLabel anexadas, mostradas na tabela são exemplos. Assim, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` diferirá no conjunto de dados do seu ambiente e assemelhar-se-á genericamente `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Contentores de monitor
Depois de ter a solução ativada no portal Azure, o azulejo dos **Contentores** mostra informações sumárias sobre os seus anfitriões de contentores e os contentores que estão a funcionar nos anfitriões.

![Mosaico de contentores](./media/containers/containers-title.png)

O mosaico mostra uma visão geral dos contentores de quantos tiver no ambiente e se estiver falha, em execução ou parado.

### <a name="using-the-containers-dashboard"></a>Utilizar o dashboard de contentores

Clique no azulejo dos **contentores.** A partir daí, verá os modos de exibição organizados por:

- **Eventos de contentores** - Mostra o estado do contentor e os computadores com contentores falhados.
- **Registos de contentores** - Mostra um gráfico de ficheiros de registo de contentores gerados ao longo do tempo e uma lista de computadores com o maior número de ficheiros de registo.
- **Eventos Kubernetes** - Mostra um gráfico de eventos kubernetes gerados ao longo do tempo e uma lista das razões pelas quais as cápsulas geraram os eventos. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Kubernetes Namespace Inventory** - Mostra o número de espaços de nome e pods e mostra a sua hierarquia. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário do Nó** do Recipiente - Mostra o número de tipos de orquestração utilizados em nós/anfitriões de contentores. Os nós de computador/anfitriões são também apresentados pelo número de contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário de Imagens** de Contentores - Mostra o número total de imagens de contentores utilizadas e o número de tipos de imagem. O número de imagens também está listado pela tag de imagem.
- **Estado dos contentores** - Mostra o número total de nós de contentores/computadores hospedeiros que têm contentores em funcionamento. Computadores também são listados pelo número de anfitriões a executar.
- **Processo de Contentores** - Mostra um gráfico de linha de processos de contentores que correm ao longo do tempo. Contentores são também apresentados ao executar o comando/processo dentro de contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- Desempenho do **CPU do recipiente** - Mostra um gráfico de linha da utilização média do CPU ao longo do tempo para nós/anfitriões de computador. Também listas os nós de computador/anfitriões com base em média de utilização da CPU.
- **Desempenho da memória** do recipiente - Mostra um gráfico de linha de utilização da memória ao longo do tempo. Também apresenta a utilização com base no nome de instância de memória do computador.
- **Computer Performance** - Mostra gráficos de linha da percentagem de desempenho do CPU ao longo do tempo, por cento do uso da memória ao longo do tempo, e megabytes de espaço de disco livre ao longo do tempo. Pode pairar o rato sobre qualquer linha num gráfico para ver mais detalhes.

Cada área do dashboard é uma representação visual de uma pesquisa que é executada sobre os dados recolhidos.

![Dashboard de contentores](./media/containers/containers-dash01.png)

![Dashboard de contentores](./media/containers/containers-dash02.png)

Na área do Estado do **Contentor,** clique na área superior, como mostrado abaixo.

![Estado dos contentores](./media/containers/containers-status.png)

O Log Analytics abre, exibindo informações sobre o estado dos seus contentores.

![Log Analytics para recipientes](./media/containers/containers-log-search.png)

A partir daqui, pode editar a consulta de pesquisa de modificá-lo para encontrar as informações específicas que está interessado. Para mais informações sobre consultas de registo, consulte consultas de [registo no Monitor Azure](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Resolver problemas ao detetar um contentor com falhas

O Log Analytics marca um recipiente como **Falhado** se tiver saído com um código de saída não zero. Pode ver uma visão geral dos erros e falhas no ambiente na área de **Contentores Falhados.**

### <a name="to-find-failed-containers"></a>Para localizar os contentores falhados

1. Clique na área do Estado do **Contentor.**  
   estado dos contentores ![](./media/containers/containers-status.png)
2. O Log Analytics abre e exibe o estado dos seus recipientes, semelhante ao seguinte.  
   ![Estado de contentores](./media/containers/containers-log-search.png)
3. Expanda a linha Failed e clique + para adicionar os seus critérios à consulta. Em seguida, comentar a linha Resumo na consulta.
   ![contentores falhados](./media/containers/containers-state-failed-select.png)  
1. Executar a consulta e, em seguida, expandir uma linha nos resultados para ver o ID de imagem.  
   ![recipientes falhados](./media/containers/containers-state-failed.png)  
1. Digite o seguinte na consulta de registo. `ContainerImageInventory | where ImageID == <ImageID>` ver detalhes sobre a imagem, como o tamanho da imagem e o número de imagens paradas e falhadas.  
   ![contentores falhados](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Registos de consulta para dados de contentores

Quando estiver a resolver problemas um erro específico, pode ajudar a verificar em que está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para retornar as informações que pretende.

- **ContainerImageInventário** – Utilize este tipo quando estiver a tentar encontrar informações organizadas por imagem e a visualizar informações de imagem, como iDs de imagem ou tamanhos.
- **ContainerInventário** – Utilize este tipo quando quiser informações sobre a localização do contentor, quais são os seus nomes e quais as imagens que estão a executar.
- **ContainerLog** – Utilize este tipo quando pretender encontrar informações e entradas específicas de registo de erros.
- **ContainerNodeInventory_CL**  Utilize este tipo quando pretender a informação sobre o hospedeiro/nó onde residem os contentores. Ele fornece Docker versão, tipo de orquestração, armazenamento e informações de rede.
- **ContainerProcess_CL** Utilize este tipo para ver rapidamente o processo a funcionar dentro do recipiente.
- **ContainerServiceLog** – Utilize este tipo quando estiver a tentar encontrar informações de rasto de auditoria para o daemon do Docker, tais como iniciar, parar, excluir ou puxar comandos.
- **KubeEvents_CL**  Use este tipo para ver os eventos kubernetes.
- **KubePodInventory_CL**  Utilize este tipo quando quiser compreender a informação da hierarquia do cluster.


### <a name="to-query-logs-for-container-data"></a>Para consultar os registos dos dados do contentor

* Escolha uma imagem que sabe que falhou recentemente e localizar os registos de erros para ele. Comece por encontrar um nome de recipiente que esteja a executar essa imagem com uma pesquisa **de ContainerInventory.** Por exemplo, procure `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Procurar contentores Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha nos resultados para ver detalhes para o recipiente.

## <a name="example-log-queries"></a>Consultas de registo de exemplo

Muitas vezes é útil criar consultas, começando com um ou dois exemplos e, em seguida, modificá-los de acordo com seu ambiente. Como ponto de partida, você pode experimentar com a área **de Perguntas de Amostra** para ajudá-lo a construir consultas mais avançadas.

![Consultas de contentores](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Guardar consultas de registo

Guardar consultas é uma característica padrão no Monitor Azure. Salvando-los, terá que encontrar útil útil para utilização futura.

Depois de criar uma consulta que ache útil, guarde-a clicando em **Favoritos** no topo da página de Pesquisa de Registos. Depois, pode aceder-lhe facilmente mais tarde a partir da página **My Dashboard.**

## <a name="next-steps"></a>Passos Seguintes

[Registos](../log-query/log-query-overview.md) de consultas para visualizar registos de dados detalhados do recipiente.
