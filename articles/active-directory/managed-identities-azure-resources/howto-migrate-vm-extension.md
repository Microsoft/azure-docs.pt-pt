---
title: Pare de usar extensão VM de identidade gerida - Azure AD
description: Instruções passo a passo para parar de usar a extensão VM e começar a usar o Serviço de Metadados de Instância Azure (IMDS) para autenticação.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539388"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de usar a extensão de identidades geridas pela máquina virtual e começar a usar o Serviço de Metadados de Instância Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão da máquina virtual para identidades geridas

A extensão da máquina virtual para identidades geridas é usada para solicitar fichas para uma identidade gerida dentro da máquina virtual. O fluxo de trabalho consiste nos seguintes passos:

1. Em primeiro lugar, a carga de trabalho dentro do recurso chama o ponto final local `http://localhost/oauth2/token` para solicitar um token de acesso.
2. A extensão da máquina virtual utiliza então as credenciais para a identidade gerida, para solicitar um token de acesso a Azure AD.. 
3. O token de acesso é devolvido ao chamador, e pode ser usado para autenticar serviços que suportem a autenticação AD AZure, como Azure Key Vault ou Azure Storage.

Devido a várias limitações delineadas na secção seguinte, a extensão de VM de identidade gerida foi depreciada a favor da utilização do ponto final equivalente no Serviço de Metadados de Instância Azure (IMDS)

### <a name="provision-the-extension"></a>Disposição da extensão 

