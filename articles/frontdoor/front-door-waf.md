---
title: 'Tutorial: Escalar e proteger uma aplicação web utilizando a Porta Frontal Azure e a Firewall de Aplicação Web Azure (WAF)'
description: Este tutorial irá mostrar-lhe como utilizar o Azure Web Application Firewall com o serviço Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: f894fd5ca7bb98c48b106c13b8fe6130b2c76c6e
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201061"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Tutorial: Dimensione rapidamente e proteja uma aplicação web utilizando a Porta Frontal Azure e a Firewall de Aplicação Web Azure (WAF)

Muitas aplicações web têm sofrido um rápido aumento de tráfego nas últimas semanas por causa do COVID-19. Estas aplicações web também estão a sofrer um aumento no tráfego malicioso, incluindo ataques de negação de serviço. Existe uma forma eficaz de escalar a sua aplicação para picos de tráfego e proteger-se de ataques: configurar a Porta frontal Azure com Azure WAF como uma camada de aceleração, caching e segurança em frente à sua aplicação web. Este artigo fornece orientações sobre como obter Azure Front Door com Azure WAF configurado para qualquer aplicação web que corre dentro ou fora de Azure. 

Vamos usar o Azure CLI para configurar a WAF neste tutorial. Pode realizar a mesma coisa utilizando o portal Azure PowerShell, Azure Resource Manager ou as APIs Azure REST. 

Neste tutorial, irá aprender a:
> [!div class="checklist"]
> - Criar uma porta da frente.
> - Crie uma política Azure WAF.
> - Configurar as regras para uma política da WAF.
> - Associe uma política da WAF à Porta da Frente.
> - Configure um domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- As instruções deste tutorial utilizam o Azure CLI. [Veja este guia](/cli/azure/get-started-with-azure-cli) para começar com o Azure CLI.

  > [!TIP] 
  > Uma maneira fácil e rápida de começar no Azure CLI é com [Bash in Azure Cloud Shell](../cloud-shell/quickstart.md).

- Certifique-se de que a `front-door` extensão é adicionada ao Azure CLI:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Para obter mais informações sobre os comandos utilizados neste tutorial, consulte [a referência Azure CLI para porta frontal.](/cli/azure/ext/front-door)

## <a name="create-an-azure-front-door-resource"></a>Criar um recurso Azure Front Door

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: O nome de domínio totalmente qualificado (FQDN) da aplicação que pretende proteger. Por exemplo, `myapplication.contoso.com`.

`--accepted-protocols`: Especifica os protocolos que pretende que a Porta Frontal Azure suporte para a sua aplicação web. Por exemplo, `--accepted-protocols Http Https`.

`--name`: O nome do seu recurso Azure Front Door.

`--resource-group`: O grupo de recursos em que pretende colocar este recurso Azure Front Door. Para saber mais sobre grupos de recursos, consulte [Gerir grupos de recursos em Azure.](../azure-resource-manager/management/manage-resource-groups-portal.md)

Na resposta que obtém quando dirige este comando, procure a `hostName` chave. Vai precisar deste valor num passo posterior. É `hostName` o nome DNS do recurso Azure Front Door que criou.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Crie um perfil Azure WAF para usar com recursos da Porta Frontal Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: O nome da nova política da Azure WAF.

`--resource-group`: O grupo de recursos em que pretende colocar este recurso WAF. 

O código CLI anterior criará uma política waf que está ativada e que está em modo de prevenção. 

> [!NOTE] 
> É melhor criar a política DAA no modo de deteção e observar como deteta e regista pedidos maliciosos (sem os bloquear) antes de decidir utilizar o modo de proteção.

Na resposta que obtém quando dirige este comando, procure a `ID` chave. Vai precisar deste valor num passo posterior. 

O `ID` campo deve estar neste formato:

/subscrições/**subscrição id**/grupos de recursos/**nome do grupo de recursos**/fornecedores/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/ WAF policy **name**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Adicione conjuntos de regras geridos à política da WAF

