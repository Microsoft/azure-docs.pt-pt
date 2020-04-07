---
title: O que é firewall de aplicação web Azure no Azure CDN?
description: Saiba como a firewall de aplicação web Azure no serviço Azure CDN protege as suas aplicações web de ataques maliciosos.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754290"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Firewall de aplicação web Azure na rede de entrega de conteúdos Azure

O Firewall de aplicações web Azure (WAF) na Rede de Entrega de Conteúdos Azure (CDN) da Microsoft fornece proteção centralizada para o seu conteúdo web. A WAF defende os seus serviços web contra explorações e vulnerabilidades comuns. Mantém o seu serviço altamente disponível para os seus utilizadores e ajuda-o a cumprir os requisitos de conformidade.

> [!IMPORTANT]
> WaF em Azure CDN da Microsoft está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

WaF em Azure CDN é uma solução global e centralizada. Está implantado em localizações de borda de rede Azure em todo o mundo. A WAF para ataques maliciosos perto das fontes de ataque, antes de chegarem à sua origem. Obtém-se proteção global em escala sem sacrificar o desempenho. 

Uma política WAF liga-se facilmente a qualquer ponto final da CDN na sua subscrição. As novas regras podem ser implementadas em poucos minutos, para que possa responder rapidamente às mudanças nos padrões de ameaça.

