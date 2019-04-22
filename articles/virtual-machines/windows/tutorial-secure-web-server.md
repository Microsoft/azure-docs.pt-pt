---
title: Tutorial – Proteger um servidor Web Windows com certificados SSL no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para proteger uma máquina virtual Windows que executa o servidor Web ISS com certificados SSL armazenados no Azure Key Vault.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ce8b52717541ea9cf12112ee83d767a23b61f636
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848218"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Tutorial: Proteger um servidor de web numa máquina virtual Windows no Azure com certificados SSL armazenados no Key Vault

Para proteger servidores Web, pode ser utilizado um certificado SSL (Secure Sockets Layer) para encriptar o tráfego da Web. Estes certificados SSL podem ser armazenados no Azure Key Vault e permitir implementações seguras de certificados para máquinas virtuais (VMs) com Windows no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web IIS
> * Inserir o certificado na VM e configurar o IIS com um enlace SSL

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="overview"></a>Descrição geral
O Azure Key Vault salvaguarda as chaves criptográficas e os segredos, como certificados ou palavras-passe. O Key Vault ajuda a simplificar o processo de gestão de chaves e permite-lhe manter o controlo das chaves que acedem a esses certificados. Pode criar um certificado autoassinado no Key Vault ou carregar um certificado fidedigno que já possui.

Em vez de utilizar uma imagem de VM personalizada, que inclua certificados integrados, pode inserir certificados numa VM em execução. Este processo garante que são instalados os certificados mais atualizados num servidor Web durante a implementação. Se renovar ou substituir um certificado, também não tem de criar uma nova imagem de VM personalizada. Os certificados mais recentes são inseridos automaticamente à medida que cria VMs adicionais. Durante todo o processo, os certificados nunca saem da plataforma do Azure nem são expostos num script, histórico de linha de comandos ou modelo.


## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault
Antes de poder criar um Key Vault e certificados, crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupSecureWeb* na localização *E.U.A. Leste*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Em seguida, crie um Key Vault com [New-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). Cada Key Vault requer um nome exclusivo com todas as letras minúsculas. Substitua `mykeyvault` no exemplo seguinte pelo seu próprio nome exclusivo do Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazená-lo no Key Vault
Para efeitos de produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [Import-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [Add-AzureKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) que utiliza a política de certificado predefinida de [New-AzureKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Definir um nome de utilizador e palavra-passe de administrador para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, pode criar a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se não existirem já, são criados os recursos de rede de apoio. Para permitir um tráfego Web seguro, o cmdlet também abre a porta *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Demora alguns minutos até que a VM seja criada. O último passo utiliza a extensão de Script personalizado do Azure para instalar o servidor de web IIS com [Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Adicionar um certificado do Key Vault à VM
Para adicionar o certificado do Key Vault a uma VM, obtenha o ID do seu certificado com [Get-AzureKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret). Adicionar o certificado à VM com o [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurar o IIS para utilizar o certificado
Utilize a extensão de Script personalizado novamente com [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) para atualizar a configuração do IIS. Esta atualização aplica o certificado injetado do Key Vault para o IIS e configura o enlace Web:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testar a aplicação Web segura
Obtenha o endereço IP público da VM com [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para `myPublicIP` criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Agora, pode abrir um browser e introduzir `https://<myPublicIP>` na barra de endereço. Para aceitar o aviso de segurança se tiver utilizado um certificado autoassinado, selecione **Detalhes** e, em seguida **Aceda à página Web**:

![Aceitar o aviso de segurança do browser](./media/tutorial-secure-web-server/browser-warning.png)

O site IIS protegido é apresentado como no exemplo seguinte:

![Ver site IIS seguro em execução](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, protegeu um servidor Web IIS com um certificado SSL armazenado no Azure Key Vault. Aprendeu a:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web IIS
> * Inserir o certificado na VM e configurar o IIS com um enlace SSL

Siga esta ligação para ver os exemplos de scripts de máquina virtual pré-criados.

> [!div class="nextstepaction"]
> [Exemplos de scripts de máquina virtual com Windows](./powershell-samples.md)
