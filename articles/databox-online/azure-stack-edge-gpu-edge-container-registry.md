---
title: Ativar um registo de contentores Edge no dispositivo GPU Azure Stack Edge Pro
description: Descreve como ativar um registo local de contentores Edge no dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f19625f1d558071ccb29487efe56146756c7692c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437541"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Ativar o registo do contentor Edge no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como ativar o registo do contentor Edge e utilizá-lo a partir do cluster Kubernetes no seu dispositivo Azure Stack Edge Pro. O exemplo usado no artigo detalha como empurrar uma imagem de um registo de origem, neste caso, o registo do Microsoft Container, para o registo no dispositivo Azure Stack Edge, o registo do contentor Edge.

### <a name="about-edge-container-registry"></a>Sobre o registo de contentores Edge

As aplicações computacional contentorizadas funcionam em imagens de contentores e estas imagens são armazenadas em registos. Os registos podem ser públicos como Docker Hub, privado ou fornecedor de nuvem gerido, como o Registo de Contentores Azure. Para mais informações, consulte [sobre registos, repositórios e imagens.](../container-registry/container-registry-concepts.md)

Um registo de contentores Edge fornece um repositório no Edge, no seu dispositivo Azure Stack Edge Pro. Pode utilizar este registo para armazenar e gerir as imagens do seu contentor privado.

Num ambiente multi-nó, as imagens do contentor podem ser descarregadas e empurradas para o registo do contentor Edge uma vez. Todas as aplicações Edge podem utilizar o registo do contentor Edge para posterior implementações.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Tem acesso a um dispositivo Azure Stack Edge Pro.

