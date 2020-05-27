---
title: O que é firewall de aplicação web Azure na Porta frontal do Azure?
description: Saiba como a firewall de aplicação web Azure no serviço Azure Front Door protege as suas aplicações web de ataques maliciosos.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: a437e474a923edc15689639b7180ebed73242bb5
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816374"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall de aplicação web azure na porta da frente do Azure

A Firewall de aplicação web Azure (WAF) na Porta Frontal Azure fornece proteção centralizada para as suas aplicações web. A WAF defende os seus serviços web contra explorações e vulnerabilidades comuns. Mantém o seu serviço altamente disponível para os seus utilizadores e ajuda-o a cumprir os requisitos de conformidade.

WaF na Porta da Frente é uma solução global e centralizada. Está implantado em localizações de borda de rede Azure em todo o mundo. As aplicações web ativadas pela WAF inspecionam todos os pedidos de entrada entregues pela Porta Frontal na borda da rede. 

A WAF evita ataques maliciosos perto das fontes de ataque, antes de entrarem na sua rede virtual. Obtém-se proteção global em escala sem sacrificar o desempenho. Uma política waf facilmente se liga a qualquer perfil front door na sua subscrição. As novas regras podem ser implementadas em poucos minutos, para que possa responder rapidamente às mudanças nos padrões de ameaça.

