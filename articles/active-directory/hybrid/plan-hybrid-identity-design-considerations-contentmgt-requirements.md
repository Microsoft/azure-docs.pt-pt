---
title: Design de identidade híbrida - requisitos de gestão de conteúdos do Azure | Documentos da Microsoft
description: Fornece informações sobre como determinar os requisitos de gestão de conteúdos da sua empresa. Normalmente, quando um utilizador tem seu próprio dispositivo, poderá também têm várias credenciais que serão alternadas, de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo que foi criado com as credenciais pessoais em comparação com aqueles criados com as credenciais da empresa. Sua solução de identidade deve ser capaz de interagir com a nuvem serviços para fornecer uma experiência totalmente integrada para o utilizador final ao mesmo tempo, certifique-se a sua privacidade e aumentar a proteção contra fugas de dados.
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
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918446"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de gestão de conteúdos para a sua solução de identidade híbrida
Compreender os requisitos de gestão de conteúdos para a sua empresa direto pode afetar sua decisão sobre qual solução de identidade híbrida para utilizar. Com a proliferação de vários dispositivos e a capacidade dos usuários para trazer os seus próprios dispositivos ([BYOD](https://aka.ms/byodcg)), a empresa precisa proteger os seus próprios dados, mas ele também deve manter privacidade do utilizador intactos. Normalmente, quando um utilizador tem seu próprio dispositivo, poderá também têm várias credenciais que serão alternadas, de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo que foi criado com as credenciais pessoais em comparação com aqueles criados com as credenciais da empresa. Sua solução de identidade deve ser capaz de interagir com a nuvem serviços para fornecer uma experiência totalmente integrada para o utilizador final ao mesmo tempo, certifique-se a sua privacidade e aumentar a proteção contra fugas de dados. 

Sua solução de identidade irá ser aproveitada pelas diferentes controlos técnicos para fornecer gestão de conteúdos, conforme mostrado na figura abaixo:

![Controlos de segurança](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Controlos de segurança que irão ser tirar partido do seu sistema de gerenciamento de identidade**

Em geral, os requisitos de gestão de conteúdos irão tirar partido do seu sistema de gerenciamento de identidade nas seguintes áreas:

* Privacidade: a identificação de usuário que possui um recurso e aplicar os controlos adequados para manter a integridade.
* Classificação de dados: identifica o utilizador ou grupo e o nível de acesso a um objeto, de acordo com sua classificação. 
* Proteção de fuga de dados: responsáveis pela proteção de dados para evitar a fuga de controlos de segurança tem de interagir com o sistema de identidade para validar a identidade do utilizador. Isso também é importante para fins de trilha de auditoria.

> [!NOTE]
> Leia [classificação de dados para preparação da cloud](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre melhores práticas e diretrizes para a classificação de dados.
> 
> 

Quando planear a sua solução de identidade híbrida Certifique-se de que as seguintes perguntas serão respondidas, de acordo com os requisitos da sua organização:

* A sua empresa tem controlos de segurança em vigor para impor a privacidade dos dados?
  * Se Sim, os controlos de segurança será possível integrar a solução de identidade híbrida que vai para adotar?
* A sua empresa utiliza a classificação de dados?
  * Se Sim, é a solução atual consegue integrar com a solução de identidade híbrida que vai para adotar?
* A sua empresa tem atualmente qualquer solução de fuga de dados? 
  * Se Sim, é a solução atual consegue integrar com a solução de identidade híbrida que vai para adotar?
* A sua empresa precisa auditar o acesso aos recursos?
  * Se Sim, o tipo de recursos?
  * Em caso afirmativo, qual o nível de informações é necessário?
  * Se Sim, em que o registo de auditoria têm de residir? No local ou na cloud?
* A sua empresa precisa encriptar e-mails que contêm dados confidenciais (números da segurança social, números de cartão de crédito, etc.)?
* A sua empresa precisa encriptar todos os documentos/conteúdos partilhados com parceiros comerciais externos?
* A sua empresa precisa de impor diretivas corporativas em determinados tipos de mensagens de e-mail (fazer não responder a todos, não reencaminhar)?

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a essas questões, vai selecionar qual opção melhor se adequa aos seu negócio precisa.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Determinar os requisitos de controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

