---
title: O que é firewall de aplicação web Azure na Porta frontal Azure?
description: Saiba como a firewall da aplicação web Azure no serviço Azure Front Door protege as suas aplicações web de ataques maliciosos.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 66ca039a449e355e47563b689f5f44cced164fea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724154"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall de aplicação web Azure na porta frontal Azure

A Azure Web Application Firewall (WAF) na Porta Frontal Azure fornece proteção centralizada para as suas aplicações web. A WAF defende os seus serviços web contra explorações e vulnerabilidades comuns. Mantém o seu serviço altamente disponível para os seus utilizadores e ajuda-o a cumprir os requisitos de conformidade.

A WAF na Porta da Frente é uma solução global e centralizada. Está implantado em locais de ponta da rede Azure em todo o mundo. As aplicações web ativadas pela WAF inspecionam todos os pedidos de entrada entregues pela Porta frontal na borda da rede. 

A WAF evita ataques maliciosos perto das fontes de ataque, antes de entrarem na sua rede virtual. Obtém-se proteção global à escala sem sacrificar o desempenho. Uma política WAF liga-se facilmente a qualquer perfil da Porta Frontal na sua subscrição. As novas regras podem ser implementadas em minutos, para que possa responder rapidamente à mudança de padrões de ameaça.

![Firewall de aplicações Web do Azure](../media/overview/wafoverview.png)

