---
title: Mitigações - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Página de mitigação da Ferramenta de Modelação de Ameaças da Microsoft destacando possíveis soluções para as ameaças geradas mais expostas.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68728035"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigações da ferramenta de modelação de ameaças da Microsoft

A Ferramenta de Modelação de Ameaças é um elemento central do ciclo de vida do desenvolvimento da segurança da Microsoft (SDL). Permite que os arquitetos de software identifiquem e mitiguem problemas de segurança mais cedo, quando são relativamente fáceis e rentáveis de resolver. Como resultado, reduz consideravelmente o custo total de desenvolvimento. Além disso, concebemos a ferramenta com especialistas em não segurança em mente, tornando a modelação de ameaças mais fácil para todos os desenvolvedores, fornecendo orientações claras sobre a criação e análise de modelos de ameaças.

Visite a **[Ferramenta de Modelação de Ameaças](threat-modeling-tool.md)** para começar hoje!

## <a name="mitigation-categories"></a>Categorias de mitigação

As mitigações da Ferramenta de Modelação de Ameaças são categorizadas de acordo com o Quadro de Segurança da Aplicação Web, que consiste no seguinte:

| Categoria | Descrição |
| -------- | ----------- |
| **[Auditoria e Registo](threat-modeling-tool-auditing-and-logging.md)** | Quem fez o quê e quando? Auditoria e registo referem-se à forma como a sua aplicação regista eventos relacionados com a segurança |
| **[Autenticação](threat-modeling-tool-authentication.md)** | Quem és? A autenticação é o processo em que uma entidade comprova a identidade de outra entidade, tipicamente através de credenciais, como um nome de utilizador e senha |
| **[Autorização](threat-modeling-tool-authorization.md)** | O que é que se pode fazer? A autorização é a forma como a sua aplicação fornece controlos de acesso para recursos e operações |
| **[Segurança de Comunicação](threat-modeling-tool-communication-security.md)** | Com quem está a falar? A Segurança da Comunicação garante que toda a comunicação feita é o mais segura possível |
| **[Gestão da Configuração](threat-modeling-tool-configuration-management.md)** | Quem é que a tua candidatura funciona? A que bases de dados se liga? Como é administrada a sua candidatura? Como estão asseguradas estas definições? A gestão de configuração refere-se à forma como a sua aplicação lida com estes problemas operacionais |
| **[Criptografia](threat-modeling-tool-cryptography.md)** | Como está a guardar segredos (confidencialidade)? Como está a alterar os seus dados ou bibliotecas (integridade)? Como está a fornecer sementes para valores aleatórios que devem ser criptograficamente fortes? Criptografia refere-se à forma como a sua aplicação impõe confidencialidade e integridade |
| **[Gestão de Exceções](threat-modeling-tool-exception-management.md)** | Quando um método de chamada na sua aplicação falha, o que faz a sua aplicação? Quanto revela? Devolve informações de erros amigáveis aos utilizadores finais? Passa informações valiosas de exceção para quem ligou? A sua candidatura falha graciosamente? |
| **[Validação de entrada](threat-modeling-tool-input-validation.md)** | Como sabe que a entrada que a sua candidatura recebe é válida e segura? A validação de entrada refere-se à forma como a sua aplicação filtra, esfrega ou rejeita a entrada antes do processamento adicional. Considere limitar a entrada através de pontos de entrada e codificar a saída através de pontos de saída. Confia em dados de fontes como bases de dados e ações de ficheiros? |
| **[Dados Sensíveis](threat-modeling-tool-sensitive-data.md)** | Como é que a sua aplicação lida com dados sensíveis? Dados sensíveis referem-se à forma como a sua aplicação lida com quaisquer dados que devem ser protegidos na memória, na rede ou nas lojas persistentes |
| **[Gestão de Sessão](threat-modeling-tool-session-management.md)** | Como lida com a sua aplicação e protege as sessões do utilizador? Uma sessão refere-se a uma série de interações relacionadas entre um utilizador e a sua aplicação Web |

Isto ajuda-o a identificar:

* Onde estão os erros mais comuns cometidos
* Onde estão as melhorias mais accuáveis

Como resultado, utiliza estas categorias para focar e priorizar o seu trabalho de segurança, para que se souber que os problemas de segurança mais predominantes ocorrem nas categorias de validação, autenticação e autorização de entrada, pode começar por aí. Para mais informações visite ** [este link de patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passos seguintes

Visite **[ameaças de modelação de ameaças](threat-modeling-tool-threats.md)** para saber mais sobre as categorias de ameaças que a ferramenta utiliza para gerar possíveis ameaças de design.