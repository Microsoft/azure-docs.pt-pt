---
title: Resolver problemas relacionados com a implementação de Kubernetes no Azure Stack | Documentos da Microsoft
description: Saiba como resolver problemas relacionados com a implementação de Kubernetes no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.reviewer: waltero
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 5436b562b4f9054e0e00e3cc6abb1724797437db
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729641"
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
    - Configura o serviço de etcd.
    - Configura o serviço de kubelet.
    - Inicia kubelet. Esta tarefa envolve os seguintes passos:
        1. Inicia o serviço de API.
        2. Inicia o serviço de controlador.
        3. Inicia o serviço de scheduler.
6. Crie agente de VMs.

7. Transfira e execute a extensão de script personalizado.

7. Execute o script de agente. O script personalizado do agente faz as seguintes tarefas:
    - Instala etcd
    - Configura o serviço de kubelet
    - Adere ao cluster de Kubernetes

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

## <a name="get-logs-from-a-vm"></a>Obter registos a partir de uma VM

Para gerar os registos, precisa de ligar ao mestre de VM para o seu cluster, abra uma linha de comandos do bash e, em seguida, executar um script. O mestre de VM pode ser encontrada no seu grupo de recursos do cluster e o nome `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Pré-requisitos

É necessário um bash linha de comandos no computador que utilizar para gerir o Azure Stack. Utilize o bash para executar os scripts de aceder aos registos. Num computador Windows, pode usar a linha de comandos de bash, que é instalada com o Git. Para obter a versão mais recente do git, veja [transferências Git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Obter registos

Para obter os registos, siga os passos seguintes:

1. Abra uma linha de comandos do bash. Se estiver a utilizar o Git num computador Windows, pode abrir uma linha de comandos bash do seguinte caminho: `c:\programfiles\git\bin\bash.exe`.
2. Execute os seguintes comandos do bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > No Windows, não precisa de executar `sudo`. Em vez disso, pode simplesmente usar `chmod 744 getkuberneteslogs.sh`.

3. Na mesma sessão, execute o seguinte comando com os parâmetros atualizados para corresponder ao seu ambiente:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmd-host 192.168.102.37
    ```

4. Reveja os parâmetros e defina os valores com base no seu ambiente.
    | Parâmetro           | Descrição                                                                                                      | Exemplo                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host       | O IP público ou o FQDN do DVM. O nome da VM começa com `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |
    | -f, --force | Não pedir antes de carregar a chave privada. | |
    | -i,-arquivo de identidade | RSA ficheiro de chave privada para ligar a VM principal do Kubernetes. A chave tem de começar com: <br>`-----BEGIN RSA PRIVATE KEY-----` | C:\data\id_rsa.pem                                                        |
    | -h, --help  | Imprimir a utilização do comando para `getkuberneteslogs.sh` script. | |
    | -m, --master-host          | O IP público ou o nome de domínio completamente qualificado (FQDN) do mestre de cluster de Kubernetes VM. O nome da VM começa com `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | O nome de utilizador do mestre de cluster de Kubernetes VM. Este nome é definir quando configurar o item do marketplace.                                                                    | azureuser                                                                     |




   Ao adicionar os valores dos parâmetros, ele poderia uma aparência semelhante ao seguinte código:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Uma execução com êxito cria os registos.

    ![Registos gerados](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Obter os registos nas pastas que foram criados pelo comando. O comando cria novas pastas e tempo apresenta-os.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Passos Seguintes

[Implementar o Kubernetes para o Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Adicionar um cluster de Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