AZure Front Door apresenta [dois novos SKUs na pré-visualização](https://docs.microsoft.com/azure/frontdoor/standard-premium/overview): Front Door Standard e Front Door Premium SKU. A WAF está integrada nativamente com o Front Door Premium SKU com todas as capacidades. Para o SKU Standard porta da frente, [apenas as regras personalizadas](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#custom-authored-rules) são suportadas.

## <a name="waf-policy-and-rules"></a>Política e regras da WAF

Pode configurar uma [política da WAF](waf-front-door-create-portal.md) e associar essa política a uma ou mais extremidades frontais da Porta da Frente para proteção. Uma política da WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que são da autoria do cliente.

- conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas pelo Azure.

Quando ambas estão presentes, as regras personalizadas são processadas antes de processar as regras num conjunto de regras gerido. Uma regra é feita de uma condição de jogo, uma prioridade, e uma ação. Os tipos de ação suportados são: ALLOW, BLOCK, LOG e REDIRECT. Pode criar uma política totalmente personalizada que satisfaça os seus requisitos específicos de proteção de aplicações, combinando regras geridas e personalizadas.

As regras dentro de uma política são processadas por ordem prioritária. Prioridade é um número inteiro único que define a ordem das regras para processar. O menor valor inteiro denota uma prioridade maior e essas regras são avaliadas antes de regras com um valor inteiro mais elevado. Uma vez que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada ao pedido. Uma vez que tal jogo é processado, regras com prioridades mais baixas não são processadas mais longe.

Uma aplicação web entregue pela Porta frontal pode ter apenas uma política de WAF associada a ela de cada vez. No entanto, pode ter uma configuração da Porta frontal sem quaisquer políticas WAF associadas a ela. Se uma política da WAF estiver presente, é replicada em todas as nossas localizações de ponta para garantir políticas de segurança consistentes em todo o mundo.

## <a name="waf-modes"></a>Modos WAF

A política da WAF pode ser configurada para ser executada nos dois modos seguintes:

- **Modo de deteção:** Quando executado no modo de deteção, a WAF não toma outras ações que não sejam monitores e regista o pedido e a sua regra WAF correspondida aos registos WAF. Pode ligar os diagnósticos de registo para a Porta frontal. Quando utilizar o portal, aceda à secção **de Diagnóstico.**

- **Modo de prevenção:** No modo de prevenção, a WAF toma a ação especificada se um pedido corresponder a uma regra. Se for encontrado um fósforo, não são avaliadas mais regras com menor prioridade. Quaisquer pedidos combinados também estão registados nos registos da WAF.

## <a name="waf-actions"></a>Ações da WAF

Os clientes da WAF podem optar por executar a partir de uma das ações quando um pedido corresponde às condições de uma regra:

- **Permitir:**  O pedido passa pela WAF e é encaminhado para o back-end. Nenhuma outra regra de prioridade inferior pode bloquear este pedido.
- **Bloco:** O pedido está bloqueado e a WAF envia uma resposta ao cliente sem encaminhar o pedido para o back-end.
- **Registo:**  O pedido é registado nos registos da WAF e a WAF continua a avaliar regras de menor prioridade.
- **Redirecionamento:** A WAF redireciona o pedido para o URI especificado. O URI especificado é uma definição de nível de política. Uma vez configurados, todos os pedidos que correspondam à ação **de redirecionamento** serão enviados para esse URI.

## <a name="waf-rules"></a>Regras da WAF

Uma política da WAF pode consistir em dois tipos de regras de segurança - regras personalizadas, da autoria do cliente e regras geridas, conjunto de regras pré-configurados geridos pela Azure.

### <a name="custom-authored-rules"></a>Regras de autoria personalizada

Pode configurar as regras personalizadas WAF da seguinte forma:

- **Lista de autorizações IP e lista de blocos:** Pode controlar o acesso às suas aplicações web com base numa lista de endereços IP do cliente ou intervalos de endereços IP. Os tipos de endereços IPv4 e IPv6 são suportados. Esta lista pode ser configurada para bloquear ou permitir os pedidos em que o IP de origem corresponde a um IP na lista.

- **Controlo de acesso baseado em geográfico:** Pode controlar o acesso às suas aplicações web com base no código de país associado ao endereço IP de um cliente.

- **Controlo de acesso baseado em parâmetros HTTP:** Pode basear as regras em combinações de cordas em parâmetros de pedido HTTP/HTTPS.  Por exemplo, cadeias de consulta, args POST, Request URI, Cabeçalho de Pedido e Corpo de Pedido.

- **Solicitar controlo de acesso baseado em métodos:** Baseou as regras no método de pedido HTTP do pedido. Por exemplo, GET, PUT ou HEAD.

- **Restrição de tamanho:** Pode basear as regras nos comprimentos de partes específicas de um pedido, como o string de consulta, Uri ou o corpo de pedido.

- **Regras limite de taxa:** Uma regra de controlo de tarifas é limitar o tráfego anormal de qualquer IP do cliente. Pode configurar um limiar no número de pedidos web permitidos a partir de um IP do cliente durante um minuto de duração. Esta regra é distinta de uma regra personalizada de permitir/bloquear com base em listas ip que permite a todos ou bloqueia todo o pedido a partir de um IP do cliente. Os limites de tarifas podem ser combinados com condições adicionais de correspondência, tais como correspondências de parâmetros HTTP(S) para o controlo da taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras geridos pelo Azure

Os conjuntos de regras geridos pelo Azure fornecem uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que tais regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque. O Conjunto de Regras Predefinidas geridos pelo Azure inclui regras contra as seguintes categorias de ameaças:

- Scripting entre sites
- Ataques de Java
- Inclusão de ficheiros locais
- Ataques de injeção de PHP
- Execução de comando remoto
- Inclusão de ficheiros remotos
- Fixação de sessão
- Proteção contra injeção de SQL
- Agressores de protocolo

O número de versão do Conjunto de Regras Predefinidos incrementa quando novas assinaturas de ataque são adicionadas ao conjunto de regras.
O Conjunto de Regras Predefinidos é ativado por padrão no modo deteção nas suas políticas WAF. Pode desativar ou ativar regras individuais dentro da Definição de Regras Predefinidoras para satisfazer os seus requisitos de aplicação. Também pode definir ações específicas (PERMITIR/BLOQUEAR/REDIRECIONAMENTO/LOG) por regra.

Por vezes, poderá ser necessário omitir certos atributos de pedido de uma avaliação da WAF. Um exemplo comum são fichas inseridas no Ative Directory que são usadas para autenticação. Pode configurar uma lista de exclusão para uma regra gerida, grupo de regras ou para toda a regra definida.  

A ação padrão é para BLOQUEAR. Além disso, as regras personalizadas podem ser configuradas na mesma política WAF se desejar contornar qualquer uma das regras pré-configuradas no Conjunto de Regras Predefinidos.

As regras personalizadas são sempre aplicadas antes de serem avaliadas as regras do Conjunto de Regras Predefinidas. Se um pedido corresponder a uma regra personalizada, aplica-se a ação de regra correspondente. O pedido é bloqueado ou passado para o back-end. Não são processadas outras regras personalizadas ou as regras do Conjunto de Regras Predefinidas. Também pode remover o Conjunto de Regras Predefinidos das suas políticas WAF.

### <a name="bot-protection-rule-set-preview"></a>Conjunto de regras de proteção do bot (pré-visualização)

Você pode ativar uma regra de proteção de bot gerida definida para tomar ações personalizadas em pedidos de categorias de bot conhecidas. 

Há três categorias de bots suportadas: Mau, Bom e Desconhecido. As assinaturas bot são geridas e atualizadas dinamicamente pela plataforma WAF.

Os bots maus incluem bots de endereços IP maliciosos e bots que falsificaram as suas identidades. Os endereços IP maliciosos são obtidos a partir do feed da Microsoft Threat Intelligence e atualizados a cada hora. [O Smart Security Graph](https://www.microsoft.com/security/operations/intelligence) alimenta a Microsoft Threat Intelligence e é usado por vários serviços, incluindo o Azure Security Center.

Os Bons Bots incluem motores de busca validados. Categorias desconhecidas incluem grupos de bots adicionais que se identificaram como bots. Por exemplo, analisador de mercado, feed fetchers e agentes de recolha de dados. 

Os bots desconhecidos são classificados através de agentes de utilizadores publicados sem validação adicional. Pode definir ações personalizadas para bloquear, permitir, registar ou redirecionar para diferentes tipos de bots.

![Conjunto de regras de proteção de bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> O conjunto de regras de proteção bot está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a proteção do bot estiver ativada, os pedidos de entrada que correspondam às regras do bot são registados no registo FrontdoorWebApplicationFirewallLog. Pode aceder aos registos waf a partir de uma conta de armazenamento, centro de eventos ou análise de registo.

## <a name="configuration"></a>Configuração

Pode configurar e implementar todos os tipos de regras WAF utilizando o portal Azure, AS APIs DE REST, modelos Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitorização

A monitorização do WAF na Porta frontal está integrada com o Azure Monitor para monitorizar os alertas e monitorizar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [firewall de aplicação web no Gateway de aplicações Azure](../ag/ag-overview.md)
