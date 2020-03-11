---
title: Red Hat Enterprise Linux traz imagens Azure de assinatura própria / Microsoft Docs
description: Saiba mais sobre as imagens de bring-your-your-own-subscrição para Red Hat Enterprise Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970488"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux traz imagens gold de subscrição própria em Azure

As imagens Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure através de um modelo pay-as-you-go ou bring-your-your-own-subscrição (BYOS) (Red Hat Gold Image). Este artigo fornece uma visão geral das Imagens de Ouro do Chapéu Vermelho em Azure.

>[!NOTE]
> As imagens de ouro RHEL BYOS estão disponíveis nas nuvens do Azure Public (comercial) e do Governo Azure. Não estão disponíveis nas nuvens azure china ou azure blackforest.

## <a name="important-points-to-consider"></a>Pontos importantes a considerar

- As Imagens Red Hat Gold fornecidas neste programa são imagens RHEL prontas para a produção semelhantes às imagens de pagamento rhel no Azure Marketplace.
- As imagens seguem as políticas atuais descritas nas [imagens red hat enterprise linux no Azure](./redhat-images.md).
- As políticas padrão de apoio aplicam-se aos VMs criados a partir destas imagens.
- Os VMs previstos na Red Hat Gold Images não transportam taxas De RHEL associadas a imagens de pagamento rhel.
- As imagens não têm direito. Tem de utilizar o Red Hat Subscription-Manager para registar e subscrever os VMs para obter atualizações diretamente da Red Hat.
- Atualmente, não é possível alternar dinamicamente entre os modelos de faturação BYOS e pay-as-you-go para imagens Linux. Para mudar o modelo de faturação, tem de recolocar o VM a partir da respetiva imagem.

