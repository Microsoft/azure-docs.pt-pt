---
title: Permissões no Centro de Segurança Azure | Microsoft Docs
description: Este artigo explica como o Azure Security Center usa o controlo de acesso baseado em funções para atribuir permissões aos utilizadores e identifica as ações permitidas para cada função.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443765"
---
# <a name="permissions-in-azure-security-center"></a>Permissions in Azure Security Center (Permissões no Centro de Segurança do Azure)

O Azure Security Center utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC),](../role-based-access-control/role-assignments-portal.md)que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

O Centro de Segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Security Center, só vê informações relacionadas com um recurso quando lhe é atribuída a função de Proprietário, Colaborador ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de Segurança**: Um utilizador que pertence a esta função tem direitos de visualização para o Centro de Segurança. O utilizador pode ver recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações.
* **Administrador de Segurança**: Um utilizador que pertença a esta função tem os mesmos direitos que o Leitor de Segurança e também pode atualizar a política de segurança e rejeitar alertas e recomendações.

> [!NOTE]
> As funções de segurança, Security Reader e Security Admin, têm acesso apenas no Centro de Segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure, tais como Armazenamento, Web & Mobile ou Internet das Coisas.
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela que se segue exibe funções e permitiu ações no Centro de Segurança.

| Ação                                                                                                                                        | Leitor de Segurança / <br> Leitor | Administrador de Segurança | Contribuinte do Grupo de Recursos / <br> Proprietário do Grupo de Recursos | Colaborador de Assinatura | Proprietário de Assinatura |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Editar a política de segurança                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Aduse/atribuir iniciativas (incluindo) normas de conformidade regulamentar)                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Ativar / desativar o Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Ativar /desativar o fornecimento automático                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Aplicar recomendações de segurança para um recurso</br> (e use [Quick Fix!](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Alertas de dispensa                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Ver alertas e recomendações                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua o papel de Leitor a utilizadores que apenas precisam de visualizar informações sobre a saúde de segurança de um recurso, mas não tomem medidas, tais como a aplicação de recomendações ou políticas de edição.
>
>

## <a name="next-steps"></a>Passos seguintes
Este artigo explicou como o Security Center usa o Azure RBAC para atribuir permissões aos utilizadores e identificou as ações permitidas para cada função. Agora que está familiarizado com as atribuições de papel necessárias para monitorizar o estado de segurança da sua subscrição, editar políticas de segurança e aplicar recomendações, aprender a:

- [Definir políticas de segurança no Centro de Segurança](tutorial-security-policy.md)
- [Gerir recomendações de segurança no Centro de Segurança](security-center-recommendations.md)
- [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
- [Monitorizar soluções de segurança dos parceiros](./security-center-partner-integration.md)