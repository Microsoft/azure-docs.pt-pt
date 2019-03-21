---
title: Parar de utilizar a extensão VM de identidade gerida e começar a utilizar o ponto de extremidade do serviço de metadados de instância do Azure
description: Passo a passo instruções sobre como deixar de utilizar a extensão de VM e começar a utilizar o serviço de metadados de instância do Azure (IMDS) para autenticação.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: priyamo
ms.openlocfilehash: c29eb22f2606fc44cfd7d948f2c363cb51c811dd
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227688"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de utilizar a máquina virtual gerido extensão de identidades e comece a utilizar o serviço de metadados de instância do Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão da máquina virtual para identidades geridas

A extensão de máquina virtual para identidades geridas é utilizada para pedir tokens para uma identidade gerida na máquina virtual. O fluxo de trabalho inclui os seguintes passos:

1. Em primeiro lugar, a carga de trabalho dentro do recurso chama o ponto final local `http://localhost/oauth2/token` para pedir um token de acesso.
2. A extensão de máquina virtual, em seguida, utiliza as credenciais para a identidade gerida, para pedir um token de acesso do Azure AD... 
3. O token de acesso é retornado ao chamador e pode ser utilizado para autenticar para serviços que suportam a autenticação do Azure AD, como o Azure Key Vault ou o armazenamento do Azure.

Devido a várias limitações descritas na secção seguinte, a extensão VM de identidade gerida foi preterida em favor do uso do ponto de extremidade equivalente no serviço de metadados de instância de Azure (IMDS)

### <a name="provision-the-extension"></a>Aprovisionar a extensão 

