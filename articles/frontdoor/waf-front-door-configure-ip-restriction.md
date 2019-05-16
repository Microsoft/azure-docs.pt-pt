---
title: Configurar uma regra de restrição de IP com a regra de firewall de aplicação web para a porta da frente do Azure
description: Saiba como configurar uma regra da WAF de restrição de endereço IP para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: b129579916330a34a2a78d98f2c7653f129d3319
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523696"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Configurar uma regra de restrição de IP com firewall de aplicações web para o Azure desde início (pré-visualização)
 Este artigo mostra-lhe como configurar regras de restrição de IP na firewall de aplicações web do Azure (WAF) para a porta de entrada com o modelo de CLI do Azure, Azure PowerShell ou do Azure Resource Manager.

Uma regra de controlo de acesso de baseada em endereço IP é uma regra de WAF personalizada que permite-lhe controlar o acesso às suas aplicações web ao especificar uma lista de endereços IP ou intervalos de endereços IP no formulário de encaminhamento de entre domínios Classless (CIDR).

Por predefinição, a sua aplicação web estiver acessível a partir da internet. Se pretender limitar o acesso às suas aplicações web apenas aos clientes de uma lista de endereços IP conhecidos ou intervalos de endereços IP, terá de criar duas regras de correspondência de IP. Regra de correspondência de IP primeiro contém a lista de endereços IP como valores correspondentes e defina a ação para "Permitir". A segunda com prioridade mais baixa, é bloquear todos os outros endereços IP ao utilizar o operador "All" e defina a ação para "Bloco". Uma vez aplicada uma regra de restrição de IP, quaisquer pedidos provenientes de endereços fora desta lista de permissões recebe uma resposta (proibido) 403.  

> [!IMPORTANT]
> O recurso de restrição de IP de WAF para a porta da frente do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Configurar a política de WAF com a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configurar o ambiente da CLI e criar um perfil de porta de entrada.

#### <a name="set-up-azure-cli-environment"></a>Configurar o ambiente da CLI do Azure
1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli), ou utilizar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure pré-instalada e configurada para utilizar com a sua conta. Selecione o **experimente** botão na CLI comandos que se seguem. Selecionando **experimente** invoca um Shell de Cloud que pode iniciar sessão sua conta do Azure com. Assim que inicia uma sessão do cloud shell, introduza `az extension add --name front-door` para adicionar a extensão de front-door.
 2. Se utilizar a CLI localmente no Bash, inicie sessão no Azure com `az login`.

#### <a name="create-front-door-profile"></a>Criar perfil de porta de entrada
Criar um perfil de porta de entrada ao seguir as instruções descritas em [início rápido: Criar um perfil de porta de entrada](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Criar uma política de WAF com o [waf-política de rede de az criar](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) comando. No exemplo abaixo, substitua o nome da política *IPAllowPolicyExampleCLI* com um nome exclusivo da política.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Adicionar regra de controlo de acesso IP personalizada

Adicionar uma regra de controlo de acesso IP personalizada para a política de WAF que criou no passo anterior com o [personalizada-regra de política de waf de rede az criar](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) comando. 

No exemplo abaixo:
-  Substitua *IPAllowPolicyExampleCLI* com a sua política exclusiva criada anteriormente.
-  Substitua *ip-endereço-intervalo de-1*, *intervalo-2 de ip-endereço* com seu próprio intervalo.

Primeiro, crie o IP permitir regra para os endereços especificados.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Em seguida, crie um bloco de todas as regras IP com prioridade mais baixa do que a regra de permissão de IP anterior. Substitua a *IPAllowPolicyExampleCLI* com a sua política exclusiva criada anteriormente.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Localizar o ID de política de WAF
Localizar o ID de uma política de WAF com o [show de waf com as políticas de rede de az](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) comando. Substitua a *IPAllowPolicyExampleCLI* com a sua política exclusiva criada anteriormente.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Política de ligação WAF para um anfitrião de front-end de porta de entrada

Definir a porta da frente *WebApplicationFirewallPolicyLink* ID para o ID de política com o [atualização de front-door de rede de az](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) comando. Substitua a *IPAllowPolicyExampleCLI* com a sua política exclusiva criada anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, é aplicada a política de WAF para FrontendEndpoints [0]. Pode ligar a política de WAF para qualquer um dos front-ends.
> [!Note]
> Só precisa de definir o **WebApplicationFirewallPolicyLink** propriedade uma vez para ligar uma política de WAF para uma porta de entrada front-end. Atualizações à política subsequentes são automaticamente aplicadas no front-end.

## <a name="configure-waf-policy-with-azure-powershell"></a>Configurar a política de WAF com o Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, como configurar o ambiente do PowerShell e criar um perfil de porta de entrada.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as credenciais do Azure e instale o módulo do PowerShell de Az.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligar ao Azure com uma caixa de diálogo interativa para início de sessão
```
Connect-AzAccount

```
Antes de instalar o módulo de porta de entrada, certifique-se de que tem a versão atual do PowerShellGet instalado. Execute o comando e volte a abrir abaixo PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Instalar o módulo de Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de porta de entrada
Criar um perfil de porta de entrada ao seguir as instruções descritas em [início rápido: Criar um perfil de porta de entrada](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Definir a condição de correspondência do IP
Utilize o [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) comando para definir uma condição de correspondência IP. No exemplo abaixo, substitua *ip-endereço-intervalo de-1*, *intervalo-2 de ip-endereço* com seu próprio intervalo.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Criar uma correspondência IP de todos os de condição de regra
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Criar um personalizado regra de permissão de IP
   Utilize o [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) comando para definir uma ação e defina uma prioridade. No exemplo a seguir, serão permitidos pedidos de cliente IPs que correspondam à lista. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Crie um bloco de todos os IP regra com prioridade mais baixa do que a regra de permissão de IP anterior.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Configurar a política de WAF
Encontrar o nome do grupo de recursos que contém o perfil de porta de entrada usando `Get-AzResourceGroup`. Em seguida, configure uma política de WAF com o bloco IP regra usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Política de ligação WAF para um anfitrião de front-end de porta de entrada

Ligar o objeto de política de WAF para um anfitrião de front-end de porta de entrada existente e atualizar as propriedades de porta de entrada. Primeiro, recuperar o objeto de porta de entrada usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, configure o front-end *WebApplicationFirewallPolicyLink* propriedade para o resourceId da *$IPAllowPolicyExamplePS* criado no passo anterior com o [conjunto-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, é aplicada a política de WAF para FrontendEndpoints [0]. Pode ligar a política de WAF para qualquer um dos front-ends. Só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para ligar uma política de WAF para uma porta de entrada front-end. Atualizações à política subsequentes são automaticamente aplicadas no front-end.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Configurar a política de WAF com o modelo do Resource Manager
Ver o modelo que cria uma porta de entrada e de uma política de WAF com regras personalizadas de restrição de IP [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um perfil de porta de entrada](quickstart-create-front-door.md).
