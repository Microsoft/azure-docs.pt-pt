---
title: Aceder ao Dashboard do Kubernetes no Azure Stack | Documentos da Microsoft
description: Saiba como aceder ao Dashboard do Kubernetes no Azure Stack
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
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: d6428fe17dabd4eb6c0d68a56e3b44cdf3b3f8b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085308"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Aceder ao Dashboard do Kubernetes no Azure Stack 

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack* 
> [!Note]   
> Kubernetes no Azure Stack está em pré-visualização. Cenário de desligado de pilha do Azure não é atualmente suportado pela pré-visualização. 

Kubernetes inclui um dashboard da web que pode utilizar para operações de gestão básicas. Este dashboard permite-lhe ver o estado de funcionamento básico e métricas para as suas aplicações, criar e implementar serviços e editar as aplicações existentes. Este artigo mostra-lhe como configurar o dashboard do Kubernetes no Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Pré-requisitos para o Dashboard do Kubernetes

* Cluster de Kubernetes de pilha do Azure

    Terá de ter implementado um cluster do Kubernetes no Azure Stack. Para obter mais informações, consulte [Kubernetes implementar](azure-stack-solution-template-kubernetes-deploy.md).

* Cliente SSH

    Precisará de um cliente SSH para segurança ligar ao seu nó principal do cluster. Se estiver a utilizar o Windows, pode utilizar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Terá a chave privada que usou quando implementou o cluster de Kubernetes.

* FTP (PSCP)

    Também poderá ser necessário um cliente FTP que suporte SSH e SSH ficheiro protocolo de transferência para transferir os certificados a partir do nó principal para o seu computador de gestão do Azure Stack. Pode usar [FileZilla](https://filezilla-project.org/download.php?type=client). Terá a chave privada que usou quando implementou o cluster de Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Descrição geral dos passos para ativar o dashboard

1.  Exporte os certificados de Kubernetes a partir do nó principal do cluster. 
2.  Importe os certificados para o Azure Stack a máquina de gerenciamento.
2.  Abra o dashboard de web do Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportar o certificado do mestre de 

Pode obter o URL para o dashboard a partir do nó principal no seu cluster.

1. Obtenha o endereço IP público e o nome de utilizador para o master de cluster a partir do dashboard do Azure Stack. Para obter estas informações:

    - Entrar para a [portal do Azure Stack](https://portal.local.azurestack.external/)
    - Selecione **todos os serviços** > **todos os recursos**. Localize o mestre no seu grupo de recursos do cluster. O mestre com o nome `k8s-master-<sequence-of-numbers>`. 

2. Abra o nó principal no portal. Copiar o **IP público** endereço. Clique em **Connect** para obter o seu nome de utilizador no **início de sessão com a conta local de VM** caixa. Este é o mesmo nome de utilizador que definiu ao criar o cluster. Utilize o endereço IP público, em vez do endereço IP privado listados no painel do connect.

3.  Abra um cliente SSH para ligar ao principal. Se estiver a trabalhar no Windows, pode usar [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) para criar a ligação. Irá utilizar o endereço IP público para o nó principal, o nome de utilizador e adicione a chave privada que utilizou quando criou o cluster.

4.  Quando se liga a terminal, escreva `kubectl` para abrir o cliente de linha de comandos do Kubernetes.

5. Execute o seguinte comando:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Encontre o URL para o dashboard. Por exemplo: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extraia o certificado autoassinado e convertê-lo para o formato PFX. Execute o seguinte comando:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Obter a lista de segredos no **kube system** espaço de nomes. Execute o seguinte comando:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Anote o kubernetes-dashboard-token -\<XXXXX > valor. 

8.  Obter o token e guarde-o. Atualização do `kubernetes-dashboard-token-<####>` com o valor secreto do passo anterior.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importar o certificado

1. Abra o Filezilla e ligue ao nó principal. Precisará de:

    - o IP público do nó principal
    - o nome de utilizador
    - o segredo privado
    - Utilize **SFTP - SSH protocolo de transferência de ficheiros**

2. Cópia `/etc/kubernetes/certs/client.pfx` e `/etc/kubernetes/certs/ca.crt` para o seu computador de gestão do Azure Stack.

3. Anote as localizações de ficheiros. Atualizar o script com as localizações e, em seguida, abra o PowerShell com uma linha de comandos elevada. Execute o script atualizado:  

    ```PowerShell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Abra o dashboard do Kubernetes 

1. Desative o Bloqueador de pop-up no seu navegador da Web.

2. Ponto de seu navegador para o URL indicado quando executou o comando `kubectl cluster-info`. Por exemplo: https://azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. Selecione o certificado de cliente.
4. Introduza o token. 
5. Voltar a ligar para a linha de comandos de bash no nó principal e conceder permissões para `kubernetes-dashboard`. Execute o seguinte comando:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    O script fornece `kubernetes-dashboard` privilégios de administrador da Cloud. Para obter mais informações, consulte [habilitados no RBAC para clusters](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Pode utilizar o dashboard. Para obter mais informações sobre o dashboard do Kubernetes, consulte [Dashboard de interface do Usuário de Web do Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Dashboard do Kubernetes do Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Passos Seguintes 

[Implementar o Kubernetes para o Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Adicionar um cluster de Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
