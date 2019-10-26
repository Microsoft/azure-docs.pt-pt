---
title: Solução de monitoramento de contêiner no Azure Monitor | Microsoft Docs
description: A solução de monitoramento de contêiner no Azure Monitor ajuda a exibir e gerenciar seus hosts de contêiner do Docker e do Windows em um único local.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: b71818d5d840a0466b5ff6f271df117043341f7b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899116"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitoramento de contêiner no Azure Monitor

![Símbolo de contêineres](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e usar a solução de monitoramento de contêiner no Azure Monitor, que ajuda a exibir e gerenciar seus hosts de contêiner do Docker e do Windows em um único local. O Docker é um sistema de virtualização de software usado para criar contêineres que automatizam a implantação de software em sua infraestrutura de ti.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra quais contêineres estão em execução, qual imagem de contêiner eles estão executando e onde os contêineres estão em execução. Você pode exibir informações de auditoria detalhadas mostrando os comandos usados com contêineres. E você pode solucionar problemas de contêineres exibindo e pesquisando logs centralizados sem precisar exibir remotamente os hosts do Docker ou do Windows. Você pode encontrar contêineres que podem estar com ruídos e consumindo recursos em excesso em um host. Além de isso, você pode exibir informações de desempenho, memória, armazenamento e utilização de rede centralizadas para contêineres. Em computadores que executam o Windows, você pode centralizar e comparar logs do Windows Server, do Hyper-V e de contêineres do Docker. A solução dá suporte aos orquestradores de contêiner a seguir:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Se você tiver contêineres implantados no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md), é recomendável habilitar a [solução Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) e essa solução para incluir o monitoramento de eventos de cluster. Antes de habilitar a solução de Service Fabric, examine [o uso da solução Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) para entender o que ele fornece e como usá-lo.

Se você estiver interessado em monitorar o desempenho de suas cargas de trabalho implantadas em ambientes kubernetes hospedados no AKS (serviço de kubernetes do Azure), consulte [monitorar o serviço kubernetes do Azure](../../azure-monitor/insights/container-insights-overview.md). A solução de monitoramento de contêiner não dá suporte ao monitoramento dessa plataforma.  

O diagrama a seguir mostra as relações entre vários hosts de contêiner e agentes com Azure Monitor.

