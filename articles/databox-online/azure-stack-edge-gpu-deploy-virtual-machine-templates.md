---
title: Implemente VMs no seu dispositivo Azure Stack Edge Pro através de modelos
description: Descreve como criar e gerir máquinas virtuais (VMs) num dispositivo Azure Stack Edge Pro utilizando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: eeefbcdc080620c60f7cd49b8f749375e23ddd02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899716"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro através de modelos

Este tutorial descreve como criar e gerir um VM no seu dispositivo Azure Stack Edge Pro usando modelos. Estes modelos são ficheiros JavaScript Object Notation (JSON) que definem a infraestrutura e configuração para o seu VM. Nestes modelos, você especifica os recursos a implementar e as propriedades para esses recursos.

Os modelos são flexíveis em diferentes ambientes, pois podem tomar parâmetros como entrada no tempo de execução a partir de um ficheiro. A estrutura de nomeação padrão é `TemplateName.json` para o modelo e para o arquivo de `TemplateName.parameters.json` parâmetros. Para obter mais informações sobre modelos ARM, vá ao [que são os modelos do Gestor de Recursos Azure?](../azure-resource-manager/templates/overview.md)

Neste tutorial, usaremos modelos de amostra pré-escritos para criar recursos. Não precisa de editar o ficheiro do modelo e pode modificar apenas os `.parameters.json` ficheiros para personalizar a implementação para a sua máquina. 

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação VM

Para implantar VMs Azure Stack Edge Pro em muitos dispositivos, pode utilizar um único VHD sysprepped para a sua frota completa, o mesmo modelo para implantação, e apenas fazer pequenas alterações nos parâmetros desse modelo para cada local de implantação (estas alterações podem ser manualmente como estamos a fazer aqui, ou programática.) 

O resumo de alto nível do fluxo de trabalho de implantação utilizando modelos é o seguinte:

1. **Configure pré-requisitos** - Existem 3 tipos de pré-requisitos; dispositivo, cliente, e para o VM.

    1. **Pré-requisitos do dispositivo**

        1. Ligue-se ao Azure Resource Manager no dispositivo.
        2. Ativar o cálculo através da UI local.

    2. **Pré-requisitos do cliente**

        1. Descarregue os modelos VM e ficheiros associados ao cliente.
        1. Configurar opcionalmente TLS 1.2 no cliente.
        1. [Descarregue e instale o Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) no seu cliente.

    3. **Pré-requisitos VM**

        1. Crie um grupo de recursos na localização do dispositivo que contenha todos os recursos VM.
        1. Crie uma conta de armazenamento para carregar o VHD usado para criar imagem VM.
        1. Adicione a conta de armazenamento local URI ao DNS ou ficheiro de anfitriões no cliente que acede ao seu dispositivo.
        1. Instale o certificado de armazenamento blob no dispositivo, bem como no cliente local que aceda ao seu dispositivo. Instale opcionalmente o certificado de armazenamento de bolhas no Explorador de Armazenamento.
        1. Crie e carrema um VHD para a conta de armazenamento criada anteriormente.

2. **Criar VM a partir de modelos**

    1. Crie uma imagem VM e um VNet utilizando `CreateImageAndVnet.parameters.json` o ficheiro de parâmetros e o modelo `CreateImageAndVnet.json` de implementação.
    1. Crie um VM com recursos previamente criados utilizando `CreateVM.parameters.json` o ficheiro de parâmetros e o modelo  `CreateVM.json` de implementação.

## <a name="device-prerequisites"></a>Pré-requisitos do dispositivo

Configure estes pré-requisitos no seu dispositivo Azure Stack Edge Pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Pré-requisitos do cliente

Configure estes pré-requisitos no seu cliente que serão utilizados para aceder ao dispositivo Azure Stack Edge Pro.

