---
title: Escala rapidamente e proteja uma aplicação web utilizando a Porta Frontal Azure e a Firewall de Aplicação Web Azure (WAF) [ Microsoft Docs
description: Este artigo ajuda-o a entender como usar firewall de aplicação web com o seu Serviço de Porta Frontal AAzure
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 3c0b23bc7c25c56953d267e5193a63fe08e35f64
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664845"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Escala rapidamente e proteja uma aplicação web utilizando a Porta Frontal Azure e a Firewall de Aplicação Web Azure (WAF)

Muitas aplicações web têm sofrido um rápido aumento de tráfego nas últimas semanas relacionadacom covid-19. Além disso, estas aplicações web também estão a observar um aumento no tráfego malicioso, incluindo a negação de ataques de serviço. Uma forma eficaz de lidar com estas duas necessidades, escalar para picos de tráfego e proteger contra ataques, é configurar a Porta Frontal Azure com azure WAF como uma camada de aceleração, cache e segurança em frente à sua aplicação web. Este artigo fornece orientações sobre como obter rapidamente esta Porta Frontal Azure com a configuração Azure WAF para quaisquer aplicações web que executem dentro ou fora de Azure. 

Vamos usar o Azure CLI para configurar o WAF neste tutorial, mas todos estes passos também são totalmente suportados no portal Azure, Azure PowerShell, Azure ARM e Azure REST APIs. 

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

As instruções deste blog utilizam a Interface da Linha de Comando Azure (CLI). Consulte este guia para [começar com o Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Dica: uma maneira fácil & rápida de começar no Azure CLI é com [Bash em Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Certifique-se de que a extensão da porta da frente é adicionada ao seu Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

Nota: Para mais detalhes sobre os comandos listados abaixo, consulte a [referência Azure CLI para porta da frente](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Passo 1: Criar um recurso azure front door (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: O endereço de backend é o nome de domínio totalmente qualificado (FQDN) da aplicação que pretende proteger. Por exemplo, myapplication.contoso.com

**--protocolos aceites**: Os protocolos aceites especificam quais os protocolos que pretende que a AFD suporte para a sua aplicação web. Um exemplo seriam os protocolos aceites http https.

**-nome**: Especificar um nome para o seu recurso AFD

**--grupo de recursos**: O grupo de recursos em que pretende colocar este recurso AFD.  Para saber mais sobre grupos de recursos, visite gerir grupos de recursos em Azure

Na resposta que obtém da execução com sucesso deste comando, procure a chave "hostName" e note o seu valor para ser usado num passo posterior. O nome anfitrião é o nome DNS do recurso AFD que tinha criado

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Passo 2: Criar um perfil Azure WAF para utilizar com recursos da Porta Frontal Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

-nome Especifique um nome para a sua política Azure WAF

-grupo de recursos O grupo de recursos em que pretende colocar este recurso WAF. 

O código CLI acima criará uma política WAF que está ativada e está no modo de Prevenção. 

Nota: também pode querer criar o WAF no modo deteção e observar como está a detetar & a registar pedidos maliciosos (e não a bloquear) antes de decidir mudar para o modo de Proteção.

Na resposta que obtém da execução com sucesso deste comando, procure a chave "ID" e note o seu valor para ser usado num passo posterior. O campo de identificação deve estar no formato

/subscrições/**id de subscrição**/grupos de recursos/**nome de grupo de recursos**/fornecedores/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**waf nome** de política

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Passo 3: Adicionar conjuntos de regras geridos a esta política waf

Numa política waf, você pode adicionar conjuntos de regras geridos que são um conjunto de regras construídas e geridas pela Microsoft e dá fora da proteção de caixa contra classes inteiras de ameaças. Neste exemplo, estamos adicionando dois conjuntos de regras (1) Regras padrão que protegem contra ameaças comuns da web e (2) regras de proteção bot, que protege contra bots maliciosos

(1) Adicione o conjunto de regras por defeito

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Adicione o conjunto de regras do gestor de bots

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

-nome da política O nome que deu para o seu recurso Azure WAF

-grupo de recursos O grupo de recursos em que colocou este recurso WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Passo 4: Associar a política waf com o recurso AFD

Neste passo, vamos associar a política waf que construímos com o recurso AFD que está em frente à sua aplicação web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

-nome O nome que tinha especificado para o seu recurso AFD

-grupo de recursos O grupo de recursos em que colocou o recurso Azure Front Door.

-set Este é o lugar onde atualiza o atributo WebApplicationFirewallPolicyLink para o frontendEndpoint associado ao seu recurso AFD com a política WAF recém-construída. A identificação da política da WAF pode ser encontrada a partir da resposta que obteve do passo #2 acima

Nota: o exemplo acima é para o caso em que não está a usar um domínio personalizado, se estiver

Se não estiver a utilizar domínios personalizados para aceder às suas aplicações web, pode saltar passo #5. Nesse caso, irá fornecer aos seus utilizadores finais o nome de anfitrião que obteve em passo #1 navegar para a sua aplicação web

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Passo 5: Configure o domínio personalizado para a sua aplicação web

Inicialmente, o nome de domínio personalizado da sua aplicação web (aquele que os clientes usam para se referir à sua aplicação, por exemplo, www.contoso.com) apontava para o local onde o tinha a funcionar antes da introdução da AFD. Após esta mudança de arquitetura adicionando AFD+WAF para fazer frente à aplicação, a entrada de DNS correspondente a esse domínio personalizado deve agora apontar para este recurso AFD. Isto pode ser feito remapeando esta entrada no seu servidor DNS para o nome de anfitrião AFD que você tinha notado em passo #1.

As etapas específicas para atualizar os seus registos DNS dependerão do seu fornecedor de serviços DNS, mas se estiver a utilizar o Azure DNS para hospedar o seu nome DNS, pode consultar a documentação para [os passos atualizarem um registo DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e apontarem para o nome de anfitrião AFD. 

Uma coisa importante a ter em conta aqui é que, se precisar que os seus utilizadores naveguem para o seu website usando o ápice da zona, para exmaple, contoso.com, você tem que usar O DNS Azure e é o tipo de [registo ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) para hospedar o seu nome DNS. 

Além disso, também precisa de atualizar a sua configuração AFD para [adicionar este domínio personalizado](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) a ele para que a AFD compreenda este mapeamento.

Finalmente, se estiver a utilizar um domínio personalizado para chegar à sua aplicação web e pretender ativar o protocolo HTTPS, precisa de ter os certificados para a configuração do [seu domínio personalizado em AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Passo 6: Bloqueie a sua aplicação web

Uma das melhores práticas opcionais a seguir é garantir que apenas as arestas AFD podem comunicar com a sua aplicação web. Esta ação garantirá que ninguém pode contornar as proteções DaFD e aceder diretamente às suas aplicações. Você pode realizar este bloqueio visitando a [seção de FAQ da AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) e referindo-se à questão relativa ao bloqueio de backends para acesso apenas pela AFD.
