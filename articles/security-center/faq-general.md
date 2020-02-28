---
title: Azure Security Center FAQ - Questões gerais
description: Perguntas gerais frequentes sobre o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças
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
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661844"
---
# <a name="faq---general-questions-about-azure-security-center"></a>FAQ - Perguntas gerais sobre o Centro de Segurança Azure

## <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Azure Security Center ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Security Center utiliza o Agente de Monitorização da Microsoft para recolher e armazenar dados. Para mais detalhes, consulte a recolha de dados no Centro de [Segurança Azure.](security-center-enable-data-collection.md)


## <a name="how-do-i-get-azure-security-center"></a>Como obtenho o Centro de segurança do Azure?
O Azure Security Center está ativado com a subscrição do Microsoft Azure e acedido a partir do [portal Azure](https://azure.microsoft.com/features/azure-portal/). Para aceder ao [mesmo, inscreva-se no portal,](https://portal.azure.com)selecione **Navegar**e percorra o Centro de **Segurança.**


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorizados pelo centro de segurança do Azure?
Centro de segurança do Azure monitoriza os seguintes recursos do Azure:

* Máquinas virtuais (VMs) (incluindo serviços em [nuvem)](../cloud-services/cloud-services-choose-me.md)
* Conjuntos de dimensionamento de máquinas virtuais
* Redes Virtuais do Azure
* Contentores
* Serviço SQL do Azure
* Conta de armazenamento do Azure
* Aplicativos Web Azure (em Ambiente de Serviço de [Aplicações)](../app-service/environment/intro.md)
* Soluções de parceiro integradas com a sua subscrição do Azure, como uma firewall de aplicações web em VMs e no ambiente de serviço de aplicações

Além disso, as máquinas não Azure (incluindo no local) também podem ser monitorizadas pelo Azure Security Center. Ambas as [máquinas Windows](./quick-onboard-windows-computer.md) e [Linux](./quick-onboard-linux-computer.md) são suportadas.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos do Azure?
A página **de visão geral** do Centro de Segurança mostra a postura de segurança geral do seu ambiente desfeito por Compute, Networking, Storage & datas e Aplicações. Cada tipo de recurso tem um indicador que mostra vulnerabilidades de segurança identificadas. Clicar em cada mosaico apresenta uma lista de problemas de segurança identificadas pelo centro de segurança, juntamente com um inventário dos recursos na sua subscrição.



## <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada. No Centro de segurança do Azure, é possível definir políticas para as suas subscrições do Azure, de acordo com requisitos de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

As políticas de segurança ativadas no monitorização e recomendações de segurança de unidade de centro de segurança do Azure. Para saber mais sobre as políticas de segurança, consulte a monitorização da [saúde de segurança no Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, tem de ser um administrador de segurança ou um proprietário ou contribuinte dessa subscrição.

Para aprender a configurar uma política de segurança, consulte definições de políticas de [segurança no Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
O Centro de Segurança do Azure analisa o estado de segurança dos recursos do Azure. Quando são identificadas potenciais vulnerabilidades de segurança, recomendações são criadas. As recomendações orientam-no durante o processo de configuração o controle necessário. Os exemplos são:

* Aprovisionamento de antimalware para ajudar a identificar e remover software malicioso
* [Grupos](../virtual-network/security-overview.md) de segurança da rede e regras para controlar o tráfego para máquinas virtuais
* Aprovisionamento de uma firewall de aplicações web para ajudar na defesa contra ataques que visam as suas aplicações web
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Apenas as recomendações que estão ativadas nas políticas de segurança são mostradas aqui.



## <a name="what-triggers-a-security-alert"></a>O que aciona um alerta de segurança?
Centro de segurança do Azure automaticamente recolhe, analisa e funde dados de registo de recursos do Azure, rede e soluções de parceiros como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Software maligno avançado detetado com o relatório de erros do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções de segurança de parceiros integradas, como o anti-malware ou Firewalls de aplicações Web


## Porque é que os valores do Secure Score mudaram? <a name="secure-score-faq"></a>
A partir de fevereiro de 2019, o Security Center ajustou a pontuação de algumas recomendações, de forma a melhor se adaptar à sua gravidade. Como resultado deste ajuste, pode haver alterações nos valores globais da Pontuação Segura.  Para obter mais informações sobre secure score, consulte [o cálculo Secure Score](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>O que é a diferença entre ameaças detetados e alertados pelo Microsoft Security Response Center em comparação com o Centro de segurança do Azure?
O Microsoft Security Response Center (MSRC) executa a segurança selecione monitorização da rede do Azure e infraestrutura e recebe a reclamações de abuso e inteligência de ameaças de terceiros. Quando o MSRC fica sabendo que os dados do cliente foram acedidos por uma entidade não autorizada ou ilícita ou que utilização do cliente do Azure não estão em conformidade com os termos para utilizar aceitável, um Gestor de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre ao enviar um e-mail para os contactos de segurança especificados no Centro de segurança do Azure ou o proprietário da subscrição do Azure, se não for especificado um contacto de segurança.

Centro de segurança é um serviço do Azure que monitoriza o ambiente do Azure do cliente e aplica-se a análise para detetar automaticamente uma vasta gama de atividades maliciosas potencialmente continuamente. Estas deteções são apresentadas como alertas de segurança no dashboard do Centro de segurança.