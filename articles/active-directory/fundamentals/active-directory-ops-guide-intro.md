---
title: Guia de referência de operações do Azure Ative Directory
description: Este guia de referência de operações descreve as verificações e ações que deve tomar para garantir e manter a gestão de identidade e acesso, autenticação, governação e operações
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74535317"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Guia de referência de operações do Azure Ative Directory

Este guia de referência de operações descreve os controlos e ações que deve tomar para proteger e manter as seguintes áreas:

- **[Gestão de identidade e acesso](active-directory-ops-guide-iam.md)** - capacidade de gerir o ciclo de vida das identidades e dos seus direitos.
- **[Gestão de autenticação](active-directory-ops-guide-auth.md)** - capacidade de gerir credenciais, definir experiência de autenticação, atribuição de delegados, medir o uso e definir políticas de acesso baseadas na postura de segurança da empresa.
- **[Governação](active-directory-ops-guide-govern.md)** - capacidade de avaliar e atestar o acesso concedido identidades não privilegiadas e privilegiadas, auditoria e controlo alterações ao ambiente.
- **[Operações](active-directory-ops-guide-ops.md)** - otimizar as operações Azure Ative Directory (Azure AD).

Algumas recomendações aqui podem não ser aplicáveis ao ambiente de todos os clientes, por exemplo, as melhores práticas da AD FS podem não se aplicar se a sua organização usar sincronização de haxixe de palavra-passe.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo. As recomendações podem mudar quando as organizações subscrevem uma licença Azure AD Premium diferente. Por exemplo, o Azure AD Premium P2 incluirá mais recomendações de governação.

## <a name="stakeholders"></a>Partes interessadas

Cada secção deste guia de referência recomenda atribuir às partes interessadas que planeiem e implementem tarefas-chave com sucesso. O quadro que se segue descreve a lista de todas as partes interessadas neste guia:

| Interveniente | Descrição |
| :- | :- |
| Equipa de Operações do IAM | Esta equipa gere a gestão das operações diárias do sistema de Gestão de Identidade e Acesso |
| Equipa de Produtividade | Esta equipa detém e gere as aplicações de produtividade, tais como e-mail, partilha de ficheiros e colaboração, mensagens instantâneas e conferências. |
| Proprietário de aplicação | Esta equipa é dona da aplicação específica de um negócio e geralmente de uma perspetiva técnica numa organização. |
| Equipa de Arquitetura InfoSec | Esta equipa planeia e projeta as práticas de Segurança da Informação de uma organização. |
| Equipa de Operações InfoSec | Esta equipa executa e monitoriza as práticas implementadas de Segurança da Informação da equipa de Arquitetura InfoSec. |

## <a name="next-steps"></a>Passos seguintes

Começar com os [controlos e ações de gestão de identidade e acesso.](active-directory-ops-guide-iam.md)
