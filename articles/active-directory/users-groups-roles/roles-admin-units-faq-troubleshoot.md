---
title: Unidades administrativas resolução de problemas e FAQ - Diretório Ativo Azure Microsoft Docs
description: Investigar unidades administrativas para conceder permissões com âmbito restrito no Diretório Ativo do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684847"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Unidades administrativas da Azure AD: Resolução de problemas e FAQ

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir aos utilizadores uma função De AD Azure com um âmbito limitado a uma ou mais unidades administrativas (UA). Para amostrar scripts PowerShell para tarefas comuns, consulte [Trabalhar com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que sou incapaz de criar uma unidade administrativa?**

**A:** Apenas um *Administrador Global* ou Administrador *de Funções Privilegiadas* pode criar uma unidade administrativa em Azure AD. Verifique se o utilizador que está a tentar criar a unidade administrativa é atribuído quer ao *Administrador Global* quer ao Papel Privilegiado *do Administrador.*

**P: Adicionei um grupo à unidade administrativa. Porque é que os membros do grupo ainda não aparecem lá?**

**A:** Quando se acrescenta um grupo à unidade administrativa, isso não resulta na adição de todos os membros do grupo. Os utilizadores devem ser diretamente atribuídos à unidade administrativa.

**P: Acabei de adicionar (ou removido) um membro da unidade administrativa. Porque é que o membro não aparece (ou continua a aparecer) na interface do utilizador?**

**A:** Por vezes, o processamento da adição ou remoção de um ou mais membros da unidade administrativa pode demorar alguns minutos a refletir-se na página das **unidades administrativas.** Em alternativa, pode ir diretamente às propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre utilizadores e grupos em UsA, consulte lista [de unidades administrativas para um utilizador](roles-admin-units-add-manage-users.md) e lista [rum as unidades administrativas de um grupo](roles-admin-units-add-manage-groups.md).

**P: Sou um administrador de senha delegado numa unidade administrativa. Por que não consigo redefinir a senha de um utilizador específico?**

**A:** Como administrador de uma unidade administrativa, só pode redefinir senhas para utilizadores que sejam atribuídos à sua unidade administrativa. Certifique-se de que o utilizador cuja redefinição da palavra-passe está a falhar pertence à unidade administrativa à qual foi atribuído. Se o utilizador pertencer à mesma unidade administrativa, mas ainda não conseguir redefinir a sua palavra-passe, verifique as funções atribuídas ao utilizador. 

Para evitar uma elevação de privilégios, um administrador administrativo de âmbito de aplicação não pode redefinir a palavra-passe de um utilizador que é atribuído a um papel com um âmbito de aplicação em toda a organização.

**P: Por que as unidades administrativas são necessárias? Não podíamos ter usado grupos de segurança como forma de definir um âmbito?**

**A:** Os grupos de segurança têm um modelo de objetivo e autorização existente. Um *Administrador de Utilizador,* por exemplo, pode gerir a adesão de todos os grupos de segurança da organização Azure AD. O papel pode usar grupos para gerir o acesso a aplicações como a Salesforce. Um *Administrador de Utilizador* não deve ser capaz de gerir o próprio modelo de delegação, o que seria o resultado se os grupos de segurança fossem alargados para apoiar cenários de "agrupamento de recursos". As unidades administrativas, como unidades organizacionais no Windows Server Ative Directory, destinam-se a fornecer uma forma de administração de âmbito de aplicação de uma vasta gama de objetos de diretório. Os próprios grupos de segurança podem ser membros dos âmbitos de recursos. A utilização de grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerir pode tornar-se confuso.

**P: O que significa adicionar um grupo a uma unidade administrativa?**

**A:** A adição de um grupo a uma unidade administrativa coloca o próprio grupo no âmbito de gestão de qualquer *Administrador de Utilizador* que também seja remetido nessa unidade administrativa. Os administradores de utilizadores da unidade administrativa podem gerir o nome e a adesão do próprio grupo. Não concede ao Administrador do *Utilizador* permissões da unidade administrativa para gerir os utilizadores do grupo (por exemplo, para redefinir as suas palavras-passe). Para conceder ao Administrador do *Utilizador* a capacidade de gerir os utilizadores, os utilizadores têm de ser membros diretos da unidade administrativa.

**P: Um recurso (utilizador ou grupo) pode ser membro de mais de uma unidade administrativa?**

**A:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerido por todos os administradores de aplicação de âmbito de aplicação da organização e administrativos que tenham permissões sobre o recurso.

**P: As unidades administrativas estão disponíveis nas organizações B2C?**

**A:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: As unidades administrativas aninhadas são apoiadas?**

**A:** Não, as unidades administrativas aninhadas não são apoiadas.

**P: As unidades administrativas são suportadas na PowerShell e na API graph?**

**R:** Sim. Encontrará suporte para unidades administrativas na [documentação cmdlet powerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e [scripts de amostra.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview) 

Encontre suporte para o tipo de [recursos da Unidade administrativa](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) no Microsoft Graph.

## <a name="next-steps"></a>Passos seguintes

- [Restringir o âmbito das funções através da utilização de unidades administrativas](directory-administrative-units.md)
- [Gerir unidades administrativas](roles-admin-units-manage.md)
