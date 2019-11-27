---
title: Parar de usar a extensão de VM de identidade gerenciada-Azure AD
description: Instruções passo a passo para parar de usar a extensão de VM e começar a usar o IMDS (serviço de metadados de instância) do Azure para autenticação.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 3440713c287967655678e1cde2c000a6ed28b900
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183958"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de usar a extensão de identidades gerenciadas da máquina virtual e começar a usar o serviço de metadados de instância do Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão da máquina virtual para identidades gerenciadas

A extensão da máquina virtual para identidades gerenciadas é usada para solicitar tokens para uma identidade gerenciada na máquina virtual. O fluxo de trabalho consiste nas seguintes etapas:

1. Primeiro, a carga de trabalho dentro do recurso chama o ponto de extremidade local `http://localhost/oauth2/token` para solicitar um token de acesso.
2. Em seguida, a extensão da máquina virtual usa as credenciais da identidade gerenciada para solicitar um token de acesso do Azure AD. 
3. O token de acesso é retornado para o chamador e pode ser usado para autenticar para serviços que dão suporte à autenticação do Azure AD, como o Azure Key Vault ou o armazenamento do Azure.

Devido a várias limitações descritas na próxima seção, a extensão de VM de identidade gerenciada foi preterida em favor de usar o ponto de extremidade equivalente no serviço de metadados de instância do Azure (IMDS)

### <a name="provision-the-extension"></a>Provisionar a extensão 

Ao configurar uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais para ter uma identidade gerenciada, você pode opcionalmente optar por provisionar as identidades gerenciadas para a extensão de VM de recursos do Azure usando o parâmetro `-Type` no cmdlet [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) . Você pode passar `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de máquina virtual, e nomeá-la usando o parâmetro `-Name`. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade de token OAuth para aquisição de token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Você também pode usar o modelo de implantação Azure Resource Manager para provisionar a extensão de VM, adicionando o JSON a seguir à seção `resources` para o modelo (use `ManagedIdentityExtensionForLinux` para os elementos Name e Type para a versão do Linux).

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
    
    
Se você estiver trabalhando com conjuntos de dimensionamento de máquinas virtuais, também poderá provisionar as identidades gerenciadas para a extensão do conjunto de dimensionamento de máquinas virtuais do Azure usando o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . Você pode passar `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de conjunto de dimensionamento de máquinas virtuais, e nomeá-lo usando o parâmetro `-Name`. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade de token OAuth para aquisição de token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para provisionar a extensão do conjunto de dimensionamento de máquinas virtuais com o modelo de implantação Azure Resource Manager, adicione o JSON a seguir à seção `extensionpProfile` ao modelo (use `ManagedIdentityExtensionForLinux` para os elementos Name e Type para a versão do Linux).

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