Quando configurar uma máquina virtual ou uma balança de máquina virtual definida para ter uma identidade gerida, pode optar opcionalmente por providenciar as identidades geridas para a extensão VM dos recursos Azure utilizando o `-Type` parâmetro no cmdlet [Set-AzVMExtension.](/powershell/module/az.compute/set-azvmextension) Pode passar `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux` ou, dependendo do tipo de máquina virtual, e nomeá-la usando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final simbólico da OAuth para a aquisição de fichas:

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Também pode utilizar o modelo de implementação do Gestor de Recursos Azure para obter a extensão VM, adicionando o seguinte JSON à `resources` secção ao modelo (utilização `ManagedIdentityExtensionForLinux` para o nome e elementos de tipo para a versão Linux).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Se estiver a trabalhar com conjuntos de escala de máquina virtual, também pode providenciar as identidades geridas para a extensão de escala virtual de máquinas de recursos Azure utilizando o [cmdlet Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Pode passar `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux` ou, dependendo do tipo de conjunto de balança de máquina virtual, e nomeá-lo usando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final simbólico da OAuth para a aquisição de fichas:

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para obter a extensão de conjunto de escala de máquina virtual com o modelo de implementação do Gestor de Recursos Azure, adicione o seguinte JSON à `extensionpProfile` secção ao modelo (use para o nome `ManagedIdentityExtensionForLinux` e elementos de tipo para a versão Linux).

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

O provisionamento da extensão da máquina virtual pode falhar devido a falhas de procura de DNS. Se isto acontecer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a extensão, utilize `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` altere (dependendo do tipo de máquina virtual) com [extensão az vm eliminar](/cli/azure/vm/), ou [extensão az vmss eliminar](/cli/azure/vmss) para conjuntos de balança de máquina virtual usando Azure CLI, ou para `Remove-AzVMExtension` PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Adquira um símbolo utilizando a extensão da máquina virtual

Segue-se um pedido de amostra utilizando as identidades geridas para o ponto final de extensão VM dos recursos Azure:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que pretende obter dados do ponto final. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades geridas para o ponto final dos recursos Azure, onde 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de consulta, indicando o ID URI da aplicação do recurso-alvo. Também aparece na `aud` reivindicação (audiência) do token emitido. Este exemplo solicita um símbolo para aceder ao Azure Resource Manager, que tem uma App ID URI de `https://management.azure.com/` . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para recursos Azure como uma mitigação contra o ataque de Falsificação de Pedidos de Pedidos do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores.|
| `object_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a object_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando a client_id da identidade gerida para a quais gostaria de ser o símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas pelo utilizador.|


Resposta da amostra:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O sinal de acesso solicitado. Ao chamar uma API REST segura, o token está incorporado no campo do cabeçalho de `Authorization` pedido como um símbolo "portador", permitindo à API autenticar o chamador. | 
| `refresh_token` | Não usado por identidades geridas para recursos Azure. |
| `expires_in` | O número de segundos em que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado na reivindicação do `iat` símbolo. |
| `expires_on` | O tempo de tempo quando a ficha de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `exp` token). |
| `not_before` | O período de tempo quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `nbf` token). |
| `resource` | O recurso para o token de acesso foi solicitado, que corresponde ao `resource` parâmetro de cadeia de consulta do pedido. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode dar acesso ao portador deste token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Resolução de problemas da extensão da máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reinicie a extensão da máquina virtual após uma falha

No Windows e em certas versões do Linux, se a extensão parar, pode ser utilizado o seguinte cmdlet para reiniciá-lo manualmente:

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- O nome de extensão e o tipo para Windows é: `ManagedIdentityExtensionForWindows`
- O nome de extensão e o tipo para Linux é: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquema para identidades geridas para extensão de recursos Azure

Quando as identidades geridas para os recursos Azure são ativadas numa máquina virtual, o seguinte erro é mostrado ao tentar utilizar a função "Script de automação" para a máquina virtual ou o seu grupo de recursos:

![Identidades geridas para erro de exportação de script de automação de recursos Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades geridas para a extensão da máquina virtual de recursos Azure não suportam atualmente a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não apresenta parâmetros de configuração para permitir identidades geridas para recursos Azure no recurso. Estas secções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades geridas para recursos Azure em uma máquina virtual Azure usando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquemas se tornar disponível para as identidades geridas para a extensão da máquina virtual de recursos Azure (prevista para a depreciação em janeiro de 2019), será listada nos [Grupos de Recursos Exportadores que contêm extensões de VM.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão da máquina virtual 

Existem várias limitações importantes para a utilização da extensão da máquina virtual. 

 * A limitação mais grave é o facto de as credenciais utilizadas para solicitar fichas serem armazenadas na máquina virtual. Um intruso que viole com sucesso a máquina virtual pode exfiltrar as credenciais. 
 * Além disso, a extensão da máquina virtual ainda não é suportada por várias distribuições linux, com um enorme custo de desenvolvimento para modificar, construir e testar a extensão em cada uma dessas distribuições. Atualmente, apenas as seguintes distribuições Linux são suportadas: 
    * CoreOS Stable
    * CentOS 7.1 
    * Chapéu Vermelho 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Existe um impacto de desempenho na implantação de máquinas virtuais com identidades geridas, uma vez que a extensão da máquina virtual também tem de ser alocada. 
 * Por fim, a extensão da máquina virtual só pode suportar ter 32 identidades geridas atribuídas pelo utilizador por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

O [Azure Instance Medata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) é um ponto final do REST que fornece informações sobre o funcionamento de casos de máquinas virtuais que podem ser usados para gerir e configurar as suas máquinas virtuais. O ponto final está disponível num conhecido endereço IP não roteível `169.254.169.254` ( ) que só pode ser acedido a partir da máquina virtual.

Existem várias vantagens em usar O Azure IMDS para solicitar fichas. 

1. O serviço é externo à máquina virtual, pelo que as credenciais utilizadas pelas identidades geridas já não estão presentes na máquina virtual. Em vez disso, são hospedados e protegidos na máquina hospedeira da máquina virtual Azure.   
2. Todos os sistemas operativos Windows e Linux suportados no Azure IaaS podem utilizar identidades geridas.
3. A implantação é mais rápida e fácil, uma vez que a extensão VM já não precisa de ser prevista.
4. Com o ponto final do IMDS, até 1000 identidades geridas atribuídas pelo utilizador podem ser atribuídas a uma única máquina virtual.
5. Não há nenhuma alteração significativa nos pedidos que utilizem o IMDS em oposição aos que utilizam a extensão da máquina virtual, pelo que é bastante simples desatar sobre as implementações existentes que atualmente utilizam a extensão da máquina virtual.

Por estas razões, o serviço Azure IMDS será a forma defacto de solicitar tokens, uma vez que a extensão da máquina virtual é depreciada. 


## <a name="next-steps"></a>Passos Seguintes

* [Como utilizar identidades geridas para recursos Azure numa máquina virtual Azure para adquirir um token de acesso](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)
