---
title: Configurar uma regra de restrição de IP com uma regra de firewall de aplicação web do serviço de porta de entrada do Azure
description: Saiba como configurar uma regra de firewall de aplicação web para restringir os endereços IP para um ponto de extremidade do serviço de porta de entrada do Azure existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743154"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurar uma regra de restrição de IP com um firewall de aplicações web do serviço de porta de entrada do Azure
Este artigo mostra-lhe como configurar regras de restrição de IP num firewall de aplicações web (WAF) desde início do serviço do Azure com a CLI do Azure, Azure PowerShell ou um modelo Azure Resource Manager.

Uma regra de controlo de acesso com base no endereço IP é uma regra personalizada da WAF que lhe permite controlar o acesso às suas aplicações web. Ele faz isso ao especificar uma lista de endereços IP ou intervalos de endereços IP no formato de encaminhamento de entre domínios Classless (CIDR).

Por predefinição, a sua aplicação web estiver acessível a partir da internet. Se pretender limitar o acesso aos clientes a partir de uma lista de endereços IP conhecidos ou intervalos de endereços IP, tem de criar duas regras de correspondência de IP. A primeira regra de correspondência de IP contém a lista de endereços IP como valores correspondentes e define a ação para **permitir**. O um segundo, com prioridade mais baixa, bloqueia todos os outros endereços IP utilizando o **todos os** operador e a definição da ação **bloco**. Após é aplicada uma regra de restrição de IP, pedidos provenientes de endereços fora desta lista de permissões recebem uma resposta de proibido 403.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurar uma diretiva de WAF com a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configurar o ambiente da CLI e criar um perfil de serviço de porta de entrada do Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurar o ambiente da CLI do Azure
1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli), ou utilize o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o **experimente** botão nos comandos da CLI que siga e, em seguida, iniciar sessão na sua conta do Azure na sessão do Cloud Shell, que abre. Depois de iniciado a sessão, introduza `az extension add --name front-door` para adicionar a extensão de serviço de porta de entrada do Azure.
 2. Se estiver a utilizar a CLI localmente no Bash, inicie sessão no Azure utilizando `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Criar um perfil de serviço de porta de entrada do Azure
Criar um perfil de serviço de porta de entrada do Azure ao seguir as instruções descritas em [início rápido: Criar uma porta de entrada para um aplicativo web global de elevada disponibilidade](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Criar uma política de WAF com o [waf-política de rede de az criar](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) comando. No exemplo que segue, substitua o nome da política *IPAllowPolicyExampleCLI* com um nome exclusivo da política.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicionar uma regra de controlo de acesso IP personalizada

Utilize o [personalizada-regra de política de waf de rede az criar](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) comando para adicionar uma IP acesso controlo regra personalizada para a política de WAF que acabou de criar.

Nos exemplos a seguir:
-  Substitua *IPAllowPolicyExampleCLI* com a sua política exclusiva criada anteriormente.
-  Substitua *ip-endereço-intervalo de-1*, *intervalo-2 de ip-endereço* com seu próprio intervalo.

Primeiro, crie o IP permitir regra para os endereços especificados.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Em seguida, crie uma **bloquear todos** regra com prioridade mais baixa anterior **permitir** regra. Mais uma vez, substitua *IPAllowPolicyExampleCLI* no exemplo seguinte pelo seu exclusivo da política que criou anteriormente.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Localizar o ID de uma política de WAF 
Encontrar o ID de uma política de WAF ao utilizar o [show de waf com as políticas de rede de az](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) comando. Substitua *IPAllowPolicyExampleCLI* no exemplo seguinte pelo seu exclusivo da política que criou anteriormente.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Ligue uma política de WAF para um anfitrião de front-end do serviço de porta de entrada do Azure

Definir o serviço de porta de entrada do Azure *WebApplicationFirewallPolicyLink* ID para o ID de política, utilizando o [atualização de front-door de rede de az](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) comando. Substitua *IPAllowPolicyExampleCLI* com a sua política exclusiva que criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, é aplicada a política de WAF para **FrontendEndpoints [0]** . Pode associar a política de WAF para qualquer um dos seus front-ends.
> [!Note]
> Tem de definir o **WebApplicationFirewallPolicyLink** propriedade apenas uma vez para ligar uma política de WAF a um front-end de serviço de porta de entrada do Azure. Atualizações à política subsequentes são automaticamente aplicadas no front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurar uma política de WAF com o Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição de IP, como configurar o ambiente do PowerShell e criar um perfil de serviço de porta de entrada do Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelos para o gerenciamento de recursos do Azure.

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para iniciar sessão no PowerShell, utilizando as credenciais do Azure e, em seguida, instale o módulo de Az.

1. Ligar ao Azure com o comando seguinte e, em seguida, utilizar uma caixa de diálogo interativa para iniciar sessão.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo de serviço de porta de entrada do Azure, certifique-se de que tem a versão atual do módulo PowerShellGet instalado. Execute o seguinte comando e, em seguida, reabra o PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo de Az.FrontDoor utilizando o seguinte comando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Criar um perfil de serviço de porta de entrada do Azure
Criar um perfil de serviço de porta de entrada do Azure ao seguir as instruções descritas em [início rápido: Criar uma porta de entrada para um aplicativo web global de elevada disponibilidade](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definir uma condição de correspondência do IP
Utilize o [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) comando para definir uma condição de correspondência IP.
No exemplo seguinte, substitua *ip-endereço-intervalo de-1*, *intervalo-2 de ip-endereço* com seu próprio intervalo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Criar um IP *corresponde à condição de todos os* regra ao utilizar o seguinte comando:
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
Criar uma **bloquear todos** regra com prioridade mais baixa que o IP anterior **permitir** regra.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Configurar uma política de WAF
Encontrar o nome do grupo de recursos que contém o perfil de serviço de porta de entrada do Azure utilizando `Get-AzResourceGroup`. Em seguida, configure uma política de WAF com o IP **bloquear todos** regra utilizando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Ligue uma política de WAF para um anfitrião de front-end do serviço de porta de entrada do Azure

Ligar um objeto de política de WAF a um front-end anfitrião e atualização serviço de porta de entrada do Azure das propriedades existentes. Em primeiro lugar, obtenha o objeto de serviço de porta de entrada do Azure, utilizando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, configure as **WebApplicationFirewallPolicyLink** propriedade para o ID de recurso de *$IPAllowPolicyExamplePS*, criada no passo anterior, ao utilizar o [conjunto-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)comando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, é aplicada a política de WAF para **FrontendEndpoints [0]** . Pode associar uma política de WAF para qualquer um dos seus front-ends. Tem de definir o **WebApplicationFirewallPolicyLink** propriedade apenas uma vez para ligar uma política de WAF a um front-end de serviço de porta de entrada do Azure. Atualizações à política subsequentes são automaticamente aplicadas no front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurar uma diretiva de WAF com um modelo do Resource Manager
Para ver o modelo que cria uma política do serviço de porta de entrada do Azure e uma política de WAF com regras personalizadas de restrição de IP, aceda a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um perfil de serviço do Azure desde início](quickstart-create-front-door.md).
