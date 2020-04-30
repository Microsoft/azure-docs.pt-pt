---
title: Restrições de acesso ao Serviço de Aplicações Azure
description: Saiba como proteger a sua aplicação no Serviço de Aplicações Azure, especificando restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639276"
---
# <a name="azure-app-service-access-restrictions"></a>Restrições de acesso ao Serviço de Aplicações Azure

As restrições de acesso permitem definir uma lista de permitir/negar prioritáriamente que controla o acesso à rede à sua aplicação. A lista pode incluir endereços IP ou subredes azure rede virtual. Quando há uma ou mais entradas, há então um implícito "negar tudo" que existe no final da lista.

A capacidade de restrições de acesso funciona com todos os serviços de aplicação hospedados, incluindo; aplicações web, aplicativos API, aplicativos Linux, aplicações de contentores Linux e Funções.

Quando um pedido é feito na sua aplicação, o endereço FROM é avaliado contra as regras de endereço IP na sua lista de restrições de acesso. Se o endereço FROM estiver numa subnet configurada com pontos finais de serviço para microsoft.Web, então a subnet de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não for permitido o acesso com base nas regras da lista, o serviço responde com um código de estado [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

A capacidade de restrições de acesso é implementada nas funções front-end do Serviço de Aplicações, que são a montante dos anfitriões dos trabalhadores onde o seu código funciona. Por conseguinte, as restrições de acesso são efetivamente ACLs de rede.

A capacidade de restringir o acesso à sua aplicação web a partir de uma Rede Virtual Azure (VNet) chama-se [pontos finais][serviceendpoints]de serviço . Os pontos finais do serviço permitem-lhe restringir o acesso a um serviço multi-inquilino a partir de subredes selecionadas. Deve ser ativado tanto no lado da rede como no serviço com o que está a ser ativado. Não funciona para restringir o tráfego a apps que estão hospedadas num Ambiente de Serviço de Aplicações. Se estiver num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação com regras de endereço IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso à sua aplicação, use o menu para abrir**restrições** de acesso à **rede**>e clique em Restrições de **Acesso Configuradas**

![Opções de rede de serviço sinuoso do serviço de aplicações](media/app-service-ip-restrictions/access-restrictions.png)  

A partir do UI de Restrições de Acesso, pode rever a lista de regras de restrição de acesso definidas para a sua aplicação.

![lista de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão na sua aplicação. Se tiver uma restrição VNet na sua aplicação, a tabela mostrará se os pontos finais do serviço estão ativados para microsoft.Web. Quando não houver restrições definidas na sua aplicação, a sua aplicação estará acessível a partir de qualquer lugar.  

## <a name="adding-ip-address-rules"></a>Adicionar regras de endereço IP

Pode clicar em **[+] Adicionar regra** para adicionar uma nova regra de restrição de acesso. Uma vez adicionado uma regra, ela vai tornar-se eficaz imediatamente. As regras são aplicadas por ordem prioritária, a partir do número mais baixo e a subir. Há uma negação implícita de tudo o que está em vigor quando se adiciona uma única regra.

Ao criar uma regra, deve selecionar permitir/negar e também o tipo de regra. Também é obrigado a fornecer o valor prioritário e aquilo a que está a restringir o acesso.  Pode, opcionalmente, adicionar um nome e descrição opcionalmente à regra.  

![adicionar uma regra de restrição de acesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Para definir uma regra baseada em endereçoIP, selecione um tipo de IPv4 ou IPv6. A notação do endereço IP deve ser especificada na notação CIDR para os endereços IPv4 e IPv6. Para especificar um endereço exato, pode utilizar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o seu endereço IP e /32 é a máscara. A notação cidr IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação do CIDR, pode ler O [Encaminhamento Inter-Domínio Sem Classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais do serviço permitem-lhe restringir o acesso a subredes de rede virtual Azure selecionadas. Para restringir o acesso a uma subnetespecífica específica, crie uma regra de restrição com um tipo de Rede Virtual. Pode escolher a subscrição, VNet e subnet que deseja permitir ou negar o acesso. Se os pontos finais do serviço ainda não estiverem ativados com a Microsoft.Web para a sub-rede que selecionou, será automaticamente ativado para si, a menos que verifique a caixa pedindo para não o fazer. A situação em que o desejaria ativar na aplicação, mas não a subnet, está em grande parte relacionada com se tiver permissões para ativar pontos finais de serviço na subnet ou não. Se precisar de alguém para ativar os pontos finais do serviço na subnet, pode verificar a caixa e configurar a sua aplicação para pontos finais de serviço, antecipando que seja ativada mais tarde na subnet. 

![adicionar uma regra de restrição de acesso VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Os pontos finais do serviço não podem ser utilizados para restringir o acesso a apps que funcionam num Ambiente de Serviço de Aplicações. Quando a sua aplicação estiver num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação com regras de acesso IP. 

Com pontos finais de serviço, pode configurar a sua aplicação com Gateways de Aplicação ou outros dispositivos WAF. Também pode configurar aplicações de vários níveis com backends seguros. Para mais detalhes sobre algumas das possibilidades, leia [as funcionalidades de Networking e o Serviço](networking-features.md) de Aplicações e a [integração do Gateway de Aplicações com pontos finais](networking/app-gateway-with-service-endpoints.md)de serviço.

## <a name="managing-access-restriction-rules"></a>Gestão das regras de restrição de acesso

Pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edificações estão em vigor imediatamente, incluindo alterações na ordem de encomendas prioritárias.

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Ao editar uma regra, não pode alterar o tipo entre uma regra de endereço IP e uma regra de Rede Virtual. 

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para eliminar uma regra, clique no **...** na sua regra e, em seguida, clique em **Remover**.

![eliminar a regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloquear um único endereço IP ##

Ao adicionar a sua primeira regra de restrição IP, o serviço adicionará uma regra **de Negação** explícita com uma prioridade de 2147483647. Na prática, a regra **explícita de Negação** será executada na última regra e bloqueará o acesso a qualquer endereço IP que não seja explicitamente permitido usando uma regra de **permitir.**

Para o cenário em que os utilizadores pretendam bloquear explicitamente um único endereço IP ou bloco de endereçoIP, mas permitir o acesso de tudo o resto, é necessário adicionar uma regra de **permitir todas** explícitas.

![bloquear endereço ip único](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Site SCM 

Além de poder controlar o acesso à sua aplicação, também pode restringir o acesso ao site SCM utilizado pela sua aplicação. O site scm é o ponto final de implantação da web e também a consola Kudu. Pode atribuir separadamente restrições de acesso ao site scm da aplicação ou utilizar o mesmo conjunto tanto para a app como para o site scm. Quando verifica a caixa para ter as mesmas restrições que a sua aplicação, tudo é apagado. Se desverificar a caixa, são aplicadas as definições que tiver anteriormente no site scm. 

![lista de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática das regras de restrição de acesso ##

[A Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) e a [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) têm suporte para a edição de restrições de acesso. Exemplo de adição de uma restrição de acesso utilizando o Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Exemplo de adicionar uma restrição de acesso utilizando o Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Os valores também podem ser definidos manualmente com uma operação [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT na configuração da aplicação em Resource Manager ou utilizando um modelo de Gestor de Recursos Azure. Como exemplo, pode utilizar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

A localização desta informação no Gestor de Recursos é:

management.azure.com/subscriptions/ ID /recursosDedados/grupos de**recursos/fornecedores/Microsoft.Web/sites/****web app name**/config/web?api-version=2018-02-01**subscription ID**

A sintaxe jSON para o exemplo anterior é:
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

## <a name="azure-functions-access-restrictions"></a>Restrições de acesso às Funções Azure

As restrições de acesso também estão disponíveis para aplicações de funções com a mesma funcionalidade que os planos do App Service. Permitir restrições de acesso irá desativar o editor de código do portal para quaisquer IPs não autorizados.

## <a name="next-steps"></a>Passos seguintes
[Restrições de acesso às Funções Azure](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integração de Gateway de aplicação com pontos finais de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