1. Ativou o seu dispositivo Azure Stack Edge Pro, conforme descrito no [Activate Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Ativou o papel de computação no dispositivo. Um cluster Kubernetes também foi criado no dispositivo quando configurava o cálculo no dispositivo de acordo com as instruções em [cálculo de Configuração no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Tem o ponto final da API de Kubernetes a partir da página **de Dispositivo** da web UI local. Para obter mais informações, consulte as instruções no [ponto de final da API da Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Tem acesso a um sistema de clientes com um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) Se utilizar um cliente Windows, o sistema deverá executar o PowerShell 5.0 ou mais tarde para aceder ao dispositivo.

    1. Se quiser puxar e empurrar as suas próprias imagens de contentores, certifique-se de que o sistema tem o cliente Docker instalado. Se utilizar um cliente Windows, [instale o Computador De Docker no Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Ativar o registo do contentor como complemento

O primeiro passo é ativar o registo do contentor Edge como complemento.

1. [Ligue-se à interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Para ativar o registo do contentor como complemento, escreva: 

    `Set-HcsKubernetesContainerRegistry`
    
    Esta operação pode demorar vários minutos a ser concluída.

    Aqui está a saída da amostra deste comando:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Para obter os dados do registo do contentor, escreva:

    `Get-HcsKubernetesContainerRegistryInfo`

    Aqui está a amostra deste comando:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Tome nota do nome de utilizador e da palavra-passe a partir da saída de `Get-HcsKubernetesContainerRegistryInfo` . Estas credenciais são usadas para iniciar sings no registo do contentor Edge enquanto empurra imagens.         


## <a name="manage-container-registry-images"></a>Gerir imagens do registo de contentores

Pode querer aceder ao registo do contentor a partir do exterior do seu dispositivo Azure Stack Edge. Também pode querer empurrar ou puxar imagens no registo.

Siga estes passos para aceder ao registo do contentor Edge:

1. Obtenha os detalhes do ponto final para o registo do contentor Edge.
    1. Na UI local do dispositivo, aceda ao **Dispositivo**.
    1. Localizar o **ponto final do registo do contentor Edge**.
        ![Ponto final do registo do contentor de borda na página do dispositivo](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Copie este ponto final e crie uma entrada de DNS correspondente no `C:\Windows\System32\Drivers\etc\hosts` ficheiro do seu cliente para ligar ao ponto final do registo do contentor Edge. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Adicione entrada DNS para o ponto final do registo do contentor Edge](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Descarregue o certificado de registo de contentores Edge da UI local. 
    1. Na UI local do dispositivo, aceda aos **Certificados.**
    1. Localize a entrada para o **certificado de registo do contentor Edge**. À direita desta entrada, selecione o **Download** para descarregar o certificado de registo do contentor Edge no seu sistema cliente que utilizará para aceder ao seu dispositivo. 

        ![Download Certificado de ponto final do registo de contentores Edge](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Instale o certificado descarregado no cliente. Se utilizar um cliente Windows, siga estes passos: 
    1. Selecione o certificado e no **Assistente de Importação de Certificados,** selecione a localização da loja como máquina **local**. 

        ![Instalar certificado 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Instale o certificado na sua máquina Local na loja de raiz fidedigna. 

        ![Instalar certificado 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Depois de instalado o certificado, reinicie o cliente Docker no seu sistema.

1. Inscreva-se no registo do contentor Edge. Escreva:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Forneça o ponto final do registo do contentor Edge a partir da página **dispositivos,** e o nome de utilizador e senha que obteve a partir da saída de `Get-HcsKubernetesContainerRegistryInfo` . 

1. Utilize comandos de empurrar ou puxar o estivador para empurrar ou retirar imagens do contentor do registo do contentor.
 
    1. Retire uma imagem da imagem do Registo do Contentor da Microsoft. Escreva:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Crie um pseudónimo da imagem que puxou com o caminho totalmente qualificado para o seu registo.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Empurre a imagem para o seu registo.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Coloque a imagem que empurrou para o seu registo.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Aqui está uma amostra de saída dos comandos de puxar e empurrar:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Procure para `http://localhost:8080` ver o recipiente de funcionamento. Neste caso, verá o webserver nginx a funcionar.

    ![Ver o recipiente de corrida](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Para parar e retirar o recipiente, prima `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Use imagens de registo de contentores Edge através de cápsulas Kubernetes

Agora pode implementar a imagem que empurrou no seu registo de contentores Edge a partir das cápsulas Kubernetes.

1. Para implementar a imagem, é necessário configurar o acesso ao cluster através *de kubectl*. Crie um espaço de nome, um utilizador, conceda o acesso do utilizador ao espaço de nomes e obtenha um ficheiro *config.* Certifique-se de que pode ligar-se às cápsulas Kubernetes. 
    
    Siga todos os passos em [Connect to e gerencie um cluster Kubernetes através de kubectl no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Aqui está uma saída de amostra para um espaço de nome no seu dispositivo de onde o utilizador pode aceder ao cluster Kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Os segredos de puxar a imagem já estão definidos em todos os espaços de nomes Kubernetes no seu dispositivo. Podes obter segredos usando o `get secrets` comando. Eis uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Coloque uma cápsula no seu espaço de identificação utilizando kubectl. Utilize o seguinte `yaml` . 

    Substitua a imagem: `<image-name>` com a imagem empurrada para o registo do contentor. Consulte os segredos nos seus espaços de nome utilizando imagensPullSecrets com um nome: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Aplique a implementação no espaço de nome que criou usando o comando de aplicação. Verifique se o recipiente está a funcionar. Eis uma saída de exemplo:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Apagar imagens do registo de contentores

O armazenamento do Registo de Contentores edge está hospedado numa parte local dentro do seu dispositivo Azure Stack Edge Pro, que é limitado pelo armazenamento disponível no dispositivo. É da sua responsabilidade eliminar imagens de estivadores não utilizados do registo do contentor utilizando o Docker HTTP v2 API ( https://docs.docker.com/registry/spec/api/) .  

Para remover uma ou mais imagens de contentores, siga estes passos:

1. Desa estale o nome da imagem para a imagem que pretende eliminar.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Deite o nome de utilizador e a palavra-passe do registo do contentor como credencial PS

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Listar as etiquetas associadas à imagem

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Lista a digestão associada à etiqueta que pretende eliminar. Isto usa $tags da saída do comando acima. Se tiver várias etiquetas, selecione uma delas e use no comando seguinte.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Eliminar a imagem usando a digestão da imagem:tag

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Depois de apagar as imagens não used, o espaço associado às imagens não reportadas é automaticamente recuperado por um processo que funciona todas as noites. 

## <a name="next-steps"></a>Passos seguintes

- [Implemente uma aplicação apátrida no seu Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
