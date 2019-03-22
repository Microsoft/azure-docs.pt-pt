---
title: Resolver problemas relacionados com a implementação de Kubernetes no Azure Stack | Documentos da Microsoft
description: Saiba como resolver problemas relacionados com a implementação de Kubernetes no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 01a9405c98160149782ab2cf248f64818d631dde
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293792"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Resolver problemas relacionados com a implementação de Kubernetes no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em pré-visualização. Cenário de desligado de pilha do Azure não é atualmente suportado pela pré-visualização.

O seguinte artigo analisa de resolução de problemas do seu cluster do Kubernetes. Pode rever o alerta de implementação e consultar o estado da implementação, os elementos necessários para a implementação. Poderá ter de recolher os registos de implementação do Azure Stack ou as VMs do Linux que alojam Kubernetes. Poderá também ter de contactar o administrador do Azure Stack para obter os registos de um ponto de extremidade administrativo.

## <a name="overview-of-deployment"></a>Descrição geral da implementação

Antes de iniciar a resolução de problemas do seu cluster, pode querer rever o processo de implementação de cluster de Kubernetes do Azure Stack. A implementação utiliza um modelo de solução do Azure Resource Manager para criar as VMs e instalar o motor de ACS para o seu cluster.

### <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O diagrama seguinte mostra o processo geral para implementar o cluster.

