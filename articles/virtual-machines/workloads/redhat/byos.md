---
title: Red Hat Enterprise Linux traz imagens Azure de subscrição própria | Microsoft Docs
description: Saiba mais sobre as imagens de trazer a sua própria subscrição para Red Hat Enterprise Linux em Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.openlocfilehash: af4f9dac30c1e443e9647a5b752cdabf9c094a6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676084"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux traz imagens de ouro em Azure

As imagens Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure através de um modelo pay-as-you-go ou bring-your-your-own-subscription (BYOS) (Red Hat Gold Image). Este artigo fornece uma visão geral das Imagens de Ouro do Chapéu Vermelho em Azure.

>[!NOTE]
> As imagens RHEL BYOS Gold estão disponíveis nas nuvens do Público Azure (comercial) e do Governo Azure. Não estão disponíveis em Azure China ou Azure Blackforest clouds.

## <a name="important-points-to-consider"></a>Pontos importantes a considerar

- As Imagens Red Hat Gold fornecidas neste programa são imagens RHEL prontas a produção semelhantes às imagens de pay-as-you-go da RHEL no Azure Marketplace.
- As imagens seguem as políticas atuais descritas nas [imagens Red Hat Enterprise Linux no Azure](./redhat-images.md).
- As políticas de suporte padrão aplicam-se aos VMs criados a partir destas imagens.
- Os VMs ateados pela Red Hat Gold Images não têm taxas RHEL associadas a imagens de pay-as-you-go da RHEL.
- As imagens não são enentadas. Tem de utilizar o Red Hat Subscription-Manager para registar e subscrever os VMs para obter atualizações diretamente da Red Hat.
- É possível passar de imagens pay-as-you-go para BYOS usando o [Azure Hybrid Benefit](../../linux/azure-hybrid-benefit-linux.md). No entanto, não é possível mudar de um BYOS inicialmente implantado para modelos de faturação pay-as-you-go para imagens Linux. Para mudar o modelo de faturação de BYOS para pay-as-you-go, tem de recolocar o VM a partir da respetiva imagem.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para aceder às Imagens De Chapéu Vermelho