O provisionamento da extensão da máquina virtual pode falhar devido a falhas de pesquisa de DNS. Se isso acontecer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a extensão, use a opção `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` (dependendo do tipo de máquina virtual) com [AZ VM Extension Delete](https://docs.microsoft.com/cli/azure/vm/)ou [AZ vmss Extension Delete](https://docs.microsoft.com/cli/azure/vmss) para conjuntos de dimensionamento de máquinas virtuais usando CLI do Azure ou `Remove-AzVMExtension` para PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Adquirir um token usando a extensão da máquina virtual

Veja a seguir um exemplo de solicitação usando as identidades gerenciadas para o ponto de extremidade de extensão de VM dos recursos do Azure:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar dados do ponto de extremidade. Nesse caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | O ponto de extremidade de identidades gerenciadas para recursos do Azure, em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta, indicando o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração de `aud` (público) do token emitido. Este exemplo solicita um token para acessar Azure Resource Manager, que tem um URI de ID de aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma mitigação contra o ataque de falsificação de solicitação do lado do servidor (SSRF). Esse valor deve ser definido como "true", em todas as letras minúsculas.|
| `object_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a object_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|
| `client_id` | Adicional Um parâmetro de cadeia de caracteres de consulta, que indica a client_id da identidade gerenciada para a qual você gostaria de obter o token. Necessário, se sua VM tiver várias identidades gerenciadas atribuídas pelo usuário.|


Exemplo de resposta:

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
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação de `Authorization` como um token de "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não é usado por identidades gerenciadas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado na declaração de `iat` do token. |
| `expires_on` | O TimeSpan quando o token de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0: 0Z UTC" (corresponde à declaração de `exp` do token). |
| `not_before` | O TimeSpan quando o token de acesso entra em vigor e pode ser aceito. A data é representada como o número de segundos de "1970-01-01T0:0: 0Z UTC" (corresponde à declaração de `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de caracteres de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "portador", que significa que o recurso pode dar acesso ao portador desse token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Solucionar problemas da extensão da máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reiniciar a extensão da máquina virtual após uma falha

No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- O nome e o tipo da extensão para o Windows é: `ManagedIdentityExtensionForWindows`
- O nome e o tipo da extensão para Linux é: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar o esquema para identidades gerenciadas para extensão de recursos do Azure

Quando as identidades gerenciadas para recursos do Azure são habilitadas em uma máquina virtual, o seguinte erro é mostrado ao tentar usar o recurso "script de automação" para a máquina virtual ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades gerenciadas para a extensão da máquina virtual de recursos do Azure atualmente não dão suporte à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades gerenciadas para recursos do Azure em uma máquina virtual do Azure usando modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema fica disponível para as identidades gerenciadas para a extensão de máquina virtual de recursos do Azure (planejada para substituição em janeiro de 2019), ela será listada na [exportação dos grupos de recursos que contêm extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão da máquina virtual 

Há várias limitações importantes no uso da extensão da máquina virtual. 

 * A limitação mais séria é o fato de que as credenciais usadas para solicitar tokens são armazenadas na máquina virtual. Um invasor que viole a máquina virtual com êxito pode exfiltrar as credenciais. 
 * Além disso, a extensão da máquina virtual ainda não tem suporte por várias distribuições do Linux, com um enorme custo de desenvolvimento para modificar, compilar e testar a extensão em cada uma dessas distribuições. Atualmente, há suporte apenas para as seguintes distribuições Linux: 
    * CoreOS estável
    * CentOS 7,1 
    * Red Hat 7,2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Há um impacto no desempenho na implantação de máquinas virtuais com identidades gerenciadas, já que a extensão da máquina virtual também precisa ser provisionada. 
 * Por fim, a extensão da máquina virtual pode dar suporte apenas a 32 identidades gerenciadas atribuídas pelo usuário por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O [serviço de metadados de instância do Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) é um ponto de extremidade REST que fornece informações sobre a execução de instâncias de máquina virtual que podem ser usadas para gerenciar e configurar suas máquinas virtuais. O ponto de extremidade está disponível em um endereço IP não roteável e conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da máquina virtual.

Há várias vantagens em usar o Azure IMDS para solicitar tokens. 

1. O serviço é externo à máquina virtual, portanto as credenciais usadas por identidades gerenciadas não estão mais presentes na máquina virtual. Em vez disso, eles são hospedados e protegidos no computador host da máquina virtual do Azure.   
2. Todos os sistemas operacionais Windows e Linux com suporte no Azure IaaS podem usar identidades gerenciadas.
3. A implantação é mais rápida e fácil, pois a extensão da VM não precisa mais ser provisionada.
4. Com o ponto de extremidade IMDS, até 1000 identidades gerenciadas atribuídas pelo usuário podem ser atribuídas a uma única máquina virtual.
5. Não há nenhuma alteração significativa nas solicitações usando IMDS em oposição àquelas que usam a extensão da máquina virtual, portanto, é razoavelmente simples de portar as implantações existentes que atualmente usam a extensão da máquina virtual.

Por esses motivos, o serviço IMDS do Azure será a maneira de desfatorar para solicitar tokens, depois que a extensão da máquina virtual for preterida. 


## <a name="next-steps"></a>Passos Seguintes

* [Como usar identidades gerenciadas para recursos do Azure em uma máquina virtual do Azure para adquirir um token de acesso](how-to-use-vm-token.md)
* [Serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
