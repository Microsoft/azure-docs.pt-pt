---
title: Implemente VMs no seu dispositivo Azure Stack Edge Pro GPU via Azure CLI e Python
description: Descreve como criar e gerir máquinas virtuais (VMs) num dispositivo GPU Azure Stack Edge Pro utilizando O Azure CLI e Python.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 38ba5679731b5e52eb06751a625aea9df69c76ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643216"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro usando Azure CLI e Python

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

Este tutorial descreve como criar e gerir um VM no seu dispositivo Azure Stack Edge Pro utilizando a Interface da Linha de Comando Azure (CLI) e Python.

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação VM

O fluxo de trabalho de implantação é ilustrado no diagrama seguinte.

![Fluxo de trabalho de implantação VM](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

O resumo de alto nível do fluxo de trabalho de implantação é o seguinte:

1. Ligue-se ao Gestor de Recursos Azure
2. Criar um grupo de recursos
3. Criar uma conta de armazenamento
4. Adicione blob URI ao arquivo de anfitriões
5. Instalar certificados
6. Carregar um VHD
7. Criar discos geridos a partir do VHD
8. Criar uma imagem VM a partir do disco gerido pela imagem
9. Criar VM com recursos previamente criados
10. Criar uma VNet
11. Criar um VNIC utilizando o ID da sub-rede VNet

Para obter uma explicação detalhada do diagrama do fluxo de trabalho, consulte [implementar VMs no seu dispositivo Azure Stack Edge Pro utilizando a Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). Para obter informações sobre como ligar-se ao Azure Resource Manager, consulte [Connect to Azure Resource Manager utilizando a Azure PowerShell](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerir um VM no seu dispositivo Azure Stack Edge Pro utilizando O Azure CLI e Python, tem de se certificar de que completou os pré-requisitos listados nos seguintes passos:

1. Completou as definições de rede no seu dispositivo Azure Stack Edge Pro, conforme descrito no [dispositivo Step 1: Configure Azure Stack Edge Pro](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Permitiu uma interface de rede para o cálculo. Este IP de interface de rede é utilizado para criar um interruptor virtual para a implementação de VM. Os seguintes passos acompanham-no através do processo:

    1. Vá ao **Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual.

        > [!IMPORTANT] 
        > Só é possível configurar uma porta para o cálculo.

    2. Ativar o cálculo na interface de rede. O Azure Stack Edge Pro cria e gere um interruptor virtual correspondente a essa interface de rede.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Criou e instalou todos os certificados no seu dispositivo Azure Stack Edge Pro e na loja de confiança do seu cliente. Siga o procedimento descrito no [Passo 2: Criar e instalar certificados](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

4. Criou um certificado de *.cer* codificado Base-64 (formato PEM) para o seu dispositivo Azure Stack Edge Pro. Este certificado já está carregado como cadeia de assinatura no dispositivo e instalado na loja de raiz fidedigna no seu cliente. Este certificado também é exigido em formato *pem* para python trabalhar neste cliente.

    Converta este certificado em `pem` formato utilizando o `certutil` comando. Tem de executar este comando no diretório que contém o seu certificado.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    O seguinte mostra a utilização do comando da amostra:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Você também vai adicionar isto `pem` à loja Python mais tarde.

5. Atribuiu o IP do dispositivo na sua página **de Rede** na UI web local do dispositivo. Adicione este IP a:

    - O ficheiro do anfitrião no cliente, OU,
    - A configuração do servidor DNS
    
    > [!IMPORTANT]
    > Recomendamos que modifique a configuração do servidor DNS para resolução de nomes de ponto final.

    1. Inicie **o Notepad** como administrador (os privilégios de administrador são necessários para guardar o ficheiro) e, em seguida, abra o ficheiro **de anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .
    
        ![Ficheiro de anfitriões do Windows Explorer](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Adicione as seguintes entradas no ficheiro dos **anfitriões** substituindo os valores adequados para o seu dispositivo:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Utilize a seguinte imagem para referência. Guarde o ficheiro dos **anfitriões.**

        ![hospeda ficheiro no Bloco de Notas](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Descarregue o script Python](https://aka.ms/ase-vm-python) usado neste procedimento.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Passo 1: Configurar O Azure CLI/Python no cliente

### <a name="verify-profile-and-install-azure-cli"></a>Verifique o perfil e instale o Azure CLI

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Instale o Azure CLI no seu cliente. Neste exemplo, foi instalado o Azure CLI 2.0.80. Para verificar a versão de Azure CLI, verifique o `az --version` comando.

    Segue-se a saída da amostra do comando acima referido:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Se não tiver O CLI Azure, faça o download e [instale o Azure CLI no Windows](/cli/azure/install-azure-cli-windows). Pode executar O Azure CLI utilizando o pedido de comando do Windows ou através do Windows PowerShell.

2. Tome nota da localização da CLI Python. Precisa da localização python para determinar a localização da loja de certificados de raiz fidedigna para o Azure CLI.

3. Para executar o script de amostra utilizado neste artigo, você precisará das seguintes versões da biblioteca Python:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Para instalar as versões, executar o seguinte comando:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    A seguinte saída da amostra mostra a instalação do Haikunator:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    A seguinte saída de amostra mostra a instalação de pip `msrestazure` para: 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Confie no certificado raiz Azure Stack Edge Pro CA

1. Encontre a localização do certificado na sua máquina. A localização pode variar dependendo do local onde `az cli` instalou. Executar o Windows PowerShell como administrador. Mude para o caminho onde `az cli` a Python instalada: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    Para obter o local do certificado, digite o seguinte comando:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    O cmdlet devolve o local do certificado, como se vê abaixo:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Tome nota deste local como irá usá-lo mais tarde - `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Confie no certificado raiz Azure Stack Edge Pro CA, anexando-o ao certificado Python existente. Fornecerá o caminho para onde guardou o certificado PEM mais cedo.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Um caminho de exemplo seria "C:\VM-scripts\rootteam3device.pem"
    
    Em seguida, digite a seguinte série de comandos no Windows PowerShell:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Ligue-se ao Azure Stack Edge Pro

1. Registe o seu ambiente Azure Stack Edge Pro executando o `az cloud register` comando.

    Em alguns cenários, a conectividade de saída direta da internet é encaminhada através de um proxy ou firewall, que impõe a interceção SSL. Nestes casos, o comando de registo de nuvem az pode falhar com um erro como \" Não conseguir obter pontos finais da nuvem. \" Para contornar este erro, desaprote as seguintes variáveis ambientais no Windows PowerShell:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Definir variáveis ambientais para o ponto final do Azure Resource Manager, localização onde os recursos são criados e o caminho para onde a fonte VHD está localizada. A localização dos recursos é fixada em todos os dispositivos Azure Stack Edge Pro e está definida para `dbelocal` . Também tem de especificar os prefixos do endereço e o endereço IP privado. Todas as seguintes variáveis ambientais são valores baseados nos seus `AZURE_RESOURCE_LOCATION` valores, exceto, que devem ser codificados para `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Registe o seu ambiente. Utilize os seguintes parâmetros ao executar o registo de nuvem az:

    | Valor | Descrição | Exemplo |
    | --- | --- | --- |
    | Nome do ambiente | O nome do ambiente a que está a tentar ligar | Fornecer um nome, por exemplo, `aze-environ` |
    | Ponto final do Gestor de Recursos | Esta URL `https://Management.<appliancename><dnsdomain>` é. <br> Para obter este URL, aceda à página **de Dispositivos** na UI web local do seu dispositivo. |Por exemplo, `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    O seguinte mostra a utilização da amostra do comando acima referido:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Desaprote o ambiente ativo utilizando os seguintes comandos:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    O seguinte mostra a utilização da amostra do comando acima referido:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Inscreva-se no ambiente Azure Stack Edge Pro utilizando o `az login` comando. Pode iniciar seduca no ambiente Azure Stack Edge Pro, quer como utilizador, quer como diretor de [serviço.](../active-directory/develop/app-objects-and-service-principals.md)

   Siga estes passos para iniciar sinscreva-se como *utilizador:*

   Pode especificar o nome de utilizador e a palavra-passe diretamente dentro do `az login` comando, ou autenticar utilizando um browser. Tem de fazer este último caso a sua conta tenha a autenticação de vários fatores ativada.

   O seguinte mostra a utilização da amostra `az login` de:
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Depois de utilizar o comando de início de sessão, é-lhe solicitada uma palavra-passe. Forneça a senha do Gestor de Recursos Azure.

   O seguinte mostra a saída da amostra para um sing-in bem sucedido após o fornecimento da palavra-passe:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Tome nota dos `id` `tenantId` valores e valores, uma vez que estes valores correspondem ao ID de subscrição do gestor de recursos Azure e ao ID do Gestor de Recursos Azure, respectivamente, e será utilizado no passo posterior.
       
   As seguintes variáveis ambientais devem ser definidas como *principais de serviço:*

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   O seu ID do Cliente do Gestor de Recursos Azure está codificado. O ID do inquilino do Azure Resource Manager e o ID de subscrição do Gestor de Recursos Azure estão ambos presentes na saída de `az login` comando que executou anteriormente. O segredo do cliente do Azure Resource Manager é a palavra-passe do Gestor de Recursos Azure que definiu.

   Para mais informações, consulte [a palavra-passe do Gestor de Recursos Azure.](/azure/azure-stack-edge-gpu-set-azure-resource-manager-password)

5. Mude o perfil para a versão 2019-03-01-híbrido. Para alterar a versão de perfil, executar o seguinte comando:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    O seguinte mostra a utilização da amostra `az cloud update` de:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Passo 2: Criar um VM

Um script Python é-lhe fornecido para criar um VM. Dependendo se está inscrito como utilizador ou definido como principal de serviço, o script leva a entrada em conformidade e cria um VM.

1. Executar o roteiro Python a partir do mesmo diretório onde python está instalado.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Quando o script é executado, o upload do VHD demora 20 a 30 minutos. Para visualizar o progresso da operação de upload, pode utilizar o Azure Storage Explorer ou o AzCopy.

    Aqui está uma amostra de uma execução bem sucedida do script. O script cria todos os recursos dentro de um grupo de recursos, utiliza esses recursos para criar um VM e, finalmente, elimina o grupo de recursos, incluindo todos os recursos que criou.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Passos seguintes

[Comandos Comuns de CLI Az para máquinas virtuais Linux](../virtual-machines/linux/cli-manage.md)
