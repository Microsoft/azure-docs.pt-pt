---
title: O que é o firewall de aplicações web do Azure para a porta da frente do Azure? (Pré-visualização)
description: Saiba como o Azure firewall de aplicações web para o serviço de porta de entrada do Azure protege as suas aplicações web contra ataques maliciosos.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 122e9687ee313edff34e5a4fd9a44b1026a63811
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478780"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>O que é o firewall de aplicações web do Azure para a porta da frente do Azure?

A firewall de aplicações Web (WAF) do Azure fornece uma proteção centralizada às suas aplicações Web fornecidas globalmente com o Azure Front Door. Esta é concebida e operada para proteger os serviços Web de vulnerabilidades e exploits comuns, assim como para manter o serviço altamente disponível para os utilizadores, além de ajudar a satisfazer os requisitos de conformidade.


Aplicativos Web são cada vez mais os alvos de ataques maliciosos, tais como a negação de inundações de serviço, ataques de injeção de SQL e ataques de script entre sites. Esses ataques maliciosos podem causar a perda de dados e de indisponibilidade de serviço, impõem uma ameaça significativa para os proprietários da aplicação web.

Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Além disso, uma solução WAF pode reagir a uma ameaça de segurança mais rápida ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações web individualmente.

WAF para a porta de entrada é uma solução global e centralizada. Está implementado nos locais de borda de rede do Azure em todo o mundo e cada solicitação de entrada para um aplicativo da web de WAF ativada pela porta de entrada é inspecioná-lo na borda da rede. Isso permite que o WAF impedir ataques maliciosos perto as origens de ataque, antes de entrarem em sua rede virtual e oferece proteção global em escala sem sacrificar o desempenho. Uma política de WAF pode ser facilmente vinculada a qualquer perfil de porta de entrada na sua subscrição e novas regras podem ser implementadas numa questão de minutos, que permite responder rapidamente aos padrões de ameaças em constante mudança.

![Firewall de aplicações web do Azure](./media/waf-overview/web-application-firewall-overview2.png)

