---
title: O que é o Firewall do aplicativo Web do Azure na porta frontal do Azure?
description: Saiba como o Firewall do aplicativo Web do Azure no serviço de porta de recepção do Azure protege seus aplicativos Web contra ataques mal-intencionados.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: b646035f6a952f679059abab86d94179f447f9ff
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406213"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall do aplicativo Web do Azure na porta frontal do Azure

O Firewall do aplicativo Web do Azure (WAF) na porta frontal do Azure fornece proteção centralizada para seus aplicativos Web que são entregues globalmente usando a porta frontal do Azure. Esta é concebida e operada para proteger os serviços Web de vulnerabilidades e exploits comuns, assim como para manter o serviço altamente disponível para os utilizadores, além de ajudar a satisfazer os requisitos de conformidade.

WAF na porta frontal é uma solução global e centralizada. Ele é implantado em locais de borda de rede do Azure em todo o globo e cada solicitação de entrada para um aplicativo Web habilitado para WAF entregue pela porta de frente é inspecionada na borda da rede. Isso permite que o WAF impeça ataques mal-intencionados próximos às fontes de ataque, antes que eles insiram sua rede virtual e oferece proteção global em escala sem sacrificar o desempenho. Uma política de WAF pode ser facilmente vinculada a qualquer perfil de porta frontal em sua assinatura e novas regras podem ser implantadas em minutos, permitindo que você responda rapidamente aos padrões de ameaça em constante mudança.

