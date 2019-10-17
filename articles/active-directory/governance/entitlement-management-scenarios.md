---
title: Cenários comuns no gerenciamento de direitos do Azure AD (versão prévia)-Azure Active Directory
description: Conheça as etapas de alto nível que devem ser seguidas para cenários comuns no gerenciamento de direitos Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81382ebecdff6c7b146386b3ae2b0768a7c834bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389070"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Cenários comuns no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Há várias maneiras de configurar o gerenciamento de direitos para sua organização. No entanto, se você estiver apenas começando, é útil entender os cenários comuns para administradores, Aprovadores e solicitantes.

## <a name="administrators"></a>Administradores

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Sou novo no gerenciamento de direitos e quero obter ajuda com a introdução

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | [Siga o tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md) | [ícone do portal ![Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Quero permitir que os usuários em meu diretório solicitem acesso a grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [criar um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [adicionar funções de recurso ao pacote de acesso](entitlement-management-access-package-resources.md#add-resource-roles)<ul><li>Grupos</li><li>Aplicações</li><li>Sites do SharePoint</li></ul> | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)<ul><li>Para usuários em seu diretório</li><li>Exigir aprovação</li><li>Configurações do ciclo de vida</li></ul> | ![Adicionar política](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Quero permitir que os usuários do meu diretório de parceiros comerciais (incluindo os usuários que ainda não estão em meu diretório) solicitem acesso a grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [criar um novo pacote de acesso em um catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [adicionar funções de recurso ao pacote de acesso](entitlement-management-access-package-resources.md#add-resource-roles) | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política para usuários externos](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)<ul><li>Para usuários que não estão em seu diretório</li><li>Exigir aprovação</li><li>Configurações do ciclo de vida</li></ul> | ![Adicionar política para usuários externos](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [enviar o link para solicitar um pacote de acesso ao seu parceiro comercial](entitlement-management-access-package-settings.md)<ul><li>O parceiro comercial pode compartilhar o link com seus usuários</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Quero alterar os grupos, aplicativos ou sites do SharePoint em um pacote do Access

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** abrir o pacote de acesso | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Adicionar ou remover funções de recurso](entitlement-management-access-package-resources.md#add-resource-roles) | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Quero exibir quem tem uma atribuição para grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** abrir um pacote de acesso | ![Adicionar funções de recurso](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [exibir atribuições](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)<ul><li>Exibir quais usuários têm acesso a um pacote de acesso</li><li>Exibir qual acesso do usuário expirou</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Quero exibir grupos, aplicativos ou sites do SharePoint aos quais um usuário tem acesso

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | [Exibir relatório de atribuições de usuário](entitlement-management-reports.md)<ul><li>Exibir quando solicitados e quem aprovou</li></ul> |  |

## <a name="approvers"></a>Aprovadores

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Quero aprovar solicitações para acessar grupos, aplicativos ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [abrir solicitação no meu portal de acesso](entitlement-management-request-approve.md#open-request) | [ícone do portal de acesso do ![My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [aprovar solicitação de acesso](entitlement-management-request-approve.md#approve-or-deny-request) | ![Aprovar acesso](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Requerentes

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Quero exibir os grupos, aplicativos ou sites do SharePoint disponíveis para mim e solicitar acesso

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [entre no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [ícone do portal de acesso do ![My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** localizar pacote de acesso |  |
> | **3.** [solicitar acesso](entitlement-management-request-access.md#request-an-access-package) | ![Pedir acesso](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Sou um usuário externo e desejo solicitar acesso a grupos, aplicativos ou sites do SharePoint com um link direto

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [localize o link do pacote de acesso que você recebeu](entitlement-management-access-package-settings.md) |  |
> | **2.** [entre no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [ícone do portal de acesso do ![My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [solicitar acesso](entitlement-management-request-access.md#request-an-access-package) | ![Solicitar acesso ao usuário externo](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Quero exibir os grupos, aplicativos ou sites do SharePoint dos quais já tenho acesso

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [entre no meu portal de acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [ícone do portal de acesso do ![My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** exibir pacotes do Access ativos |  |

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Delegação e funções](entitlement-management-delegate.md)
