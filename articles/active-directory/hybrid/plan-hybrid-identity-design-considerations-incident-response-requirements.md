---
title: Design de identidade híbrida - requisitos de resposta a incidentes Azure [ Microsoft Docs
description: Determine capacidades de monitorização e reporte para a solução de identidade híbrida que pode ser alavancada pela TI para tomar ações para identificar e mitigar potenciais ameaças
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109281"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de resposta a incidentes para a sua solução de identidade híbrida
As organizações grandes ou médias provavelmente terão uma resposta de incidente de [segurança](https://technet.microsoft.com/library/cc700825.aspx) no local para ajudar a TI a tomar medidas em conformidade com o nível de incidente. O sistema de gestão de identidade é um componente importante no processo de resposta a incidentes porque pode ser usado para ajudar a identificar quem realizou uma ação específica contra o alvo. A solução de identidade híbrida deve ser capaz de fornecer capacidades de monitorização e reporte que possam ser alavancadas pela TI para tomar ações para identificar e mitigar uma ameaça potencial. Num plano típico de resposta a incidentes terá as seguintes fases como parte do plano:

1. Avaliação inicial.
2. Comunicação de incidentes.
3. Controlo de danos e redução de risco.
4. Identificação do que era compromisso e gravidade.
5. Preservação de provas.
6. Notificação às partes apropriadas.
7. Recuperação do sistema.
8. Documentação.
9. Avaliação de danos e custos.
10. Processar e planear a revisão.

Durante a identificação do que foi a fase de compromisso e gravidade, será necessário identificar os sistemas que foram comprometidos, ficheiros que foram acedidos e determinar a sensibilidade desses ficheiros. O seu sistema de identidade híbrida deve ser capaz de cumprir estes requisitos para o ajudar a identificar o utilizador que efez essas alterações. 

## <a name="monitoring-and-reporting"></a>Monitorização e relatórios
Muitas vezes o sistema de identidade também pode ajudar na fase inicial de avaliação principalmente se o sistema tiver construído em capacidades de auditoria e reporte. Durante a avaliação inicial, a Administração de TI deve ser capaz de identificar uma atividade suspeita, ou o sistema deve ser capaz de desencadeá-la automaticamente com base numa tarefa pré-configurada. Muitas atividades podem indicar um possível ataque, no entanto, noutros casos, um sistema mal configurado pode levar a uma série de falsos positivos num sistema de deteção de intrusões. 

O sistema de gestão de identidade deve ajudar os administradores de TI a identificar e reportar essas atividades suspeitas. Normalmente, estes requisitos técnicos podem ser cumpridos através da monitorização de todos os sistemas e de uma capacidade de reporte que pode destacar potenciais ameaças. Use as perguntas abaixo para ajudá-lo a projetar a sua solução de identidade híbrida, tendo em consideração os requisitos de resposta a incidentes:

* A sua empresa tem uma resposta de incidente de segurança?
  * Se sim, o atual sistema de gestão de identidade é usado como parte do processo?
* A sua empresa precisa de identificar tentativas suspeitas de inscrição de utilizadores em diferentes dispositivos?
* A sua empresa precisa de detetar potenciais credenciais de utilizador comprometidas?
* A sua empresa precisa de auditar o acesso e a ação do utilizador?
* A sua empresa precisa de saber quando um utilizador repõe a sua palavra-passe?

## <a name="policy-enforcement"></a>Aplicação da política
Durante o controlo de danos e a fase de redução de riscos, é importante reduzir rapidamente os efeitos reais e potenciais de um ataque. Essa ação que irá tomar neste momento pode fazer a diferença entre um menor e um dos maiores. A resposta exata dependerá da sua organização e da natureza do ataque que enfrenta. Se a avaliação inicial concluiu que uma conta foi comprometida, terá de impor a política para bloquear esta conta. É apenas um exemplo em que o sistema de gestão de identidades será alavancado. Use as perguntas abaixo para ajudá-lo a projetar a sua solução de identidade híbrida, tendo em conta como as políticas serão aplicadas para reagir a um incidente em curso:

* A sua empresa tem políticas em vigor para bloquear o acesso dos utilizadores à rede, se necessário?
  * Se sim, a atual solução integra-se com o sistema híbrido de gestão de identidade que vai adotar?
* A sua empresa precisa de impor o Acesso Condicional aos utilizadores que estão em quarentena? 

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a estratégia](plan-hybrid-identity-design-considerations-data-protection-strategy.md) de proteção de dados irá passar por cima das opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Definir estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