1. Familiarize-se com os termos do [programa Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Ative as subscrições do Red Hat para o Cloud Access no [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Tem de ter à mão as subscrições do Azure que vão estar registadas no Cloud Access.

1. Se as subscrições do Red Hat que ativou para o Cloud Access cumprirem os requisitos de elegibilidade, as suas subscrições Azure são automaticamente ativadas para acesso à Imagem Dourada.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Depois de terminar os passos de ativação do Cloud Access, o Red Hat valida a sua elegibilidade para as Imagens De Ouro do Chapéu Vermelho. Se a validação for bem sucedida, terá acesso às Imagens Douradas dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as imagens de ouro do chapéu vermelho do portal Azure

1. Depois da subscrição do Azure ter acesso a Imagens Gold hat vermelhas, pode localizá-las no [portal Azure.](https://portal.azure.com) Ir para **criar um recurso** Ver  >  **tudo.**

1. No topo da página, verá que tem ofertas privadas.

    ![Ofertas privadas de marketplace](./media/rhel-byos-privateoffers.png)

1. Selecione o link roxo ou desloque-se até ao fundo da página para ver as suas ofertas privadas.

1. O resto do provisionamento na UI não é diferente de qualquer outra imagem do Chapéu Vermelho existente. Escolha a sua versão RHEL e siga as instruções para a disponibilização do seu VM. Este processo também permite aceitar os termos da imagem no passo final.

>[!NOTE]
>Estes passos até agora não permitem a sua Imagem de Ouro do Chapéu Vermelho para implantação programática. É necessário um passo adicional, tal como descrito na secção "Informações adicionais".

O resto deste documento centra-se no método CLI para prever e aceitar termos na imagem. A UI e a CLI são totalmente permutáveis no que diz respeito ao resultado final (um VM de imagem de ouro RHEL provisionado).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as imagens de ouro do chapéu vermelho do Azure CLI

As seguintes instruções acompanham-no através do processo de implantação inicial de um RHEL VM utilizando o Azure CLI. Estas instruções pressupõem que tem o [Azure CLI instalado](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de que utiliza todas as letras minúsculas na editora, oferta, plano e referências de imagem para todos os seguintes comandos.

1. Verifique se está na subscrição desejada.

    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para o seu VM de imagem de ouro de chapéu vermelho.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos de imagem.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Estes termos precisam de ser *aceites uma vez por subscrição do Azure, por imagem SKU*.

1. (Opcional) Validar a sua implantação em VM com o seguinte comando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Provisionar o seu VM executando o mesmo comando que mostrado no exemplo anterior sem o `--validate` argumento.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. SSH no seu VM, e verifique se tem uma imagem não enentada. Para fazer este passo, `sudo yum repolist` corra. Para o RHEL 8, utilize `sudo dnf repolist` . A saída pede-lhe que utilize Subscription-Manager para registar o VM com o Chapéu Vermelho.

>[!NOTE]
>No RHEL 8, `dnf` e `yum` são permutáveis. Para mais informações, consulte o [guia de administração RHEL 8.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Use as imagens de ouro do chapéu vermelho da PowerShell

O seguinte guião é um exemplo. Substitua o grupo de recursos, a localização, o nome VM, as informações de login e outras variáveis com a configuração da sua escolha. A informação do editor e do plano deve ser minúscula.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Encriptar Red Hat Enterprise Linux bring-your-own-subscription Gold Images

Red Hat Enterprise Linux BYOS Imagens douradas podem ser protegidas através da utilização da Encriptação do [Disco Azure](../../linux/disk-encryption-overview.md). A subscrição *deve* ser registada antes de poder ativar a encriptação. Para obter mais informações sobre como registar uma imagem de ouro RHEL BYOS, consulte [Como registar e subscrever um sistema no Portal do Cliente do Chapéu Vermelho utilizando o Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se tiver uma subscrição ativa do Red Hat, também pode ler [criar chaves de ativação do portal do cliente do chapéu vermelho](https://access.redhat.com/articles/1378093).

A encriptação do disco Azure não é suportada em [imagens personalizadas do Red Hat](../../linux/redhat-create-upload-vhd.md). Os requisitos adicionais de encriptação do disco Azure e os pré-requisitos estão documentados na [Encriptação do Disco Azure para Os VMs Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Para obter medidas para aplicar a encriptação do disco Azure, consulte [os cenários de encriptação do disco Azure em VMs Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>Informações adicionais

- Se tentar oferecer um VM a uma subscrição que não esteja habilitada para esta oferta, obtém a seguinte mensagem:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neste caso, contacte a Microsoft ou a Red Hat para ativar a sua subscrição.

- Se modificar uma imagem de UMA IMAGEM RHEL BYOS e tentar publicar essa imagem personalizada na Galeria de [Imagens Partilhadas,](../../shared-image-galleries.md)deve fornecer informações de plano que correspondam à origem original do instantâneo. Por exemplo, o comando pode ser assim:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Observe os parâmetros do plano na linha final.

    [A encriptação do disco Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) não é suportada em imagens personalizadas.

- Se utilizar a automatização para fornecer VMs a partir das imagens RHEL BYOS, deve fornecer parâmetros de plano semelhantes aos mostrados nos comandos da amostra. Por exemplo, se utilizar o Terraform, fornece as informações do plano num [bloco de planos.](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)

## <a name="next-steps"></a>Passos seguintes

- Mais detalhes sobre o Red Hat Cloud Access estão disponíveis na [documentação da nuvem pública do Chapéu Vermelho](https://access.redhat.com/public-cloud)
- Para guias passo a passo e detalhes do programa para o Acesso à Nuvem, consulte a [documentação do Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Para saber mais sobre a Infraestrutura de Atualização do Chapéu Vermelho, consulte [a Infraestrutura de Atualização do Chapéu Vermelho Azure.](./redhat-rhui.md)
- Para saber mais sobre todas as imagens do Chapéu Vermelho em Azure, consulte a página de [documentação](./redhat-images.md).
- Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte a página do [ciclo de vida da Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Para obter documentação adicional sobre as imagens de ouro rhel, consulte a [documentação do Chapéu Vermelho.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access)