>[!NOTE]
> As imagens RHEL BYOS da Geração 2 não estão atualmente disponíveis através da oferta de mercado. Se necessitar de uma imagem RHEL BYOS da Geração 2, visite o painel de controlo cloud Access na gestão de subscrição do Chapéu Vermelho. Para mais informações, consulte a documentação do [Chapéu Vermelho.](https://access.redhat.com/articles/4847681)

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para aceder às Imagens de Ouro do Chapéu Vermelho

1. Conheça os termos do [programa Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Ative as subscrições do Red Hat para o Cloud Access no [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). É necessário ter em mãos as subscrições do Azure que vão estar registadas para o Cloud Access.

1. Se as subscrições do Chapéu Vermelho que habilitaste para o Cloud Access cumprirem os requisitos de elegibilidade, as subscrições do Azure estão automaticamente ativadas para acesso à Imagem Dourada.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Depois de terminar os passos de acesso à nuvem, o Chapéu Vermelho valida a sua elegibilidade para as Imagens de Ouro do Chapéu Vermelho. Se a validação for bem sucedida, terá acesso às Imagens Douradas dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as imagens de ouro do chapéu vermelho do portal Azure

1. Depois da subscrição do Azure ter acesso às Imagens Red Hat Gold, pode localizá-las no [portal Azure.](https://portal.azure.com) Vá criar **um recurso** > **Ver tudo.**

1. No topo da página, verá que tem ofertas privadas.

    ![Ofertas privadas do marketplace](./media/rhel-byos-privateoffers.png)

1. Selecione o link roxo ou desloque-se até ao fundo da página para ver as suas ofertas privadas.

1. O resto do fornecimento na UI não é diferente de qualquer outra imagem do Chapéu Vermelho existente. Escolha a sua versão RHEL e siga as instruções para fornecer o seu VM. Este processo também permite aceitar os termos da imagem no passo final.

>[!NOTE]
>Estes passos até agora não permitem a sua Imagem de Ouro do Chapéu Vermelho para implantação programática. É necessário um passo adicional, tal como descrito na secção "Informação Adicional".

O resto deste documento centra-se no método CLI para fornecer e aceitar termos sobre a imagem. Os UI e o CLI são totalmente permutáveis no que diz respeito ao resultado final (um VM de imagem de ouro rHEL provisionado) em causa.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as imagens de ouro do chapéu vermelho do Azure CLI

As seguintes instruções passam pelo processo inicial de implantação de um VM RHEL utilizando o ClI Azure. Estas instruções pressupõem que tem o [Azure CLI instalado](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de que utiliza todas as letras minúsculas da editora, oferta, plano e referências de imagem para todos os seguintes comandos.

1. Verifique se está na subscrição desejada.

    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para o seu Red Hat Gold Image VM.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos da imagem.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Estes termos precisam de ser aceites *uma vez por subscrição do Azure, por imagem SKU*.

1. (Opcional) Valide a sua implantação VM com o seguinte comando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Provisão do seu VM executando o mesmo comando mostrado no exemplo anterior sem o argumento `--validate`.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH no seu VM, e verifique se tem uma imagem sem direito. Para fazer este passo, corra `sudo yum repolist`. Para rHEL 8, utilize `sudo dnf repolist`. A saída pede-lhe que utilize o Subscription-Manager para registar o VM com Chapéu Vermelho.

>[!NOTE]
>No RHEL 8, `dnf` e `yum` são permutáveis. Para mais informações, consulte o [guia de administração RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Use as imagens de ouro do chapéu vermelho da PowerShell

O seguinte guião é um exemplo. Substitua o grupo de recursos, localização, nome VM, informações de login e outras variáveis com a configuração da sua escolha. A informação do editor e do plano deve ser minúscula.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Criptografe Red Hat Enterprise Linux traga imagens gold da sua própria subscrição

Red Hat Enterprise Linux BYOS Gold Images pode ser protegido através da utilização de encriptação de [disco azure](../../linux/disk-encryption-overview.md). A subscrição *deve* ser registada antes de poder ativar a encriptação. Para obter mais informações sobre como registar uma Imagem Dourada RHEL BYOS, consulte [Como registar e subscrever um sistema no Portal do Cliente da Cartovermelho utilizando o Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se tiver uma subscrição ativa do Red Hat, também pode ler As Teclas de Ativação do Portal do [Cliente do Chapéu Vermelho.](https://access.redhat.com/articles/1378093)

A encriptação do disco azure não é suportada em [imagens personalizadas do Chapéu Vermelho.](../../linux/redhat-create-upload-vhd.md) Requisitos e pré-requisitos adicionais de encriptação do disco Azure estão documentados na [encriptação do disco Azure para VMs Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Para obter passos para aplicar encriptação de disco azure, consulte cenários de encriptação do [disco Azure em VMs Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>Informações adicionais

- Se tentar fornecer um VM numa subscrição que não esteja ativada para esta oferta, obtém a seguinte mensagem:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neste caso, contacte a Microsoft ou a Red Hat para ativar a sua subscrição.

- Se modificar um instantâneo de uma imagem RHEL BYOS e tentar publicar essa imagem personalizada na Galeria de [Imagem Partilhada,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)deve fornecer informações de plano que correspondam à origem original do instantâneo. Por exemplo, o comando pode parecer assim:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Reparem nos parâmetros do plano na linha final.

    [A encriptação](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) do disco azure não é suportada em imagens personalizadas.

- Se utilizar a automatização para fornecer VMs a partir das imagens RHEL BYOS, deve fornecer parâmetros de plano semelhantes aos mostrados nos comandos da amostra. Por exemplo, se utilizar a Terraform, fornece a informação do plano num bloco de [planos](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Passos seguintes

- Para obter guias passo a passo e detalhes do programa para o Cloud Access, consulte a [documentação de Acesso](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)à Nuvem de Chapéu Vermelho .
- Para saber mais sobre a Infraestrutura de Atualização do Chapéu Vermelho, consulte a Infraestrutura de Atualização do [Chapéu Vermelho Azure](./redhat-rhui.md).
- Para saber mais sobre todas as imagens do Chapéu Vermelho em Azure, consulte a página de [documentação.](./redhat-images.md)
- Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte a página do ciclo de [vida Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Para obter documentação adicional sobre as Imagens De Ouro RHEL, consulte a documentação do [Chapéu Vermelho.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)