![Firewall do aplicativo Web do Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Política e regras do WAF

Você pode configurar uma política de WAF e associá-la a um ou mais front-ends de porta frontal para proteção. Uma política de WAF consiste em dois tipos de regras de segurança:

- regras personalizadas que são criadas pelo cliente.

- conjuntos de regras gerenciadas que são uma coleção do conjunto de regras pré-configuradas gerenciadas pelo Azure.

Quando ambos estiverem presentes, as regras personalizadas serão processadas antes de processar as regras em um conjunto de regras gerenciadas. Uma regra é feita de uma condição de correspondência, uma prioridade e uma ação. Os tipos de ação com suporte são: permitir, bloquear, registrar e redirecionar. Você pode criar uma política totalmente personalizada que atenda aos seus requisitos de proteção de aplicativo específicos combinando regras gerenciadas e personalizadas.

As regras em uma política são processadas em uma ordem priorizada em que Priority é um inteiro exclusivo que define a ordem das regras que estão sendo processadas. Um valor inteiro menor denota uma prioridade mais alta e elas são avaliadas antes das regras com um valor inteiro mais alto. Depois que uma regra for correspondida, a ação correspondente definida na regra será aplicada à solicitação. Uma vez que essa correspondência seja processada, as regras com prioridades inferiores não são processadas ainda mais.

Um aplicativo Web entregue pela porta de front-end pode ter apenas uma política WAF associada a ela por vez. No entanto, você pode ter uma configuração de porta frontal sem nenhuma política de WAF associada a ela. Se uma política de WAF estiver presente, ela será replicada para todos os nossos locais de borda para garantir a consistência em políticas de segurança em todo o mundo.

## <a name="waf-modes"></a>Modos de WAF

A política WAF pode ser configurada para ser executada nos dois modos a seguir:

- **Modo de detecção:** Quando executado no modo de detecção, o WAF não executa outras ações além de monitores e registra a solicitação e sua regra WAF correspondente nos logs do WAF. Você pode ativar o diagnóstico de log para a porta frontal (ao usar o portal, isso pode ser feito acessando a seção de **diagnóstico** no portal do Azure).

- **Modo de prevenção:** Quando configurado para ser executado no modo de prevenção, WAF executará a ação especificada se uma solicitação corresponder a uma regra e se uma correspondência for encontrada, nenhuma regra adicional com prioridade mais baixa será avaliada. Todas as solicitações correspondentes também são registradas nos logs do WAF.

## <a name="waf-actions"></a>Ações de WAF

Os clientes do WAF podem optar por executar uma das ações quando uma solicitação corresponde às condições de uma regra:

- **Permitir:**  A solicitação passa pelo WAF e é encaminhada para o back-end. Nenhuma regra de prioridade mais baixa pode bloquear essa solicitação.
- **Bloquear:** A solicitação é bloqueada e o WAF envia uma resposta ao cliente sem encaminhar a solicitação para o back-end.
- **Log:**  A solicitação é registrada nos logs do WAF e o WAF continua avaliando as regras de prioridade mais baixa.
- **Redirecionar:** WAF redireciona a solicitação para o URI especificado. O URI especificado é uma configuração de nível de política. Uma vez configurado, todas as solicitações que correspondem à ação de **redirecionamento** serão enviadas para esse URI.

## <a name="waf-rules"></a>Regras de WAF

Uma política de WAF pode consistir em dois tipos de regras de segurança – regras personalizadas, criadas pelo cliente e conjuntos de regras gerenciados, conjunto de regras pré-configuradas gerenciadas pelo Azure.

### <a name="custom-authored-rules"></a>Regras de autoria personalizadas

Você pode configurar regras personalizadas WAF da seguinte maneira:

- Lista **de permissões de IP e lista de bloqueios:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos Web com base em uma lista de endereços IP do cliente ou intervalos de endereços IP. Há suporte para os tipos de endereço IPv4 e IPv6. Essa lista pode ser configurada para bloquear ou permitir essas solicitações em que o IP de origem corresponde a um IP na lista.

- **Controle de acesso baseado em geográfico:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos Web com base no código do país associado ao endereço IP de um cliente.

- **Controle de acesso baseado em parâmetros http:** Você pode configurar regras personalizadas com base em parâmetros de solicitação HTTP/HTTPS de correspondência de cadeia de caracteres, como cadeias de consulta, argumentos POST, URI de solicitação, cabeçalho de solicitação e corpo da solicitação.

- **Solicitar controle de acesso baseado em método:** Você pode configurar regras personalizadas com base no método de solicitação HTTP da solicitação, como GET, PUT ou HEAD.

- **Restrição de tamanho:** Você pode configurar regras personalizadas com base nos comprimentos de partes específicas de uma solicitação, como cadeia de caracteres de consulta, URI ou corpo da solicitação.

- **Regras de limitação de taxa:** Uma regra de controle de taxa é limitar o tráfego de alta anormal de qualquer IP do cliente. Você pode configurar um limite no número de solicitações da Web permitidas de um IP de cliente durante uma duração de um minuto. Isso é diferente de uma regra personalizada permitir/bloquear com base na lista de IPS que permite todos ou bloqueia todas as solicitações de um IP do cliente. A limitação de taxa pode ser combinada com condições de correspondência adicionais, como parâmetros HTTP (S) correspondentes para controle de taxa granular.

### <a name="azure-managed-rule-sets"></a>Conjuntos de regras gerenciadas pelo Azure

Os conjuntos de regras gerenciadas pelo Azure fornecem uma maneira fácil de implantar a proteção contra um conjunto comum de ameaças de segurança. Como esses RuleSets são gerenciados pelo Azure, as regras são atualizadas conforme necessário para proteção contra novas assinaturas de ataque. Na visualização pública, o conjunto de regras padrão gerenciadas pelo Azure inclui regras em relação às seguintes categorias de ameaça:

- Scripts entre sites
- Ataques de Java
- Inclusão de arquivo local
- Ataques de injeção de PHP
- Execução de comando remoto
- Inclusão de arquivo remoto
- Fixação da sessão
- Proteção contra injeção de SQL
- Invasores de protocolo

O número de versão do conjunto de regras padrão será incrementado quando novas assinaturas de ataque forem adicionadas ao conjunto de regras.
O conjunto de regras padrão é habilitado por padrão no modo de detecção em suas políticas de WAF. Você pode desabilitar ou habilitar regras individuais dentro do conjunto de regras padrão para atender aos requisitos do aplicativo. Você também pode definir ações específicas (permitir/bloquear/redirecionar/registrar) por regra. A ação padrão é bloquear. Além disso, as regras personalizadas podem ser configuradas na mesma política de WAF se você quiser ignorar qualquer uma das regras pré-configuradas no conjunto de regras padrão.
As regras personalizadas são sempre aplicadas antes que as regras no conjunto de regras padrão sejam avaliadas. Se uma solicitação corresponder a uma regra personalizada, a ação de regra correspondente será aplicada e a solicitação será bloqueada ou passada para o back-end, sem a invocação de nenhuma regra personalizada adicional ou das regras no conjunto de regras padrão. Além disso, você tem a opção de remover o conjunto de regras padrão de suas políticas de WAF.


### <a name="bot-protection-rule-set-preview"></a>Conjunto de regras de proteção de bot (versão prévia)

Um conjunto de regras de proteção de bot gerenciado pode ser habilitado para que seu WAF faça ações personalizadas em solicitações de categorias de bot conhecidas. Há três categorias de bot com suporte: bots defeituosos, bons bots e bots desconhecidos. As assinaturas de bot são gerenciadas e atualizadas dinamicamente pela plataforma WAF. Endereços IP mal-intencionados para bots inválidos são originados do feed do Microsoft Threat Intelligence. [Gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence) capacita a inteligência contra ameaças da Microsoft e é usada por vários serviços, incluindo a central de segurança do Azure. Os bons bots incluem mecanismos de pesquisa validados. As categorias desconhecidas incluem grupos de bot adicionais. Você pode definir ações personalizadas para bloquear, permitir, registrar ou redirecionar para diferentes tipos de bots.

![Conjunto de regras de proteção de bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a proteção de bot estiver habilitada, as solicitações de entrada que corresponderem às regras de bot serão registradas no log FrontdoorWebApplicationFirewallLog. Você pode acessar os logs do WAF de uma conta de armazenamento, Hub de eventos ou log Analytics.

## <a name="configuration"></a>Configuração

A configuração e implantação de todos os tipos de regra WAF tem suporte total usando portal do Azure, APIs REST, modelos de Azure Resource Manager e Azure PowerShell.

## <a name="monitoring"></a>Monitorização

O monitoramento para WAF na porta frontal é integrado com Azure Monitor para controlar alertas e monitorar facilmente as tendências de tráfego.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Firewall do aplicativo Web no gateway de aplicativo Azure](../ag/ag-overview.md)
