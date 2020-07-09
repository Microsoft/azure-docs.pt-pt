---
title: Dimensione e proteja rapidamente uma aplicação web usando Azure Front Door e Azure Web Application Firewall (WAF) Microsoft Docs
description: Este artigo ajuda-o a entender como usar firewall de aplicação web com o seu serviço de porta frontal AAzure
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 6f91a98372aa85a52a6013a121235ca354004a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743546"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Dimensione rapidamente e proteja uma aplicação web usando Azure Front Door e Azure Web Application Firewall (WAF)

Muitas aplicações web têm experimentado um rápido aumento de tráfego nas últimas semanas relacionado com COVID-19. Além disso, estas aplicações web também estão a observar um aumento no tráfego malicioso, incluindo a negação de ataques de serviço. Uma forma eficaz de lidar com ambas as necessidades, reduzir para os picos de tráfego e proteger de ataques, é configurar a Porta Frontal Azure com a Azure WAF como uma camada de aceleração, caching e segurança em frente à sua aplicação web. Este artigo fornece orientações sobre como obter rapidamente esta Azure Front Door com configuração Azure WAF para quaisquer aplicações web em execução dentro ou fora de Azure. 

Vamos usar o Azure CLI para configurar o WAF neste tutorial, mas todos estes passos também são totalmente suportados no portal Azure PowerShell, Azure ARM e Azure REST APIs. 

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

As instruções neste blog utilizam a Interface da Linha de Comando Azure (CLI). Consulte este guia para [começar com o Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Dica: uma maneira fácil & rápida de começar no Azure CLI é com [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Certifique-se de que a extensão da porta da frente é adicionada ao seu Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

Nota: Para mais detalhes sobre os comandos listados abaixo, consulte a [referência Azure CLI para porta frontal](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Passo 1: Criar um recurso Azure Front Door (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: O endereço de backend é o nome de domínio totalmente qualificado (FQDN) da aplicação que pretende proteger. Por exemplo, myapplication.contoso.com

**--protocolos aceites**: Os protocolos aceites especificam todos os protocolos que pretende que a AFD suporte para a sua aplicação web. Um exemplo seria -protocolos aceites Http Https.

**--nome**: Especificar um nome para o seu recurso AFD

**--grupo de recursos**: O grupo de recursos em que pretende colocar este recurso AFD.  Para saber mais sobre grupos de recursos, visite gerir grupos de recursos em Azure

Na resposta que obtém da execução com sucesso deste comando, procure a chave "HostName" e note o seu valor a ser usado num passo posterior. O nome anfitrião é o nome DNS do recurso AFD que criou

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Passo 2: Criar um perfil Azure WAF para usar com recursos da Porta Frontal Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--nome Especifique um nome para a sua política Azure WAF

-- grupo de recursos O grupo de recursos em que pretende colocar este recurso WAF. 

O código CLI acima irá criar uma política waf que está ativada e está no modo prevenção. 

Nota: pode também querer criar o MODO DE DETEÇÃO e observar como está a detetar & registar pedidos maliciosos (e não bloquear) antes de decidir mudar para o modo De Proteção.

Na resposta que obtém da execução com sucesso deste comando, procure a chave "ID" e note o seu valor a ser usado num passo posterior. O campo de ID deve estar no formato

/subscrições/**subscrição id**/grupos de recursos/**nome do grupo de recursos**/fornecedores/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/ WAF policy**name**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Passo 3: Adicione regras geridas a esta política da WAF

Numa política da WAF, pode adicionar regras geridas que são um conjunto de regras construídas e geridas pela Microsoft e que cedem da proteção da caixa contra classes inteiras de ameaças. Neste exemplo, estamos a adicionar duas regras (1) Regras padrão que protegem contra ameaças comuns da web e (2) regras de proteção bot, que protege contra bots maliciosos

(1) Adicionar o pressuposto predefinido

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Adicione o bot manager ruleset

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--nome de política O nome que deu para o seu recurso Azure WAF

-- grupo de recursos O grupo de recursos em que colocou este recurso WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Passo 4: Associar a política da WAF com o recurso AFD

Neste passo, vamos associar a política da WAF que construímos com o recurso AFD que está em frente à sua aplicação web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--nome O nome que especificou para o seu recurso AFD

-- grupo de recursos O grupo de recursos em que colocou o recurso Azure Front Door.

--definir Este é o ponto em que atualiza o atributo WebApplicationFirewallPolicyLink para o frontendEndpoint associado ao seu recurso AFD com a nova política WAF. A ID da política da WAF pode ser encontrada a partir da resposta que obteve desde o passo #2 acima

Nota: o exemplo acima é para o caso em que não está a usar um domínio personalizado, se estiver

Se não estiver a utilizar quaisquer domínios personalizados para aceder às suas aplicações web, pode saltar #5 passo. Nesse caso, irá fornecer aos seus utilizadores finais o nome de anfitrião que obteve em #1 para navegar para a sua aplicação web

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Passo 5: Configurar o domínio personalizado para a sua aplicação web

Inicialmente, o nome de domínio personalizado da sua aplicação web (a que os clientes usam para se referir à sua aplicação, por exemplo, www.contoso.com) estava a apontar para o local onde o tinha a funcionar antes da introdução da AFD. Após esta mudança de arquitetura adicionando AFD+WAF para fazer frente à aplicação, a entrada DNS correspondente a esse domínio personalizado deve agora apontar para este recurso AFD. Isto pode ser feito remapping esta entrada no seu servidor DNS para o nome de anfitrião AFD que você tinha notado em passo #1.

As etapas específicas para atualizar os seus registos DNS dependerão do seu prestador de serviços DNS, mas se estiver a utilizar o Azure DNS para hospedar o seu nome DNS, pode consultar a documentação para [os passos atualizar um registo DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) e apontar para o nome anfitrião do AFD. 

Uma coisa importante a notar aqui é que, se você precisa que os seus utilizadores naveguem para o seu website usando o ápice da zona, para exmaple, contoso.com, você tem que usar Azure DNS e é tipo de [gravação ALIAS](https://docs.microsoft.com/azure/dns/dns-alias) para hospedar o seu nome DNS. 

Além disso, também precisa atualizar a sua configuração AFD para [adicionar este domínio personalizado](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) ao mesmo para que a AFD compreenda este mapeamento.

Finalmente, se estiver a utilizar um domínio personalizado para chegar à sua aplicação web e pretender ativar o protocolo HTTPS, precisa de ter os certificados para a [configuração de domínio personalizado em AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Passo 6: Bloqueie a sua aplicação web

Uma das melhores práticas opcionais a seguir é garantir que apenas as bordas afD podem comunicar com a sua aplicação web. Esta ação garantirá que ninguém pode contornar as proteções AFD e aceder diretamente às suas aplicações. Você pode realizar este bloqueio visitando a [secção de FAQ da AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) e referindo-se à questão sobre bloquear backends para acesso apenas por AFD.
