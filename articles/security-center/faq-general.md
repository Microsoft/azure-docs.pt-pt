---
title: Azure Security Center FAQ - Perguntas gerais
description: Frequentemente fez perguntas gerais sobre o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095549"
---
# <a name="faq---general-questions-about-azure-security-center"></a>FAQ - Perguntas gerais sobre o Azure Security Center

## <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Centro de Segurança Azure ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Security Center utiliza o agente Log Analytics para recolher e armazenar dados. Para mais detalhes, consulte a recolha de [dados no Centro de Segurança Azure.](security-center-enable-data-collection.md)


## <a name="how-do-i-get-azure-security-center"></a>Como consigo o Centro de Segurança Azure?
O Azure Security Center está ativado com a subscrição do Microsoft Azure e acedido a partir do [portal Azure](https://azure.microsoft.com/features/azure-portal/). Para aceder ao mesmo, [inscreva-se no portal,](https://portal.azure.com)selecione **Procurar** e percorra para **o Centro de Segurança.**


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Que recursos da Azure são monitorizados pelo Centro de Segurança Azure?
O Azure Security Center monitoriza os seguintes recursos Azure:

* Máquinas virtuais (VMs) (incluindo [serviços em nuvem)](../cloud-services/cloud-services-choose-me.md)
* Conjuntos de dimensionamento de máquinas virtuais
* Soluções de parceiros integradas com a sua subscrição Azure, como uma firewall de aplicações web em VMs e no Ambiente de Serviço de Aplicações
* [Os muitos serviços Azure PaaS listados na visão geral do produto](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos Azure?
A página **de visão geral do Centro** de Segurança mostra a postura de segurança geral do seu ambiente desacomprem-se por dados de computação, rede, armazenamento & e aplicações. Cada tipo de recurso tem um indicador que mostra vulnerabilidades de segurança identificadas. Clicar em cada azulejo apresenta uma lista de problemas de segurança identificados pelo Security Center, juntamente com um inventário dos recursos na sua subscrição.



## <a name="what-is-a-security-initiative"></a>O que é uma iniciativa de segurança?
Uma iniciativa de segurança define o conjunto de controlos (políticas) que são recomendados para os recursos dentro da subscrição especificada. No Azure Security Center, atribui iniciativas para as suas subscrições Azure de acordo com os requisitos de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

As políticas de segurança ativadas no Azure Security Center impulsionam recomendações de segurança e monitorização. Saiba mais em [Quais são as políticas de segurança, iniciativas e recomendações?](security-policy-concept.md)


## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, tem de ser um **Administrador de Segurança** ou um **Proprietário** dessa subscrição.

Para aprender a configurar uma política de segurança, consulte [definição de políticas de segurança no Centro de Segurança Azure](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
O Centro de Segurança do Azure analisa o estado de segurança dos recursos do Azure. Quando potenciais vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam-no através do processo de configuração do controlo necessário. Eis alguns exemplos:

* Fornecimento de anti-malware para ajudar a identificar e remover software malicioso
* [Grupos](../virtual-network/network-security-groups-overview.md) de segurança de rede e regras para controlar o tráfego em máquinas virtuais
* Provisionamento de uma firewall de aplicação web para ajudar a defender contra ataques direcionados para as suas aplicações web
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Apenas as recomendações que estão habilitados nas Políticas de Segurança são apresentadas aqui.


## <a name="what-triggers-a-security-alert"></a>O que desencadeia um alerta de segurança?
O Azure Security Center recolhe, analisa e funde automaticamente dados de registo dos seus recursos Azure, da rede e de soluções parceiras como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Malware avançado detetado usando relatórios de erro do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções integradas de segurança de parceiros, tais como Anti-Malware ou Firewalls de Aplicações Web


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detetadas e alertadas pelo Microsoft Security Response Center contra o Azure Security Center?
O Microsoft Security Response Center (MSRC) realiza uma monitorização de segurança selecionada da rede e infraestrutura Azure e recebe queixas de ameaças e queixas de abuso de terceiros. Quando a MSRC tomar conhecimento de que os dados do cliente foram acedidos por uma parte ilícita ou não autorizada ou que a utilização do Azure pelo cliente não cumpre os termos de Utilização Aceitável, um gestor de incidentes de segurança notifica o cliente. A notificação ocorre normalmente enviando um e-mail para os contactos de segurança especificados no Azure Security Center ou no proprietário da subscrição Azure se não for especificado um contacto de segurança.

O Security Center é um serviço Azure que monitoriza continuamente o ambiente Azure do cliente e aplica análises para detetar automaticamente uma ampla gama de atividades potencialmente maliciosas. Estas deteções são emergidas como alertas de segurança no painel do Centro de Segurança.