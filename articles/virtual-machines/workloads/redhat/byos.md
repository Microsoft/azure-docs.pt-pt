---
title: Red Hat Enterprise Linux traz imagens Azure de assinatura própria / Microsoft Docs
description: Saiba mais sobre as imagens de bring-your-your-own-subscrição para Red Hat Enterprise Linux no Azure
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
ms.openlocfilehash: 712626345e10ab0e4290ac91b0f121ff6960303e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920574"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images in Azure

As imagens Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure através de um modelo pay-as-you-go (PAYG) ou bring-your-your-own-subscription (Red Hat Gold Image). Este documento fornece uma visão geral das Imagens de Ouro do Chapéu Vermelho em Azure.

>[!NOTE]
> A RHEL BYOS Gold Images está disponível no Azure Public (comercial) e nas nuvens do Governo de Azure. Não estão disponíveis nas nuvens Azure China ou Azure Blackforest.

## <a name="important-points-to-consider"></a>Pontos importantes a considerar

- As Imagens Red Hat Gold fornecidas neste programa são imagens RHEL prontas para a produção semelhantes às imagens RHEL PAYG na Azure Gallery/Marketplace.

- As imagens seguem as nossas políticas atuais descritas nas [imagens do Red Hat Enterprise Linux no Azure](./redhat-images.md)

- As políticas padrão de apoio aplicam-se aos VMs criados a partir destas imagens

- Os VMs previstos a partir de Red Hat Gold Images não transportam taxas De RHEL associadas às imagens RHEL PAYG

- As imagens não têm direito, por isso deve utilizar o gestor de subscrição para registar e subscrever os VMs para obter atualizações da Red Hat diretamente

- Atualmente, não é possível alternar dinamicamente entre os modelos de faturação BYOS e PAYG para imagens Linux. A recolocação do VM a partir da respetiva imagem é necessária para mudar o modelo de faturação

