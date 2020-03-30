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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661844"
---
# <a name="faq---general-questions-about-azure-security-center"></a>FAQ - Perguntas gerais sobre o Centro de Segurança Azure

## <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Azure Security Center ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Security Center utiliza o Agente de Monitorização da Microsoft para recolher e armazenar dados. Para mais detalhes, consulte a recolha de dados no Centro de [Segurança Azure.](security-center-enable-data-collection.md)


## <a name="how-do-i-get-azure-security-center"></a>Como é que consigo o Centro de Segurança Azure?
O Azure Security Center está ativado com a subscrição do Microsoft Azure e acedido a partir do [portal Azure](https://azure.microsoft.com/features/azure-portal/). Para aceder ao [mesmo, inscreva-se no portal,](https://portal.azure.com)selecione **Navegar**e percorra o Centro de **Segurança.**


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Que recursos azure são monitorizados pelo Azure Security Center?
O Azure Security Center monitoriza os seguintes recursos Azure:

* Máquinas virtuais (VMs) (incluindo serviços em [nuvem)](../cloud-services/cloud-services-choose-me.md)
* Conjuntos de dimensionamento de máquinas virtuais
* Redes Virtuais do Azure
* Contentores
* Serviço Azure SQL
* conta de Armazenamento do Azure
* Aplicativos Web Azure (em Ambiente de Serviço de [Aplicações)](../app-service/environment/intro.md)
* Soluções parceiras integradas com a sua subscrição Azure, como uma firewall de aplicação web em VMs e no App Service Environment

Além disso, as máquinas não Azure (incluindo no local) também podem ser monitorizadas pelo Azure Security Center. Ambas as [máquinas Windows](./quick-onboard-windows-computer.md) e [Linux](./quick-onboard-linux-computer.md) são suportadas.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos Azure?
A página **de visão geral** do Centro de Segurança mostra a postura de segurança geral do seu ambiente desfeito por Dados computacionais, de networking, armazenamento & e aplicações. Cada tipo de recurso tem um indicador que mostra vulnerabilidades de segurança identificadas. Clicar em cada azulejo apresenta uma lista de problemas de segurança identificados pelo Security Center, juntamente com um inventário dos recursos na sua subscrição.



## <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para os recursos dentro da subscrição especificada. No Azure Security Center, define políticas para as suas subscrições Azure de acordo com os requisitos de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

As políticas de segurança ativadas no Azure Security Center impulsionam recomendações de segurança e monitorização. Para saber mais sobre as políticas de segurança, consulte a monitorização da [saúde de segurança no Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, deve ser administrador de segurança ou proprietário ou colaborador dessa subscrição.

Para aprender a configurar uma política de segurança, consulte definições de políticas de [segurança no Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
O Centro de Segurança do Azure analisa o estado de segurança dos recursos do Azure. Quando potenciais vulnerabilidades de segurança são identificadas, recomendações são criadas. As recomendações guiam-no através do processo de configuração do controlo necessário. Os exemplos são:

* Fornecimento de anti-malware para ajudar a identificar e remover software malicioso
* [Grupos](../virtual-network/security-overview.md) de segurança da rede e regras para controlar o tráfego para máquinas virtuais
* Fornecimento de uma firewall de aplicação web para ajudar a defender contra ataques direcionados para as suas aplicações web
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Apenas as recomendações que estão habilitadas nas Políticas de Segurança são apresentadas aqui.



## <a name="what-triggers-a-security-alert"></a>O que desencadeia um alerta de segurança?
O Azure Security Center recolhe, analisa e funde automaticamente dados de registo dos seus recursos Azure, da rede e soluções parceiras como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Malware avançado detetado usando relatórios de erros do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções integradas de segurança de parceiros, tais como Firewalls anti-Malware ou Aplicações Web


## <a name="why-did-secure-score-values-change"></a>Porque é que os valores do Secure Score mudaram? <a name="secure-score-faq"></a>
A partir de fevereiro de 2019, o Security Center ajustou a pontuação de algumas recomendações, de forma a melhor se adaptar à sua gravidade. Como resultado deste ajuste, pode haver alterações nos valores globais da Pontuação Segura.  Para obter mais informações sobre secure score, consulte [o cálculo Secure Score](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detetadas e alertadas pelo Microsoft Security Response Center contra o Azure Security Center?
O Microsoft Security Response Center (MSRC) realiza uma monitorização de segurança selecionada da rede e infraestrutura do Azure e recebe informações de ameaças e queixas de abuso de terceiros. Quando a MSRC tiver conhecimento de que os dados dos clientes foram acedidos por uma parte ilegal ou não autorizada ou que o uso do Azure pelo cliente não cumpre os termos de Utilização Aceitável, um gestor de incidentes de segurança notifica o cliente. A notificação ocorre normalmente enviando um e-mail para os contactos de segurança especificados no Azure Security Center ou no proprietário da subscrição Azure se não for especificado um contacto de segurança.

O Security Center é um serviço Azure que monitoriza continuamente o ambiente Azure do cliente e aplica análises para detetar automaticamente uma vasta gama de atividades potencialmente maliciosas. Estas deteções surgem como alertas de segurança no painel do Centro de Segurança.