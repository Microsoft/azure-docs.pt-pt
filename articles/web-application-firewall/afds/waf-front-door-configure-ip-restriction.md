---
title: Configure regra WAF de restrição IP para porta frontal Azure
description: Saiba como configurar uma regra de Firewall de aplicação web para restringir os endereços IP para um ponto final da porta frontal Azure existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860856"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configure uma regra de restrição IP com uma firewall de aplicação web para a porta frontal Azure

Este artigo mostra-lhe como configurar as regras de restrição IP numa Firewall de Aplicação Web (WAF) para Azure Front Door utilizando o portal Azure, Azure CLI, Azure PowerShell ou um modelo de Gestor de Recursos Azure.

Uma regra de controlo de acesso baseada em endereços IP é uma regra personalizada da WAF que permite controlar o acesso às suas aplicações web. Fá-lo especificando uma lista de endereços IP ou intervalos de endereços IP no formato Classless Inter-Domain Encaminhamento (CIDR). Existem dois tipos de variáveis de correspondência na correspondência de endereço IP, **RemoteAddr** e **SocketAddr**. RemoteAddr é o IP do cliente original que é normalmente enviado através do cabeçalho de pedido X-Forwarded.Para. SocketAddr é o endereço IP de origem que a WAF vê. Se o seu utilizador estiver por detrás de um proxy, o SocketAddr é frequentemente o endereço do servidor proxy.

Por predefinição, a sua aplicação web está acessível a partir da Internet. Se pretender limitar o acesso aos clientes a partir de uma lista de endereços IP ou intervalos de endereços IP conhecidos, poderá criar uma regra de correspondência IP que contenha a lista de endereços IP como valores correspondentes e que o operador defina para "Não" (a negação é verdadeira) e a ação para **Bloquear.** Após a aplicação de uma regra de restrição de IP, os pedidos originários de endereços fora desta lista permitida recebem uma resposta 403 Proibida.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configure uma política da WAF com o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Crie um perfil da Porta Frontal Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

1. No portal Azure, **selecione Criar um recurso**, digitar firewall de  **aplicação Web** na caixa de pesquisa e, em seguida, selecione Web Application Firewall **(WAF)**.
2. Selecione **Criar**.
3. Na página **de política da CREATE,** utilize os seguintes valores para completar o **separador Básicos:**
   
   |Definição  |Valor  |
   |---------|---------|
   |Política para     |WAF Global (Porta da Frente)|
   |Subscrição     |Selecione a sua subscrição|
   |Grupo de recursos     |Selecione o grupo de recursos onde fica a porta da frente.|
   |Nome da política     |Digite um nome para a sua política|
   |Estado político     |Ativado|

   Selecione **Seguinte: Definições de política**

1. No **separador Definições de Política,** selecione **Prevenção**. Para o corpo de resposta do **Bloco,** tipo *Você foi bloqueado!* para que possa ver que a sua regra personalizada está em vigor.
2. Selecione **Seguinte: Regras geridas**.
3. Selecione **Seguinte: Regras personalizadas**.
4. **Selecione Adicionar regra personalizada**.
5. Na página **de regras personalizadas Adicionar,** utilize os seguintes valores de teste para criar uma regra personalizada:

   |Definição  |Valor  |
   |---------|---------|
   |Nome da regra personalizada     |FdWafCustRule|
   |Estado     |Ativado|
   |Tipo de regra     |Match|
   |Prioridade    |100|
   |Tipo de correspondência     |Endereço IP|
   |Variável de correspondência|RemoteAddr|
   |Operação|Não contém|
   |Endereço IP ou alcance|10.10.10.0/24|
   |Então|Negar o tráfego|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regra personalizada":::

   Selecione **Adicionar**.
6. Selecione **Seguinte: Associação**.
7. **Selecione Adicionar anfitrião frontend**.
8. Para **o anfitrião Frontend**, selecione o anfitrião frontend e selecione **Add**.
9. Selecione **Rever + criar**.
10. Depois de passar a sua validação de política, **selecione Criar**.

### <a name="test-your-waf-policy"></a>Teste a sua política de WAF

