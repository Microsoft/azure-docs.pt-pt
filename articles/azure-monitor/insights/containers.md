---
title: Solução de monitorização de contentores no Monitor Azure [ Monitor De contentores] Microsoft Docs
description: A solução de monitorização de contentores no Monitor Azure ajuda-o a visualizar e gerir os seus anfitriões de contentores Docker e Windows num único local.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77664699"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitorização de contentores no Monitor Azure

![Símbolo de recipientes](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e utilizar a solução de monitorização de contentores no Monitor Azure, que o ajuda a visualizar e gerir os anfitriões de contentores Docker e Windows num único local. Docker é um sistema de virtualização de software usado para criar recipientes que automatizam a implementação de software para a sua infraestrutura de TI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra quais os contentores que estão a funcionar, que imagem de contentor estão a funcionar e onde os contentores estão a funcionar. Pode ver informações detalhadas de auditoria mostrando comandos utilizados com contentores. E, você pode resolver os recipientes visualizando e pesquisando registos centralizados sem ter que ver remotamente os anfitriões do Docker ou windows. Você pode encontrar recipientes que podem ser barulhentos e consumir recursos em excesso em um hospedeiro. E, pode ver CPU centralizado, memória, armazenamento e utilização da rede e informações de desempenho para contentores. Nos computadores que executam o Windows, pode centralizar e comparar registos dos recipientes Windows Server, Hyper-V e Docker. A solução suporta os seguintes orquestradores de contentores:

- Docker Swarm
- DC/OS
- Utilizar o Kubernetes
- Service Fabric
- Red Hat OpenShift

Se tiver contentores implantados em Tecido de [Serviço Azure,](../../service-fabric/service-fabric-overview.md)recomendamos que a [solução Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) e esta solução incluam a monitorização de eventos de cluster. Antes de ativar a solução Service Fabric, reveja [Utilizando a solução Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) para compreender o que fornece e como utilizá-la.

Se estiver interessado em monitorizar o desempenho das suas cargas de trabalho implantadas em ambientes Kubernetes hospedados no Serviço Azure Kubernetes (AKS), consulte [monitor Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md). A solução de monitorização do contentor não suporta a monitorização dessa plataforma.  

O diagrama seguinte mostra as relações entre vários hospedeiros de contentores e agentes com o Monitor Azure.

![Diagrama de recipientes](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos do sistema e plataformas suportadas

Antes de começar, reveja os seguintes dados para verificar se cumpre os pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Suporte à solução de monitorização de contentores para docker orchestrator e plataforma OS

A tabela seguinte descreve a orquestração docker e o suporte de monitorização do sistema operativo de inventário de contentores, desempenho e registos com o Monitor Azure.   

| | ACS | Linux | Windows | Contentor<br>Inventário | Imagem<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registar | Contentor<br>Registar |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Utilizar o Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosfera<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Enxame | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Recursos de infraestrutura | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Chapéu Vermelho Aberto<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autónomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Servidor Linux<br>(autónomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Versões Docker suportadas no Linux

- Estivador 1.11 a 1.13
- Docker CE e EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 distribuições linux suportadas como hospedeiros de contentores

- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS (estável)
- Amazon Linux 2016.09.0
- abreSUSE 13.2
- abre SUSE LEAP 42.2
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Plataforma de contentores OpenShift (OCP) 3.4 e 3.5
- ACS Mesosfera DC/OS 1.7.3 a 1.8.8
- ACS Kubernetes 1.4.5 a 1.6
    - Eventos kubernetes, inventário kubernetes e processos de contentores são suportados apenas com a versão 1.4.1-45 e mais tarde do agente Log Analytics para linux
- Enxame ACS Docker

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows suportado

- Windows Server 2016
- Edição de Aniversário do Windows 10 (Profissional ou Empresarial)

### <a name="docker-versions-supported-on-windows"></a>Versões Docker suportadas no Windows

- Estivador 1.12 e 1.13
- Docker 17.03.0 e mais tarde

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

Utilize as seguintes informações para instalar e configurar a solução.

1. Adicione a solução de monitorização de contentores ao seu espaço de trabalho Log Analytics a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou utilizando o processo descrito em [Adicionar soluções de monitorização a partir da Galeria soluções](../../azure-monitor/insights/solutions.md).

2. Instale e utilize o Docker com um agente Log Analytics. Com base no seu sistema operativo e no orquestrador Dono do Docker, pode utilizar os seguintes métodos para configurar o seu agente.
   - Para anfitriões autónomos:
     - Nos sistemas operativos Linux suportados, instale e execute o Docker e, em seguida, instale e configure o [agente Log Analytics para o Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - No CoreOS, não é possível executar o agente Log Analytics para o Linux. Em vez disso, executa uma versão contentorizada do agente Log Analytics para o Linux. Reveja os anfitriões de contentores Linux, incluindo os anfitriões de contentores CoreOS ou Azure Government Linux, incluindo o CoreOS, se estiver a trabalhar com contentores em Azure Government Cloud.
     - No Windows Server 2016 e Windows 10, instale o Motor E cliente Docker e, em seguida, conecte um agente para recolher informações e enviá-lo para o Monitor Azure. Reveja [Instalar e configurar os anfitriões](#install-and-configure-windows-container-hosts) do recipiente Windows se tiver um ambiente Windows.
   - Para a orquestração multi-anfitriãde Docker:
     - Se tiver um ambiente OpenShift de chapéu vermelho, reveja o Configure um agente de Log Analytics para o Red Hat OpenShift.
     - Se tiver um cluster Kubernetes utilizando o Serviço de Contentores Azure:
       - Reveja [um agente Linux de Log Analytics para Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Reveja [um agente do Windows de Log Analytics para Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Reveja o Helm para implantar o agente Log Analytics em Linux Kubernetes.
     - Se tiver um cluster azure container Service DC/OS, saiba mais no Monitor um cluster de Serviço de [Contentores Azure DC/OS com o Monitor Azure](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Se tiver um ambiente de modo Docker Swarm, saiba mais na Configure um agente de Log Analytics para Docker Swarm.
     - Se tiver um cluster de Tecido de Serviço, saiba mais em [recipientes monitor com o Monitor Azure](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Reveja o artigo do [Docker Engine no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obter informações adicionais sobre como instalar e configurar os seus Motores Docker em computadores que executam o Windows.

> [!IMPORTANT]
> O Docker deve estar a funcionar **antes** de instalar o [agente Log Analytics para o Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) nos seus anfitriões de contentores. Se já instalou o agente antes de instalar o Docker, tem de reinstalar o agente Log Analytics para o Linux. Para mais informações sobre o Docker, consulte o site do [Docker.](https://www.docker.com)

### <a name="install-and-configure-linux-container-hosts"></a>Instale e configure os anfitriões de contentores Linux

Depois de ter instalado o Docker, utilize as seguintes definições para o seu hospedeiro de contentores para configurar o agente para utilização com o Docker. Primeiro precisa do id e da chave do espaço de trabalho Log Analytics, que pode encontrar no portal Azure. No seu espaço de trabalho, clique em**Computadores** **Quick Start** > para visualizar o id do **espaço de trabalho** e a **chave primária**.  Copie e cole ambos no seu editor favorito.

**Para todos os anfitriões de contentores Linux, exceto o CoreOS:**

- Para obter mais informações e passos sobre como instalar o agente Log Analytics para o Linux, consulte a visão geral do [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Para todos os anfitriões de contentores Linux, incluindo o CoreOS:**

Ligue o recipiente que pretende monitorizar. Modificar e utilizar o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os anfitriões de contentores do Governo Azure Linux, incluindo o CoreOS:**

Ligue o recipiente que pretende monitorizar. Modificar e utilizar o seguinte exemplo:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Mudar de usar um agente Linux instalado para um num recipiente**

Se usou previamente o agente instalado diretamente e pretende utilizar um agente que funciona num recipiente, primeiro deve remover o agente Log Analytics para o Linux. Consulte [desinstalar o agente Log Analytics para](../../azure-monitor/learn/quick-collect-linux-computer.md) o Linux para perceber como desinstalar o agente com sucesso.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configure um agente de Log Analytics para Docker Swarm

Pode dirigir o agente Log Analytics como um serviço global no Docker Swarm. Utilize as seguintes informações para criar um serviço de agente Log Analytics. Tem de fornecer o id do espaço de trabalho de Análise de Log Analytics e a chave primária.

- Corra o seguinte no nó principal.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Segredos seguros para Docker Swarm

Para Docker Swarm, uma vez criado o segredo para o Id do Espaço de Trabalho e chave primária, utilize as seguintes informações para criar as suas informações secretas.

1. Corra o seguinte no nó principal.

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

3. Execute o seguinte comando para montar os segredos para o agente de Log Analytics contentorizado.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Configure um agente de Log Analytics para O OpenShift do Chapéu Vermelho

Existem três formas de adicionar o agente Log Analytics ao Red Hat OpenShift para começar a recolher dados de monitorização de contentores.

* [Instale o agente Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) diretamente em cada nó OpenShift  
* [Ativar a extensão VM de Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em cada nó OpenShift residente em Azure  
* Instale o agente Log Analytics como um conjunto de daemon OpenShift  

Nesta secção cobrimos os passos necessários para instalar o agente Log Analytics como um conjunto de daemon OpenShift.  

1. Inscreva-se no nó master OpenShift e copie o ficheiro yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o seu nó principal e modifique o valor com o seu ID de log analytics workspace e com a sua Chave Primária.
2. Executar os seguintes comandos para criar um projeto para o Monitor Azure e definir a conta de utilizador.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Para implantar o conjunto daemon, executar o seguinte:

    `oc create -f ocp-omsagent.yaml`

4. Para verificar se está configurado e a funcionar corretamente, escreva o seguinte:

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

Se pretender utilizar segredos para proteger o ID do espaço de trabalho do Log Analytics e a chave primária ao utilizar o ficheiro yaml do agente daemon-set de Log Analytics, execute os seguintes passos.

1. Inscreva-se no nó principal openShift e copie o ficheiro yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o script gerador secreto [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do GitHub.  Este script irá gerar os segredos do ficheiro yaml para log analytics workspace ID e chave primária para garantir a sua informação secreta.  
2. Executar os seguintes comandos para criar um projeto para o Monitor Azure e definir a conta de utilizador. O script gerador secreto pede o seu `<WSID>` ID de `<KEY>` espaço de trabalho de Log Analytics e chave primária e, após a conclusão, cria o ficheiro ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implementar o ficheiro secreto executando o seguinte:

    `oc create -f ocp-secret.yaml`

4. Verificar a colocação executando o seguinte:

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

5. Implemente o ficheiro yaml do agente daemon-set-daemon-set de log analytics executando o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verificar a colocação executando o seguinte:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configure um agente Linux log analytics para Kubernetes

Para a Kubernetes, utiliza um script para gerar os segredos do ficheiro yaml para o seu ID workspace e chave primária para instalar o agente Log Analytics para o Linux. Na página [Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) existem ficheiros que pode utilizar com ou sem as suas informações secretas.

- O agente Desanálise de Registo padrão para Linux DaemonSet não tem informações secretas (omsagent.yaml)
- O agente Log Analytics do ficheiro yaml Linux DaemonSet usa informações secretas (omsagent-ds-secrets.yaml) com scripts de geração secreta para gerar os segredos do ficheiro yaml (omsagentsecret.yaml).

Pode optar por criar DaemonSets omsagent com ou sem segredos.

**Ficheiro yaml padrão OMSagent DaemonSet sem segredos**

- Para o ficheiro yaml do agente de Log `<WSID>` `<KEY>` Analytics predefinido DaemonSet, substitua o wSID e o KEY. Copie o ficheiro para o seu nó principal e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Ficheiro yaml padrão OMSagent DaemonSet com segredos**

1. Para utilizar o agente Log Analytics DaemonSet usando informações secretas, crie primeiro os segredos.
    1. Copie o script e o ficheiro do modelo secreto e certifique-se de que estão no mesmo diretório.
        - Script gerador secreto - secret-gen.sh
        - modelo secreto - secret-template.yaml
    2. Executar o guião, como o seguinte exemplo. O script pede o ID do Espaço de Trabalho de Log Analytics e chave primária e depois de os introduzir, o script cria um ficheiro yaml secreto para que possa executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie a cápsula de segredos executando o seguinte:
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

    5. Crie o seu damsagent daemon-set correndo```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Verifique se o agente de Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Para kubernetes, use um script para gerar os segredos do ficheiro yaml para workspace ID e chave primária para o agente Log Analytics para Linux. Utilize as seguintes informações de exemplo com o [ficheiro yaml omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger as suas informações secretas.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configure um agente do Windows de Log Analytics para Kubernetes

Para windows Kubernetes, você usa um script para gerar os segredos do ficheiro yaml para o seu ID workspace e chave primária para instalar o agente Log Analytics. Na página [Log Analytics Docker Kubernetes GitHub,](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) existem ficheiros que pode utilizar com as suas informações secretas.  É necessário instalar o agente Log Analytics separadamente para os nódosos de mestre e agente.  

1. Para utilizar o agente Log Analytics DaemonSet usando informações secretas sobre o nó Master, inicie sessão e crie os segredos primeiro.
    1. Copie o script e o ficheiro do modelo secreto e certifique-se de que estão no mesmo diretório.
        - Script gerador secreto - secret-gen.sh
        - modelo secreto - secret-template.yaml

    2. Executar o guião, como o seguinte exemplo. O script pede o ID do Espaço de Trabalho de Log Analytics e chave primária e depois de os introduzir, o script cria um ficheiro yaml secreto para que possa executá-lo.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Crie o seu damsagent daemon-set correndo```kubectl create -f omsagentsecret.yaml```
    4. Para verificar, faça o seguinte:

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

    5. Crie o seu damsagent daemon-set correndo```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Verifique se o agente de Log Analytics DaemonSet está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no Nó de Trabalhador, que está a executar o Windows, siga os passos na secção [instalar e configurar](#install-and-configure-windows-container-hosts)os anfitriões do recipiente Windows .

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Use o Helm para implementar o agente Log Analytics em Linux Kubernetes

Para utilizar o leme para implantar o agente Log Analytics no ambiente do Linux Kubernetes, execute os seguintes passos.

1. Crie o seu damsagent daemon-set correndo```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar anfitriões de contentores Windows

Utilize as informações na secção para instalar e configurar os anfitriões dos contentores windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar agentes do Windows

Antes de instalar agentes em computadores que executam o Windows, tem de configurar o serviço Docker. A configuração permite ao agente Windows ou à extensão da máquina virtual Do Monitor Azure utilizar a tomada Docker TCP para que os agentes possam aceder remotamente ao daemon do Docker e capturar dados para monitorização.

##### <a name="to-configure-the-docker-service"></a>Para configurar o serviço Docker  

Execute os seguintes comandos PowerShell para ativar o tubo TCP e o tubo nomeado para o Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Para obter mais informações sobre a configuração do daemon Dono Docker utilizada com os Recipientes windows, consulte [o Motor Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalar agentes do Windows

Para permitir a monitorização de contentores Windows e Hyper-V, instale o Microsoft Monitoring Agent (MMA) nos computadores Windows que são anfitriões de contentores. Para computadores que executem o Windows no seu ambiente no local, consulte [connect Windows computers to Azure Monitor](../../azure-monitor/platform/agent-windows.md). Para máquinas virtuais em funcionamento em Azure, ligue-as ao Monitor Azure utilizando a [extensão](../../azure-monitor/learn/quick-collect-azurevm.md)da máquina virtual .

Pode monitorizar os recipientes windows em funcionamento no Tecido de Serviço. No entanto, apenas [as máquinas virtuais em funcionamento em Azure](../../azure-monitor/learn/quick-collect-azurevm.md) e [computadores que executam o Windows no seu ambiente no local](../../azure-monitor/platform/agent-windows.md) estão atualmente suportadas para o Tecido de Serviço.

Pode verificar se a solução de monitorização do contentor está corretamente definida para o Windows. Para verificar se o pacote de gestão foi descarregado corretamente, procure *ContainerManagement.xxx*. Os ficheiros devem estar na pasta C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.

## <a name="solution-components"></a>Componentes da solução

A partir do portal Azure, navegue até à *Galeria soluções* e adicione a Solução de Monitorização de **Contentores.** Se estiver a utilizar agentes windows, o seguinte pacote de gestão é instalado em cada computador com um agente quando adicionar esta solução. Não é necessária qualquer configuração ou manutenção para o pacote de gestão.

- *ContainerManagement.xxx* instalado si em C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Detalhes da recolha de dados de contentores

A solução de monitorização do contentor recolhe várias métricas de desempenho e dados de registo de hospedeiros e contentores de contentores utilizando agentes que você ativa.

Os dados são recolhidos de três em três minutos pelos seguintes tipos de agentes.

- [Agente de Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agente windows](../../azure-monitor/platform/agent-windows.md)
- [Extensão VM de Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Registos de contentores

A tabela que se segue apresenta exemplos de registos recolhidos pela solução de monitorização do contentor e dos tipos de dados que aparecem nos resultados de pesquisa de registo.

| Tipo de dados | Tipo de dados em Pesquisa de Registo | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | Computador, Nome de Objetos, Contranome &#40;%Tempo de processador, Disco lê MB, Disk Writes MB, Utilização de Memória MB, Bytes de Receção de Rede, Bytes de Envio de Rede, Processador de Utilização,&#41; de rede, Contravalor,TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentores | `ContainerInventory` | TimeGenerated, Computer, nome de contentor, nome de contentorhost, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventário de imagem de contentor | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopd, Failed, SourceSystem, ImageID, TotalContainer |
| Tronco de contentor | `ContainerLog` | TimeGenerated, Computador, ID de imagem, nome do recipiente, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Registo de serviço de contentores | `ContainerServiceLog`  | TimeGenerated, Computador, TimeOfCommand, Imagem, Comando, SourceSystem, ContainerID |
| Inventário do nó de contentores | `ContainerNodeInventory_CL`| TimeGenerated, Computador, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário kubernetes | `KubePodInventory_CL` | TimeGenerated, Computador, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo de contentores | `ContainerProcess_CL` | TimeGenerated, Computador, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, Computador, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Mensagem |

As etiquetas anexadas aos tipos de dados *PodLabel* são as suas próprias etiquetas personalizadas. As etiquetas PodLabel anexadas mostradas na tabela são exemplos. Assim, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` será diferente no conjunto de dados do `PodLabel_yourlabel_s`seu ambiente e genericamente se assemelha .

## <a name="monitor-containers"></a>Contentores de monitor
Depois de ter a solução ativada no portal Azure, o azulejo dos **Contentores** mostra informações sumárias sobre os seus anfitriões de contentores e os contentores que estão a funcionar nos anfitriões.

![Azulejos de contentores](./media/containers/containers-title.png)

O azulejo mostra uma visão geral de quantos contentores você tem no ambiente e se eles estão falhados, correndo ou parados.

### <a name="using-the-containers-dashboard"></a>Utilização do painel de instrumentos de contentores

Clique no azulejo dos **contentores.** A partir daí, verá vistas organizadas por:

- **Eventos de contentores** - Mostra o estado do contentor e os computadores com contentores falhados.
- **Registos de contentores** - Mostra um gráfico de ficheiros de registo de contentores gerados ao longo do tempo e uma lista de computadores com o maior número de ficheiros de registo.
- **Eventos Kubernetes** - Mostra um gráfico de eventos kubernetes gerados ao longo do tempo e uma lista das razões pelas quais as cápsulas geraram os eventos. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Kubernetes Namespace Inventory** - Mostra o número de espaços de nome e pods e mostra a sua hierarquia. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário do Nó** do Recipiente - Mostra o número de tipos de orquestração utilizados em nós/anfitriões de contentores. Os nós/anfitriões do computador também estão listados pelo número de contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- **Inventário de Imagens** de Contentores - Mostra o número total de imagens de contentores utilizadas e o número de tipos de imagem. O número de imagens também está listado pela etiqueta de imagem.
- **Estado dos contentores** - Mostra o número total de nós de contentores/computadores hospedeiros que têm contentores em funcionamento. Os computadores também estão listados pelo número de anfitriões em execução.
- **Processo de Contentores** - Mostra um gráfico de linha de processos de contentores que correm ao longo do tempo. Os recipientes também são listados por comando/processo dentro dos contentores. *Este conjunto de dados é utilizado apenas em ambientes Linux.*
- Desempenho do **CPU do recipiente** - Mostra um gráfico de linha da utilização média do CPU ao longo do tempo para nós/anfitriões de computador. Também lista os nós/anfitriões do computador com base na utilização média do CPU.
- **Desempenho da memória** do recipiente - Mostra um gráfico de linha de utilização da memória ao longo do tempo. Também lista a utilização da memória do computador com base no nome da instância.
- **Computer Performance** - Mostra gráficos de linha da percentagem de desempenho do CPU ao longo do tempo, por cento do uso da memória ao longo do tempo, e megabytes de espaço de disco livre ao longo do tempo. Pode pairar sobre qualquer linha de um gráfico para ver mais detalhes.

Cada área do painel de instrumentos é uma representação visual de uma pesquisa que é executada em dados recolhidos.

![Painel de instrumentos de contentores](./media/containers/containers-dash01.png)

![Painel de instrumentos de contentores](./media/containers/containers-dash02.png)

Na área do Estado do **Contentor,** clique na área superior, como mostrado abaixo.

![Estado dos contentores](./media/containers/containers-status.png)

O Log Analytics abre, exibindo informações sobre o estado dos seus contentores.

![Log Analytics para recipientes](./media/containers/containers-log-search.png)

A partir daqui, pode editar a consulta de pesquisa para modificá-la para encontrar as informações específicas que lhe interessam. Para mais informações sobre consultas de registo, consulte consultas de [registo no Monitor Azure](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Problemas ao encontrar um recipiente falhado

O Log Analytics marca um recipiente como **Falhado** se tiver saído com um código de saída não zero. Pode ver uma visão geral dos erros e falhas no ambiente na área de **Contentores Falhados.**

### <a name="to-find-failed-containers"></a>Encontrar contentores falhados

1. Clique na área do Estado do **Contentor.**  
   ![estado dos contentores](./media/containers/containers-status.png)
2. O Log Analytics abre e exibe o estado dos seus recipientes, semelhante ao seguinte.  
   ![estado de contentores](./media/containers/containers-log-search.png)
3. Expanda a linha Failed e clique + para adicionar os seus critérios à consulta. Em seguida, comentar a linha Resumo na consulta.
   ![recipientes falhados](./media/containers/containers-state-failed-select.png)  
1. Executar a consulta e, em seguida, expandir uma linha nos resultados para ver o ID de imagem.  
   ![recipientes falhados](./media/containers/containers-state-failed.png)  
1. Digite o seguinte na consulta de registo. `ContainerImageInventory | where ImageID == <ImageID>`para ver detalhes sobre a imagem, como o tamanho da imagem e o número de imagens paradas e falhadas.  
   ![recipientes falhados](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Registos de consulta para dados de contentores

Quando se está a resolver um erro específico, pode ajudar a ver onde está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para devolver a informação que deseja.

- **ContainerImageInventário** – Utilize este tipo quando estiver a tentar encontrar informações organizadas por imagem e a visualizar informações de imagem, como iDs de imagem ou tamanhos.
- **ContainerInventário** – Utilize este tipo quando quiser informações sobre a localização do contentor, quais são os seus nomes e quais as imagens que estão a executar.
- **ContainerLog** – Utilize este tipo quando pretender encontrar informações e entradas específicas de registo de erros.
- **ContainerNodeInventory_CL**  Utilize este tipo quando pretender a informação sobre o hospedeiro/nó onde residem os contentores. Fornece-lhe versão Docker, tipo de orquestração, armazenamento e informação de rede.
- **ContainerProcess_CL** Utilize este tipo para ver rapidamente o processo a funcionar dentro do recipiente.
- **ContainerServiceLog** – Utilize este tipo quando estiver a tentar encontrar informações de rasto de auditoria para o daemon do Docker, tais como iniciar, parar, excluir ou puxar comandos.
- **KubeEvents_CL**  Use este tipo para ver os eventos kubernetes.
- **KubePodInventory_CL**  Utilize este tipo quando quiser compreender a informação da hierarquia do cluster.


### <a name="to-query-logs-for-container-data"></a>Para consultar os registos dos dados do contentor

* Escolha uma imagem que sabe que falhou recentemente e encontre os registos de erro para a mesmo. Comece por encontrar um nome de recipiente que esteja a executar essa imagem com uma pesquisa **de ContainerInventory.** Por exemplo, procurar`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Pesquisa de contentores Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha nos resultados para ver detalhes para o recipiente.

## <a name="example-log-queries"></a>Consultas de registo de exemplo

É muitas vezes útil construir consultas começando com um exemplo ou dois e, em seguida, modificá-las para se adaptar ao seu ambiente. Como ponto de partida, você pode experimentar com a área **de Perguntas de Amostra** para ajudá-lo a construir consultas mais avançadas.

![Consultas de contentores](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Guardar consultas de registo

Guardar consultas é uma característica padrão no Monitor Azure. Ao salvá-los, terá aqueles que achou úteis para uso futuro.

Depois de criar uma consulta que ache útil, guarde-a clicando em **Favoritos** no topo da página de Pesquisa de Registos. Depois, pode aceder-lhe facilmente mais tarde a partir da página **My Dashboard.**

## <a name="next-steps"></a>Passos seguintes

[Registos](../log-query/log-query-overview.md) de consultas para visualizar registos de dados detalhados do recipiente.