![Firewall de aplicação web Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Política e regras da WAF

Pode configurar uma [política waf](waf-front-door-create-portal.md) e associar essa política a uma ou mais frentes front door para proteção. Uma política de WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que são da autoria do cliente.

- conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas pelo Azure.

Quando ambos estão presentes, as regras personalizadas são processadas antes de processar as regras num conjunto de regras gerido. Uma regra é feita de uma condição de jogo, uma prioridade, e uma ação. Os tipos de ação suportados são: ALLOW, BLOCK, LOG e REDIRECT. Pode criar uma política totalmente personalizada que satisfaça os seus requisitos específicos de proteção de aplicações, combinando regras geridas e personalizadas.

As regras dentro de uma política são processadas por ordem prioritária. A prioridade é um inteiro único que define a ordem das regras para processar. O valor mais pequeno denota uma prioridade maior e essas regras são avaliadas antes de regras com um valor mais elevado. Uma vez que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada ao pedido. Uma vez que tal jogo é processado, as regras com prioridades mais baixas não são processadas mais.

Uma aplicação web entregue pela Front Door pode ter apenas uma política waf associada a ela de cada vez. No entanto, pode ter uma configuração porta da frente sem quaisquer políticas waf associadas a ela. Se uma política de WAF estiver presente, é replicada em todos os nossos locais de vantagem para garantir políticas de segurança consistentes em todo o mundo.

## <a name="waf-modes"></a>Modos WAF

A política waf pode ser configurada para ser executada nos dois modos seguintes:

- **Modo de deteção:** Quando executado em modo de deteção, o WAF não toma outras ações que não os monitores e regista o pedido e a sua regra WAF compatível com os registos WAF. Pode ligar o diagnóstico de registo para a Porta da Frente. Quando utilizar o portal, vá à secção **de Diagnósticos.**

- **Modo de prevenção:** No modo de prevenção, a WAF toma a medida especificada se um pedido corresponder a uma regra. Se for encontrado um jogo, não são avaliadas mais regras com menor prioridade. Quaisquer pedidos combinados também são registados nos registos waf.

## <a name="waf-actions"></a>Ações waf

Os clientes waf podem optar por correr a partir de uma das ações quando um pedido corresponde às condições de uma regra:

- **Permitir:**  O pedido passa pela WAF e é encaminhado para o back-end. Nenhuma outra regra de prioridade inferior pode bloquear este pedido.
- **Bloco:** O pedido está bloqueado e a WAF envia uma resposta ao cliente sem reencaminhar o pedido para o back-end.
- **Registo:**  O pedido está registado nos registos waf e a WAF continua a avaliar regras de prioridade mais baixa.
- **Redirecionamento:** A WAF redireciona o pedido para o URI especificado. O URI especificado é uma definição de nível de política. Uma vez configurados, todos os pedidos que correspondam à ação **Redirecionamento** serão enviados para esse URI.

## <a name="waf-rules"></a>Regras waf

Uma política waf pode consistir em dois tipos de regras de segurança - regras personalizadas, da autoria do cliente e conjuntos de regras geridos pelo cliente, conjunto de regras pré-configuradas geridas pelo Azure.

### <a name="custom-authored-rules"></a>Regras de autoria personalizada

Pode configurar regras personalizadas WAF da seguinte forma:

- **IP permitir lista e lista de blocos:** Pode controlar o acesso às suas aplicações web com base numa lista de endereços IP do cliente ou intervalos de endereços IP. Tanto os tipos de endereços IPv4 como IPv6 são suportados. Esta lista pode ser configurada para bloquear ou permitir os pedidos em que o IP de origem corresponda a um IP na lista.

- **Controlo de acesso baseado em geográficos:** Pode controlar o acesso às suas aplicações web com base no código do país que está associado ao endereço IP de um cliente.

- **Controlo de acesso baseado em parâmetros HTTP:** Pode basear as regras em correspondências de cordas em parâmetros de pedido HTTP/HTTPS.  Por exemplo, cordas de consulta, POST args, Request URI, Request Header e Request Body.

- **Solicitar controlo de acesso baseado no método:** Baseia-se em regras no método de pedido http do pedido. Por exemplo, GET, PUT ou HEAD.

- **Restrição de tamanho:** Você pode basear regras sobre os comprimentos de partes específicas de um pedido como corda de consulta, Uri, ou solicitar corpo.

- **Regras de limitação de taxas:** Uma regra de controlo de tarifas é limitar o tráfego anormal de qualquer CLIENTE IP. Pode configurar um limiar sobre o número de pedidos web permitidos a partir de um IP cliente durante um minuto de duração. Esta regra é distinta de uma regra personalizada baseada na lista IP que permite a toda ou bloqueia todo o pedido de um IP cliente. Os limites de tarifas podem ser combinados com condições adicionais de correspondência, tais como partidas de parâmetros HTTP(S) para o controlo da taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras geridos pelo Azure

Os conjuntos de regras geridos pelo Azure proporcionam uma forma fácil de implementar proteção contra um conjunto comum de ameaças à segurança. Uma vez que tais regras são geridas pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque. O Conjunto de Regras padrão gerido pelo Azure inclui regras contra as seguintes categorias de ameaças:

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
O conjunto de regras predefinidos é ativado por predefinição no modo de deteção nas suas políticas WAF. Pode desativar ou ativar regras individuais dentro do Conjunto de Regras Predefinidas para satisfazer os seus requisitos de aplicação. Também pode definir ações específicas (ALLOW/BLOCK/REDIRECT/LOG) por regra.

Por vezes, pode ser necessário omitir certos atributos de pedido de uma avaliação waf. Um exemplo comum são as fichas inseridas no Diretório Ativo que são usadas para autenticação. Pode configurar uma lista de exclusão para uma regra gerida, grupo de regras ou para todo o conjunto de regras.  

A ação Padrão é bloquear. Além disso, as regras personalizadas podem ser configuradas na mesma política de WAF se desejar contornar qualquer uma das regras pré-configuradas no Conjunto de Regras Predefinidas.

As regras personalizadas são sempre aplicadas antes de serem avaliadas as regras do Conjunto de Regras predefinidas. Se um pedido corresponder a uma regra personalizada, a ação de regra correspondente é aplicada. O pedido está bloqueado ou passado para o fundo da questão. Nenhuma outra regra personalizada ou as regras do Conjunto de Regras Padrão são processadas. Também pode remover o conjunto de regras padrão das suas políticas WAF.

### <a name="bot-protection-rule-set-preview"></a>Conjunto de regras de proteção de bot (pré-visualização)

Pode ativar uma regra de proteção de bots gerida definida para tomar ações personalizadas em pedidos de categorias de bots conhecidas. 

Existem três categorias de bots suportadas: Mau, Bom e Desconhecido. As assinaturas bot são geridas e atualizadas dinamicamente pela plataforma WAF.

Os bots maus incluem bots de endereços IP maliciosos e bots que falsificaram as suas identidades. Os endereços IP maliciosos são obtidos a partir do feed da Microsoft Threat Intelligence e atualizados a cada hora. [O Smart Security Graph](https://www.microsoft.com/security/operations/intelligence) alimenta a Microsoft Threat Intelligence e é usado por vários serviços, incluindo o Azure Security Center.

Os bons Bots incluem motores de busca validados. Categorias desconhecidas incluem grupos de bots adicionais que se identificaram como bots. Por exemplo, analisador de mercado, recolectores de alimentos e agentes de recolha de dados. 

Os bots desconhecidos são classificados através de agentes de utilizador publicados sem validação adicional. Pode definir ações personalizadas para bloquear, permitir, registar ou redirecionar para diferentes tipos de bots.

![Conjunto de regras de proteção de bots](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> O conjunto de regras de proteção bot está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a proteção do bot estiver ativada, os pedidos de entrada que correspondem às regras do bot são registados no registo FrontdoorWebApplicationFirewallLog. Pode aceder a registos waf a partir de uma conta de armazenamento, centro de eventos ou análise de registo.

## <a name="configuration"></a>Configuração

Pode configurar e implementar todos os tipos de regras WAF utilizando o portal Azure, OS MODELOS REST APIs, Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitorização

A monitorização para WAF na Porta Frontal está integrada com o Monitor Azure para rastrear alertas e monitorizar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre firewall de aplicação web no Portal de [Aplicações Azure](../ag/ag-overview.md)