---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 48deeec7a2c8767ab5dbb81b622e6d40483ed455
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093555"
---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Estabelecer uma ligação remota a um cluster Kubernetes, DC/OS ou Docker Swarm
Depois de criar um cluster do Azure Container Service, tem de ligar ao mesmo para implementar e gerir cargas de trabalho. Este artigo descreve como ligar à VM principal do cluster a partir de um computador remoto. 

Os clusters do Kubernetes, de DC/OS e do Docker Swarm disponibilizam pontos finais HTTP localmente. No Kubernetes, este ponto final é exposto em segurança na Internet e pode aceder ao mesmo ao executar a ferramenta de linha de comandos `kubectl` em qualquer máquina ligada à Internet. 

Para DC/SO e Docker Swarm, recomendamos que crie um túnel secure shell (SSH) a partir do computador local para o sistema de gestão de cluster. Depois de estabelecido o túnel, pode executar comandos que utilizam os pontos finais HTTP e ver a interface Web do orchestrator no sistema local (se disponível). 

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Kubernetes, DC/OS, Docker ou Swarm [implementado no Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md).
* Um ficheiro de chave privada RSA SSH, que corresponda à chave pública adicionada ao cluster durante a implementação. Estes comandos partem do princípio de que a chave pública SSH está em `$HOME/.ssh/id_rsa` no seu computador. Veja estas instruções para [macOS e Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../articles/virtual-machines/linux/ssh-from-windows.md), para obter mais informações. Se a ligação SSH não estiver a funcionar, poderá ter de [repor as chaves SSH](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Ligar a um cluster do Kubernetes

Siga estes passos para instalar e configurar `kubectl` no seu computador.

> [!NOTE] 
> Em Linux ou macOS, poderá ter de executar os comandos desta secção com `sudo`.
> 

### <a name="install-kubectl"></a>Instalar o kubectl
Uma forma de instalar esta ferramenta é utilizar o `az acs kubernetes install-cli` comando da CLI do Azure. Para executar este comando, certifique-se de que [instalados](/cli/azure/install-az-cli2) a versão mais recente da CLI do Azure e a sessão iniciada numa conta do Azure (`az login`).

```azurecli
# Linux or macOS
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Em alternativa, pode transferir o cliente `kubectl` mais recente diretamente a partir da [página de lançamentos do Kubernetes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Para obter mais informações, consulte [Installing and Setting up kubectl (Instalar e Configurar o kubectl)](https://kubernetes.io/docs/tasks/kubectl/install/).

### <a name="download-cluster-credentials"></a>Transferir as credenciais do cluster
Assim que tiver o `kubectl` instalado, deve copiar as credenciais de cluster para a máquina. Uma forma de obter as credenciais é utilizar o comando `az acs kubernetes get-credentials`. Transmita o nome do grupo de recursos e o nome do recurso do serviço de contentores:

```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Este comando transfere as credenciais do cluster para `$HOME/.kube/config`, em que `kubectl` espera que estejam localizadas.

Em alternativa, pode utilizar `scp` para copiar de forma segura o ficheiro a partir de `$HOME/.kube/config` na VM principal para o seu computador local. Por exemplo:

```bash
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Se estiver no Windows, pode utilizar Bash on Ubuntu no Windows, o cliente de cópia de ficheiros segura PuTTY ou uma ferramenta semelhante.

### <a name="use-kubectl"></a>Utilizar o kubectl

Assim que tiver configurado o `kubectl`, teste a ligação listando os nós do cluster:

```bash
kubectl get nodes
```

Pode experimentar outros comandos do `kubectl`. Por exemplo, pode ver o Dashboard do Kubernetes. Em primeiro lugar, execute um proxy para o servidor de API do Kubernetes:

```bash
kubectl proxy
```

A IU do Kubernetes está agora disponível em `http://localhost:8001/ui`.

Para obter mais informações, veja o [início rápido do Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Ligar a um cluster de DC/OS ou do Swarm

Para utilizar os clusters de DC/OS e do Docker Swarm implementados pelo Azure Container Service, siga estas instruções para criar um túnel de SSH do seu sistema Linux, macOS ou Windows local. 

> [!NOTE]
> Estas instruções centram-se no encapsulamento do tráfego TCP através de SSH. Também pode iniciar uma sessão SSH interativa com um dos sistemas de gestão de clusters internos, mas não o recomendamos. Trabalhar diretamente num sistema interno acarreta riscos de fazer alterações à configuração inadvertidamente.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-macos"></a>Criar um túnel SSH no Linux ou macOS
A primeira coisa que faz quando cria um túnel SSH no Linux ou macOS é localizar o nome DNS público dos modelos de estrutura mestres com balanceamento de carga. Siga estes passos.


1. No [portal do Azure](https://portal.azure.com), navegue para o grupo de recursos que contém o cluster do serviço do contentor. Expanda o grupo de recursos, para que cada recurso seja apresentado. 

2. Clique no recurso do **serviço de Contentor** e clique em **Descrição Geral**. O **FQDN Principal** do cluster é apresentado em **Essenciais**. Guarde este nome para utilização posterior. 

    ![Nome DNS público](./media/container-service-connect/pubdns.png)

    Em alternativa, execute o comando `az acs show` no serviço de contentor. Procure a propriedade **Master Profile:fqdn** no resultado do comando.

3. Agora, abra a shell e execute o comando `ssh`, ao especificar os valores seguintes: 

    **LOCAL_PORT** é a porta TCP do lado do serviço do túnel ao qual ligar. Para Swarm, defina-a como 2375. Para DC/OS, como 80. 
    **REMOTE_PORT** é a porta do ponto final que pretende expor. Para Swarm, utilize a porta 2375. Para o DC/OS, utilize a porta 80.  
    **USERNAME** é o nome de utilizador que foi fornecido quando implementou o cluster.  
    **DNSPREFIX** é o prefixo DNS que forneceu quando implementou o cluster.  
    **REGION** é a região no qual o grupo de recursos está localizado.  
    **PATH_TO_PRIVATE_KEY** [OPCIONAL] é o caminho para a chave privada que corresponde à chave pública que indicou quando criou o cluster. Utilize esta opção com o sinalizador `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com
    ```
  
   > [!NOTE]
   > A porta de ligação SSH é 2200 e não a porta 22 padrão. Em cluster com mais de uma VM principal, esta é a porta de ligação para a primeira VM principal.
   > 

   Devolve o comando sem saída.

Veja os exemplos relativos a DC/OS e Swarm nas secções seguintes.    

### <a name="dcos-tunnel"></a>Túnel do DC/OS
Para abrir um túnel para pontos finais de DC/OS, execute um comando como o seguinte:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Certifique-se de que não tem outro processo local que vincule a porta 80. Se necessário, pode especificar uma porta local diferente da 80, como a 8080. Contudo, se utilizar esta porta, alguns links da IU da Web poderão não funcionar.
>

Agora, pode aceder aos pontos finais de DC/OS a partir do seu sistema local através dos URLs seguintes (no pressuposto de que é utilizada a porta 80):

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

Da mesma forma, pode aceder ao resto das APIs de cada aplicação através deste túnel.

### <a name="swarm-tunnel"></a>Túnel do Swarm
Para abrir um túnel para o ponto final do Swarm, execute um comando como o seguinte:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```
> [!NOTE]
> Certifique-se de que não tem outro processo local que vincule a porta 2375. Por exemplo, se estiver a executar o daemon do Docker localmente, este está predefinido para utilizar a porta 2375. Se necessário, pode especificar uma porta local diferente da 2375.
>

Agora pode aceder ao cluster do Docker Swarm utilizando a interface de linha de comandos de Docker (Docker CLI) no sistema local. Para instruções de instalação, veja [Instalar Docker](https://docs.docker.com/engine/installation/).

Defina a variável de ambiente DOCKER_HOST com a porta local que configurou para o túnel. 

```bash
export DOCKER_HOST=:2375
```

Execute comandos Docker que dirijam o túnel para o cluster Docker Swarm. Por exemplo:

```bash
docker info
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Criar um túnel SSH no Windows
Existem várias opções para criar túneis SSH no Windows. Se estiver a executar o Bash no Ubuntu no Windows ou uma ferramenta semelhante, pode seguir as instruções de túnel SSH mostradas anteriormente no artigo para macOS e Linux. Como alternativa, no Windows, esta secção descreve como utilizar o PuTTY para criar o túnel.

1. [Transfira o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para o seu sistema Windows.

2. Execute a aplicação.

3. Introduza um nome de anfitrião composto pelo nome de utilizador administrador do cluster e o nome DNS público do primeiro modelo de estrutura mestre no cluster. O **Nome de Anfitrião** é semelhante a `azureuser@PublicDNSName`. Introduza 2200 para a **Porta**.

    ![Configuração do puTTY 1](./media/container-service-connect/putty1.png)

4. Selecione **SSH > Auth**. Adicione um caminho para o ficheiro da chave privada (formato .ppk) para autenticação. Pode utilizar uma ferramenta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para gerar este ficheiro a partir da chave SSH utilizada para criar o cluster.

    ![Configuração do puTTY 2](./media/container-service-connect/putty2.png)

5. Selecione **SSH > Túneis** e configure as portas reencaminhadas seguintes:

   * **Porta de origem:** Utilize a 80 para DC/OS ou 2375 para Swarm.
   * **Destino:** Utilize localhost:80 para DC/OS ou localhost:2375 para Swarm.

     O exemplo seguinte foi configurado para o DC/OS, mas será semelhante para o Docker Swarm.

     > [!NOTE]
     > A porta 80 não deve ser utilizada quando cria este túnel.
     > 

     ![Configuração do puTTY 3](./media/container-service-connect/putty3.png)

6. Quando tiver terminado, clique em **Sessão > Guardar**, para guardar a configuração da ligação.

7. Para ligar à sessão PuTTY, clique em **Abrir**. Quando se liga, pode ver a configuração da porta no registo de eventos do PuTTY.

    ![Registo de eventos do puTTY](./media/container-service-connect/putty4.png)

Depois de configurar o túnel para DC/OS, pode aceder ao pontos finais relacionados em:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Depois de configurar o túnel para Docker Swarm, abra as definições do Windows para configurar uma variável de ambiente do sistema com o nome `DOCKER_HOST` e um valor de `:2375`. Depois, pode aceder ao cluster do Swarm através da CLI do Docker.

## <a name="next-steps"></a>Passos Seguintes
Implementar e gerir contentores no seu cluster:

* [Trabalhar com o Azure Container Service e o Kubernetes](../articles/container-service/kubernetes/container-service-kubernetes-ui.md)
* [Trabalhar com o Azure Container Service e o DC/OS](../articles/container-service//dcos-swarm/container-service-mesos-marathon-rest.md)
* [Trabalhar com o Azure Container Service e o Docker Swarm](../articles//container-service/dcos-swarm/container-service-docker-swarm.md)

