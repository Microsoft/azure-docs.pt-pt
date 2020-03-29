---
title: Pare de usar extensão VM de identidade gerida - Azure AD
description: Instruções passo a passo para parar de utilizar a extensão VM e começar a utilizar o Serviço de Metadados de Instância Seletiva (IMDS) para autenticação.
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
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049069"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de usar a extensão de identidades geridas pela máquina virtual e começar a usar o Serviço de Metadados de Instância Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão virtual da máquina para identidades geridas

A extensão virtual da máquina para identidades geridas é usada para solicitar fichas para uma identidade gerida dentro da máquina virtual. O fluxo de trabalho consiste nos seguintes passos:

1. Em primeiro lugar, a carga de `http://localhost/oauth2/token` trabalho dentro do recurso chama o ponto final local para solicitar um sinal de acesso.
2. A extensão da máquina virtual utiliza então as credenciais para a identidade gerida, para solicitar um sinal de acesso da Azure AD.. 
3. O token de acesso é devolvido ao chamador, e pode ser usado para autenticar serviços que suportam a autenticação Azure AD, como Azure Key Vault ou Azure Storage.

Devido a várias limitações descritas na secção seguinte, a extensão vM de identidade gerida foi depreciada a favor da utilização do ponto final equivalente no Serviço de Metadados de Instância Seletiva (IMDS)

### <a name="provision-the-extension"></a>Prever a extensão 

Quando configurar uma máquina virtual ou uma escala de máquina virtual definida para ter uma identidade gerida, pode opcionalmente `-Type` optar por fornecer as identidades geridas para a extensão VM dos recursos Azure utilizando o parâmetro no cmdlet [set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Pode passar `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de máquina virtual, e nomeá-la usando o `-Name` parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final simbólico da OAuth para aquisição simbólica:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Também pode utilizar o modelo de implementação do Gestor de Recursos Azure para `resources` fornecer a extensão VM, adicionando o seguinte JSON à secção ao modelo (utilização `ManagedIdentityExtensionForLinux` para o nome e elementos de tipo para a versão Linux).

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
    
    
Se estiver a trabalhar com conjuntos de escala de máquina virtual, também pode fornecer as identidades geridas para a extensão de conjunto de máquinas virtuais de recursos Azure utilizando o cmdlet [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Pode passar `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de conjunto de escala `-Name` de máquina virtual, e nomeá-lo usando o parâmetro. O `-Settings` parâmetro especifica a porta utilizada pelo ponto final simbólico da OAuth para aquisição simbólica:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para fornecer a extensão de conjunto de escala de máquina virtual com `extensionpProfile` o modelo de `ManagedIdentityExtensionForLinux` implementação do Gestor de Recursos Azure, adicione o seguinte JSON à secção ao modelo (utilização para o nome e elementos de tipo para a versão Linux).

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

O fornecimento da extensão virtual da máquina pode falhar devido a falhas de procura do DNS. Se isto acontecer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a `-n ManagedIdentityExtensionForWindows` extensão, a utilização ou `-n ManagedIdentityExtensionForLinux` o interruptor (dependendo do tipo de máquina virtual) com a eliminação da [extensão az vm,](https://docs.microsoft.com/cli/azure/vm/)ou a eliminação da [extensão az vmss](https://docs.microsoft.com/cli/azure/vmss) para conjuntos de escala de máquinas virtuais utilizando o Azure CLI, ou `Remove-AzVMExtension` para a Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
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
| `GET` | O verbo HTTP, indicando que pretende recuperar dados do ponto final. Neste caso, um sinal de acesso da OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades geridas para o ponto final dos recursos Azure, onde 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de corda de consulta, indicando o ID da aplicação URI do recurso alvo. Também aparece na `aud` reivindicação (audiência) do símbolo emitido. Este exemplo solicita um sinal de acesso ao Azure Resource `https://management.azure.com/`Manager, que tem um ID app URI de . |
| `Metadata` | Um campo de cabeçalho de pedido HTTP, exigido por identidades geridas para os recursos Do Azure como uma mitigação contra o ataque de Forgery de Pedido lateral do Servidor (SSRF). Este valor deve ser definido como "verdadeiro", em todos os casos inferiores.|
| `object_id` | (Opcional) Um parâmetro de corda de consulta, indicando o object_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|
| `client_id` | (Opcional) Um parâmetro de corda de consulta, indicando o client_id da identidade gerida para a que gostaria do símbolo. Necessário, se o seu VM tiver várias identidades geridas atribuídas ao utilizador.|


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
| `access_token` | O sinal de acesso solicitado. Ao chamar uma API DE REPOUSO segura, `Authorization` o símbolo está incorporado no campo de cabeçalho de pedido como um símbolo "portador", permitindo que a API autenticar o chamador. | 
| `refresh_token` | Não usado por identidades geridas para recursos Azure. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válido, antes de expirar, a partir do momento da emissão. O tempo de emissão pode ser encontrado `iat` na reivindicação do símbolo. |
| `expires_on` | O tempo de espera quando o sinal de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `exp` token). |
| `not_before` | O tempo de espera quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `nbf` token). |
| `resource` | O recurso para o sinal de acesso `resource` foi solicitado, que corresponde ao parâmetro de corda de consulta do pedido. |
| `token_type` | O tipo de ficha, que é um símbolo de acesso "Portador", o que significa que o recurso pode dar acesso ao portador deste símbolo. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Resolução de problemas da extensão da máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reiniciar a extensão da máquina virtual após uma falha

