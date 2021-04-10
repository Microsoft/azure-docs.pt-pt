---
title: 'Tutorial: Proteja um servidor web do Windows com certificados TLS/SSL em Azure'
description: Neste tutorial, aprende-se a usar o Azure PowerShell para proteger uma máquina virtual Windows que executa o servidor web IIS com certificados TLS/SSL armazenados no Cofre da Chave Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a1009d24c1fb8712e88075097460ad179629f478
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557408"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Tutorial: Fixe um servidor web numa máquina virtual do Windows em Azure com certificados TLS/SSL armazenados no Cofre de Chaves

> [!NOTE]
> Atualmente este doc só funciona para imagens generalizadas. Se tentar este tutorial utilizando um disco especializado, receberá um erro. 

Para proteger servidores web, um Segurança da Camada de Transporte (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), o certificado pode ser usado para encriptar o tráfego web. Estes certificados TLS/SSL podem ser armazenados no Cofre da Chave Azure e permitem a implementação segura de certificados para máquinas virtuais Windows (VMs) em Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web IIS
> * Injetar o certificado no VM e configurar o IIS com uma ligação TLS


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="overview"></a>Descrição Geral
O Azure Key Vault salvaguarda as chaves criptográficas e os segredos, como certificados ou palavras-passe. O Key Vault ajuda a simplificar o processo de gestão de chaves e permite-lhe manter o controlo das chaves que acedem a esses certificados. Pode criar um certificado autoassinado no Key Vault ou carregar um certificado fidedigno que já possui.

Em vez de utilizar uma imagem de VM personalizada, que inclua certificados integrados, pode inserir certificados numa VM em execução. Este processo garante que são instalados os certificados mais atualizados num servidor Web durante a implementação. Se renovar ou substituir um certificado, também não tem de criar uma nova imagem de VM personalizada. Os certificados mais recentes são inseridos automaticamente à medida que cria VMs adicionais. Durante todo o processo, os certificados nunca saem da plataforma do Azure nem são expostos num script, histórico de linha de comandos ou modelo.


## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault
Antes de criar um Cofre-Chave e certificados, crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupSecureWeb* na localização *E.U.A. Leste*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Em seguida, crie um [Cofre-Chave com New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Cada Key Vault requer um nome exclusivo com todas as letras minúsculas. Substitua `mykeyvault` no exemplo seguinte pelo seu próprio nome exclusivo do Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazená-lo no Key Vault
Para uso de produção, deve importar um certificado válido assinado por fornecedor fidedigno com [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate). Para este tutorial, o exemplo a seguir mostra como pode gerar um certificado auto-assinado com [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) que utiliza a política de certificados predefinidos da [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Definir um nome de utilizador e palavra-passe de administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora pode criar o VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se não existirem já, são criados os recursos de rede de apoio. Para permitir um tráfego Web seguro, o cmdlet também abre a porta *443*.

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

Demora alguns minutos até que a VM seja criada. O último passo utiliza a Extensão de Script Personalizado Azure para instalar o servidor web IIS com [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Adicionar um certificado do Key Vault à VM
Para adicionar o certificado do Key Vault a um VM, obtenha a identificação do seu certificado com [a Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret). Adicione o certificado ao VM com [Add-AzVMSecret:](/powershell/module/az.compute/add-azvmsecret)

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurar o IIS para utilizar o certificado
Utilize novamente a extensão de script personalizada com [a extensão de Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) para atualizar a configuração do IIS. Esta atualização aplica o certificado injetado do Key Vault para o IIS e configura o enlace Web:

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
Obtenha o endereço IP público do seu VM com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para `myPublicIP` criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Agora, pode abrir um browser e introduzir `https://<myPublicIP>` na barra de endereço. Para aceitar o aviso de segurança se tiver utilizado um certificado autoassinado, selecione **Detalhes** e, em seguida **Aceda à página Web**:

![Aceitar o aviso de segurança do browser](./media/tutorial-secure-web-server/browser-warning.png)

O site IIS protegido é apresentado como no exemplo seguinte:

![Ver site IIS seguro em execução](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você garantiu um servidor web IIS com um certificado TLS/SSL armazenado no Cofre da Chave Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web IIS
> * Injetar o certificado no VM e configurar o IIS com uma ligação TLS

Siga esta ligação para ver os exemplos de scripts de máquina virtual pré-criados.

> [!div class="nextstepaction"]
> [Exemplos de scripts de máquina virtual com Windows](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)