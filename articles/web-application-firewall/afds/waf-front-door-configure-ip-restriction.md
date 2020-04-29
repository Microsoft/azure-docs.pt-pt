---
title: Configure regra WAF de restrição IP para porta frontal azure
description: Saiba como configurar uma regra de Firewall de aplicação Web para restringir os endereços IP para um ponto final da porta frontal azure existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336091"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configure uma regra de restrição IP com uma firewall de aplicação web para porta frontal azure

Este artigo mostra-lhe como configurar as regras de restrição IP numa Firewall de Aplicação Web (WAF) para A Porta Frontal Azure utilizando o portal Azure, Azure CLI, Azure PowerShell ou um modelo de Gestor de Recursos Azure.

Uma regra de controlo de acesso baseada em endereçoIP é uma regra WAF personalizada que permite controlar o acesso às suas aplicações web. Fá-lo especificando uma lista de endereços IP ou intervalos de endereços IP no formato de Encaminhamento Inter-Domínio (CIDR) sem classe.

Por padrão, a sua aplicação web é acessível a partir da Internet. Se pretender limitar o acesso aos clientes a partir de uma lista de endereços IP conhecidos ou intervalos de endereços IP, poderá criar uma regra de correspondência IP que contenha a lista de endereços IP como valores correspondentes e define o operador para "Não" (negação é verdade) e a ação para **bloquear**. Após a aplicação de uma regra de restrição IP, os pedidos originários de endereços fora desta lista permitida recebem uma resposta proibida 403.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configure uma política waf com o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Crie um perfil Azure Front Door seguindo as instruções descritas em [Quickstart: Crie uma Porta Frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política waf

1. No portal Azure, selecione **Criar um recurso,** digitar firewall de **aplicação Web** na caixa de pesquisa e, em seguida, selecione Firewall de **aplicação web (WAF)**.
2. Selecione **Criar**.
3. Na página **de política Create a WAF,** utilize os seguintes valores para completar o separador **Basics:**
   
   |Definição  |Valor  |
   |---------|---------|
   |Política para     |WAF global (porta da frente)|
   |Subscrição     |Selecione a sua subscrição|
   |Grupo de recursos     |Selecione o grupo de recursos onde está a porta da frente.|
   |Nome da política     |Escreva um nome para a sua política|
   |Estado político     |Ativado|

   Selecione **Seguinte: Definições de política**

1. No separador definições de **Política,** selecione **Prevenção**. Para o corpo de **resposta do Bloco**, tipo Que foi *bloqueado!* para que possa ver que a sua regra personalizada está em vigor.
2. Selecione **Seguinte: Regras geridas**.
3. Selecione **Seguinte: Regras personalizadas**.
4. Selecione **Adicionar regra personalizada**.
5. Na página de **regra personalizada Adicionar,** utilize os seguintes valores de teste para criar uma regra personalizada:

   |Definição  |Valor  |
   |---------|---------|
   |Nome de regra personalizado     |FdWafCustRule|
   |Estado     |Ativado|
   |Tipo de regra     |Match|
   |Prioridade    |100|
   |Tipo de correspondência     |Endereço IP|
   |Variável de correspondência|Adaddr remoto|
   |Operação|Não contém|
   |Endereço IP ou gama|10.10.10.0/24|
   |Então|Negar o tráfego|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regra personalizada":::

   Selecione **Adicionar**.
6. Selecione **Seguinte: Associação**.
7. **Selecione Adicionar o anfitrião frontal**.
8. Para o **anfitrião Frontend,** selecione o seu anfitrião frontend e selecione **Adicionar**.
9. Selecione **Rever + criar**.
10. Depois de passar a validação da sua política, selecione **Criar**.

### <a name="test-your-waf-policy"></a>Teste a sua política de WAF

1. Depois de a sua implementação de política WAF terminar, navegue até ao nome do anfitrião frontend frontdoor.
2. Devia ver a sua mensagem de bloqueio personalizada.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Teste de regra WAF":::

   > [!NOTE]
   > Um endereço IP privado foi intencionalmente usado na regra personalizada para garantir que a regra iria desencadear. Numa implementação real, crie regras *de permitir* e *negar* regras utilizando endereços IP para a sua situação particular.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configure uma política waf com o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição IP, crie o seu ambiente CLI e crie um perfil Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Configurar o ambiente Azure CLI
1. Instale o [Azure CLI,](/cli/azure/install-azure-cli)ou utilize a Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **Experimente** os comandos CLI que se seguem e, em seguida, inscreva-se na sua conta Azure na sessão Cloud Shell que abre. Depois do início `az extension add --name front-door` da sessão, introduza para adicionar a extensão da Porta Frontal Azure.
 2. Se estiver a usar o CLI localmente em Bash, inscreva-se no Azure utilizando `az login`.

#### <a name="create-an-azure-front-door-profile"></a>Criar um perfil de porta da frente azure
Crie um perfil Azure Front Door seguindo as instruções descritas em [Quickstart: Crie uma Porta Frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política waf

Crie uma política WAF utilizando a [política waf-porta da rede az criar](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) comando. No exemplo que se segue, substitua o nome de política *IPAllowPolicyExampleCLI* por um nome de política único.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicione uma regra personalizada de controlo de acesso IP

Use a regra personalizada de [waf-policy](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) de porta dianteira da rede az para adicionar uma regra personalizada de controlo de acesso IP para a política WAF que acabou de criar.

Nos seguintes exemplos:
-  Substitua o *IPAllowPolicyExampleCLI* pela sua política única criada anteriormente.
-  Substitua o *ip-address-range-1,* *ip-address-range-2* com o seu próprio alcance.

Em primeiro lugar, criar uma regra de permitir a política criada a partir do passo anterior. 
> [!NOTE]
> **-adiar** é necessário porque uma regra deve ter uma condição de jogo a ser adicionada no próximo passo.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Em seguida, adicione a condição de jogo à regra:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Encontre a identificação de uma política da WAF 
Encontre uma identificação da política waf usando o comando de [waf-policy da rede az.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Substitua o *IPAllowPolicyExampleCLI* no exemplo seguinte com a sua política única que criou anteriormente.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Ligue uma política waf a um anfitrião da frente da Porta Azure

Detete o ID *WebApplicationPolicyLink* da porta frontal Azure para o ID da política utilizando o comando de atualização da porta frontal da [rede Az.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Substitua o *IPAllowPolicyExampleCLI* pela sua política única que criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a política waf é aplicada aos **FrontendEndpoints[0]**. Pode ligar a política da WAF a qualquer uma das suas extremidades dianteiras.
> [!Note]
> Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para ligar uma política WAF a uma extremidade frontal da Porta Frontal Azure. As atualizações de políticas subsequentes são automaticamente aplicadas na parte frontal.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configure uma política waf com Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição IP, crie o seu ambiente PowerShell e crie um perfil Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
A Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure.

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para iniciar sessão no PowerShell utilizando as suas credenciais Azure e, em seguida, instale o módulo Az.

1. Ligue-se ao Azure utilizando o seguinte comando e, em seguida, use um diálogo interativo para iniciar sessão.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo Azure Front Door, certifique-se de que tem a versão atual do módulo PowerShellGet instalada. Executar o seguinte comando e, em seguida, reabrir PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo Az.FrontDoor utilizando o seguinte comando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Criar um perfil de porta da frente azure
Crie um perfil Azure Front Door seguindo as instruções descritas em [Quickstart: Crie uma Porta Frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Defina uma condição de correspondência IP
Utilize o comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) para definir uma condição de correspondência IP.
No exemplo seguinte, substitua o *ip-address-range-1*, *ip-address-range-2* com o seu próprio alcance.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Criar uma regra de permitir IP personalizada

Utilize o comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) para definir uma ação e definir uma prioridade. No exemplo seguinte, os pedidos não dos IPs do cliente que correspondam à lista serão bloqueados.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurar uma política waf
Encontre o nome do grupo de recursos que contém `Get-AzResourceGroup`o perfil da Porta Frontal Azure utilizando . Em seguida, configure uma política waf com a regra IP utilizando [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Ligue uma política waf a um anfitrião da frente da Porta Azure

Ligue um objeto de política WAF a um anfitrião frontal existente e atualize as propriedades da Porta Frontal Azure. Primeiro, recupere o objeto da porta da frente azure utilizando [o Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, detete teda a propriedade **WebApplicationFirewallPolicyLink** para o ID de recursos de *$IPAllowPolicyExamplePS*, criado no passo anterior, utilizando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a política waf é aplicada aos **FrontendEndpoints[0]**. Pode ligar uma política de WAF a qualquer uma das suas extremidades dianteiras. Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para ligar uma política WAF a uma extremidade frontal da Porta Frontal Azure. As atualizações de políticas subsequentes são automaticamente aplicadas na parte frontal.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configure uma política waf com um modelo de Gestor de Recursos
Para ver o modelo que cria uma política de Porta Frontal Azure e uma política waf com regras de restrição IP personalizadas, vá ao [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passos seguintes

- Aprenda a [criar um perfil de Porta Frontal Azure.](../../frontdoor/quickstart-create-front-door.md)