No Windows e em certas versões do Linux, se a extensão parar, o seguinte cmdlet pode ser utilizado para reiniciá-lo manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- O nome e o tipo de extensão para Windows é:`ManagedIdentityExtensionForWindows`
- O nome e o tipo de extensão para Linux é:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Roteiro de automação" falha ao tentar exportar esquemas para identidades geridas para extensão de recursos do Azure

Quando as identidades geridas para os recursos Azure são ativadas numa máquina virtual, o seguinte erro é demonstrado ao tentar utilizar a função "Automação script" para a máquina virtual, ou o seu grupo de recursos:

![Identidades geridas para erro de exportação de script de automação de recursos Do Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades geridas para a extensão virtual de máquinas de recursos Azure não suportam atualmente a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para permitir identidades geridas para recursos Azure no recurso. Estas secções podem ser adicionadas manualmente seguindo os exemplos em [Identidades geridas pela Configure para os recursos Azure numa máquina virtual Azure utilizando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquemas estiver disponível para as identidades geridas para a extensão virtual da máquina de recursos Azure (prevista para a depreciação em janeiro de 2019), será listada em Grupos de [Recursos Exportadores que contenham extensões VM.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão da máquina virtual 

Existem várias limitações importantes para a utilização da extensão da máquina virtual. 

 * A limitação mais grave é o facto de as credenciais utilizadas para solicitar fichas serem armazenadas na máquina virtual. Um intruso que viole com sucesso a máquina virtual pode exfiltrar as credenciais. 
 * Além disso, a extensão da máquina virtual ainda não é suportada por várias distribuições linux, com um enorme custo de desenvolvimento para modificar, construir e testar a extensão em cada uma dessas distribuições. Atualmente, apenas são suportadas as seguintes distribuições Linux: 
    * CoreOS Estável
    * Centos 7.1 
    * Chapéu Vermelho 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Existe um impacto de desempenho na implementação de máquinas virtuais com identidades geridas, uma vez que a extensão da máquina virtual também tem de ser aprovisionada. 
 * Finalmente, a extensão da máquina virtual só pode suportar ter 32 identidades geridas por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância Azure

O Serviço de [Metadados de InstânciaS Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) é um ponto final do REST que fornece informações sobre a execução de casos de máquinas virtuais que podem ser usados para gerir e configurar as suas máquinas virtuais. O ponto final está disponível num conhecido endereço`169.254.169.254`IP não resaída ( ) que só pode ser acedido a partir da máquina virtual.

Existem várias vantagens em usar o Azure IMDS para solicitar fichas. 

1. O serviço é externo à máquina virtual, pelo que as credenciais utilizadas pelas identidades geridas já não estão presentes na máquina virtual. Em vez disso, são hospedados e fixados na máquina hospedeira da máquina virtual Azure.   
2. Todos os sistemas operativos Windows e Linux suportados no Azure IaaS podem utilizar identidades geridas.
3. A implantação é mais rápida e fácil, uma vez que a extensão VM já não precisa de ser prevista.
4. Com o ponto final do IMDS, até 1000 identidades geridas atribuídas ao utilizador podem ser atribuídas a uma única máquina virtual.
5. Não existe uma alteração significativa nos pedidos que utilizam o IMDS em oposição aos que utilizam a extensão virtual da máquina, pelo que é bastante simples a porta sobre as implementações existentes que utilizam atualmente a extensão virtual da máquina.

Por estas razões, o serviço Azure IMDS será a forma de facto de solicitar fichas, uma vez que a extensão da máquina virtual é depreciada. 


## <a name="next-steps"></a>Passos Seguintes

* [Como usar identidades geridas para recursos Azure numa máquina virtual Azure para adquirir um símbolo de acesso](how-to-use-vm-token.md)
* [Serviço de Metadados de Instância Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
