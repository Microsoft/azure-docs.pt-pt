---
title: O que é firewall de aplicação web Azure no Azure CDN?
description: Saiba como a firewall da aplicação web Azure no serviço Azure CDN protege as suas aplicações web de ataques maliciosos.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: 6949c1e8f83ebf47878a3d449796ccc03920756a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89225157"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Firewall de aplicação web Azure na rede de entrega de conteúdos Azure

A azure Web Application Firewall (WAF) na Azure Content Delivery Network (CDN) da Microsoft fornece proteção centralizada para o seu conteúdo web. A WAF defende os seus serviços web contra explorações e vulnerabilidades comuns. Mantém o seu serviço altamente disponível para os seus utilizadores e ajuda-o a cumprir os requisitos de conformidade.

> [!IMPORTANT]
> WAF on Azure CDN da Microsoft está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

A WAF on Azure CDN é uma solução global e centralizada. Está implantado em locais de ponta da rede Azure em todo o mundo. A WAF para ataques maliciosos perto das fontes de ataque, antes que atinjam a sua origem. Obtém-se proteção global à escala sem sacrificar o desempenho. 

Uma política WAF liga-se facilmente a qualquer ponto final da CDN na sua subscrição. As novas regras podem ser implementadas em minutos, para que possa responder rapidamente à mudança de padrões de ameaça.

