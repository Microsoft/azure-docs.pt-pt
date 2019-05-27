---
title: Design de identidade híbrida - requisitos de resposta a incidentes do Azure | Documentos da Microsoft
description: Determinar as capacidades de monitorização e de relatórios para a solução de identidade híbrida que podem ser usados pelo departamento de TI tomar medidas para identificar e atenuar um potenciais ameaças
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
ms.openlocfilehash: 08bf7950ada5db90d2b8bfea751b39ffc21f3ee9
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950845"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de resposta a incidentes para a sua solução de identidade híbrida
Grandes e médias organizações provavelmente terá um [resposta a incidentes de segurança](https://technet.microsoft.com/library/cc700825.aspx) para ajudar a IT agir em conformidade para o nível de incidente. O sistema de gestão de identidade é um componente importante do processo de resposta a incidentes, porque este pode ser utilizado para o ajudar a identificar quem realizou uma ação específica contra o destino. A solução de identidade híbrida tem de ser capaz de fornecer capacidades de monitorização e relatórios que podem ser usadas pelo departamento de TI tomar medidas para identificar e mitigar uma ameaça potencial. Um plano de resposta a incidentes típico terão as seguintes fases como parte do plano:

1. Avaliação inicial.
2. Comunicação de incidentes.
3. Controle de danos e redução de riscos.
4. Identificação do que era compromisso e a gravidade.
5. Preservação de evidências.
6. Notificação às partes apropriadas.
7. Recuperação do sistema.
8. Documentação.
9. Avaliação de danos e os custos.
10. Revisão do plano e de processo.

Durante a identificação do que era o compromisso e a fase de gravidade, será necessário identificar os sistemas que tenham sido comprometidos, ficheiros que foram acedidos e determinam a sensibilidade desses ficheiros. O sistema de identidade híbrida deve ser capaz de satisfazer estes requisitos para ajudar a identificar o utilizador que efetuou essas alterações. 

## <a name="monitoring-and-reporting"></a>Monitorização e relatórios
Número de vezes que o sistema de identidade também pode ajudar na fase de avaliação inicial, principalmente se o sistema criou de auditoria e dos recursos de relatórios. Durante a avaliação inicial, administrador de TI tem de ser capaz de identificar uma atividade suspeita ou o sistema deve ser capaz de Acionador automaticamente com base numa tarefa previamente configurada. Muitas atividades poderiam indicar um possível ataque, no entanto, em outros casos, um sistema mal configurado pode acarretar um número de falsos positivos num sistema de detecção de intrusão. 

O sistema de gestão de identidade deve ajudar os administradores de TI para identificar e relatar essas atividades suspeitas. Normalmente, estes requisitos técnicos podem ser cumpridos, todos os sistemas de monitorização e ter uma capacidade de relatórios que pode destacar a potenciais ameaças. Utilize as perguntas abaixo para o ajudar a sua solução de identidade híbrida ao levando-se em requisitos de resposta a incidentes de consideração de design:

* A sua empresa tem uma resposta a incidentes de segurança no local?
  * Se Sim, é o sistema de gestão de identidade atual utilizado como parte do processo?
* A sua empresa precisa identificar tentativas de início de sessão suspeitas de usuários em diferentes dispositivos?
* A sua empresa precisa detetar potenciais comprometido as credenciais do utilizador?
* A sua empresa precisa para fazer a auditoria de acesso e a ação do utilizador?
* A sua empresa precisa de saber quando um utilizador repõe a palavra-passe?

## <a name="policy-enforcement"></a>Aplicação da política
Durante o controle de danos e a fase de redução de risco, é importante reduzir os efeitos reais e potenciais de um ataque rapidamente. Neste momento, essa ação que irá efetuar pode fazer a diferença entre pequenas e grandes proporções. A resposta exata dependerá-se a sua organização e a natureza do ataque enfrentado. Se a avaliação inicial concluíram que uma conta foi comprometida, será necessário para impor a política para bloquear esta conta. Isso é apenas um exemplo onde o sistema de gestão de identidade irá ser aproveitado. Utilize as perguntas abaixo para criar a sua solução de identidade híbrida ao mesmo tempo que em consideração a forma como serão aplicadas políticas de reagir a um incidente em curso:

* A sua empresa tem políticas in-loco para impedir que os utilizadores de acesso a rede se necessário?
  * Se Sim, a atual solução de integrar o sistema de gestão de identidade híbrida que vai para adotar?
* A sua empresa precisa de impor o acesso condicional para utilizadores que estão em quarentena? 

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a essas questões, vai selecionar qual opção melhor se adequa aos seu negócio precisa.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