![Implementar o processo de Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Passos da implementação

1. Recolha parâmetros de entrada do item do marketplace.

    Introduza os valores necessários para configurar o cluster de Kubernetes, incluindo:
    -  **Nome de utilizador**: O nome de utilizador para as máquinas de virtuais de Linux que fazem parte do cluster de Kubernetes e DVM.
    -  **Chave pública SSH**: A chave que é utilizada para a autorização de todas as máquinas Linux que foram criadas como parte do cluster de Kubernetes e DVM.
    -  **Principal de serviço**: O ID é utilizado pelo fornecedor de nuvem do Azure do Kubernetes. O ID de cliente identificado como o ID da aplicação quando criou o seu principal de serviço. 
    -  **Segredo do cliente**: Eles chave que criou quando criou o seu principal de serviço.

2. Criar a VM de implementação e extensão de script personalizado.
    -  Criar a implementação da VM do Linux com a imagem do Linux marketplace **Ubuntu Server 16.04-LTS**.
    -  Transfira e execute a extensão de script personalizado do marketplace. O script é **Script personalizado para Linux 2.0**.
    -  Execute o script personalizado do DVM. O script realiza as seguintes tarefas:
        1. Obtém o ponto final da Galeria a partir do ponto de final de metadados do Azure Resource Manager.
        2. Obtém o ID de recurso do Active Directory a partir do ponto de final de metadados do Azure Resource Manager.
        3. Carrega o modelo de API para o motor de ACS.
        4. Implementa o motor de ACS para o cluster de Kubernetes e guarda o perfil de cloud do Azure Stack para `/etc/kubernetes/azurestackcloud.json`.
3. Crie as VMs principais.

4. Transferir e executar as extensões de script personalizado.

5. Execute o script principal.

    O script realiza as seguintes tarefas:
    - Instala etcd, Docker e Kubernetes recursos, tais como kubelet. etcd é um arquivo de chave-valor distribuída que fornece uma forma de armazenar dados num cluster de máquinas. Docker suporta virtualizations de nível do sistema operacional básico conhecidos como contentores. Kubelet é o agente de nó que é executado em cada nó de Kubernetes.
    - Configura a **etcd** serviço.
    - Configura a **kubelet** serviço.
    - Inicia kubelet. Esta tarefa envolve os seguintes passos:
        1. Inicia o serviço de API.
        2. Inicia o serviço de controlador.
        3. Inicia o serviço de scheduler.
6. Crie agente de VMs.

7. Transfira e execute a extensão de script personalizado.

7. Execute o script de agente. O script personalizado do agente faz as seguintes tarefas:
    - Instala **etcd**.
    - Configura a **kubelet** serviço.
    - Adere ao cluster de Kubernetes.

## <a name="steps-for-troubleshooting"></a>Passos de resolução de problemas

Pode recolher registos nas VMs que suportam o seu cluster do Kubernetes. Também pode rever o registo de implementação. Poderá ter comunicar com o seu administrador do Azure Stack para verificar a versão do Azure Stack que terá de usar e obter registos do Azure Stack que estão relacionadas com a implementação.

1. Reveja a [estado de implementação](#review-deployment-status) e [obtém os registos](#get-logs-from-a-vm) partir do nó principal no cluster do Kubernetes.
2. Certifique-se de que está a utilizar a versão mais recente do Azure Stack. Se tiver a certeza de qual é a versão que está a utilizar, contacte o administrador do Azure Stack.
3.  Reveja os ficheiros de criação de VM. Pode ter tinha os seguintes problemas:  
    - A chave pública pode ser inválida. Reveja a chave que criou.  
    - A criação da VM pode ter acionada por um erro interno ou acionada por um erro de criação. Vários fatores podem provocar erros, incluindo as limitações de capacidade para a sua subscrição do Azure Stack.
    - Certifique-se de que o nome de domínio completamente qualificado (FQDN) para a VM começa com um prefixo duplicado.
4.  Se a VM estiver **OK**, em seguida, avaliar o DVM. Se o DVM tem uma mensagem de erro:

    - A chave pública pode ser inválida. Reveja a chave que criou.  
    - Terá de contactar o administrador do Azure Stack para obter os registos para o Azure Stack, com os pontos de extremidade com privilégios. Para obter mais informações, consulte [ferramentas de diagnóstico do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Se tiver uma pergunta sobre a implementação, pode postá-lo ou ver se alguém tem já respondeu à pergunta na [fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Rever o estado de implementação

Ao implementar o cluster do Kubernetes, pode rever o estado de implementação para verificar a existência de quaisquer problemas.

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).
2. Selecione **grupos de recursos**e, em seguida, selecione o nome do grupo de recursos que utilizou quando implementar o cluster de Kubernetes.
3. Selecione **implementações**e, em seguida, selecione a **nome da implementação**.

    ![Resolução de problemas](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consulte a janela resolução de problemas. Cada recurso implementado fornece as seguintes informações:
    
    | Propriedade | Descrição |
    | ----     | ----        |
    | Recurso | O nome do recurso. |
    | Type | O fornecedor de recursos e o tipo de recurso. |
    | Estado | O estado do item. |
    | Carimbo de data/hora | O carimbo de hora UTC do tempo. |
    | Detalhes da operação | Os detalhes da operação, como o fornecedor de recursos que estava envolvido a operação, o ponto final de recurso e o nome do recurso. |

    Cada item tem um ícone de estado de verde ou vermelho.

## <a name="review-deployment-logs"></a>Rever os registos de implementação

Se o portal do Azure Stack não fornece informações suficientes para resolver problemas ou superar uma falha de implementação, a próxima etapa é examinar os registos do cluster. Para obter manualmente os logs de implantação, normalmente, tem de ligar a uma das máquinas de virtuais de principal do cluster. Uma abordagem alternativa mais simples seria para transferir e executar o seguinte procedimento [script de Bash](https://aka.ms/AzsK8sLogCollectorScript) fornecidos pela equipa do Azure Stack. Este script liga-se para o DVM e máquinas de virtuais do cluster, recolhe registos de cluster e de sistema relevantes e transfere-os para a estação de trabalho.

### <a name="prerequisites"></a>Pré-requisitos

Precisará de uma linha de comandos do Bash na máquina que utiliza para gerir o Azure Stack. Num computador Windows, pode obter um Bash prompt instalando [Git para Windows](https://git-scm.com/downloads). Uma vez instalado, procure _Git Bash_ no seu menu Iniciar.

### <a name="retrieving-the-logs"></a>A obter os registos

Siga estes passos para recolher e transferir os registos do cluster:

1. Abra uma linha de comandos do Bash. A partir de uma máquina Windows, abra _Git Bash_ ou executar: `C:\Program Files\Git\git-bash.exe`.

2. Transferir o script de recoletor de registo ao executar os comandos seguintes na sua linha de comandos do Bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Procure as informações necessárias pelo script e executá-lo:

    | Parâmetro           | Descrição                                                                                                      | Exemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | O IP público ou o nome de domínio completamente qualificado (FQDN) do DVM. Nome da máquina virtual é iniciado com `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Imprima a utilização do comando. | |
    | -i,-arquivo de identidade | O ficheiro de chave privada RSA passado para o item do marketplace, ao criar o cluster de Kubernetes. É necessário para remoto para os nós do Kubernetes. | C:\data\id_rsa.pem (Putty)<br>~/.SSH/id_rsa (SSH)
    | -m, --master-host   | O IP público ou o nome de domínio completamente qualificado (FQDN) de um nó principal do Kubernetes. Nome da máquina virtual é iniciado com `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | O nome de utilizador passado para o item do marketplace, ao criar o cluster de Kubernetes. Necessário para remoto para os nós do Kubernetes | azureuser (valor predefinido) |


   Ao adicionar os valores dos parâmetros, o comando pode ser algo semelhante a isto:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Após alguns minutos, o script irá transmitir os registos recolhidos para um diretório chamado `KubernetesLogs_{{time-stamp}}`. Lá encontrará um diretório para cada máquina virtual que pertença ao cluster.

    O script de recoletor de registo também busca de erros nos ficheiros de registo e incluem os passos de resolução de problemas, caso isso aconteça, encontrar um problema conhecido. Certifique-se de que está a executar a versão mais recente do script para aumentar as chances de encontrar problemas conhecidos.

> [!Note]  
> Confira esta GitHub [repositório](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) para obter mais detalhes sobre o script de recoletor de registo.

## <a name="next-steps"></a>Passos Seguintes

[Implementar o Kubernetes para o Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Adicionar um cluster de Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
