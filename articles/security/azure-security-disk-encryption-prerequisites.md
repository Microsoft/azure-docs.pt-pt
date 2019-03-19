---
title: Pré-requisitos - Azure Disk Encryption para IaaS VMs | Documentos da Microsoft
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 495095df65a03dca573f38c93eb5e77176c16359
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895911"
---
# <a name="azure-disk-encryption-prerequisites"></a>Pré-requisitos do Azure Disk Encryption

 Neste artigo, os pré-requisitos do Azure Disk Encryption, explica os itens que precisam de estar em vigor antes de poder utilizar o Azure Disk Encryption. O Azure Disk Encryption está integrado [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) para ajudar a gerir as chaves de encriptação. Pode usar [do Azure PowerShell](/powershell/azure/overview), [CLI do Azure](/cli/azure/), ou o [portal do Azure](https://portal.azure.com) para configurar a encriptação de disco do Azure.

Antes de ativar o Azure Disk Encryption em VMs de IaaS do Azure para os cenários suportados que foram abordados os [descrição geral de encriptação de disco do Azure](azure-security-disk-encryption-overview.md) article, certifique-se de que tem os pré-requisitos necessários. 

> [!WARNING]
> - Se tiver utilizado anteriormente [do Azure Disk Encryption, com a aplicação do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) para encriptar esta VM, terá de continuar a utilizar esta opção para encriptar a sua VM. Não é possível usar [do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nesta VM encriptada como isso não é um cenário suportado, significado mudar para fora da aplicação do AAD para encriptados esta VM não é suportada ainda.
> - Algumas recomendações podem aumentar de dados, a rede ou a utilização de recursos de computação, resultando em custos adicionais de licença ou subscrição. Tem de ter uma subscrição do Azure Active Directory válida para criar recursos no Azure nas regiões suportadas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> Sistemas operativos suportados
O Azure Disk Encryption tem suporte nos seguintes sistemas operativos:

- Versões do Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.
  - Para o Windows Server 2008 R2, tem de ter o .NET Framework 4.5 instalado antes de ativar a encriptação no Azure. Instale-o do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados em x64 do Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Versões de cliente do Windows: Cliente Windows 8 e o cliente do Windows 10.
- O Azure Disk Encryption é apenas suportado em específico galeria do Azure com base em distribuições de servidor Linux e versões. Para obter a lista de versões atualmente suportadas, consulte a [FAQ de encriptação de disco do Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- O Azure Disk Encryption requer que o Cofre de chaves e VMs residem na mesma região do Azure e subscrição. Configurar os recursos em regiões separadas faz com que uma falha na ativação da funcionalidade do Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Pré-requisitos adicionais para VMs de IaaS Linux 

- O Azure Disk Encryption para o Linux requer 7 GB de RAM na VM para ativar a encriptação de disco de SO no [suportadas imagens](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Quando o processo de encriptação de disco do SO estiver concluído, a VM pode ser configurada para executar com menos memória.
- O Azure Disk Encryption requer o módulo de vfat estar presente no sistema.  A remoção ou desativação este módulo a partir da imagem predefinida irá impedir que o sistema de conseguir ler o volume de chave e obter a chave necessária para desbloquear os discos nas reinicializações subseqüentes. Passos de proteção do sistema que remover o módulo de vfat do sistema não são compatíveis com o Azure Disk Encryption. 
- Antes de ativar a encriptação, os discos de dados sejam encriptados tem de estar corretamente listado em /etc/fstab. Utilize um nome de dispositivo de bloco persistente para esta entrada, como nomes no formato "/ desenvolvimento/sdX" não podem ser confiados para ser associado com o mesmo disco em reinícios, particularmente depois de é aplicada a encriptação de dispositivo. Para obter mais detalhes sobre esse comportamento, consulte: [Resolver problemas relacionados com alterações de nome de dispositivo de VM do Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Certifique-se de que as definições de /etc/fstab. estão configuradas corretamente para a montagem. Para configurar estas definições, execute o comando mount - a ou reinicie a VM e acionar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar que a unidade ainda está instalada. 
  - Se o ficheiro de /etc/fstab. não montar a unidade corretamente antes de ativar a encriptação, Azure Disk Encryption não será capaz de montá-la corretamente.
  - O processo de Azure Disk Encryption irá mover as informações de montagem fora /etc/fstab. e em seu próprio arquivo de configuração como parte do processo de encriptação. Não se assuste para ver a entrada em falta de /etc/fstab. depois de encriptação de unidade de dados é concluída.
  -  Após a reinicialização, levará tempo para o processo de encriptação de disco do Azure montar os discos encriptados recentemente. Eles não estarão imediatamente disponíveis após um reinício. O processo tem tempo para iniciar, desbloquear e, em seguida, montar os discos encriptados antes de estarem disponíveis para outros processos aceder. Este processo pode demorar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar o necessário/etc/fstab entradas pode ser encontrado na [linhas 244-248 deste ficheiro de script](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 


## <a name="bkmk_GPO"></a> Funcionamento em rede e a política de grupo

**Para ativar o Azure Disk Encryption funcionalidade, as VMs de IaaS tem de cumprir os seguintes requisitos de configuração do ponto final de rede:**
  - Para obter um token para ligar ao seu Cofre de chaves, a VM de IaaS tem de ser capaz de ligar a um ponto de extremidade do Azure Active Directory \[login.microsoftonline.com\].
  - Para escrever as chaves de encriptação para o seu Cofre de chaves, a VM de IaaS tem de conseguir ligar ao ponto final do Cofre de chaves.
  - A VM de IaaS tem de ser capaz de ligar a um ponto de extremidade de armazenamento do Azure que aloja o repositório de extensão do Azure e uma conta de armazenamento do Azure que aloja os ficheiros VHD.
  -  Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [do Azure Key Vault protegido por uma firewall](../key-vault/key-vault-access-behind-firewall.md).    


**Política de grupo:**
 - A solução Azure Disk Encryption utiliza o protetor de chave externo do BitLocker para VMs de IaaS do Windows. Para VMs associados ao domínio, não enviar por push as políticas de grupo que impõem protetores TPM. Para informações sobre a política de grupo para "Permitir BitLocker sem um TPM compatível", consulte [referência de política de grupo de BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Política do BitLocker em máquinas de virtuais associados a um domínio com a política de grupo personalizado tem de incluir a definição seguinte: [Configurar o armazenamento de usuário do bitlocker informações de recuperação -> chave de recuperação permitem 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). O Azure Disk Encryption irá falhar quando as definições de política de grupo personalizado para o BitLocker são incompatíveis. Nas máquinas que não tinham a definição de política correta, aplicar a nova política, forçar a nova política de atualização (gpupdate.exe /force) e, em seguida, reiniciar poderá ser necessário.

- O Azure Disk Encryption irá falhar se o algoritmo AES-CBC, que é utilizado pelo Bitlocker bloqueia a diretiva de grupo ao nível do domínio.


## <a name="bkmk_PSH"></a> O Azure PowerShell
[O Azure PowerShell](/powershell/azure/overview) fornece um conjunto de cmdlets que utiliza o [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelos para gerir os recursos do Azure. Pode usá-lo no seu browser com [Azure Cloud Shell](../cloud-shell/overview.md), ou pode instalá-lo no seu computador local com as instruções abaixo para utilizá-lo em qualquer sessão do PowerShell. Se já tiver instalado localmente, certifique-se de que utilizar a versão mais recente da versão do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a versão mais recente do [do Azure PowerShell versão](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instale o Azure PowerShell para utilização no seu computador local (opcional): 
1. Siga as instruções nas ligações para o seu sistema operativo, em seguida, continuar entanto o resto dos passos abaixo.      
   - [Instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps). 
     - Instalar o PowerShellGet, Azure PowerShell e carregar o módulo de Az. 

2. Verifique se as versões instaladas do módulo Az. Se for necessário, [atualizar o módulo Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    É recomendado utilizar a versão mais recente do módulo de Az.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Inicie sessão no Azure com o [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  Se for necessário, reveja [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Instalar a CLI do Azure para utilização no seu computador local (opcional)

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta da linha de comandos para gerir recursos do Azure. A CLI é concebida para consultar dados com flexibilidade, oferecem suporte a operações de longa execução como processos sem bloquear e tornar a escrita de scripts fácil. Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell.

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli) para utilização no seu computador local (opcional):

2. Certifique-se a versão instalada.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Inicie sessão no Azure com [início de sessão az](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Revisão [introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli) se for necessário. 


## <a name="prerequisite-workflow-for-key-vault"></a>Fluxo de trabalho dos pré-requisitos para o Key Vault
Se já estiver familiarizado com os pré-requisitos do Key Vault e o Azure AD para a encriptação de disco do Azure, pode utilizar o [script do PowerShell de pré-requisitos de Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter mais informações sobre como utilizar o script de pré-requisitos, consulte a [encriptar um início rápido de VM](quick-encrypt-vm-powershell.md) e o [apêndice de encriptação de disco do Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Se necessário, crie um grupo de recursos.
2. Criar um cofre de chaves. 
3. Cofre de chaves conjunto avançado de políticas de acesso.

>[!WARNING]
>Antes de eliminar um cofre de chaves, certifique-se de que não a criptografar todas as VMs existentes com o mesmo. Para proteger um cofre de eliminação acidental, [ativar a eliminação de forma recuperável](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) e uma [bloqueio de recurso](../azure-resource-manager/resource-group-lock-resources.md) no cofre. 
 
## <a name="bkmk_KeyVault"></a> Criar um cofre de chaves 
O Azure Disk Encryption está integrado [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. Pode criar um cofre de chaves ou utilize um já existente para a encriptação de disco do Azure. Para obter mais informações sobre os cofres de chaves, consulte [introdução ao Azure Key Vault](../key-vault/key-vault-get-started.md) e [proteger o seu Cofre de chave](../key-vault/key-vault-secure-your-key-vault.md). Pode utilizar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada. 


### <a name="bkmk_KVPSH"></a> Criar um cofre de chaves com o PowerShell

Pode criar um cofre de chaves com o Azure PowerShell através da [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet. Para obter cmdlets adicionais para o Key Vault, consulte [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Criar um novo grupo de recursos, se necessário, com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar localizações de Datacenter, utilize [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Criar um novo cofre de chaves utilizando [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Tenha em atenção a **nome do cofre**, **nome do grupo de recursos**, **ID de recurso**, **URI do cofre**e o **ID de objeto** que são devolvidos para utilização posterior ao encriptar os discos. 


### <a name="bkmk_KVCLI"></a> Criar um cofre de chaves com a CLI do Azure
Pode gerir o seu Cofre de chaves com a CLI do Azure com o [az keyvault](/cli/azure/keyvault#commands) comandos. Para criar um cofre de chaves, utilize [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).

1. Se for necessário, [ligar à sua subscrição do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Criar um novo grupo de recursos, se necessário, com [criar grupo az](/cli/azure/group#az-group-create). Para listar localizações, utilize [lista de conta de az-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Criar um novo cofre de chaves utilizando [az keyvault criar](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Tenha em atenção a **nome do cofre** (nome), **nome do grupo de recursos**, **ID de recurso** (ID), **URI do cofre**e o **ID de objeto** que são devolvidos para utilização posterior. 

### <a name="bkmk_KVRM"></a> Criar um cofre de chaves com um modelo do Resource Manager

Pode criar um cofre de chaves utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**.
2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, Key Vault nome, ID de objeto, os termos legais e contrato e, em seguida, clique em **Compra**. 


## <a name="bkmk_KVper"></a> Cofre de chaves de conjunto de políticas de acesso avançadas
A plataforma do Azure precisa de aceder a chaves de encriptação ou segredos no Cofre de chaves para que fiquem disponíveis para a VM para o arranque e a desencriptação de volumes. Ativar a encriptação de disco no Cofre de chaves ou implementações irão falhar.  

### <a name="bkmk_KVperPSH"></a> Cofre de chaves conjunto avançado de políticas de acesso com o Azure PowerShell
 Utilize o cmdlet PowerShell do Cofre de chaves [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para ativar a encriptação de disco para o Cofre de chaves.

  - **Ative o Key Vault para encriptação de disco:** EnabledForDiskEncryption é necessário para a encriptação de disco do Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Ative o Key Vault para a implementação, se necessário:** Permite que o fornecedor de recursos Microsoft. Compute obter segredos a partir deste cofre de chaves, quando este Cofre de chaves é referenciado na criação de recursos, por exemplo, quando criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Ative o Key Vault para a implementação de modelo, se necessário:** Permite que o Azure Resource Manager para obter segredos a partir deste cofre de chaves, quando este Cofre de chaves é referenciado numa implementação do modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Cofre de chaves conjunto avançado de políticas de acesso com a CLI do Azure
Uso [atualização do Cofre de chaves de az](/cli/azure/keyvault#az-keyvault-update) para ativar a encriptação de disco para o Cofre de chaves. 

 - **Ative o Key Vault para encriptação de disco:** Habilitado-para--encriptação de disco é necessário. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ative o Key Vault para a implementação, se necessário:** Permite que o fornecedor de recursos Microsoft. Compute obter segredos a partir deste cofre de chaves, quando este Cofre de chaves é referenciado na criação de recursos, por exemplo, quando criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Ative o Key Vault para a implementação de modelo, se necessário:** Permitir Gestor de recursos para obter segredos a partir do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Cofre de chaves conjunto avançado de políticas de acesso através do portal do Azure

1. Selecione o seu Cofre de chaves, aceda ao **políticas de acesso**, e **clique para mostrar as políticas de acesso avançadas**.
2. Selecione a caixa rotulada **ativar o acesso ao Azure Disk Encryption para encriptação de volume**.
3. Selecione **ativar o acesso às máquinas de virtuais do Azure para a implantação** e/ou **ativar o acesso ao Azure Resource Manager para a implementação de modelo**, se for necessário. 
4. Clique em **Guardar**.

![Cofre de chaves do Azure, as políticas de acesso avançadas](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurar uma chave de encriptação de chave (opcional)
Se pretender utilizar uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação, adicione um KEK ao seu Cofre de chaves. Utilize o [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet para criar uma chave de encriptação de chave no Cofre de chaves. Também pode importar um KEK de sua gestão de chaves HSM no local. Para obter mais informações, consulte [documentação do Cofre de chave](../key-vault/key-vault-hsm-protected-keys.md). Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault.

* Ao gerar as chaves, utilize um tipo de chave RSA. O Azure Disk Encryption não suporta ainda a utilização de chaves de criptografia de curva elíptica.

* O segredo do Cofre de chaves e URLs de KEK devem ter a versão. Azure impõe essa restrição de controle de versão. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

  * Exemplo de um URL de segredo válido:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL válido da KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* O Azure Disk Encryption não suporta a especificação de números de porta como parte de segredos do Cofre de chaves e URLs de KEK. Para obter exemplos de URLs de Cofre de chaves suportadas e não suportados, consulte os exemplos seguintes:

  * URL do Cofre de chaves inaceitável  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do Cofre de chaves aceitável:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Configurar uma chave de encriptação de chave com o Azure PowerShell 
Antes de utilizar o script do PowerShell, deve estar familiarizado com os pré-requisitos do Azure Disk Encryption para compreender os passos no script. O script de exemplo poderá ter as alterações para o seu ambiente. Este script cria todos os pré-requisitos do Azure Disk Encryption e criptografa uma VM de IaaS existente, a chave de encriptação de disco de encapsulamento de aplicações com uma chave de encriptação de chave. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Linux](azure-security-disk-encryption-linux.md)