1. [Descarregue o Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se estiver a usá-lo para fazer o upload de um VHD. Em alternativa, pode baixar o AzCopy para fazer o upload de um VHD. Poderá ser necessário configurar o TLS 1.2 na sua máquina cliente se executar versões mais antigas do AzCopy. 
1. [Descarregue os modelos e os ficheiros de parâmetros VM](https://aka.ms/ase-vm-templates) para a sua máquina de clientes. Desaperte-o num diretório que usarás como diretório de trabalho.


## <a name="vm-prerequisites"></a>Pré-requisitos VM

Configure estes pré-requisitos para a criação de recursos que serão necessários para a criação de VM. 

    
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um recipiente lógico no qual os recursos Azure, tais como conta de armazenamento, disco, disco gerido são implantados e geridos.

> [!IMPORTANT]
> Todos os recursos são criados no mesmo local que o do dispositivo e a localização está definida para **DBELocal.**

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

É apresentada abaixo uma saída de exemplo.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma nova conta de armazenamento utilizando o grupo de recursos criado no passo anterior. Esta é uma **conta de armazenamento local** que será usada para carregar a imagem virtual do disco para o VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Apenas as contas de armazenamento locais, como o armazenamento localmente redundante (Standard_LRS ou Premium_LRS) podem ser criadas através do Azure Resource Manager. Para criar contas de armazenamento hierarquizadas, consulte os passos em [Add, conecte-se às contas de armazenamento no seu Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

É apresentada abaixo uma saída de exemplo.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Para obter a chave da conta de armazenamento, executar o `Get-AzureRmStorageAccountKey` comando. Uma amostra deste comando é mostrada aqui.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Adicione blob URI ao arquivo de anfitriões

Certifique-se de que já adicionou o blob URI no ficheiro hosts para o cliente que está a usar para ligar ao armazenamento blob. **Executar o Bloco de Notas como administrador** e adicionar a seguinte entrada para o blob URI no `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Num ambiente típico, o seu DNS seria configurado para que todas as contas de armazenamento apontassem para o dispositivo Azure Stack Edge Pro com uma `*.blob.devicename.domainname.com` entrada.

### <a name="optional-install-certificates"></a>(Opcional) Instalar certificados

Ignore este passo se se ligar através do Storage Explorer utilizando *http*. Se estiver a utilizar *https,* terá de instalar certificados apropriados no Explorador de Armazenamento. Neste caso, instale o certificado de ponta final blob. Para mais informações, consulte como criar e carregar certificados em [Obter certificados.](azure-stack-edge-j-series-manage-certificates.md) 

### <a name="create-and-upload-a-vhd"></a>Criar e carregar um VHD

Certifique-se de que tem uma imagem de disco virtual que pode usar para carregar no passo posterior. Siga os passos na [Criar uma imagem VM](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Copie quaisquer imagens de disco a serem usadas em bolhas de página na conta de armazenamento local que criou nos passos anteriores. Pode utilizar uma ferramenta como [o Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ou a [AzCopy para fazer o upload do VHD para a conta de armazenamento](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) que criou em etapas anteriores. 

### <a name="use-storage-explorer-for-upload"></a>Use o Explorador de Armazenamento para fazer upload

1. Abra o Explorador de Armazenamento. Vá à **Edição** e certifique-se de que a aplicação está definida para **APIs target Azure Stack**.

    ![Definir o alvo para APIs de pilha de Azure](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Instale o certificado de cliente em formato PEM. Vá a **editar > certificados SSL > Certificados de Importação.** Aponte para o certificado do cliente.

    ![Certificado de ponto final de armazenamento de blob de importação](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Se estiver a utilizar certificados gerados pelo dispositivo, descarregue e converta o certificado de ponto final de armazenamento blob `.cer` para um `.pem` formato. Execute o seguinte comando. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Se estiver a levar o seu próprio certificado, utilize o certificado raiz da corrente de assinatura em `.pem` formato.

3. Depois de importar o certificado, reinicie o Storage Explorer para que as alterações entrem em vigor.

    ![Reiniciar explorador de armazenamento](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. No painel esquerdo, clique nas contas de **armazenamento** à direita e selecione **Connect to Azure Storage**. 

    ![Ligar ao Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selecione **Use a storage account name and key** (Utilizar o nome e a chave de uma conta de armazenamento). Selecione **Seguinte**.

    ![Ligar ao Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Na **Ligação com**o Nome e a Chave , forneça o **nome do Visor**, **nome da conta de armazenamento**, **Tecla conta**de armazenamento Azure . Selecione **Outro domínio** de Armazenamento e, em seguida, forneça a `<device name>.<DNS domain>` cadeia de ligação. Se não instalou um certificado no Storage Explorer, verifique a opção **Utilização HTTP.** Selecione **Seguinte**.

    ![Conecte-se com nome e chave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Reveja o **resumo da Ligação** e selecione **Connect**.

8. A conta de armazenamento aparece no painel esquerdo. Selecione e expanda a conta de armazenamento. Selecione **recipientes Blob,** clique à direita e selecione **Criar o Recipiente Blob**. Forneça um nome para o seu recipiente de bolhas.

9. Selecione o recipiente que acabou de criar e no painel direito, selecione **upload > carregar ficheiros**. 

    ![Carregar ficheiro VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Navegue e aponte para o VHD que pretende carregar nos **ficheiros Selecionados**. Selecione **o tipo Blob** como **blob de página** e selecione **Upload**.

    ![Carregar ficheiro VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Uma vez que o VHD é carregado para o recipiente blob, selecione o VHD, clique à direita e, em seguida, selecione **Propriedades**. 

    ![Carregar ficheiro VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copie e guarde o **Uri** como utilizará nos passos posteriores.

    ![Copiar URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Crie imagem e VNet para o seu VM

Para criar imagem e uma rede virtual para o seu VM, terá de editar o ficheiro de `CreateImageAndVnet.parameters.json` parâmetros e, em seguida, implementar o modelo `CreateImageAndVnet.json` que utiliza este ficheiro de parâmetros.


### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O ficheiro `CreateImageAndVnet.parameters.json` toma os seguintes parâmetros: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Edite o ficheiro `CreateImageAndVnet.parameters.json` para incluir o seguinte para o seu dispositivo Azure Stack Edge Pro:

1. Forneça o tipo de SO correspondente ao VHD que irá carregar. O tipo de SO pode ser Windows ou Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Altere o URI de imagem para o URI da imagem que carregou no passo anterior:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Se estiver a utilizar *https com* o Storage Explorer, altere-o para *um URI https.*

3. Mude o `addressPrefix` e `subnetPrefix` . . Na UI local do seu dispositivo, aceda à página **'Rede'.** Encontre a porta que ativou para calcular. Obtenha o endereço IP da rede base e adicione a máscara de sub-rede para criar a notação CIDR. Se tiver uma sub-rede padrão de 255.255.255.0, faça-o substituindo o último número do endereço IP por 0 e adicionando /24 ao fim. Assim, 10.126.68.0 com uma máscara de sub-rede 255.255.255.0 torna-se 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Forneça o nome de imagem único, nome VNet e nome da sub-rede para os parâmetros.

    Aqui está uma amostra json que é usada neste artigo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Guarde o ficheiro de parâmetros.


### <a name="deploy-template"></a>Implementar o modelo 

Implemente o modelo `CreateImageAndVnet.json` . Este modelo implementa os recursos de imagem vNet e que serão usados para criar VMs no passo posterior.

> [!NOTE]
> Quando implementar o modelo se tiver um erro de autenticação, as suas credenciais Azure para esta sessão podem ter expirado. Volte a fazer o `login-AzureRM` comando para ligar ao Azure Resource Manager no seu dispositivo Azure Stack Edge Pro novamente.

1. Execute o seguinte comando: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Verifique se a imagem e os recursos VNet são a provisionados com sucesso. Aqui está uma amostra de uma imagem criada com sucesso e VNet.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Criar VM

### <a name="edit-parameters-file-to-create-vm"></a>Editar ficheiro de parâmetros para criar VM
 
Para criar um VM, utilize o `CreateVM.parameters.json` ficheiro de parâmetros. São precisos os seguintes parâmetros.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Atribua parâmetros apropriados `CreateVM.parameters.json` para o seu dispositivo Azure Stack Edge Pro.

1. Forneça um nome único, nome de interface de rede e nome ipconfig. 
1. Introduza um nome de utilizador, palavra-passe e um tamanho VM suportado.
1. Dê o mesmo nome para **VnetName,** **subnetName**e **ImageName** como indicado nos parâmetros para `CreateImageAndVnet.parameters.json` . Por exemplo, se tiver dado vnetName, subnetName e ImageName como **vnet1**, **subnet1**e **imagem1**, mantenha esses valores iguais para os parâmetros deste modelo também.
1. Agora vai precisar de um endereço IP estático para atribuir ao VM que está na rede de sub-redes definida acima. Substitua **o PrivateIPAddress** por este endereço no ficheiro de parâmetros. Para que o VM obtenha um endereço IP do servidor DCHP local, deixe o `privateIPAddress` valor em branco.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Guarde o ficheiro de parâmetros.

    Aqui está uma amostra json que é usada neste artigo.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Implementar modelo para criar VM

Implementar o modelo de criação VM `CreateVM.json` . Este modelo cria uma interface de rede a partir do VNet existente e cria VM a partir da imagem implementada.

1. Execute o seguinte comando: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    A criação de VM levará 15-20 minutos. Aqui está uma amostra de uma VM criada com sucesso.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
Também pode executar o `New-AzureRmResourceGroupDeployment` comando assíncronosamente com `–AsJob` parâmetro. Aqui está uma saída de amostra quando o cmdlet corre em segundo plano. Em seguida, pode consultar o estado de trabalho que é criado usando o `Get-Job` cmdlet.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Completed     True            localhost            New-AzureRmResourceGro...
    ```

7. Verifique se o VM está a ser a provisionado com sucesso. Execute o seguinte comando:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Ligar a uma VM

Dependendo se criou um Windows ou um Linux VM, os passos para ligar podem ser diferentes.

### <a name="connect-to-windows-vm"></a>Ligue-se ao Windows VM

Siga estes passos para ligar a um VM do Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Ligue-se ao Linux VM

Siga estes passos para ligar a um Linux VM.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Operações e cmdlets não suportados

Extensões, conjuntos de escala, conjuntos de disponibilidade, instantâneos não são suportados.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Passos seguintes

[Cmdlets do Gestor de Recursos Azure](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