Pode adicionar conjuntos de regras geridos a uma política da WAF. Um conjunto de regras geridas é um conjunto de regras construídas e geridas pela Microsoft que ajudam a protegê-lo contra uma classe de ameaças. Neste exemplo, estamos a adicionar dois conjuntos de regras:
- O conjunto de regras padrão, que ajuda a protegê-lo contra ameaças comuns da web. 
- O conjunto de regras de proteção de bots, que ajuda a protegê-lo contra bots maliciosos.

Adicione o conjunto de regras predefinidos:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Adicione o conjunto de regras de proteção do bot:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: O nome especificado para o seu recurso Azure WAF.

`--resource-group`: O grupo de recursos em que colocou o recurso WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Associe a política da WAF ao recurso Azure Front Door

Neste passo, associaremos a política da WAF que criámos com o recurso Azure Front Door que está em frente à sua aplicação web:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: O nome especificado para o seu recurso Azure Front Door.

`--resource-group`: O grupo de recursos em que colocou o recurso Azure Front Door.

`--set`: É onde atualiza o atributo para o associado `WebApplicationFirewallPolicyLink` com o seu recurso `frontendEndpoint` Azure Front Door com a nova política WAF. Você deve ter o ID da política da WAF a partir da resposta que obteve quando criou o perfil WAF mais cedo neste tutorial.

 > [!NOTE] 
> O exemplo anterior é aplicável quando não está a utilizar um domínio personalizado. Se não estiver a utilizar quaisquer domínios personalizados para aceder às suas aplicações web, pode saltar a secção seguinte. Nesse caso, dará aos seus clientes o `hostName` que obteve quando criou o recurso Azure Front Door. Vão usar isto `hostName` para ir à tua aplicação web.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Configure o domínio personalizado para a sua aplicação web

O nome de domínio personalizado da sua aplicação web é aquele que os clientes usam para se referir à sua aplicação. Por exemplo, www.contoso.com. Inicialmente, este nome de domínio personalizado apontava para o local onde estava a funcionar antes de introduzir a Porta frontal Azure. Depois de adicionar Azure Front Door e WAF para fazer frente à aplicação, a entrada DNS que corresponde a esse domínio personalizado deve apontar para o recurso Azure Front Door. Pode efeitá-la remapping a entrada no seu servidor DNS para a Porta frontal Azure `hostName` que notou quando criou o recurso Azure Front Door.

Os passos específicos para atualizar os seus registos DNS dependerão do seu prestador de serviços DNS. Se utilizar o Azure DNS para hospedar o seu nome DNS, pode consultar a documentação para [os passos para atualizar um registo DNS](../dns/dns-operations-recordsets-cli.md) e apontar para a Porta frontal do Azure `hostName` . 

Há uma coisa importante a notar se precisa que os seus clientes entrem no seu website usando o ápice da zona (por exemplo, contoso.com). Neste caso, tem de utilizar o Azure DNS e o [seu tipo de gravação de pseudónimo para](../dns/dns-alias.md) hospedar o seu nome DNS. 

Também precisa de atualizar a configuração da porta frontal Azure para [adicionar o domínio personalizado](./front-door-custom-domain.md) ao mesmo para que esteja ciente deste mapeamento.

Finalmente, se estiver a utilizar um domínio personalizado para chegar à sua aplicação web e quiser ativar o protocolo HTTPS. É necessário [configurar os certificados para o seu domínio personalizado na Porta frontal Azure](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Bloqueie a sua aplicação web

Recomendamos que garanta que apenas as bordas da porta dianteira Azure podem comunicar com a sua aplicação web. Ao fazê-lo, ninguém poderá contornar a proteção da Porta Frontal Azure e aceder diretamente à sua aplicação. Para conseguir este bloqueio, como [posso bloquear o acesso ao meu backend para apenas Azure Front Door?](./front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não necessitar dos recursos utilizados neste tutorial, utilize o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos, porta frontal e política da WAF:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: O nome do grupo de recursos para todos os recursos utilizados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Para aprender a resolver problemas na porta da frente, consulte os guias de resolução de problemas:

> [!div class="nextstepaction"]
> [Resolução de problemas problemas comuns de encaminhamento](front-door-troubleshoot-routing.md)