![Diagrama de contêineres](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos do sistema e plataformas com suporte

Antes de iniciar, examine os detalhes a seguir para verificar se você atende aos pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Suporte de solução de monitoramento de contêiner para orquestrador e plataforma de sistema operacional do Docker

A tabela a seguir descreve a orquestração do Docker e o suporte ao monitoramento do sistema operacional de inventário de contêiner, desempenho e logs com Azure Monitor.   

| | ACS | Linux | Windows | Contentor<br>Inventário | Imagem<br>Inventário | Nó<br>Inventário | Contentor<br>Desempenho | Contentor<br>Evento | Evento<br>Registo | Contentor<br>Registo |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Recursos de infraestrutura | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat aberto<br>Alternância | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>autônomo | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Servidor Linux<br>autônomo | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Versões do Docker com suporte no Linux

- Docker 1,11 para 1,13
- Docker CE e EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>distribuições do Linux x64 com suporte como hosts de contêiner

- Ubuntu 14, 4 LTS e 16, 4 LTS
- CoreOS (estável)
- Amazon Linux 2016.09.0
- openSUSE 13,2
- openSUSE LEAP 42,2
- CentOS 7,2 e 7,3
- SLES 12
- RHEL 7,2 e 7,3
- Plataforma de contêiner do Red Hat OpenShift (OCP) 3,4 e 3,5
- ACS Mesosphere DC/OS 1.7.3 to 1.8.8
- ACS kubernetes 1.4.5 para 1,6
    - Os eventos kubernetes, o inventário kubernetes e os processos de contêiner só têm suporte com a versão 1.4.1-45 e posterior do agente de Log Analytics para Linux
- Swarm do Docker ACS

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Sistema operacional Windows com suporte

- Windows Server 2016
- Edição de aniversário do Windows 10 (Professional ou Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Versões do Docker com suporte no Windows

- Docker 1,12 e 1,13
- Docker 17.03.0 e posterior

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

Utilize as seguintes informações para instalar e configurar a solução.

1. Adicione a solução de monitoramento de contêiner ao seu espaço de trabalho Log Analytics do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](../../azure-monitor/insights/solutions.md).

2. Instale e use o Docker com um agente de Log Analytics. Com base no seu sistema operacional e no orquestrador do Docker, você pode usar os métodos a seguir para configurar o agente.
   - Para hosts autônomos:
     - Em sistemas operacionais Linux com suporte, instale e execute o Docker e, em seguida, instale e configure o [agente de log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - No CoreOS, você não pode executar o agente de Log Analytics para Linux. Em vez disso, você executa uma versão em contêiner do agente de Log Analytics para Linux. Examine os hosts de contêiner do Linux, incluindo CoreOS ou hosts de contêiner do Linux do Azure governamental, incluindo CoreOS se você estiver trabalhando com contêineres na nuvem do Azure governamental.
     - No Windows Server 2016 e no Windows 10, instale o mecanismo e o cliente do Docker e, em seguida, conecte um agente para coletar informações e enviá-las para Azure Monitor. Examine [instalar e configurar hosts de contêiner do Windows](#install-and-configure-windows-container-hosts) se você tiver um ambiente do Windows.
   - Para orquestração de vários hosts do Docker:
     - Se você tiver um ambiente Red Hat OpenShift, examine configurar um agente de Log Analytics para Red Hat OpenShift.
     - Se você tiver um cluster kubernetes usando o serviço de contêiner do Azure:
       - Examine [configurar um log Analytics agente do Linux para kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Examine [configurar um log Analytics agente do Windows para kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Examine usar o Helm para implantar o agente Log Analytics no Linux kubernetes.
     - Se você tiver um cluster DC/OS do serviço de contêiner do Azure, saiba mais em [monitorar um cluster DC/os do serviço de contêiner do Azure com o Azure monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Se você tiver um ambiente do modo Docker Swarm, saiba mais em configurar um agente de Log Analytics para o Docker Swarm.
     - Se você tiver um Cluster Service Fabric, saiba mais em [monitorar contêineres com Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Examine o artigo [Docker Engine no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obter informações adicionais sobre como instalar e configurar os mecanismos do Docker em computadores que executam o Windows.

> [!IMPORTANT]
> O Docker deve estar em execução **antes** de instalar o [agente de log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) em seus hosts de contêiner. Se você já tiver instalado o agente antes de instalar o Docker, será necessário reinstalar o agente de Log Analytics para Linux. Para obter mais informações sobre o Docker, consulte o [site do Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar hosts de contêiner do Linux

Depois de instalar o Docker, use as configurações a seguir para o host do contêiner para configurar o agente para uso com o Docker. Primeiro, você precisa do Log Analytics ID e da chave do espaço de trabalho, que podem ser encontrados na portal do Azure. Em seu espaço de trabalho, clique em **Início Rápido** > **computadores** para exibir a ID do **espaço de trabalho** e a **chave primária**.  Copie e cole ambos no seu editor favorito.

**Para todos os hosts de contêiner do Linux, exceto CoreOS:**

- Para obter mais informações e etapas sobre como instalar o agente de Log Analytics para Linux, consulte [visão geral do agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Para todos os hosts de contêiner do Linux, incluindo CoreOS:**

Inicie o contêiner que você deseja monitorar. Modifique e use o exemplo a seguir:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os hosts de contêiner do Linux do Azure governamental, incluindo CoreOS:**

Inicie o contêiner que você deseja monitorar. Modifique e use o exemplo a seguir:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Alternando do uso de um agente Linux instalado para um em um contêiner**

Se você usou anteriormente o agente instalado diretamente e quiser usar um agente em execução em um contêiner, primeiro você deve remover o agente de Log Analytics para Linux. Consulte [desinstalando o agente de log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para entender como desinstalar o agente com êxito.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configurar um agente de Log Analytics para o Docker Swarm

Você pode executar o agente de Log Analytics como um serviço global no Docker Swarm. Use as informações a seguir para criar um serviço de agente de Log Analytics. Você precisa fornecer sua ID do espaço de trabalho Log Analytics e a chave primária.

- Execute o seguinte no nó mestre.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Proteger segredos para o Docker Swarm

Para o Docker Swarm, depois que o segredo para a ID do espaço de trabalho e a chave primária for criado, use as informações a seguir para criar suas informações secretas.

1. Execute o seguinte no nó mestre.

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

3. Execute o comando a seguir para montar os segredos para o agente de Log Analytics em contêineres.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Configurar um agente de Log Analytics para Red Hat OpenShift

Há três maneiras de adicionar o agente de Log Analytics ao Red Hat OpenShift para começar a coletar dados de monitoramento de contêiner.

* [Instalar o agente de log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) diretamente em cada nó do OpenShift  
* [Habilitar log Analytics extensão de VM](../../azure-monitor/learn/quick-collect-azurevm.md) em cada nó do OpenShift que reside no Azure  
* Instalar o agente de Log Analytics como um OpenShift daemon-Set  

Nesta seção, abordaremos as etapas necessárias para instalar o agente de Log Analytics como um OpenShift daemon-set.  

1. Faça logon no nó mestre do OpenShift e copie o arquivo YAML [OCP-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o nó mestre e modifique o valor com sua ID do espaço de trabalho log Analytics e com a chave primária.
2. Execute os comandos a seguir para criar um projeto para Azure Monitor e definir a conta de usuário.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Para implantar o daemon-set, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

4. Para verificar se ele está configurado e funcionando corretamente, digite o seguinte:

    `oc describe daemonset omsagent`  

    e a saída deve ser semelhante a:

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

Se você quiser usar segredos para proteger sua ID do espaço de trabalho Log Analytics e a chave primária ao usar o arquivo YAML do daemon do agente Log Analytics, execute as etapas a seguir.

1. Faça logon no nó mestre do OpenShift e copie o arquivo YAML [OCP-DS-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o script de geração de segredo [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do github.  Esse script gerará o arquivo YAML de segredos para Log Analytics ID do espaço de trabalho e a chave primária para proteger suas informações secretas.  
2. Execute os comandos a seguir para criar um projeto para Azure Monitor e definir a conta de usuário. O script de geração de segredo solicita seu Log Analytics ID de espaço de trabalho `<WSID>` e a chave primária `<KEY>` e, após a conclusão, ele cria o arquivo OCP-Secret. YAML.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implante o arquivo secreto executando o seguinte:

    `oc create -f ocp-secret.yaml`

4. Verifique a implantação executando o seguinte:

    `oc describe secret omsagent-secret`  

    e a saída deve ser semelhante a:  

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

5. Implante o arquivo YAML do daemon do agente de Log Analytics, executando o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verifique a implantação executando o seguinte:

    `oc describe ds oms`

    e a saída deve ser semelhante a:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configurar um agente do Log Analytics Linux para kubernetes

Para kubernetes, você usa um script para gerar o arquivo de segredos YAML para a ID do espaço de trabalho e a chave primária para instalar o agente de Log Analytics para Linux. Na página do [GitHub log Analytics Docker kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) , há arquivos que você pode usar com ou sem suas informações secretas.

- O agente de Log Analytics padrão para o Daemonset do Linux não tem informações secretas (omsagent. YAML)
- O arquivo YAML do Daemonset do agente do Log Analytics para Linux usa informações secretas (omsagent-DS-Secrets. YAML) com scripts de geração de segredo para gerar o arquivo YAML (omsagentsecret. YAML) de segredos.

Você pode optar por criar omsagent DaemonSets com ou sem segredos.

**Arquivo OMSagent Daemonset YAML padrão sem segredos**

- Para o arquivo padrão YAML Daemonset do agente de Log Analytics, substitua o `<WSID>` e `<KEY>` para o WSID e a chave. Copie o arquivo para o nó mestre e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Arquivo OMSagent Daemonset YAML padrão com segredos**

1. Para usar o Daemonset do Log Analytics Agent usando informações secretas, crie os segredos primeiro.
    1. Copie o arquivo de modelo de script e segredo e verifique se eles estão no mesmo diretório.
        - Script de geração de segredo-secret-gen.sh
        - modelo secreto-segredo-modelo. YAML
    2. Execute o script, como no exemplo a seguir. O script solicita a ID do espaço de trabalho Log Analytics e a chave primária e, depois de inseri-las, o script cria um arquivo YAML secreto para que você possa executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie o pod dos segredos executando o seguinte:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Para verificar, execute o seguinte:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        A saída deve ser semelhante a:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        A saída deve ser semelhante a:

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

    5. Criar o daemon omsagent-definido executando ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Verifique se o Daemonset do agente de Log Analytics está em execução, semelhante ao seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Para kubernetes, use um script para gerar o arquivo YAML de segredos para a ID do espaço de trabalho e a chave primária para o agente de Log Analytics para Linux. Use as informações de exemplo a seguir com o [arquivo omsagent YAML](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger suas informações secretas.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configurar um Log Analytics agente do Windows para kubernetes

Para o Windows kubernetes, você usa um script para gerar o arquivo de segredos YAML para a ID do espaço de trabalho e a chave primária para instalar o agente de Log Analytics. Na página do [GitHub log Analytics Docker kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) , há arquivos que você pode usar com suas informações secretas.  Você precisa instalar o agente de Log Analytics separadamente para os nós mestre e de agente.  

1. Para usar o Daemonset do Log Analytics Agent usando informações secretas no nó mestre, entre e crie os segredos primeiro.
    1. Copie o arquivo de modelo de script e segredo e verifique se eles estão no mesmo diretório.
        - Script de geração de segredo-secret-gen.sh
        - modelo secreto-segredo-modelo. YAML

    2. Execute o script, como no exemplo a seguir. O script solicita a ID do espaço de trabalho Log Analytics e a chave primária e, depois de inseri-las, o script cria um arquivo YAML secreto para que você possa executá-lo.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Criar o daemon omsagent-definido executando ```kubectl create -f omsagentsecret.yaml```
    4. Para verificar, execute o seguinte:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        A saída deve ser semelhante a:

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

    5. Criar o daemon omsagent-definido executando ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Verifique se o Daemonset do agente de Log Analytics está em execução, semelhante ao seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no nó de trabalho, que executa o Windows, siga as etapas na seção [instalar e configurar hosts de contêiner do Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Usar o Helm para implantar o agente Log Analytics no Linux kubernetes

Para usar o Helm para implantar o agente Log Analytics em seu ambiente Linux kubernetes, execute as etapas a seguir.

1. Criar o daemon omsagent-definido executando ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Os resultados serão semelhantes ao seguinte:

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

3. Você pode verificar o status do omsagent executando: ```helm status "omsagent"``` e a saída será semelhante ao seguinte:

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
   
    Para obter mais informações, visite o [gráfico da solução de contêiner Helm](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar hosts de contêiner do Windows

Use as informações na seção para instalar e configurar hosts de contêiner do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar os agentes do Windows

Antes de instalar agentes em computadores que executam o Windows, você precisa configurar o serviço do Docker. A configuração permite que o agente do Windows ou a extensão de máquina virtual do Azure Monitor use o soquete TCP do Docker para que os agentes possam acessar o daemon do Docker remotamente e capturar dados para monitoramento.

##### <a name="to-configure-the-docker-service"></a>Para configurar o serviço do Docker  

Execute os seguintes comandos do PowerShell para habilitar o pipe TCP e o pipe nomeado para o Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Para obter mais informações sobre a configuração do daemon do Docker usada com contêineres do Windows, consulte [mecanismo do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalar agentes do Windows

Para habilitar o monitoramento de contêineres do Windows e do Hyper-V, instale o Microsoft Monitoring Agent (MMA) em computadores com Windows que são hosts de contêiner. Para computadores que executam o Windows em seu ambiente local, consulte [conectar computadores Windows ao Azure monitor](../../azure-monitor/platform/agent-windows.md). Para máquinas virtuais em execução no Azure, conecte-as a Azure Monitor usando a [extensão da máquina virtual](../../azure-monitor/learn/quick-collect-azurevm.md).

Você pode monitorar contêineres do Windows em execução no Service Fabric. No entanto, somente as [máquinas virtuais em execução no Azure](../../azure-monitor/learn/quick-collect-azurevm.md) e [computadores que executam o Windows em seu ambiente local](../../azure-monitor/platform/agent-windows.md) têm suporte no momento para Service Fabric.

Você pode verificar se a solução de monitoramento de contêiner está definida corretamente para o Windows. Para verificar se o pacote de gerenciamento foi baixado corretamente, procure *ContainerManagement.xxx*. Os arquivos devem estar na pasta C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management packs.

## <a name="solution-components"></a>Componentes da solução

No portal do Azure, navegue até a *Galeria de soluções* e adicione a **solução de monitoramento de contêiner**. Se você estiver usando agentes do Windows, o pacote de gerenciamento a seguir será instalado em cada computador com um agente quando você adicionar essa solução. Nenhuma configuração ou manutenção é necessária para o pacote de gerenciamento.

- *ContainerManagement.xxx* instalado em C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management packs

## <a name="container-data-collection-details"></a>Detalhes da coleta de dados do contêiner

A solução de monitoramento de contêiner coleta várias métricas de desempenho e dados de log de hosts de contêiner e contêineres usando agentes que você habilita.

Os dados são coletados a cada três minutos pelos seguintes tipos de agente.

- [Agente de Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agente do Windows](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics extensão de VM](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Registros de contêiner

A tabela a seguir mostra exemplos de registros coletados pela solução de monitoramento de contêiner e os tipos de dados que aparecem nos resultados da pesquisa de log.

| Data type | Tipo de dados na pesquisa de logs | Campos |
| --- | --- | --- |
| Desempenho para hosts e contêineres | `Perf` | Computador, ObjectName, CounterName &#40;% de tempo do processador, leituras de disco MB, gravações em disco MB, uso de memória MB, bytes de recebimento de rede, bytes de&#41;envio de rede, uso de processador s, rede, valor de intervalo, tempo gerado, caminho, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, computador, nome do contêiner, ContainerHostname, imagem, ImageTag, Contêinerstate, ExitCode, EnvironmentVar, comando, Createdtime, preparoutime, Finalizatime, SourceSystem, Idcontêiner, Imageid |
| Inventário de imagem de contêiner | `ContainerImageInventory` | TimeGenerated, computador, imagem, ImageTag, ImageSize, VirtualSize, em execução, em pausa, parado, com falha, SourceSystem, Imageid, TotalContainer |
| Log de contêiner | `ContainerLog` | TimeGenerated, Computer, ID da imagem, nome do contêiner, LogEntry, LogEntry, SourceSystem, ContainerId |
| Log do serviço de contêiner | `ContainerServiceLog`  | TimeGenerated, computador, TimeOfCommand, imagem, comando, SourceSystem, ContainerId |
| Inventário de nó de contêiner | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo de contêiner | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de kubernetes | `KubeEvents_CL` | TimeGenerated, computador, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, mensagem |

Rótulos anexados a tipos de dados *PodLabel* são seus próprios rótulos personalizados. Os rótulos PodLabel acrescentados mostrados na tabela são exemplos. Portanto, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` será diferente no conjunto de dados do seu ambiente e se parecer genericamente `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Contentores de monitor
Depois que a solução estiver habilitada no portal do Azure, o bloco **contêineres** mostrará informações resumidas sobre os hosts de contêiner e os contêineres em execução nos hosts.

![Bloco contêineres](./media/containers/containers-title.png)

O bloco mostra uma visão geral de quantos contêineres você tem no ambiente e se eles estão com falha, em execução ou parado.

### <a name="using-the-containers-dashboard"></a>Usando o painel contêineres

Clique no bloco **contêineres** . A partir daí, você verá exibições organizadas por:

- **Eventos de contêiner** -mostra o status do contêiner e os computadores com contêineres com falha.
- **Logs de contêiner** – mostra um gráfico de arquivos de log de contêiner gerados ao longo do tempo e uma lista de computadores com o número mais alto de arquivos de log.
- **Eventos de kubernetes** – mostra um gráfico de eventos de kubernetes gerados ao longo do tempo e uma lista dos motivos pelos quais os pods geraram os eventos. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de namespace kubernetes** -mostra o número de namespaces e pods e mostra sua hierarquia. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de nó de contêiner** – mostra o número de tipos de orquestração usados em nós/hosts de contêiner. Os nós/hosts do computador também são listados pelo número de contêineres. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de imagens de contêiner** – mostra o número total de imagens de contêiner usadas e o número de tipos de imagem. O número de imagens também é listado pela marca de imagem.
- **Status dos contêineres** – mostra o número total de nós de contêiner/computadores host que têm contêineres em execução. Os computadores também são listados pelo número de hosts em execução.
- **Processo de contêiner** -mostra um gráfico de linhas de processos de contêiner em execução ao longo do tempo. Os contêineres também são listados por meio da execução de comando/processo nos contêineres. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Desempenho da CPU do contêiner** – mostra um gráfico de linhas da utilização média da CPU ao longo do tempo para nós/hosts de computador. Também lista os nós/hosts de computador com base na utilização média da CPU.
- **Desempenho da memória do contêiner** -mostra um gráfico de linhas de uso de memória ao longo do tempo. Também lista a utilização de memória do computador com base no nome da instância.
- **Desempenho do computador** – mostra os gráficos de linhas da porcentagem de desempenho da CPU ao longo do tempo, a porcentagem de uso da memória ao longo do tempo e megabytes de espaço livre em disco ao longo do tempo. Você pode focalizar qualquer linha em um gráfico para exibir mais detalhes.

Cada área do painel é uma representação visual de uma pesquisa que é executada nos dados coletados.

![Painel de contêineres](./media/containers/containers-dash01.png)

![Painel de contêineres](./media/containers/containers-dash02.png)

Na área **status do contêiner** , clique na área superior, conforme mostrado abaixo.

![Status dos contêineres](./media/containers/containers-status.png)

Log Analytics é aberto, exibindo informações sobre o estado de seus contêineres.

![Log Analytics para contêineres](./media/containers/containers-log-search.png)

A partir daqui, você pode editar a consulta de pesquisa para modificá-la para localizar as informações específicas em que você está interessado. Para obter mais informações sobre consultas de log, consulte [log queries in Azure monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Solucionar problemas encontrando um contêiner com falha

Log Analytics marca um contêiner como **com falha** se ele foi encerrado com um código de saída diferente de zero. Você pode ver uma visão geral dos erros e falhas no ambiente na área de **contêineres com falha** .

### <a name="to-find-failed-containers"></a>Para localizar contêineres com falha

1. Clique na área **status do contêiner** .  
   status de contêineres de ![](./media/containers/containers-status.png)
2. Log Analytics abre e exibe o estado de seus contêineres, semelhante ao seguinte.  
   ![Estado dos contêineres](./media/containers/containers-log-search.png)
3. Expanda a linha com falha e clique em + para adicionar seus critérios à consulta. Em seguida, comente a linha de resumo na consulta.
   ![contêineres com falha](./media/containers/containers-state-failed-select.png)  
1. Execute a consulta e, em seguida, expanda uma linha nos resultados para exibir a ID da imagem.  
   ![contêineres com falha](./media/containers/containers-state-failed.png)  
1. Digite o seguinte na consulta de log. `ContainerImageInventory | where ImageID == <ImageID>` para ver detalhes sobre a imagem, como o tamanho da imagem e o número de imagens interrompidas e com falha.  
   ![contêineres com falha](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Logs de consulta para dados de contêiner

Quando você está solucionando um erro específico, ele pode ajudar a ver onde ele está ocorrendo em seu ambiente. Os seguintes tipos de log o ajudarão a criar consultas para retornar as informações desejadas.

- **ContainerImageInventory** – Use este tipo quando estiver tentando encontrar informações organizadas por imagem e para exibir informações da imagem, como IDs ou tamanhos de imagem.
- **ContainerInventory** – Use esse tipo quando desejar obter informações sobre o local do contêiner, quais são seus nomes e quais imagens eles estão executando.
- **ContainerLog** – Use esse tipo quando desejar localizar informações e entradas de log de erros específicas.
- **ContainerNodeInventory_CL**  Use esse tipo quando desejar as informações sobre o host/nó em que os contêineres estão residindo. Ele fornece a versão do Docker, o tipo de orquestração, o armazenamento e as informações de rede.
- **ContainerProcess_CL** Use esse tipo para ver rapidamente o processo em execução no contêiner.
- **ContainerServiceLog** – Use esse tipo quando estiver tentando localizar informações de trilha de auditoria para o daemon do Docker, como comandos Iniciar, parar, excluir ou efetuar pull.
- **KubeEvents_CL**  Use esse tipo para ver os eventos kubernetes.
- **KubePodInventory_CL**  Use esse tipo quando desejar entender as informações de hierarquia de cluster.


### <a name="to-query-logs-for-container-data"></a>Para consultar logs de dados de contêiner

* Escolha uma imagem que você sabe que falhou recentemente e localize os logs de erros para ele. Comece encontrando um nome de contêiner que esteja executando essa imagem com uma pesquisa **ContainerInventory** . Por exemplo, pesquise `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Pesquisar por contêineres Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha nos resultados para exibir os detalhes desse contêiner.

## <a name="example-log-queries"></a>Exemplo de consultas de log

Geralmente, é útil criar consultas começando com um exemplo ou duas e, em seguida, modificando-as para se adequar ao seu ambiente. Como ponto de partida, você pode experimentar a área **consultas de exemplo** para ajudá-lo a criar consultas mais avançadas.

![Consultas de contêineres](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Salvando consultas de log

Salvar consultas é um recurso padrão no Azure Monitor. Ao salvá-los, você terá aqueles que você encontrou úteis para uso futuro.

Depois de criar uma consulta que você acha útil, salve-a clicando em **favoritos** na parte superior da página pesquisa de logs. Em seguida, você pode acessá-lo facilmente mais tarde na página **meu painel** .

## <a name="next-steps"></a>Passos seguintes

[Logs de consulta](../log-query/log-query-overview.md) para exibir registros de dados de contêiner detalhados.