>[!NOTE]
> As imagens RHEL BYOS da Geração 2 não estão atualmente disponíveis através da oferta de mercado. Se necessitar de uma imagem Da Gen 2 RHEL BYOS, visite o painel coud Access na Red Hat Subscription Management. Mais detalhes estão disponíveis na documentação do [Chapéu Vermelho.](https://access.redhat.com/articles/4847681)

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para aceder às Imagens de Ouro do Chapéu Vermelho

1. Conheça os termos do [programa Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e ative as subscrições do Red Hat para o Cloud Access no Red Hat Subscription [Manager](https://access.redhat.com/management/cloud). Terá de ter em mãos as subscrições do Azure que vão estar registadas para o Cloud Access.

1. Se tiver ativado subscrições do Red Hat para o Cloud Access que satisfaçam os requisitos de elegibilidade adequados, a subscrição do Azure(s) será automaticamente ativada para o acesso à Imagem Dourada.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Ao completar os passos de acesso à nuvem, o Chapéu Vermelho validará a sua elegibilidade para as Imagens de Ouro do Chapéu Vermelho. Se a validação for bem sucedida, terá acesso às Imagens Douradas dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as imagens de ouro do chapéu vermelho do portal Azure

1. Depois da subscrição do Azure ter acesso às imagens Red Hat Gold, pode localizá-las no [portal Azure](https://portal.azure.com) navegando para **Criar um Recurso** e depois Ver **tudo.**

1. No topo da página, verá que tem ofertas privadas.

    ![Ofertas privadas do marketplace](./media/rhel-byos-privateoffers.png)

1. Pode clicar no link roxo ou rolar até ao fundo da página para ver as suas ofertas privadas.

1. O resto do fornecimento na UI não será diferente de qualquer outra imagem do Chapéu Vermelho existente. Escolha a sua versão RHEL e siga as instruções para fornecer o seu VM. Este processo também lhe permitirá aceitar os termos da imagem no passo final.

>[!NOTE]
>Estes passos até agora não permitirão a sua imagem de Imagem Dourada do Chapéu Vermelho para implementação programática – será necessário um passo adicional, como descrito na secção "Informações Adicionais" abaixo.

O resto deste documento centra-se no método CLI para fornecer e aceitar termos sobre a imagem. Os UI e o CLI são totalmente permutáveis no que diz respeito ao resultado final (um VM de imagem de ouro rHEL provisionado) em causa.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as imagens de ouro do chapéu vermelho do Azure CLI
O seguinte conjunto de instruções irá acompanhá-lo através do processo inicial de implantação de um VM RHEL utilizando o ClI Azure. Estas instruções pressupõem que tem o [Azure CLI instalado](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de que utiliza todas as letras minúsculas na editora, oferta, plano e referências de imagem para todos os seguintes comandos

1. Verifique se está na subscrição desejada:
    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para o seu Red Hat Gold Image VM:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos de imagem:
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

1. Provisão do seu VM executando o mesmo comando que acima sem o argumento `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH no seu VM e verifique se tem uma imagem sem direito. Para tal, faça `sudo yum repolist` (para rHEL 8 use `sudo dnf repolist`). A saída irá pedir-lhe que utilize o gestor de subscrição para registar o VM com Chapéu Vermelho.

>[!NOTE]
>No RHEL 8 `dnf` e `yum` são permutáveis, mais sobre isso no [Guia de Administração RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Use as imagens de ouro do chapéu vermelho da PowerShell
Segue-se um guião de exemplo. Deverá substituir o Grupo de Recursos, localização, nome VM, informações de login e outras variáveis pela configuração da sua escolha. A informação do editor e do plano deve ser minúscula.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Criptografe red hat enterprise Linux bring-your-your-own-subscription gold images

Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images pode ser protegido através da utilização da [Encriptação do Disco Azure](../../linux/disk-encryption-overview.md). No entanto, a subscrição **deve** ser registada antes de permitir a encriptação.  Os detalhes sobre o registo de uma Imagem Dourada RHEL BYOS estão disponíveis no site da Red Hat. Ver [Como registar e subscrever um sistema no Portal do Cliente da Cartovermelho utilizando o Red Hat Subscription-Manager;](https://access.redhat.com/solutions/253273) se tiver uma subscrição ativa do Red Hat, também pode ler [As Teclas](https://access.redhat.com/articles/1378093)de Ativação do Portal do Cliente do Chapéu Vermelho .

A encriptação do disco azure não é suportada em [imagens personalizadas do Chapéu Vermelho](../../linux/redhat-create-upload-vhd.md). Requisitos e pré-requisitos adicionais da ADE estão documentados na encriptação do [disco Azure para VMs Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Os passos para a aplicação da encriptação do disco Azure estão disponíveis em cenários de encriptação do [disco Azure em VMs Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>Informações adicionais

- Se tentar fornecer um VM numa subscrição que não esteja ativada para esta oferta, terá o seguinte erro:

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
    Repare nos parâmetros do plano na linha final acima.

    [A encriptação](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) do disco azure não é suportada em imagens personalizadas.

- Se estiver a utilizar a automatização para fornecer VMs a partir das imagens RHEL BYOS, deve fornecer parâmetros de plano semelhantes aos acima indicados. Por exemplo, se estiver a usar terrafora, fornecerá a informação do plano num bloco de [planos](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Passos seguintes
- Guias passo a passo e detalhes do programa para o Cloud Access estão disponíveis na [documentação red hat cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Saiba mais sobre a Infraestrutura de Atualização do [Chapéu Vermelho Azure.](./redhat-rhui.md)
- Para saber mais sobre todas as imagens do Chapéu Vermelho em Azure, vá à página de [documentação.](./redhat-images.md)
- As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
- Documentação adicional sobre as Imagens Douradas RHEL pode ser encontrada na documentação do [Chapéu Vermelho.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)
