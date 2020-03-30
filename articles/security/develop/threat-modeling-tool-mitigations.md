---
title: Mitigações - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
description: Página de mitigações para a Ferramenta de Modelação de Ameaças da Microsoft destacando possíveis soluções para as ameaças geradas mais expostas.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728035"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigações de ferramentas de modelação de ameaças da Microsoft

A Ferramenta de Modelação de Ameaças é um elemento central do Ciclo de Vida de Desenvolvimento de Segurança da Microsoft (SDL). Permite que os arquitetos de software identifiquem e mitiem potenciais problemas de segurança mais cedo, quando são relativamente fáceis e rentáveis para resolver. Como resultado, reduz consideravelmente o custo total do desenvolvimento. Além disso, desenhamos a ferramenta com especialistas não-de-segurança em mente, tornando a modelação de ameaças mais fácil para todos os desenvolvedores, fornecendo orientações claras sobre a criação e análise de modelos de ameaças.

Visite a Ferramenta de **[Modelação de Ameaças](threat-modeling-tool.md)** para começar hoje!

## <a name="mitigation-categories"></a>Categorias de mitigação

As mitigações da ferramenta de modelação de ameaças são categorizadas de acordo com o Quadro de Segurança da Aplicação Web, que consiste no seguinte:

| Categoria | Descrição |
| -------- | ----------- |
| **[Auditoria e Exploração Madeireira](threat-modeling-tool-auditing-and-logging.md)** | Quem fez o quê e quando? Auditoria e registo de registo referem-se à forma como a sua aplicação regista eventos relacionados com a segurança |
| **[Autenticação](threat-modeling-tool-authentication.md)** | Quem és? A autenticação é o processo em que uma entidade prova a identidade de outra entidade, tipicamente através de credenciais, como um nome de utilizador e uma senha |
| **[Autorização](threat-modeling-tool-authorization.md)** | O que é que se pode fazer? Autorização é como a sua aplicação fornece controlos de acesso para recursos e operações |
| **[Segurança da Comunicação](threat-modeling-tool-communication-security.md)** | Com quem está a falar? A Segurança da Comunicação garante que todas as comunicações feitas são o mais seguras possível |
| **[Gestão de Configuração](threat-modeling-tool-configuration-management.md)** | Quem é que a sua candidatura funciona? A que bases de dados se liga? Como é administrada a sua candidatura? Como estão as definições seguras? A gestão da configuração refere-se à forma como a sua aplicação lida com estas questões operacionais |
| **[Criptografia](threat-modeling-tool-cryptography.md)** | Como está a guardar segredos (confidencialidade)? Como está a impermeabilização dos seus dados ou bibliotecas (integridade)? Como está a fornecer sementes para valores aleatórios que devem ser criptograficamente fortes? Criptografia refere-se à forma como a sua aplicação impõe confidencialidade e integridade |
| **[Gestão de Exceção](threat-modeling-tool-exception-management.md)** | Quando uma chamada de método na sua aplicação falha, o que faz a sua aplicação? Quanto revela? Devolve informações de erro amigáveis aos utilizadores finais? Passa informações valiosas de exceção ao chamador? A sua candidatura falha graciosamente? |
| **[Validação de Entrada](threat-modeling-tool-input-validation.md)** | Como sabe que a entrada que a sua aplicação recebe é válida e segura? A validação da entrada refere-se à forma como a sua aplicação filtra, esfrega ou rejeita a entrada antes do processamento adicional. Considere limitar a entrada através de pontos de entrada e codificar a saída através de pontos de saída. Confia em dados de fontes como bases de dados e partilhas de ficheiros? |
| **[Dados Sensíveis](threat-modeling-tool-sensitive-data.md)** | Como é que a sua aplicação lida com dados sensíveis? Dados sensíveis referem-se à forma como a sua aplicação lida com quaisquer dados que devem ser protegidos na memória, através da rede ou em lojas persistentes |
| **[Gestão de Sessões](threat-modeling-tool-session-management.md)** | Como é que a sua aplicação lida e protege as sessões de utilizador? Uma sessão refere-se a uma série de interações relacionadas entre um utilizador e a sua aplicação Web |

Isto ajuda-o a identificar:

* Onde estão os erros mais comuns cometidos
* Onde estão as melhorias mais acionáveis

Como resultado, você usa estas categorias para focar e priorizar o seu trabalho de segurança, de modo que se você sabe que os problemas de segurança mais prevalentes ocorrem nas categorias de validação de entrada, autenticação e autorização, você pode começar por lá. Para mais informações visite ** [este link de patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passos seguintes

Visite ameaças de ferramentas de **[modelação](threat-modeling-tool-threats.md)** de ameaças para saber mais sobre as categorias de ameaça que a ferramenta utiliza para gerar possíveis ameaças de design.