Também pode ser ativada a WAF do Azure com o Gateway de aplicação. Para obter mais informações, consulte [firewall de aplicações Web](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Regras e políticas de WAF

Pode configurar uma política de WAF e associar essa política para um ou mais desde início front-ends para proteção. Uma política de WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que são criadas pelo cliente.

- conjuntos de regras geridos que são uma coleção de geridos pelo Azure configurado previamente o conjunto de regras.

Quando ambos estiverem presentes, regras personalizadas são processadas antes do processamento de regras num conjunto de regras gerido. Uma regra é constituída por uma condição de correspondência, uma prioridade e uma ação. Tipos de ação suportados são: PERMITIR, bloquear, registo e REDIRECIONAMENTO. Pode criar uma política totalmente personalizada que satisfaça os requisitos da proteção de aplicação específica ao combinar regras personalizadas e não geridos.

As regras dentro de uma política são processadas uma ordem de prioridade em que a prioridade é um único inteiro que define a ordem das regras a ser processado. Valor de número inteiro menor denota uma prioridade mais alta e aqueles são avaliadas antes das regras com um valor de número inteiro superior. Depois de uma regra for encontrada, a ação correspondente que foi definida na regra é aplicada ao pedido. Assim que essa correspondência é processada, as regras com prioridades mais baixas não são processadas ainda mais.

Um aplicativo web pela porta de entrada pode ter apenas uma política de WAF associada ao mesmo tempo. No entanto, pode ter uma configuração de porta de entrada sem quaisquer políticas de WAF associado ele. Se uma política de WAF estiver presente, é replicado para todos os nossos localizações de borda para garantir a consistência nas políticas de segurança em todo o mundo.

## <a name="waf-modes"></a>Modos de WAF

Política de WAF pode ser configurada para ser executado em dois modos seguintes:

- **Modo de Deteção:** Quando executar no modo de deteção, WAF não tomar outras ações que não seja de monitores e regista o pedido e a sua regra de WAF correspondente registos WAF. Pode ativar diagnósticos de Registro em log para a porta de entrada (ao utilizar o portal, isso pode ser conseguido indo para o **diagnóstico** secção no portal do Azure).

- **Modo de prevenção:** Quando configurado para ser executado no modo de prevenção, o WAF leva a ação especificada, se corresponde a um pedido de uma regra e se uma correspondência for encontrada, não existem regras adicionais com prioridade mais baixa são avaliadas. Todos os pedidos correspondentes também são registados nos registos WAF.

## <a name="waf-actions"></a>Ações de WAF

Os clientes de WAF podem optar por executar a partir de uma das ações quando as condições de uma regra corresponde a um pedido:

- **Permitir:**  Pedido passa por meio da WAF e seja reencaminhado para o back-end. Não são necessárias mais regras de prioridade inferior podem bloquear essa solicitação.
- **Bloco:** O pedido é bloqueado e WAF envia uma resposta para o cliente sem reencaminhar o pedido para o back-end.
- **Log:**  Pedido é registado nos registos WAF e WAF continua a avaliar as regras de prioridade inferior.
- **Redireciona:** WAF redireciona o pedido para o URI especificado. O URI especificado é uma definição de política de nível. Uma vez configurado, todos os pedidos que correspondam a **redirecionar** ação será enviada para esse URI.

## <a name="waf-rules"></a>Regras de WAF

Uma política de WAF pode ser composta por dois tipos de regras de segurança - regras personalizadas, criados pelo cliente e conjuntos de regras geridos, geridos pelo Azure pré-configuradas o conjunto de regras.

### <a name="custom-authored-rules"></a>Regras criadas personalizadas

Pode configurar regras personalizadas WAF da seguinte forma:

- **As permissões de IP lista e a lista de bloqueios:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base numa lista de endereços IP do cliente ou intervalos de endereços IP. Tipos de endereços IPv4 e IPv6 são suportados. Esta lista pode ser configurada para bloquear ou permitir que essas solicitações, onde o IP de origem corresponde a um IP na lista.

- **Controlo de acesso baseado em geográfica:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base no código de país associado com o endereço IP de um cliente.

- **Controlo de acesso com base em parâmetros HTTP:** Pode configurar regras personalizadas com base na cadeia de caracteres correspondentes aos parâmetros do pedido HTTP/HTTPS, como cadeias de consulta, POST args, URI do pedido, o cabeçalho de pedido e o corpo do pedido.

- **Controlo de acesso com base no método do pedido:** Só pode configurar regras personalizadas com base no método de pedido de HTTP do pedido, como GET, PUT ou HEAD.

- **Restrição de tamanho:** Pode configurar regras personalizadas com base nos comprimentos das partes específicas de um pedido, como a cadeia de caracteres de consulta, Uri, ou o corpo do pedido.

- **Taxa de limitação de regras:** Uma regra de controlo de taxa é limitar o tráfego elevado anormal de qualquer IP de cliente. Só pode configurar um limite no número de solicitações da web permitido a partir de um IP de cliente durante um período de um minuto. Isso é diferente de uma regra de personalizado o IP com base em lista permitir/bloquear que o permita todos ou blocos de todos os pedidos de um IP de cliente. Limitação de velocidade pode ser combinada com condições de correspondência adicionais, tais como parâmetros de HTTP (S) correspondente para o controle granular de taxa.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras geridos pelo Azure

Conjuntos de regras geridos pelo Azure fornecem uma forma fácil de implementar a proteção contra um conjunto comum de ameaças de segurança. Uma vez que esses conjuntos de regras são geridos pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataque. Na pré-visualização pública, geridos pelo Azure regra conjunto predefinidos inclui regras contra as seguintes categorias de ameaças:

- Scripts entre sites
- Ataques de Java
- Inclusão de ficheiros local
- Ataques de injeção PHP
- Execução do comando remoto
- Inclusão de ficheiros remota
- Fixação de sessão
- Proteção contra injeção de SQL
- Atacantes de protocolo

O número de versão da regra padrão definida sofrerá um incremento quando novas assinaturas de ataque são adicionadas para o conjunto de regras.
Conjunto de regras de predefinição está ativada por predefinição no modo de Deteção em suas diretivas de WAF. Pode desativar ou ativar regras individuais dentro de regra conjunto predefinidos para atender aos requisitos da sua aplicação. Também pode definir ações específicas (permitir/bloquear/REDIRECIONAMENTO/início de sessão), por regra. Ação predefinida é bloco. Além disso, podem ser configuradas regras personalizadas na mesma política de WAF, se pretender ignorar qualquer uma das regras previamente configuradas na regra conjunto predefinidos.
Regras personalizadas são sempre aplicadas antes das regras na regra conjunto predefinidos são avaliadas. Se um pedido de corresponder a uma regra personalizada, é aplicada a ação de regra correspondente e o pedido está bloqueado ou transmitido para o back-end, sem a invocação de quaisquer regras ainda mais personalizadas ou as regras na regra conjunto predefinidos. Além disso, tem a opção para remover o conjunto de regra predefinidos a partir das suas políticas de WAF.


### <a name="bot-protection-rule-preview"></a>Regra de proteção de bot (pré-visualização)

Um conjunto de regras de proteção de Bot gerido pode ser ativado para o WAF efetuar ações personalizadas nos pedidos de endereços IP maliciosos conhecidos. Os endereços IP são originados da Microsoft informações sobre ameaças do feed. [Gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence) alimenta sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de segurança do Azure.

![Conjunto de regras de proteção de bot](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> Conjunto de regras de proteção de bot está atualmente em pré-visualização pública e é fornecido com um contrato de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se estiver ativada a proteção de Bot, solicitações de entrada que corresponde ao cliente de Bots malicioso IPs são registadas no FrontdoorWebApplicationFirewallLog início de sessão. Pode aceder a registos WAF de conta de armazenamento, análise de registo ou hub de eventos. 

## <a name="configuration"></a>Configuração

Configurando e implantando a todos os tipos de regras WAF é totalmente suportado através do portal do Azure, REST APIs, modelos Azure Resource Manager e do Azure PowerShell.

## <a name="monitoring"></a>Monitorização

Monitorização da WAF desde início está integrado com o Azure Monitor para acompanhar os alertas e monitorizar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [configurar uma política de WAF para a porta de entrada com o portal do Azure](waf-front-door-create-portal.md)