1. Depois da implementação da sua política WAF estar concluída, navegue pelo nome de anfitrião da porta da frente da porta da frente.
2. Devia ver a sua mensagem de bloqueio personalizada.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Teste de regra da WAF":::

   > [!NOTE]
   > Um endereço IP privado foi intencionalmente utilizado na regra personalizada para garantir que a regra iria desencadear. Numa implementação real, crie regras *de permitir* e *negar* usando endereços IP para a sua situação particular.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configure uma política da WAF com o CLI Azure

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição IP, crie o seu ambiente CLI e crie um perfil Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Configurar o ambiente Azure CLI
1. Instale o [Azure CLI,](/cli/azure/install-azure-cli)ou utilize a Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **'Experimente-o'** nos comandos CLI que se seguem e, em seguida, inscreva-se na sua conta Azure na sessão Cloud Shell que se abre. Após o início da sessão, `az extension add --name front-door` insira para adicionar a extensão da Porta Frontal Azure.
 2. Se estiver a utilizar o CLI localmente em Bash, inscreva-se no Azure utilizando `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Criar um perfil da porta frontal azul
Crie um perfil da Porta Frontal Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Crie uma política WAF utilizando o comando da [política de waf-porta frontal da rede az.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) No exemplo que se segue, substitua o nome de política *IPAllowPolicyExampleCLI* por um nome de política único.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicione uma regra de controlo de acesso IP personalizada

Utilize a regra personalizada da [waf-policy da porta frontal az](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) para adicionar uma regra de controlo de acesso IP personalizada para a política WAF que acabou de criar.

Nos seguintes exemplos:
-  Substitua *o IPAllowPolicyExampleCLI* pela sua política única criada anteriormente.
-  Substitua *o ip-address-range-1,* *ip-address-range-2* com a sua própria gama.

Em primeiro lugar, criar uma regra de IP permite a regra para a política criada a partir do passo anterior. 
> [!NOTE]
> **--adiar** é necessário porque uma regra deve ter uma condição de jogo a ser adicionada no passo seguinte.

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
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Encontre o ID de uma política da WAF 
Encontre o ID de uma política da WAF utilizando o comando de [exibição de waf-policy de porta frontal da rede az.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) Substitua *o IPAllowPolicyExampleCLI* no exemplo seguinte com a sua política única que criou anteriormente.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Ligue uma política da WAF a um anfitrião frontal da Porta frontal Azure

Desave o ID da porta frontal Azure, *FirewallPolicyLink,* para o ID da política, utilizando o comando [de atualização da porta frontal da rede Az.](/cli/azure/network/front-door#az_network_front_door_update) Substitua *o IPAllowPolicyExampleCLI* pela sua política única que criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a política da WAF é aplicada à **Frontendendpoints[0]**. Pode ligar a política da WAF a qualquer uma das suas extremidades frontais.
> [!Note]
> Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para ligar uma política WAF a uma extremidade frontal da porta frontal Azure. As atualizações de política subsequentes são automaticamente aplicadas na parte frontal.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configure uma política da WAF com a Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de restrição IP, crie o seu ambiente PowerShell e crie um perfil Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
A Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os recursos da Azure.

Pode instalar o [Azure PowerShell](/powershell/azure/) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para iniciar seduca no PowerShell utilizando as suas credenciais Azure e, em seguida, instale o módulo Az.

1. Ligue-se ao Azure utilizando o seguinte comando e, em seguida, utilize um diálogo interativo para iniciar seduca.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo Azure Front Door, certifique-se de que tem a versão atual do módulo PowerShellGet instalada. Executar o seguinte comando, e depois reabrir PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo Az.FrontDoor utilizando o seguinte comando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Criar um perfil da porta frontal azul
Crie um perfil da Porta Frontal Azure seguindo as instruções descritas no [Quickstart: Crie uma porta frontal para uma aplicação web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definir uma condição de correspondência IP
Utilize o comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) para definir uma condição de correspondência IP.
No exemplo seguinte, substitua *o ip-address-range-1*, *ip-address-range-2* com a sua própria gama.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Crie uma regra de ip personalizado

Utilize o comando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) para definir uma ação e definir uma prioridade. No exemplo seguinte, os pedidos não de IPs de cliente que correspondam à lista serão bloqueados.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configure uma política da WAF
Encontre o nome do grupo de recursos que contém o perfil da porta frontal Azure utilizando `Get-AzResourceGroup` . Em seguida, configurar uma política da WAF com a regra IP utilizando [a New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Ligue uma política da WAF a um anfitrião frontal da Porta frontal Azure

Ligue um objeto de política WAF a um anfitrião frontal existente e atualize as propriedades da Porta Frontal Azure. Em primeiro lugar, recupere o objeto da porta frontal Azure utilizando [o Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, deite a propriedade **WebApplicationFirewallPolicyLink** para o ID de recursos de *$IPAllowPolicyExamplePS*, criado no passo anterior, utilizando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a política da WAF é aplicada à **Frontendendpoints[0]**. Pode ligar uma política da WAF a qualquer uma das suas extremidades frontais. Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para ligar uma política WAF a uma extremidade frontal da porta frontal Azure. As atualizações de política subsequentes são automaticamente aplicadas na parte frontal.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configure uma política de WAF com um modelo de gestor de recursos
Para ver o modelo que cria uma política de Porta Frontal Azure e uma política de WAF com regras de restrição ip personalizadas, vá ao [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um perfil da Porta Frontal Azure](../../frontdoor/quickstart-create-front-door.md).