Ao configurar uma máquina virtual ou um conjunto de ter uma identidade gerida de dimensionamento de máquina virtual, poderá opcional escolha, opcionalmente, pode optar por aprovisionar as identidades geridas para a utilização da extensão VM de recursos do Azure a `-Type` parâmetro sobre a [ Conjunto AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet. Pode transmitir o `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de máquina virtual e nomeie-o usando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto de final do token do OAuth para aquisição do token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Também pode utilizar o modelo de implementação Azure Resource Manager para aprovisionar a extensão de VM, adicionando o seguinte JSON para o `resources` secção para o modelo (utilizar `ManagedIdentityExtensionForLinux` para os elementos de nome e tipo para a versão do Linux).

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
    
    
Se estiver a trabalhar com conjuntos de dimensionamento de máquina virtual, também pode aprovisionar as identidades de geridos para utilizar a extensão do conjunto de dimensionamento de máquinas virtuais de recursos do Azure a [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet. Pode transmitir o `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de dimensionamento de máquinas virtuais definido e nomeie-o usando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto de final do token do OAuth para aquisição do token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para aprovisionar o dimensionamento de máquinas virtuais, definir extensão com o modelo de implementação Azure Resource Manager, adicione o seguinte JSON para o `extensionpProfile` secção para o modelo (utilizar `ManagedIdentityExtensionForLinux` para os elementos de nome e tipo para a versão do Linux).

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

Aprovisionamento de extensão da máquina virtual poderá falhar devido a falhas de pesquisa DNS. Se isto acontecer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a extensão, utilize `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` mudar (consoante o tipo de máquina virtual) com [delete de extensão az vm](https://docs.microsoft.com/cli/azure/vm/), ou [delete de extensão az vmss](https://docs.microsoft.com/cli/azure/vmss) para dimensionamento de máquinas virtuais os conjuntos com a CLI do Azure, ou `Remove-AzVMExtension` para o Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Adquirir um token com a extensão da máquina virtual

Segue-se um pedido de exemplo com as identidades geridas para recursos do Azure ponto final da extensão de VM:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, que indica que deseja recuperar dados a partir do ponto final. Neste caso, um OAuth acessar o token. | 
| `http://localhost:50342/oauth2/token` | Identidades geridas para o ponto final de recursos do Azure, onde 50342 é a porta predefinida e é configurável. |
| `resource` | Um parâmetro da cadeia de consulta, que indica o URI de ID de aplicação do recurso de destino. É também apresentado no `aud` afirmação (público) do token emitido. Neste exemplo pede um token para aceder ao Azure Resource Manager, que tem um URI de ID de aplicação de https://management.azure.com/. |
| `Metadata` | Um HTTP pedido campo de cabeçalho, necessário para identidades geridas para recursos do Azure como uma atenuação contra ataques de falsificação de solicitação de lado do servidor (SSRF). Este valor tem de ser definido como "true", em minúsculas.|
| `object_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o object_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|
| `client_id` | (Opcional) Um parâmetro da cadeia de consulta, que indica o client_id de a identidade gerida que gostaria de ter o token para. Necessário se a VM tiver várias identidades geridas atribuído ao utilizador.|


Resposta de exemplo:

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
| `access_token` | O token de acesso solicitado. Ao chamar uma API de REST segura, o token está incorporado a `Authorization` campo de cabeçalho de pedido como um token de "bearer", permitindo que a API para autenticar o chamador. | 
| `refresh_token` | Não utilizado por identidades geridas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válida, antes de expirar, a partir da hora de emissão. Hora de emissão pode ser encontrada no token de `iat` de afirmação. |
| `expires_on` | O período de tempo quando o token de acesso expira. A data é representada como o número de segundos a partir de "1970-01-01T0:0:0Z UTC" (corresponde do token `exp` afirmação). |
| `not_before` | O período de tempo quando o token de acesso entra em vigor e pode ser aceites. A data é representada como o número de segundos a partir de "1970-01-01T0:0:0Z UTC" (corresponde do token `nbf` afirmação). |
| `resource` | O recurso o token de acesso foi pedido para que corresponde ao `resource` parâmetro de cadeia de caracteres do pedido de consulta. |
| `token_type` | O tipo de token, que é um token de acesso de "Bearer", o que significa que o recurso pode conceder acesso para o portador do token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Resolver problemas relacionados com a extensão de máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reinicie a extensão de máquina virtual após uma falha

No Windows e determinadas versões do Linux, se a extensão de parar, o cmdlet seguinte pode ser usado para reiniciar manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome de extensão e tipo para Windows é: `ManagedIdentityExtensionForWindows`
- Nome de extensão e o tipo para Linux é: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação de" Falha ao tentar exportar de esquema para identidades geridas para a extensão de recursos do Azure

Quando as identidades geridas para recursos do Azure é ativada numa máquina virtual, o seguinte erro é apresentado durante a tentativa de utilizar a funcionalidade de "Script de automatização" para a máquina virtual ou o respetivo grupo de recursos:

![Erro de exportação de identidades geridas para o script de automação de recursos do Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades geridas para a extensão de máquina virtual de recursos do Azure não suporta atualmente a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra os parâmetros de configuração para ativar identidades geridas para recursos do Azure no recurso. Estas secções podem ser adicionadas manualmente ao seguir os exemplos [configurar geridos identidades para recursos do Azure numa máquina virtual do Azure através de um modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema fica disponível para as identidades geridas para a extensão de máquina virtual de recursos do Azure (planeada para preterição em Janeiro de 2019), serão listado na [e grupos de recursos de exportação, que contêm as extensões de VM ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão da máquina virtual 

Existem várias limitações principais para utilizar a extensão de máquina virtual. 

 * A limitação mais grave é o fato de que as credenciais utilizadas para solicitar tokens são armazenadas na máquina virtual. Um invasor que casos de violações com êxito a máquina virtual pode retirar as credenciais. 
 * Além disso, a extensão de máquina virtual não é ainda suportada por várias distribuições do Linux, com um enorme modificar, criar e testar a extensão em cada um desses distribuições de custos de desenvolvimento. Atualmente, apenas as distribuições de Linux seguintes são suportadas: 
    * Stable do CoreOS
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Há um impacto de desempenho para a implantação de máquinas virtuais com identidades geridas, como também tem a extensão de máquina virtual a ser aprovisionado. 
 * Por fim, a extensão de máquina virtual só pode suportar ter 32 geridos identidiades atribuídas por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O [serviço de metadados de instância do Azure (IMDS)](https://docs.microsoft.com/azure/virtual-machines/instance-metadata-service) é um ponto de final do REST que fornece informações sobre as instâncias de máquina virtual que podem ser utilizadas para gerir e configurar as suas máquinas virtuais em execução. O ponto final está disponível num endereço IP bem conhecido não encaminháveis internos (`169.254.169.254`) que podem ser acedidos apenas a partir da máquina virtual.

Existem diversas vantagens em usar IMDS do Azure para pedir tokens. 

1. O serviço é externo à máquina virtual, já não estão presentes na máquina virtual, por conseguinte, as credenciais utilizadas pelo identidades geridas. Em vez disso, eles são alojados e protegidos no computador anfitrião de máquina virtual do Azure.   
2. Todos os sistemas de operativos Windows e Linux suportados no Azure IaaS, pode utilizar identidades geridas.
3. Implantação é mais rápido e fácil, uma vez que a extensão da VM já não precisa de ser aprovisionada.
4. Com o IMDS, ponto de extremidade, até 1000 atribuído ao utilizador identidades geridas pode ser atribuído a uma única máquina virtual.
5. Não há nenhuma alteração significativa para os pedidos com IMDS em vez das utilizar a extensão de máquina virtual, pelo que é bastante simples para a porta através de implementações existentes que atualmente utilizam a extensão de máquina virtual.

Por esses motivos, o serviço de Azure IMDS será a maneira certa para pedir tokens, assim que a extensão de máquina virtual foi preterida. 


## <a name="next-steps"></a>Próximos Passos

* [Como utilizar identidades geridas para recursos do Azure numa máquina virtual do Azure para adquirir um token de acesso](how-to-use-vm-token.md)
* [Serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
