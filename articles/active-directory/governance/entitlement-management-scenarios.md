---
title: Cenários comuns de gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Conheça as etapas de alto nível que deve seguir para cenários comuns de gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473116"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Cenários comuns de gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Existem várias formas que pode configurar a gestão de direitos para a sua organização. No entanto, se estiver apenas começando, é útil compreender os cenários comuns para os administradores, aprovadores e requerentes.

## <a name="administrators"></a>Administradores

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Sou iniciante em gerenciamento de direitos e quero obter ajuda com a introdução

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | [Siga o tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md) | [![Ícone de portal do Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Eu quero permitir que os utilizadores no meu diretório para solicitar acesso a grupos, aplicações ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Criar um novo pacote de acesso num catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Adicionar funções de recursos para acessar o pacote](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Grupos</li><li>Aplicações</li><li>Sites do SharePoint</li></ul> | ![Adicionar funções de recursos](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Para os utilizadores no seu diretório</li><li>Exigir a aprovação</li><li>Definições de expiração</li></ul> | ![Adicionar política](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Eu quero permitir que os utilizadores do meu diretório de parceiros de negócios (incluindo utilizadores ainda não no meu diretório) para solicitar acesso a grupos, aplicações ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Criar um novo pacote de acesso num catálogo](entitlement-management-access-package-create.md#start-new-access-package) | ![Criar um pacote de acesso](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Adicionar funções de recursos para acessar o pacote](entitlement-management-access-package-edit.md#add-resource-roles) | ![Adicionar funções de recursos](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Adicionar uma política para utilizadores externos](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Para os utilizadores não no seu diretório</li><li>Exigir a aprovação</li><li>Definições de expiração</li></ul> | ![Adicionar política para utilizadores externos](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Envie a ligação do portal de acesso My para pedir o pacote de acesso para o seu parceiro comercial](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Parceiro de negócios pode partilhar a ligação com os seus utilizadores</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Quero alterar os grupos, aplicações ou sites do SharePoint num pacote de acesso

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** Abra o pacote de acesso | ![Adicionar funções de recursos](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Adicionar ou remover funções de recursos](entitlement-management-access-package-edit.md#add-resource-roles) | ![Adicionar funções de recursos](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Quero ver quem tem uma atribuição de grupos, aplicações ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** Abrir um pacote de acesso | ![Adicionar funções de recursos](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Atribuições de vista](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Ver os utilizadores que têm acesso a um pacote de acesso</li><li>Vista de acesso que utilizador expirou</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Quero ver grupos, aplicações ou um utilizador tem acesso a sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | [Ver relatório de atribuições de utilizador](entitlement-management-reports.md)<ul><li>Exibição quando solicitado por eles e que aprovado</li></ul> |  |

## <a name="approvers"></a>Aprovadores

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Quero aprovar pedidos de acesso a grupos, aplicações ou sites do SharePoint

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Pedido aberto no portal de acesso My](entitlement-management-request-approve.md#open-request) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Aprovar pedido de acesso](entitlement-management-request-approve.md#approve-or-deny-request) | ![Aprovar o acesso](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Requerentes

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Quero ver os grupos, aplicações ou sites do SharePoint disponíveis para mim e pedir acesso

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Inicie sessão portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Localizar o pacote de acesso |  |
> | **3.** [Pedir acesso](entitlement-management-request-access.md#request-an-access-package) | ![Pedir acesso](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Sou um utilizador externo e quero solicitar acesso a grupos, aplicações ou sites do SharePoint com uma ligação direta

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Localizar a ligação de portal de meu acesso que recebeu](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Inicie sessão portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Pedir acesso](entitlement-management-request-access.md#request-an-access-package) | ![Utilizador externo do pedido de acesso](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Quero ver os grupos, aplicações ou sites do SharePoint que já tenho acesso ao

> [!div class="mx-tableFixed"]
> | Passos | Exemplo |
> | --- | --- |
> | **1.** [Inicie sessão portal do meu acesso](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Meu ícone do portal de acesso](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Visualizar pacotes de acesso do Active Directory |  |

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Crie seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Editar e gerir um pacote de acesso existente](entitlement-management-access-package-edit.md)
