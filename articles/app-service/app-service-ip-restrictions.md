---
title: Restrições de acesso ao Serviço de Aplicações Azure
description: Saiba como proteger a sua aplicação no Azure App Service, configurando restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fea189952b1452c680255ceb99e38609775a8bd6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502693"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurar restrições de acesso ao Serviço de Aplicações Azure

Ao configurar restrições de acesso, pode definir uma lista de autorizações/negação de prioridades que controla o acesso à rede à sua aplicação. A lista pode incluir endereços IP ou sub-redes de Rede Virtual Azure. Quando há uma ou mais entradas, uma *negação* implícita tudo existe no final da lista.

A capacidade de restrição de acesso funciona com todas as cargas de trabalho hospedadas pelo Azure App Service. As cargas de trabalho podem incluir aplicações web, aplicações API, aplicações Linux, aplicações de contentores Linux e Funções.

Quando um pedido é feito à sua aplicação, o endereço FROM é avaliado contra as regras da sua lista de restrições de acesso. Se o endereço FROM estiver numa sub-rede configurada com pontos finais de serviço para o Microsoft.Web, a sub-rede de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não tiver acesso com base nas regras da lista, o serviço responde com um código de estado [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

A capacidade de restrição de acesso é implementada nas funções frontais do Serviço de Aplicações, que estão a montante dos anfitriões dos trabalhadores onde o seu código funciona. Portanto, as restrições de acesso são efetivamente listas de controlo de acesso à rede (ACLs).

A capacidade de restringir o acesso à sua aplicação web a partir de uma rede virtual Azure é ativada por [pontos finais de serviço.][serviceendpoints] Com pontos finais de serviço, você pode restringir o acesso a um serviço multi-inquilino a partir de sub-redes selecionadas. Não funciona para restringir o tráfego a aplicações que estão hospedadas num Ambiente de Serviço de Aplicações. Se estiver num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação aplicando regras de endereço IP.

> [!NOTE]
> Os pontos finais de serviço devem ser ativados tanto no lado da rede como para o serviço Azure com o qual estão a ser ativados. Para obter uma lista de serviços Azure que suportam pontos finais de serviço, consulte [os pontos finais do serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagrama do fluxo de restrições de acesso.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Gerir regras de restrição de acesso no portal

Para adicionar uma regra de restrição de acesso à sua aplicação, faça o seguinte:

1. Inicie sessão no Portal do Azure.

1. No painel esquerdo, selecione **Networking**.

1. No painel **de rede,** sob **restrições de acesso,** selecione **Restrições de Acesso Configure**.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Screenshot do painel de opções de rede do Serviço de Aplicações no portal Azure.":::

1. Na página **'Restrições de Acesso',** reveja a lista de regras de restrição de acesso que são definidas para a sua aplicação.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Screenshot da página 'Restrições de Acesso' no portal Azure, mostrando a lista de regras de restrição de acesso definidas para a aplicação selecionada.":::

   A lista apresenta todas as restrições atuais que são aplicadas à aplicação. Se tiver uma restrição de rede virtual na sua aplicação, a tabela mostra se os pontos finais do serviço estão ativados para o Microsoft.Web. Se não forem definidas restrições na sua aplicação, a aplicação está acessível a partir de qualquer lugar.

### <a name="add-an-access-restriction-rule"></a>Adicionar uma regra de restrição de acesso

Para adicionar uma regra de restrição de acesso à sua aplicação, no painel **de restrições** de acesso, selecione **'Adicionar regra'.** Depois de adicionar uma regra, torna-se eficaz imediatamente. 

As regras são aplicadas por ordem prioritária, a partir do número mais baixo da coluna **Prioridade.** Uma *negação* implícita tudo está em vigor depois de adicionar uma única regra.

No painel **de restrição de acesso** de adicionar, quando criar uma regra, faça o seguinte:

1. Em **Ação**, selecione **Ou permitir** ou **negar**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Screenshot do painel 'Adicionar restrição de acesso'.":::

1. Opcionalmente, insira um nome e descrição da regra.
1. Na caixa **Prioridade,** insira um valor prioritário.
1. Na lista de drop-down **tipo,** selecione o tipo de regra.

Os diferentes tipos de regras são descritos nas seguintes secções.

> [!NOTE]
> - Existe um limite de 512 regras de restrição de acesso. Se necessitar de mais de 512 regras de restrição de acesso, sugerimos que considere instalar um produto de segurança autónomo, como a Azure Front Door, Azure App Gateway ou uma WAF alternativa.
>
#### <a name="set-an-ip-address-based-rule"></a>Definir uma regra baseada em endereços IP

Siga o procedimento descrito na secção anterior, mas com o seguinte aditamento:
* Para o passo 4, na lista de drop-down **tipo,** selecione **IPv4** ou **IPv6**. 

Especifique o **Bloco de Endereços IP** em notação de encaminhamento de Inter-Domain (CIDR) sem classe para os endereços IPv4 e IPv6. Para especificar um endereço, pode utilizar algo como *1.2.3.4/32*, onde os primeiros quatro octetos representam o seu endereço IP e */32* é a máscara. A notação CIDR IPv4 para todos os endereços é de 0.0.0.0/0. Para saber mais sobre a notação CIDR, consulte [o Roteamento de Inter-Domain Sem Classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Definir uma regra baseada no ponto final de serviço

* Para o passo 4, na lista de drop-down **tipo,** selecione **Virtual Network**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Screenshot do painel 'Adicionar Restrição' com o tipo de Rede Virtual selecionado.":::

Especificar as listas de **subscrição,** **Rede Virtual** e **Sub-rede,** correspondendo ao que pretende restringir o acesso.

Ao utilizar pontos finais de serviço, pode restringir o acesso a sub-redes de rede virtuais Azure selecionadas. Se os pontos finais de serviço ainda não estiverem ativados com o Microsoft.Web para a sub-rede que selecionou, serão automaticamente ativados, a menos que selecione a caixa de verificação **de pontos finais do serviço Microsoft.Web em falta.** O cenário em que poderá querer ativar os pontos finais do serviço na app, mas não a sub-rede, depende principalmente de ter as permissões para os ativar na sub-rede. 

Se precisar de outra pessoa para ativar os pontos finais do serviço na sub-rede, selecione a caixa de **verificação de pontos finais do serviço Microsoft.Web em** falta. A sua aplicação será configurada para os pontos finais de serviço, antecipando-se a sua ativação posterior na sub-rede. 

Não é possível utilizar pontos finais de serviço para restringir o acesso a aplicações que funcionam num Ambiente de Serviço de Aplicações. Quando a sua aplicação está num Ambiente de Serviço de Aplicações, pode controlar o acesso à ela aplicando regras de acesso IP. 

Com os pontos finais de serviço, pode configurar a sua aplicação com gateways de aplicações ou outros dispositivos de firewall de aplicações web (WAF). Também pode configurar aplicações de vários níveis com extremidades traseiras seguras. Para obter mais informações, consulte [funcionalidades de Networking e integração do Serviço](networking-features.md) de Aplicações e [Gateway de Aplicações com pontos finais de serviço.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Os pontos finais de serviço não são suportados atualmente para aplicações web que usam IP IP (SSL) virtual Layer (SSL) virtual (SSL).
>
#### <a name="set-a-service-tag-based-rule-preview"></a>Desa parte de uma regra baseada em etiquetas de serviço (pré-visualização)

* Para o passo 4, na lista de drop-down **tipo,** selecione Tag de **Serviço (pré-visualização)**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png" alt-text="Screenshot do painel 'Adicionar Restrição' com o tipo de etiqueta de serviço selecionado.":::

Cada etiqueta de serviço representa uma lista de gamas IP dos serviços Azure. Uma lista destes serviços e ligações às gamas específicas pode ser encontrada na documentação da [etiqueta de serviço.][servicetags]

A seguinte lista de etiquetas de serviço é suportada nas regras de restrição de acesso durante a fase de pré-visualização:
* Grupo de Ação
* AzureCloud
* AzureCognitiveSearch
* AzureConnectors
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* Azuresignalr
* AzureTrafficManager
* LogicApps
* ServiceFabric

### <a name="edit-a-rule"></a>Editar uma regra

1. Para começar a editar uma regra de restrição de acesso existente, na página **'Restrições de Acesso',** selecione a regra que pretende editar.

1. No painel **de restrição de acesso de edição,** faça as alterações e, em seguida, selecione **a regra de Atualização**. As edições são eficazes imediatamente, incluindo alterações na encomenda prioritária.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Screenshot do painel 'Editar Restrição de Acesso' no portal Azure, mostrando os campos para uma regra de restrição de acesso existente.":::

   > [!NOTE]
   > Quando se edita uma regra, não se pode alternar entre tipos de regras. 

### <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar uma regra, na página **'Restrições de Acesso',** selecione a elipse **(...**) ao lado da regra que pretende eliminar e, em seguida, selecione **Remover**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Screenshot da página 'Restrições de acesso', mostrando a elipse 'Remover' ao lado da regra de restrição de acesso a ser eliminada.":::

## <a name="access-restriction-advanced-scenarios"></a>Restrição de acesso cenários avançados
As secções seguintes descrevem alguns cenários avançados usando restrições de acesso.
### <a name="block-a-single-ip-address"></a>Bloqueie um único endereço IP

Quando adiciona a sua primeira regra de restrição de acesso, o serviço adiciona uma regra de *Negar tudo* com uma prioridade de 2147483647. Na prática, a regra de Negar explícito *é* a regra final a ser executada, e bloqueia o acesso a qualquer endereço IP que não seja explicitamente permitido por uma regra *de Permitir.*

Para um cenário em que pretende bloquear explicitamente um único endereço IP ou um bloco de endereços IP, mas permitir o acesso a tudo o resto, adicione uma regra *de Permitir Tudo* explícito.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Screenshot da página 'Restrições de acesso' no portal Azure, mostrando um único endereço IP bloqueado.":::

### <a name="restrict-access-to-an-scm-site"></a>Restringir o acesso a um site SCM 

Além de conseguir controlar o acesso à sua app, pode restringir o acesso ao site SCM que é utilizado pela sua app. O site SCM é tanto o ponto final de implementação web como a consola Kudu. Pode atribuir restrições de acesso ao site SCM separadamente ou utilizar o mesmo conjunto de restrições tanto para a aplicação como para o site SCM. Quando seleciona as **mesmas restrições \<app name> que** a caixa de verificação, tudo é apagado. Se limpar a caixa de verificação, as definições do seu site SCM são reaplicadas. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Screenshot da página 'Restrições de Acesso' no portal Azure, mostrando que não estão definidas restrições de acesso para o site SCM ou para a aplicação.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance-preview"></a>Restringir o acesso a uma instância específica da Porta Frontal Azure (pré-visualização)
O tráfego da Porta Frontal Azure para a sua aplicação tem origem num conhecido conjunto de gamas IP definidas na etiqueta de serviço AzureFrontDoor.Backend. Utilizando uma regra de restrição de etiqueta de serviço, pode restringir o tráfego apenas a partir da Porta frontal Azure. Para garantir que o tráfego só tem origem na sua instância específica, terá de filtrar ainda mais os pedidos de entrada com base no único cabeçalho http que a Porta Frontal Azure envia. Durante a pré-visualização pode conseguir isto com PowerShell ou REST/ARM. 

* Exemplo PowerShell (ID da porta frontal pode ser encontrado no portal Azure):

   ```azurepowershell-interactive
    $frontdoorId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
      -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
      -HttpHeader @{'x-azure-fdid' = $frontdoorId}
    ```
## <a name="manage-access-restriction-rules-programmatically"></a>Gerir regras de restrição de acesso programáticamente

Pode adicionar restrições de acesso programáticamente fazendo qualquer uma das seguintes: 

* Utilize [o Azure CLI](/cli/azure/webapp/config/access-restriction). Por exemplo:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Utilize [a Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Por exemplo:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Trabalhar com etiquetas de serviço, cabeçalhos http ou regras multi-fontes requer pelo menos a versão 5.1.0. Pode verificar a versão do módulo instalado com: **Get-InstalledModule -Name Az**

Também pode definir valores manualmente fazendo qualquer um dos seguintes:

* Utilize uma operação [Azure REST API](/rest/api/azure/) PUT na configuração da aplicação no Azure Resource Manager. A localização desta informação no Azure Resource Manager é:

  **management.azure.com/subscriptions/ subscrição ID**/resourceGroups/**grupos de recursos**/fornecedores/Microsoft.Web/sites/ web app **name**/config/web?api-version=2020-06-01

* Utilize um modelo de Gestor de Recursos. Como exemplo, pode utilizar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

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
  A sintaxe JSON para um exemplo avançado usando a etiqueta de serviço e a restrição de cabeçalho http é:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Configurar restrições de acesso a funções Azure

As restrições de acesso também estão disponíveis para aplicações de função com a mesma funcionalidade que os planos do Serviço de Aplicações. Quando ativa as restrições de acesso, também desativa o editor de código do portal Azure para quaisquer IPs não permitidos.

## <a name="next-steps"></a>Passos seguintes
[Restrições de acesso para Funções Azure](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integração de Gateway de Aplicação com pontos finais de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md