![Firewall de aplicações Web do Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Política e regras da WAF

Pode configurar uma política da WAF e associar essa política a um ou mais pontos finais da CDN para proteção. Uma política da WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que pode criar.

- conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas Azure.

Quando ambas estão presentes, as regras personalizadas são processadas antes de processar as regras num conjunto de regras gerido. Uma regra é feita de uma condição de jogo, uma prioridade, e uma ação. Os tipos de ação suportados são: *PERMITIR,* *BLOQUEAR,* *LOG*e *REDIRECIONamento*. Pode criar uma política totalmente personalizada que satisfaça os seus requisitos específicos de proteção de aplicações, combinando regras geridas e personalizadas.

As regras dentro de uma política são processadas por ordem prioritária. Prioridade é um número único que define a ordem das regras para processar. Os números mais pequenos são uma prioridade maior e essas regras são avaliadas antes de regras com um valor maior. Uma vez que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada ao pedido. Uma vez que tal jogo é processado, regras com prioridades mais baixas não são processadas mais longe.

Uma aplicação web hospedada no Azure CDN pode ter apenas uma política waf associada a ela de cada vez. No entanto, pode ter um ponto final cdn sem quaisquer políticas WAF associadas a ele. Se uma política da WAF estiver presente, é replicada em todas as nossas localizações de ponta para garantir políticas de segurança consistentes em todo o mundo.

## <a name="waf-modes"></a>Modos WAF

A política da WAF pode ser configurada para ser executada nos dois modos seguintes:

- *Modo de deteção*: Quando funciona no modo de deteção, a WAF não toma outras ações que não sejam monitores e regista o pedido e a sua regra WAF correspondida aos registos WAF. Pode ligar os diagnósticos de registo de registo sonoro para a CDN. Quando utilizar o portal, aceda à secção **de Diagnóstico.**

- *Modo de prevenção*: No modo de prevenção, a WAF toma a ação especificada se um pedido corresponder a uma regra. Se for encontrado um fósforo, não são avaliadas mais regras com uma prioridade inferior. Quaisquer pedidos combinados também estão registados nos registos da WAF.

## <a name="waf-actions"></a>Ações da WAF

Pode escolher uma das seguintes ações quando um pedido corresponde às condições de uma regra:

- *Permitir*: O pedido passa pela WAF e é encaminhado para o back-end. Nenhuma outra regra de prioridade inferior pode bloquear este pedido.
- *Bloco*: O pedido está bloqueado e a WAF envia uma resposta ao cliente sem encaminhar o pedido para o back-end.
- *Log*: O pedido está registado nos registos da WAF e a WAF continua a avaliar as regras de menor prioridade.
- *Redirecionar*: WAF redireciona o pedido para o URI especificado. O URI especificado é uma definição de nível de política. Uma vez configurados, todos os pedidos que correspondam à ação *de redirecionamento* são enviados para esse URI.

## <a name="waf-rules"></a>Regras da WAF

Uma política da WAF pode consistir em dois tipos de regras de segurança:

- *regras personalizadas*: regras que cria a si mesmo 
- *conjuntos de regras geridos*: Azure gerido conjunto de regras pré-configuradas

### <a name="custom-rules"></a>Regras personalizadas

As regras personalizadas podem ter regras de controlo de correspondência e taxas de controlo.

Pode configurar as seguintes regras de jogo personalizadas:

- *Lista de autorizações IP e lista de blocos*: Pode controlar o acesso às suas aplicações web com base numa lista de endereços IP do cliente ou intervalos de endereços IP. Os tipos de endereços IPv4 e IPv6 são suportados. Esta lista pode ser configurada para bloquear ou permitir os pedidos em que o IP de origem corresponde a um IP na lista.

- *Controlo de acesso baseado em*geografia : Pode controlar o acesso às suas aplicações web com base no código de país que está associado ao endereço IP de um cliente.

- *Controlo de acesso baseado em parâmetros HTTP*: Pode basear as regras em combinações de cordas em parâmetros de pedido HTTP/HTTPS.  Por exemplo, cadeias de consulta, args POST, Request URI, Cabeçalho de Pedido e Corpo de Pedido.

- *Solicitar controlo de acesso baseado em métodos:* Baseie as regras no método de pedido HTTP do pedido. Por exemplo, GET, PUT ou HEAD.

- *Restrição de tamanho*: Pode basear as regras nos comprimentos de partes específicas de um pedido, tais como cadeia de consulta, Uri ou corpo de pedido.

Uma regra de controlo de taxas limita o tráfego anormalmente elevado a partir de qualquer endereço IP do cliente.

- *Regras limite de tarifas*: Pode configurar um limiar no número de pedidos web permitidos a partir de um endereço IP do cliente durante um minuto de duração. Esta regra é distinta de uma regra personalizada baseada em listas ip que permite a todos ou bloqueia todo o pedido a partir de um endereço IP do cliente. Os limites de tarifas podem ser combinados com condições adicionais de correspondência, tais como correspondências de parâmetros HTTP(S) para o controlo da taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras geridos pelo Azure

Os conjuntos de regras geridos pelo Azure fornecem uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que estas regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque. O Conjunto de Regras Por Defeito gerido pelo Azure inclui regras contra as seguintes categorias de ameaças:

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
O Conjunto de Regras Predefinidos é ativado por padrão no modo *deteção* nas suas políticas WAF. Pode desativar ou ativar regras individuais dentro da Definição de Regras Predefinidoras para satisfazer os seus requisitos de aplicação. Também pode definir ações específicas (PERMITIR/BLOQUEAR/REDIRECIONAMENTO/LOG) por regra. A ação predefinitiva para o conjunto de regras por defeito gerido é *o Bloco*.

As regras personalizadas são sempre aplicadas antes de serem avaliadas as regras do Conjunto de Regras Predefinidas. Se um pedido corresponder a uma regra personalizada, aplica-se a ação de regra correspondente. O pedido é bloqueado ou passado para o back-end. Não são processadas outras regras personalizadas ou as regras do Conjunto de Regras Predefinidas. Também pode remover o Conjunto de Regras Predefinidos das suas políticas WAF.

## <a name="configuration"></a>Configuração

Pode configurar e implementar todos os tipos de regras WAF utilizando o portal Azure, AS APIs DE REST, modelos Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitorização

A monitorização do WAF com CDN está integrada com o Azure Monitor para monitorizar os alertas e monitorizar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma política de WAF com Azure CDN usando o portal Azure](waf-cdn-create-portal.md)
