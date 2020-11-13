---
title: Restrições de acesso ao Serviço de Aplicações Azure
description: Saiba como proteger a sua aplicação no Azure App Service, configurando restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e074be91f343e04ba3049aea51f83f7f6364cdb0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564043"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurar restrições de acesso ao Serviço de Aplicações Azure

Ao configurar restrições de acesso, pode definir uma lista de autorizações/negação de prioridades que controla o acesso à rede à sua aplicação. A lista pode incluir endereços IP ou sub-redes de Rede Virtual Azure. Quando há uma ou mais entradas, uma *negação* implícita tudo existe no final da lista.

A capacidade de restrição de acesso funciona com todas as cargas de trabalho hospedadas pelo Azure App Service. As cargas de trabalho podem incluir aplicações web, aplicações API, aplicações Linux, aplicações de contentores Linux e funções.

Quando um pedido é feito para a sua aplicação, o endereço FROM é avaliado contra as regras de endereço IP na sua lista de restrições de acesso. Se o endereço FROM estiver numa sub-rede configurada com pontos finais de serviço para o Microsoft.Web, a sub-rede de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não tiver acesso com base nas regras da lista, o serviço responde com um código de estado [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

A capacidade de restrição de acesso é implementada nas funções frontais do Serviço de Aplicações, que estão a montante dos anfitriões dos trabalhadores onde o seu código funciona. Portanto, as restrições de acesso são efetivamente listas de controlo de acesso à rede (ACLs).

A capacidade de restringir o acesso à sua aplicação web a partir de uma rede virtual Azure é ativada por [pontos finais de serviço.][serviceendpoints] Com os pontos finais de serviço, pode restringir o acesso a um serviço multitenante a partir de sub-redes selecionadas. Não funciona para restringir o tráfego a aplicações que estão hospedadas num Ambiente de Serviço de Aplicações. Se estiver num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação aplicando regras de endereço IP.

> [!NOTE]
> Os pontos finais de serviço devem ser ativados tanto no lado da rede como para o serviço Azure com o qual estão a ser ativados. Para obter uma lista de serviços Azure que suportam pontos finais de serviço, consulte [os pontos finais do serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![Diagrama do fluxo de restrições de acesso.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Adicionar ou editar regras de restrição de acesso no portal

Para adicionar uma regra de restrição de acesso à sua aplicação, faça o seguinte:

1. Inicie sessão no portal do Azure.

1. No painel esquerdo, selecione **Networking**.

1. No painel **de rede,** sob **restrições de acesso,** selecione **Restrições de Acesso Configure**.

   ![Screenshot do painel de opções de rede do Serviço de Aplicações no portal Azure.](media/app-service-ip-restrictions/access-restrictions.png)  

1. Na página **'Restrições de Acesso',** reveja a lista de regras de restrição de acesso que são definidas para a sua aplicação.

   ![Screenshot da página 'Restrições de Acesso' no portal Azure, mostrando a lista de regras de restrição de acesso definidas para a aplicação selecionada.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   A lista apresenta todas as restrições atuais que são aplicadas à aplicação. Se tiver uma restrição de rede virtual na sua aplicação, a tabela mostra se os pontos finais do serviço estão ativados para o Microsoft.Web. Se não forem definidas restrições na sua aplicação, a aplicação está acessível a partir de qualquer lugar.  

### <a name="add-an-access-restriction-rule"></a>Adicionar uma regra de restrição de acesso

Para adicionar uma regra de restrição de acesso à sua aplicação, no painel **de restrições** de acesso, selecione **'Adicionar regra'.** Depois de adicionar uma regra, torna-se eficaz imediatamente. 

As regras são aplicadas por ordem prioritária, a partir do número mais baixo da coluna **Prioridade.** Uma *negação* implícita tudo está em vigor depois de adicionar uma única regra.

No painel **de restrição de IP adicionar,** quando criar uma regra, faça o seguinte:

1. Em **Ação** , selecione **Ou permitir** ou **negar**.  

   ![Screenshot do painel "Adicionar restrição IP".](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Opcionalmente, insira um nome e descrição da regra.  
1. Na lista de drop-down **tipo,** selecione o tipo de regra.  
1. Na caixa **Prioridade,** insira um valor prioritário.  
1. Nas **listas de subscrição,** **Rede Virtual** e **Sub-rede,** selecione o que pretende restringir o acesso.  

### <a name="set-an-ip-address-based-rule"></a>Definir uma regra baseada em endereços IP

Siga o procedimento descrito na secção anterior, mas com a seguinte variação:
* Para o passo 3, na lista de drop-down **tipo,** selecione **IPv4** ou **IPv6**. 

Especifique o endereço IP na notação de encaminhamento de Inter-Domain (CIDR) sem classe para os endereços IPv4 e IPv6. Para especificar um endereço, pode utilizar algo como *1.2.3.4/32* , onde os primeiros quatro octetos representam o seu endereço IP e */32* é a máscara. A notação CIDR IPv4 para todos os endereços é de 0.0.0.0/0. Para saber mais sobre a notação CIDR, consulte [o Roteamento de Inter-Domain Sem Classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Utilizar pontos finais de serviço

Ao utilizar pontos finais de serviço, pode restringir o acesso a sub-redes de rede virtuais Azure selecionadas. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo **de Rede Virtual.** Pode então selecionar a subscrição, a rede virtual e a sub-rede a que pretende permitir ou negar o acesso. 

Se os pontos finais de serviço ainda não estiverem ativados com o Microsoft.Web para a sub-rede que selecionou, serão automaticamente ativados, a menos que selecione a caixa de verificação **de pontos finais do serviço Microsoft.Web em falta.** O cenário em que poderá querer ativar os pontos finais do serviço na app, mas não a sub-rede, depende principalmente de ter as permissões para os ativar na sub-rede. 

Se precisar de outra pessoa para ativar os pontos finais do serviço na sub-rede, selecione a caixa de **verificação de pontos finais do serviço Microsoft.Web em** falta. A sua aplicação será configurada para os pontos finais de serviço, antecipando-se a sua ativação posterior na sub-rede. 

![Screenshot do painel "Adicionar restrição IP" com o tipo de Rede Virtual selecionado.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Não é possível utilizar pontos finais de serviço para restringir o acesso a aplicações que funcionam num Ambiente de Serviço de Aplicações. Quando a sua aplicação está num Ambiente de Serviço de Aplicações, pode controlar o acesso à ela aplicando regras de acesso IP. 

Com os pontos finais de serviço, pode configurar a sua aplicação com gateways de aplicações ou outros dispositivos de firewall de aplicações web (WAF). Também pode configurar aplicações de vários níveis com extremidades traseiras seguras. Para obter mais informações, consulte [funcionalidades de Networking e integração do Serviço](networking-features.md) de Aplicações e [Gateway de Aplicações com pontos finais de serviço.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Os pontos finais de serviço não são suportados atualmente para aplicações web que usam IP IP (SSL) virtual Layer (SSL) virtual (SSL).
> - Existe um limite de 512 linhas de restrições ip ou ponto final de serviço. Se necessitar de mais de 512 linhas de restrições, sugerimos que considere instalar um produto de segurança autónomo, como a Azure Front Door, Azure App Gateway ou um WAF.
>

## <a name="manage-access-restriction-rules"></a>Gerir regras de restrição de acesso

Pode editar ou eliminar uma regra de restrição de acesso existente.

### <a name="edit-a-rule"></a>Editar uma regra

1. Para começar a editar uma regra de restrição de acesso existente, na página Restrições de Acesso, clique **duas vezes** na regra que pretende editar.

1. No painel **de restrição IP editar,** faça as alterações e, em seguida, selecione **a regra de Atualização**. As edições são eficazes imediatamente, incluindo alterações na encomenda prioritária.

   ![Screenshot do painel "Editar A Restrição IP" no portal Azure, mostrando os campos para uma regra de restrição de acesso existente.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Quando edita uma regra, não pode alternar entre uma regra de endereço IP e uma regra de rede virtual. 

   ![Screenshot do painel "Editar A Restrição IP" no portal Azure, mostrando as definições para uma regra de rede virtual.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar uma regra, na página **'Restrições de Acesso',** selecione a elipse **(...** ) ao lado da regra que pretende eliminar e, em seguida, selecione **Remover**.

![Screenshot da página "Restrições de Acesso", mostrando a elipse "Remover" ao lado da regra de restrição de acesso a ser eliminada.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Bloqueie um único endereço IP

Quando adiciona a sua primeira regra de restrição IP, o serviço adiciona uma regra de *Negar* tudo com uma prioridade de 2147483647. Na prática, a regra de Negar explícito *é* a regra final a ser executada, e bloqueia o acesso a qualquer endereço IP que não seja explicitamente permitido por uma regra *de Permitir.*

Para um cenário em que pretende bloquear explicitamente um único endereço IP ou um bloco de endereços IP, mas permitir o acesso a tudo o resto, adicione uma regra *de Permitir Tudo* explícito.

![Screenshot da página "Restrições de Acesso" no portal Azure, mostrando um único endereço IP bloqueado.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Restringir o acesso a um site SCM 

Além de conseguir controlar o acesso à sua app, pode restringir o acesso ao site SCM que é utilizado pela sua app. O site SCM é tanto o ponto final de implementação web como a consola Kudu. Pode atribuir restrições de acesso ao site SCM separadamente ou utilizar o mesmo conjunto de restrições tanto para a aplicação como para o site SCM. Quando seleciona as **mesmas restrições \<app name> que** a caixa de verificação, tudo é apagado. Se limpar a caixa de verificação, as definições do seu site SCM são reaplicadas. 

![Screenshot da página "Restrições de Acesso" no portal Azure, mostrando que não estão definidas restrições de acesso para o site SCM ou para a aplicação.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Gerir as regras de restrição de acesso programaticamente

Pode adicionar restrições de acesso programaticamente fazendo qualquer uma das seguintes: 

* Utilize [o Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Por exemplo:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Utilize [a Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Por exemplo:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

Também pode definir valores manualmente fazendo qualquer um dos seguintes:

* Utilize uma operação [Azure REST API](/rest/api/azure/) PUT na configuração da aplicação no Azure Resource Manager. A localização desta informação no Azure Resource Manager é:

  **management.azure.com/subscriptions/ subscrição ID** /resourceGroups/ **grupos de recursos** /fornecedores/Microsoft.Web/sites/ web app **name** /config/web?api-version=2018-02-01

* Utilize um modelo ARM. Como exemplo, pode utilizar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

  A sintaxe JSON para o exemplo anterior é:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Configurar restrições de acesso a funções Azure

As restrições de acesso também estão disponíveis para aplicações de função com a mesma funcionalidade que os planos do Serviço de Aplicações. Quando ativa as restrições de acesso, também desativa o editor de código do portal Azure para quaisquer IPs não permitidos.

## <a name="next-steps"></a>Passos seguintes
[Restrições de acesso para Funções Azure](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)  
[Integração de Gateway de Aplicação com pontos finais de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
