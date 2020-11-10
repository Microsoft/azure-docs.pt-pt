---
title: Design de identidade híbrida - requisitos de gestão de conteúdos Azure / Microsoft Docs
description: Fornece informações sobre como determinar os requisitos de gestão de conteúdos do seu negócio. Normalmente, quando um utilizador tem o seu próprio dispositivo, também pode ter múltiplas credenciais que serão alternadas de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo criado usando credenciais pessoais contra as criadas com recurso a credenciais corporativas. A sua solução de identidade deve ser capaz de interagir com os serviços na nuvem para proporcionar uma experiência perfeita ao utilizador final, garantindo a sua privacidade e aumentando a proteção contra fugas de dados.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408537"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de gestão de conteúdos para a sua solução de identidade híbrida
Compreender os requisitos de gestão de conteúdos para o seu negócio pode afetar diretamente a sua decisão sobre qual a solução de identidade híbrida a utilizar. Com a proliferação de vários dispositivos e a capacidade dos utilizadores de apresentarem os seus próprios dispositivos[(BYOD),](/mem/intune/fundamentals/byod-technology-decisions)a empresa deve proteger os seus próprios dados, mas também deve manter intacta a privacidade dos utilizadores. Normalmente, quando um utilizador tem o seu próprio dispositivo, também pode ter múltiplas credenciais que serão alternadas de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo criado usando credenciais pessoais contra as criadas com recurso a credenciais corporativas. A sua solução de identidade deve ser capaz de interagir com os serviços na nuvem para proporcionar uma experiência perfeita ao utilizador final, garantindo a sua privacidade e aumentando a proteção contra fugas de dados. 

A sua solução de identidade será alavancada por diferentes controlos técnicos, de forma a fornecer a gestão de conteúdos, tal como indicado na figura abaixo:

![controlos de segurança](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Controlos de segurança que irão alavancar o seu sistema de gestão de identidade**

Em geral, os requisitos de gestão de conteúdos irão alavancar o seu sistema de gestão de identidade nas seguintes áreas:

* Privacidade: identificar o utilizador que detém um recurso e aplicar os controlos adequados para manter a integridade.
* Classificação de Dados: identificar o utilizador ou grupo e o nível de acesso a um objeto de acordo com a sua classificação. 
* Proteção de fugas de dados: os controlos de segurança responsáveis pela proteção de dados para evitar fugas terão de interagir com o sistema de identidade para validar a identidade do utilizador. Isto também é importante para a auditoria do fim do trail.

> [!NOTE]
> Leia [a classificação de dados para a prontidão na nuvem](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre as melhores práticas e orientações para a classificação de dados.
> 
> 

Ao planear a sua solução de identidade híbrida, certifique-se de que as seguintes questões são respondidas de acordo com os requisitos da sua organização:

* A sua empresa dispõe de controlos de segurança para impor a privacidade dos dados?
  * Se sim, os controlos de segurança poderão integrar-se com a solução de identidade híbrida que vai adotar?
* A sua empresa utiliza a classificação de dados?
  * Se sim, a solução atual é capaz de se integrar com a solução de identidade híbrida que vai adotar?
* A sua empresa tem atualmente alguma solução para fugas de dados? 
  * Se sim, a solução atual é capaz de se integrar com a solução de identidade híbrida que vai adotar?
* A sua empresa precisa de auditar o acesso aos recursos?
  * Se sim, que tipo de recursos?
  * Se sim, que nível de informação é necessário?
  * Se sim, onde deve residir o registo de auditoria? No local ou na nuvem?
* A sua empresa precisa de encriptar quaisquer e-mails que contenham dados sensíveis (SSNs, números de cartões de crédito, etc.)?
* A sua empresa precisa de encriptar todos os documentos/conteúdos partilhados com parceiros de negócios externos?
* A sua empresa precisa de impor políticas corporativas em determinados tipos de e-mails (não responda a todos, não encaminha)?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a Estratégia de Proteção de Dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) irá sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às necessidades do seu negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos do controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)