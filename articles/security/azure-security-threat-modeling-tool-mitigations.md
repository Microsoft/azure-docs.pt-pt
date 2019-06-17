---
title: Atenuações - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: Página de atenuações para a Microsoft Threat Modeling Tool realçar possíveis soluções para a mais exposta gerada ameaças.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611326"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Atenuações de ferramenta de modelagem de ameaças na Microsoft

A ferramenta de modelagem de ameaças é um elemento central do Microsoft Security Development Lifecycle (SDL). Ele permite que arquitetos de software identificar e mitigar potenciais problemas de segurança no início, quando são relativamente fácil e económica para resolver. Como resultado, reduz consideravelmente o custo total de desenvolvimento. Além disso, projetamos a ferramenta de com não-especialistas em segurança em mente, facilitando a Modelagem de ameaças para todos os desenvolvedores ao fornecer uma orientação clara sobre como criar e analisar os modelos de ameaças.

Visite o **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** para começar já hoje!

## <a name="mitigation-categories"></a>Categorias de atenuação

As atenuações de ferramenta de modelagem de ameaças são categorizadas, de acordo com o quadro de segurança de aplicação Web, que consiste no seguinte:

| Category | Descrição |
| -------- | ----------- |
| **[Auditoria e registo](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Quem fez o quê e quando? Registo de auditoria e veja como o seu aplicativo registra os eventos relacionados à segurança |
| **[Autenticação](./azure-security-threat-modeling-tool-authentication.md)** | Quem é? A autenticação é o processo em que uma entidade comprova a identidade de outra entidade, normalmente através de credenciais, tal como um nome de utilizador e palavra-passe |
| **[Autorização](./azure-security-threat-modeling-tool-authorization.md)** | O que fazer? Autorização é como o seu aplicativo fornece controlos de acesso para recursos e operações |
| **[Segurança da comunicação](./azure-security-threat-modeling-tool-communication-security.md)** | Que estão falando para? Segurança da comunicação garante que todas as comunicações feita são tão segura quanto possível |
| **[Gestão de configuração](./azure-security-threat-modeling-tool-configuration-management.md)** | Quem executar seu aplicativo como? Quais bancos de dados que estabelecer ligação ao? Como é que o seu aplicativo é administrado? Como são protegidas estas definições? Gestão de configuração que se refere a como seu aplicativo lida com esses problemas operacionais |
| **[Cryptography](./azure-security-threat-modeling-tool-cryptography.md)** | Como mantendo segredos (confidencialidade)? Como está protegendo contra adulteração seus dados ou bibliotecas (integridade)? Como está a fornecer sementes para valores aleatórios que têm de ser criptograficamente fortes? Criptografia se refere à forma como o seu aplicativo impõe a confidencialidade e integridade |
| **[Gestão de exceções](./azure-security-threat-modeling-tool-exception-management.md)** | Quando uma chamada de método em seu aplicativo falha, o que faz seu aplicativo? Quanto revelar? Devolve informações de erro amigável aos utilizadores finais? Passar informações valiosas de exceção para o chamador? Seu aplicativo falhar corretamente? |
| **[Validação de entrada](./azure-security-threat-modeling-tool-input-validation.md)** | Como sabe que o seu aplicativo receba a entrada é válido e seguro? Validação de entrada se refere à forma como seu aplicativo filtra, scrubs ou rejeita a entrada antes de processamento adicional. Considere a restrição de entrada por meio de pontos de entrada e a codificação de saída por meio de pontos de saída. Confia dados a partir de origens, como bases de dados e partilhas de ficheiros? |
| **[Dados confidenciais](./azure-security-threat-modeling-tool-sensitive-data.md)** | Como é que o seu aplicativo com dados confidenciais? Dados confidenciais que se refere a como seu aplicativo lida com todos os dados que têm de ser protegidos na memória, através da rede, ou nos arquivos persistentes |
| **[Gerenciamento de sessões](./azure-security-threat-modeling-tool-session-management.md)** | Como seu aplicativo processar e proteger as sessões de utilizador? Uma sessão refere-se a uma série de relacionados interações entre um utilizador e a sua aplicação Web |

Isto ajuda a identificar:

* Em que são feitos os erros mais comuns
* Onde estão os aprimoramentos mais passíveis de ação

Como resultado, pode utiliza estas categorias concentrar-se e priorizar seu trabalho de segurança, para que se conhece que os problemas de segurança mais predominantes ocorrerem nas categorias de autorização, autenticação e validação de entrada, pode começar por aqui. Para obter mais informações, visite  **[esta ligação de patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passos Seguintes

Visite **[ameaças de ferramenta de modelagem de ameaças](./azure-security-threat-modeling-tool-threats.md)** para saber mais sobre as categorias de ameaças, a ferramenta usa para gerar a ameaças de design possíveis.