![Firewall de aplicação web Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Política e regras da WAF

Pode configurar uma política waf e associar essa política a um ou mais pontos finais de CDN para proteção. Uma política de WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que pode criar.

- conjuntos de regras geridos que são uma coleção de regras geridas pelo Azure pré-configuradas.

Quando ambos estão presentes, as regras personalizadas são processadas antes de processar as regras num conjunto de regras gerido. Uma regra é feita de uma condição de jogo, uma prioridade, e uma ação. Os tipos de ação suportados são: *ALLOW,* *BLOCK,* *LOG*, e *REDIRECT*. Pode criar uma política totalmente personalizada que satisfaça os seus requisitos específicos de proteção de aplicações, combinando regras geridas e personalizadas.

As regras dentro de uma política são processadas por ordem prioritária. A prioridade é um número único que define a ordem das regras para processar. Os números mais pequenos são uma prioridade maior e essas regras são avaliadas antes de regras com um valor maior. Uma vez que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada ao pedido. Uma vez que tal jogo é processado, as regras com prioridades mais baixas não são processadas mais.

Uma aplicação web hospedada no Azure CDN pode ter apenas uma política waf associada a ela de cada vez. No entanto, pode ter um ponto final de CDN sem quaisquer políticas waf associadas a ele. Se uma política de WAF estiver presente, é replicada em todos os nossos locais de vantagem para garantir políticas de segurança consistentes em todo o mundo.

## <a name="waf-modes"></a>Modos WAF

A política waf pode ser configurada para ser executada nos dois modos seguintes:

- *Modo de deteção*: Quando executado no modo de deteção, o WAF não toma outras ações que não os monitores e regista o pedido e a sua regra WAF compatível com os registos WAF. Pode ligar o diagnóstico de registo para a Porta da Frente. Quando utilizar o portal, vá à secção **de Diagnósticos.**

- *Modo de prevenção*: No modo de prevenção, a WAF toma as medidas especificadas se um pedido corresponder a uma regra. Se for encontrado um jogo, não são avaliadas mais regras com menor prioridade. Quaisquer pedidos combinados também são registados nos registos waf.

## <a name="waf-actions"></a>Ações waf

Pode escolher uma das seguintes ações quando um pedido corresponde às condições de uma regra:

- *Permitir*: O pedido passa pela WAF e é encaminhado para trás. Nenhuma outra regra de prioridade inferior pode bloquear este pedido.
- *Bloco*: O pedido está bloqueado e a WAF envia uma resposta ao cliente sem reencaminhar o pedido para o back-end.
- *Log*: O pedido está registado nos registos waf e o WAF continua a avaliar regras de prioridade inferior.
- *Redirecionamento*: WaF redireciona o pedido para o URI especificado. O URI especificado é uma definição de nível de política. Uma vez configurados, todos os pedidos que correspondam à ação *Redirecionamento* são enviados para esse URI.

## <a name="waf-rules"></a>Regras waf

Uma política de WAF pode consistir em dois tipos de regras de segurança:

- *regras personalizadas*: regras que cria a si mesmo 
- *conjuntos*de regras geridos : Azure gerido conjunto de regras pré-configuradas

### <a name="custom-rules"></a>Regras personalizadas

As regras personalizadas podem ter regras de correspondência e regras de controlo de tarifas.

Pode configurar as seguintes regras de correspondência personalizadas:

- *IP permitir lista e lista de blocos*: Pode controlar o acesso às suas aplicações web com base numa lista de endereços IP do cliente ou intervalos de endereços IP. Tanto os tipos de endereços IPv4 como IPv6 são suportados. Esta lista pode ser configurada para bloquear ou permitir os pedidos em que o IP de origem corresponda a um IP na lista.

- *Controlo de acesso baseado em geográficos:* Pode controlar o acesso às suas aplicações web com base no código do país que está associado ao endereço IP de um cliente.

- *Controlo de acesso baseado em parâmetros HTTP:* Pode basear regras em correspondências de cordas em parâmetros de pedido HTTP/HTTPS.  Por exemplo, cordas de consulta, POST args, Request URI, Request Header e Request Body.

- *Solicite o controlo de acesso baseado no método do método:* Baseia as regras do método de pedido http do pedido. Por exemplo, GET, PUT ou HEAD.

- *Restrição de tamanho*: Pode basear regras nos comprimentos de partes específicas de um pedido, como corda de consulta, Uri ou solicitar corpo.

Uma regra de controlo de tarifas limita o tráfego anormalmente elevado de qualquer endereço IP do cliente.

- Regras de *limitação de tarifas*: Pode configurar um limiar sobre o número de pedidos web permitidos a partir de um endereço IP do cliente durante um minuto de duração. Esta regra é distinta de uma regra personalizada baseada na lista IP que permite a toda ou bloqueia todo o pedido de um endereço IP do cliente. Os limites de tarifas podem ser combinados com condições adicionais de correspondência, tais como partidas de parâmetros HTTP(S) para o controlo da taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras geridos pelo Azure

Os conjuntos de regras geridos pelo Azure proporcionam uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que estas regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque. O Conjunto de Regras de Padrão gerido pelo Azure inclui regras contra as seguintes categorias de ameaças:

- Scripting entre sites
- Ataques de Java
- Inclusão de ficheiros locais
- Ataques de injeção php
- Execução de comando remoto
- Inclusão de ficheiros remotos
- Fixação de sessão
- Proteção contra injeção de SQL
- Atacantes protocolares

O número da versão do Conjunto de Regras Padrão aumenta quando novas assinaturas de ataque são adicionadas ao conjunto de regras.
O conjunto de regras predefinidos é ativado por predefinição no modo *de deteção* nas suas políticas WAF. Pode desativar ou ativar regras individuais dentro do Conjunto de Regras Predefinidas para satisfazer os seus requisitos de aplicação. Também pode definir ações específicas (ALLOW/BLOCK/REDIRECT/LOG) por regra. A ação predefinida para o conjunto de regras predefinidas gerido é *block*.

As regras personalizadas são sempre aplicadas antes de serem avaliadas as regras do Conjunto de Regras predefinidas. Se um pedido corresponder a uma regra personalizada, a ação de regra correspondente é aplicada. O pedido está bloqueado ou passado para o fundo da questão. Nenhuma outra regra personalizada ou as regras do Conjunto de Regras Padrão são processadas. Também pode remover o conjunto de regras padrão das suas políticas WAF.

## <a name="configuration"></a>Configuração

Pode configurar e implementar todos os tipos de regras WAF utilizando o portal Azure, OS MODELOS REST APIs, Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitorização

A monitorização para WAF com CDN está integrada com o Azure Monitor para rastrear alertas e monitorizar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar uma política waf com o Azure CDN usando o portal Azure](waf-cdn-create-portal.md)
