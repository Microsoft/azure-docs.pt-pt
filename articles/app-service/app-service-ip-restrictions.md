---
title: Restrições de acesso ao Serviço de Aplicações Azure
description: Saiba como proteger a sua aplicação no Azure App Service especificando restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6f1a94ae070419c38efb481e8f3967aec6a212d0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533959"
---
# <a name="azure-app-service-access-restrictions"></a>Restrições de acesso ao Serviço de Aplicações Azure

As restrições de acesso permitem-lhe definir uma lista de prioridades ordenada para permitir/negar que controla o acesso à rede à sua aplicação. A lista pode incluir endereços IP ou sub-redes de Rede Virtual Azure. Quando há uma ou mais entradas, há então um "negar tudo" implícito que existe no final da lista.

A capacidade de restrições de acesso funciona com todas as cargas de trabalho hospedadas do Serviço de Aplicações, incluindo; aplicativos web, aplicativos API, aplicativos Linux, aplicativos de contentores Linux e Funções.

Quando um pedido é feito para a sua aplicação, o endereço FROM é avaliado contra as regras de endereço IP na sua lista de restrições de acesso. Se o endereço FROM estiver numa sub-rede configurada com pontos finais de serviço para o Microsoft.Web, então a sub-rede de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não tiver acesso com base nas regras da lista, o serviço responde com um código de estado [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

A capacidade de restrições de acesso é implementada nas funções frontais do Serviço de Aplicações, que estão a montante dos anfitriões dos trabalhadores onde o seu código funciona. Portanto, as restrições de acesso são efetivamente ACLs de rede.

A capacidade de restringir o acesso à sua aplicação web a partir de uma Rede Virtual Azure (VNet) [chama-se pontos finais de serviço][serviceendpoints]. Os pontos finais de serviço permitem-lhe restringir o acesso a um serviço multi-inquilino a partir de sub-redes selecionadas. Deve ser ativado tanto no lado da rede como no serviço que está a ser ativado. Não funciona para restringir o tráfego a aplicações que estão hospedadas num Ambiente de Serviço de Aplicações. Se estiver num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação com regras de endereço IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso à sua aplicação, use o menu para abrir restrições de acesso à **rede** > **Access Restrictions** e clique em **Configurar Restrições** de Acesso

![Opções de networking do Serviço de Aplicações](media/app-service-ip-restrictions/access-restrictions.png)  

A partir do UI de Restrições de Acesso, pode rever a lista de regras de restrição de acesso definidas para a sua aplicação.

![Screenshot do ecrã de Restrições de Acesso no portal Azure mostrando a lista de regras de restrição de acesso definidas para a aplicação selecionada.](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão na sua aplicação. Se tiver uma restrição VNet na sua aplicação, a tabela mostrará se os pontos finais do serviço estão ativados para o Microsoft.Web. Quando não houver restrições definidas na sua aplicação, a sua aplicação estará acessível a partir de qualquer lugar.  

## <a name="adding-ip-address-rules"></a>Adicionar regras de endereço IP

Pode clicar na **regra [+] Adicionar regra** para adicionar uma nova regra de restrição de acesso. Uma vez que adicione uma regra, ela irá tornar-se eficaz imediatamente. As regras são aplicadas por ordem prioritária a partir do número mais baixo e subindo. Há uma negação implícita de tudo o que está em vigor uma vez que se adiciona uma única regra.

Ao criar uma regra, deve selecionar permitir/negar e também o tipo de regra. Também é obrigado a fornecer o valor prioritário e a que está a restringir o acesso.  Pode opcionalmente adicionar um nome e descrição à regra.  

![adicionar uma regra de restrição de acesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Para definir uma regra baseada em endereço IP, selecione um tipo de IPv4 ou IPv6. A notação do endereço IP deve ser especificada na notação CIDR tanto para endereços IPv4 como IPv6. Para especificar um endereço exato, pode utilizar algo como 1.2.3.4/32 onde os primeiros quatro octetos representam o seu endereço IP e /32 é a máscara. A notação CIDR IPv4 para todos os endereços é de 0.0.0.0/0. Para saber mais sobre a notação CIDR, pode ler [o Roteamento de Inter-Domain Classe.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

## <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais de serviço permitem restringir o acesso a sub-redes de rede virtuais Azure selecionadas. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo de Rede Virtual. Pode escolher a subscrição, VNet e sub-rede com a quais deseja permitir ou negar o acesso. Se os pontos finais de serviço ainda não estiverem ativados com o Microsoft.Web para a sub-rede que selecionou, será automaticamente ativado para si, a menos que verifique a caixa pedindo para não o fazer. A situação em que pretende ative na aplicação, mas não na sub-rede, está em grande parte relacionada se tiver as permissões para ativar os pontos finais do serviço na sub-rede ou não. Se precisar de arranjar outra pessoa para ativar os pontos finais do serviço na sub-rede, pode verificar a caixa e configurar a sua aplicação para os pontos finais de serviço, antecipando que esta seja ativada mais tarde na sub-rede. 

![adicionar uma regra de restrição de acesso VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Os pontos finais de serviço não podem ser utilizados para restringir o acesso a aplicações que funcionam num Ambiente de Serviço de Aplicações. Quando a sua aplicação está num Ambiente de Serviço de Aplicações, pode controlar o acesso à sua aplicação com regras de acesso IP. 

Com os pontos finais de serviço, pode configurar a sua aplicação com gateways de aplicações ou outros dispositivos WAF. Também pode configurar aplicações de vários níveis com backends seguros. Para mais detalhes sobre algumas das possibilidades, leia funcionalidades de [Networking e](networking-features.md) Integração do Serviço de Aplicações e [Gateway de Aplicações com pontos finais de serviço.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Os pontos finais de serviço atualmente não são suportados para aplicações web que usam IP virtual IP (VIP) IP.
> - Existe um limite de 512 linhas de restrições de IP ou de ponto final de serviço. Se necessitar de mais de 512 linhas de restrições, sugerimos que procure um produto de segurança autónomo, como a Porta Frontal Azure, o Gateway da Aplicação Azure ou uma Firewall de Aplicação Web (WAF).
>

## <a name="managing-access-restriction-rules"></a>Gerir regras de restrição de acesso

Pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições são efetivas imediatamente, incluindo alterações na encomenda prioritária.

![Screenshot do diálogo de restrição IP editar no portal Azure mostrando os campos para uma regra de restrição de acesso existente.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando edita uma regra, não é possível alterar o tipo entre uma regra de endereço IP e uma regra de Rede Virtual. 

![Screenshot do diálogo de restrição IP editar no portal Azure mostrando as definições de uma regra de Rede Virtual.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para eliminar uma regra, clique **na** sua regra e clique em **Remover** .

![eliminar regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloquear um único endereço IP ##

Ao adicionar a sua primeira regra de Restrição IP, o serviço adicionará uma regra de **Negar explicitamente toda a** regra com uma prioridade de 2147483647. Na prática, a regra de Negar explícito **será** a última regra executada e bloqueará o acesso a qualquer endereço IP que não seja explicitamente permitido usando uma regra **Desinclusão.**

Para o cenário em que os utilizadores pretendem bloquear explicitamente um único endereço IP ou um bloco de endereços IP, mas permitir o acesso de tudo o resto, é necessário adicionar uma regra de **Permitir Tudo** explícito.

![bloquear endereço ip único](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM site 

Além de conseguir controlar o acesso à sua app, também pode restringir o acesso ao site scm utilizado pela sua app. O site scm é o ponto final de implementação web e também a consola Kudu. Pode atribuir separadamente restrições de acesso ao site scm a partir da aplicação ou usar o mesmo conjunto tanto para a aplicação como para o site scm. Quando verifica a caixa para ter as mesmas restrições que a sua aplicação, tudo é apagado. Se desmarca a caixa, as definições que tinha anteriormente no site scm são aplicadas. 

![Screenshot do ecrã de Restrições de Acesso no portal Azure mostrando que não estão definidas restrições de acesso para o site scm ou para a aplicação.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática das regras de restrição de acesso ##

[A Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) e [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) tem suporte para editar restrições de acesso. Exemplo de adicionar uma restrição de acesso usando Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Exemplo de adicionar uma restrição de acesso utilizando a Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Os valores também podem ser definidos manualmente com uma operação [Azure REST API](/rest/api/azure/) PUT na configuração da aplicação no Gestor de Recursos ou utilizando um modelo de Gestor de Recursos Azure. Como exemplo, pode utilizar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

A localização desta informação no Gestor de Recursos é:

**management.azure.com/subscriptions/ subscrição ID** /resourceGroups/ **grupos de recursos** /fornecedores/Microsoft.Web/sites/ web app **name** /config/web?api-version=2018-02-01

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

## <a name="azure-functions-access-restrictions"></a>Restrições de acesso a funções Azure

As restrições de acesso também estão disponíveis para aplicações de função com a mesma funcionalidade que os planos do Serviço de Aplicações. Permitir restrições de acesso irá desativar o editor de código do portal para quaisquer IPs não permitidos.

## <a name="next-steps"></a>Passos seguintes
[Restrições de acesso para Funções Azure](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integração de Gateway de Aplicação com pontos finais de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
