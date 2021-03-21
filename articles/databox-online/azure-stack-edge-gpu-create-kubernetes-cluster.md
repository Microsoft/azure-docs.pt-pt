---
title: Crie e gere um cluster Kubernetes no dispositivo GPU Azure Stack Edge Pro| Microsoft Docs
description: Descreve como criar e gerir um cluster Kubernetes no dispositivo GPU Azure Stack Edge Pro através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631966"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Ligue e gere um cluster Kubernetes através de kubectl no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

No seu dispositivo Azure Stack Edge Pro, é criado um cluster Kubernetes quando configura o papel de computação. Uma vez criado o cluster Kubernetes, então pode ligar e gerir o cluster localmente a partir de uma máquina de clientes através de uma ferramenta nativa como *kubectl*.

Este artigo descreve como ligar a um cluster Kubernetes no seu dispositivo Azure Stack Edge Pro e, em seguida, geri-lo usando *kubectl*. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Tem acesso a um dispositivo Azure Stack Edge Pro.

2. Ativou o seu dispositivo Azure Stack Edge Pro, conforme descrito no [Activate Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

3. Ativou o papel de computação no dispositivo. Um cluster Kubernetes também foi criado no dispositivo quando configurava o cálculo no dispositivo de acordo com as instruções em [cálculo de Configuração no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

4. Tem acesso a um sistema de clientes Windows que executa o PowerShell 5.0 ou mais tarde para aceder ao dispositivo. Também pode ter qualquer outro cliente com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 

5. Tem o ponto final da API de Kubernetes a partir da página **de Dispositivo** da web UI local. Para mais informações, consulte as instruções no [ponto final da API da Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)


## <a name="connect-to-powershell-interface"></a>Ligue-se à interface PowerShell

Após a criação do cluster Kubernetes, pode aceder a este cluster para criar espaços de nome e utilizadores e atribuir aos utilizadores espaços de nome. Isto requer que se conecte à interface PowerShell do dispositivo. Siga estes passos no cliente do Windows que executa o PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Configure o acesso ao cluster via Kubernetes RBAC

Após a criação do cluster Kubernetes, pode utilizar o *kubectl* através de cmdline para aceder ao cluster. 

Nesta abordagem, cria-se um espaço de nome e um utilizador. Em seguida, associe o utilizador ao espaço de nomes. Também precisa de obter um ficheiro *config* que lhe permite utilizar um cliente Kubernetes para falar diretamente com o cluster Kubernetes que criou sem ter de se ligar à interface PowerShell do seu dispositivo Azure Stack Edge Pro.

1. Criar um espaço de nome. Escreva:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Tanto para nomes como para nomes de utilizadores, aplicam-se as [convenções de nomeação do subdomínio DNS.](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)

    Eis uma saída de exemplo:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Crie um utilizador e obtenha um ficheiro config. Escreva:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Não é possível utilizar o *aseuser* como nome de utilizador, uma vez que é reservado para um utilizador predefinido associado ao espaço de nome IoT para O Azure Stack Edge Pro.

    Aqui está uma amostra de saída do ficheiro config:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Um ficheiro config é apresentado em texto simples. Copie este ficheiro e guarde-o como um ficheiro *config.* 

    > [!IMPORTANT]
    > Não guarde o ficheiro config como *.txt* ficheiro, guarde o ficheiro sem qualquer extensão de ficheiro.

4. O ficheiro config deve viver na `.kube` pasta do seu perfil de utilizador na máquina local. Copie o ficheiro para essa pasta no seu perfil de utilizador.

    ![Localização do ficheiro config no cliente](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. Associe o espaço de nomes ao utilizador que criou. Escreva:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Eis uma saída de exemplo:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Uma vez que tenha o ficheiro config, não precisa de acesso físico ao cluster. Se o seu cliente conseguir localizar o ip do dispositivo Azure Stack Edge Pro, deverá ser capaz de direcionar o cluster utilizando comandos *kubectl.*

6. Inicie uma nova sessão powerShell sobre o seu cliente. Não precisa de estar ligado à interface do dispositivo. Pode agora instalar `kubectl` no seu cliente utilizando o seguinte comando:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Por exemplo, se o nó mestre Kubernetes estava a funcionar v1.15.2, instale v1.15.2 no cliente.

    > [!IMPORTANT]
    > Transfira um cliente com um desvio de, no máximo, uma versão secundária em relação ao mestre. A versão do cliente, mas pode liderar o mestre até uma versão menor. Por exemplo, um mestre v1.3 deve trabalhar com v1.1, v1.2 e v1.3 nós, e deve trabalhar com clientes v1.2, v1.3 e v1.4. Para obter mais informações sobre a versão do cliente Kubernetes, consulte [a versão e a versão de Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew) Para obter mais informações sobre a versão do servidor Kubernetes no Azure Stack Edge Pro, vá para obter a versão do servidor De Kubernetes.<!-- insert link-->
    > Por `kubectl` vezes, está pré-instalado no seu sistema se estiver a executar o Docker para windows ou outras ferramentas. É importante descarregar a versão específica de `kubectl` como indicado nesta secção para trabalhar com este cluster kubernetes. 

    A instalação demora vários minutos.

7. Verifique se a versão instalada é a que descarregou. Deve especificar o caminho absoluto para onde o `kubectl.exe` foi instalado no seu sistema.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Para obter mais informações sobre `kubectl` os comandos utilizados para gerir o cluster Kubernetes, vá à [visão geral do kubectl.](https://kubernetes.io/docs/reference/kubectl/overview/)

8. Adicione uma entrada DNS no ficheiro hostes no seu sistema. 

    1. Executar o Bloco de Notas como administrador e abrir o `hosts` ficheiro localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Utilize as informações que guardou da página **dispositivo** na UI local no passo anterior para criar a entrada no ficheiro dos anfitriões. 

        Por exemplo, copie este ponto final `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` para criar a seguinte entrada com endereço IP do dispositivo e domínio DNS: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Para verificar se pode ligar-se às cápsulas Kubernetes, escreva:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Agora pode implementar as suas aplicações no espaço de nomes e, em seguida, ver essas aplicações e os seus registos.

> [!IMPORTANT]   
> Há muitos comandos que não poderá executar, por exemplo, os comandos que exigem que tenha acesso administrativo. Só pode executar operações permitidas no espaço de nomes.


## <a name="remove-kubernetes-cluster"></a>Remover o cluster Kubernetes

Para remover o cluster Kubernetes, terá de remover a configuração IoT Edge.

Para obter instruções detalhadas, aceda à [configuração manage IoT Edge](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration).
   

## <a name="next-steps"></a>Passos seguintes

- [Implemente uma aplicação apátrida no seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
