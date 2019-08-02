---
title: Mitigações-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: A página de mitigações para o Microsoft Threat Modeling Tool realçando as possíveis soluções para as ameaças geradas mais expostas.
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728035"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool atenuações

O Threat Modeling Tool é um dos principais elementos do SDL (Microsoft Security Development Lifecycle). Ele permite que os arquitetos de software identifiquem e reduzam possíveis problemas de segurança no início, quando são relativamente fáceis e econômicos de resolver. Como resultado, ele reduz consideravelmente o custo total de desenvolvimento. Além disso, projetamos a ferramenta com especialistas não relacionados à segurança em mente, tornando a modelagem de ameaças mais fácil para todos os desenvolvedores, fornecendo orientação clara sobre como criar e analisar modelos de ameaças.

Visite o **[Threat Modeling Tool](threat-modeling-tool.md)** para começar hoje mesmo!

## <a name="mitigation-categories"></a>Categorias de mitigação

As atenuações Threat Modeling Tool são categorizadas de acordo com o quadro de segurança do aplicativo Web, que consiste no seguinte:

| Category | Descrição |
| -------- | ----------- |
| **[Auditoria e registro em log](threat-modeling-tool-auditing-and-logging.md)** | Quem fez o quê e quando? Auditoria e registro em log referem-se a como seu aplicativo registra eventos relacionados à segurança |
| **[Authentication](threat-modeling-tool-authentication.md)** | Quem é? A autenticação é o processo em que uma entidade comprova a identidade de outra entidade, normalmente por meio de credenciais, como um nome de usuário e senha |
| **[Nesse](threat-modeling-tool-authorization.md)** | O que fazer? A autorização é como seu aplicativo fornece controles de acesso para recursos e operações |
| **[Segurança de comunicação](threat-modeling-tool-communication-security.md)** | Com quem você está falando? A segurança de comunicação garante que toda a comunicação feita seja o mais segura possível |
| **[Gerenciamento de configuração](threat-modeling-tool-configuration-management.md)** | Com quem seu aplicativo é executado? A quais bancos de dados ele se conecta? Como seu aplicativo é administrado? Como essas configurações são protegidas? Gerenciamento de configuração refere-se a como seu aplicativo lida com esses problemas operacionais |
| **[Criptografia](threat-modeling-tool-cryptography.md)** | Como você mantém os segredos (confidencialidade)? Como você está à prova de adulteração de seus dados ou bibliotecas (integridade)? Como você está fornecendo sementes para valores aleatórios que devem ser criptograficamente fortes? Criptografia refere-se a como seu aplicativo impõe confidencialidade e integridade |
| **[Gerenciamento de exceções](threat-modeling-tool-exception-management.md)** | Quando uma chamada de método em seu aplicativo falha, o que o aplicativo faz? Quanto você revela? Você retorna informações de erro amigáveis aos usuários finais? Você passa informações de exceção valiosas de volta para o chamador? Seu aplicativo falha normalmente? |
| **[Validação de entrada](threat-modeling-tool-input-validation.md)** | Como você sabe que a entrada que seu aplicativo recebe é válida e segura? A validação de entrada refere-se a como o aplicativo filtra, faz a limpeza ou rejeita a entrada antes do processamento adicional. Considere restringir a entrada por meio de pontos de entrada e saída de codificação por meio de pontos de saída. Você confia em dados de fontes como bancos de dados e compartilhamentos de arquivos? |
| **[Dados confidenciais](threat-modeling-tool-sensitive-data.md)** | Como seu aplicativo lida com dados confidenciais? Dados confidenciais referem-se a como seu aplicativo lida com todos os dados que devem ser protegidos na memória, pela rede ou em armazenamentos persistentes |
| **[Gerenciamento de sessão](threat-modeling-tool-session-management.md)** | Como o aplicativo lida e protege as sessões de usuário? Uma sessão refere-se a uma série de interações relacionadas entre um usuário e seu aplicativo Web |

Isso ajuda a identificar:

* Onde estão os erros mais comuns feitos
* Onde estão os aprimoramentos mais acionáveis

Como resultado, você usa essas categorias para se concentrar e priorizar seu trabalho de segurança, de modo que, se você souber que os problemas de segurança mais predominantes ocorrem nas categorias validação de entrada, autenticação e autorização, poderá iniciar lá. Para obter mais informações, visite  **[este link de patente](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passos seguintes

Visite **[Threat Modeling Tool ameaças](threat-modeling-tool-threats.md)** para saber mais sobre as categorias de ameaças que a ferramenta usa para gerar possíveis ameaças de design.