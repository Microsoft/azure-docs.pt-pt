---
title: Unidades administrativas de resolução de problemas e FAQ - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Investigar unidades administrativas a delegação de permissões com âmbito restrito no Diretório Ativo do Azure
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
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428150"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Resolução de problemas e FAQ para unidades administrativas em Diretório Ativo Azure

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir aos utilizadores uma função De AD Azure com um âmbito limitado a uma ou mais unidades administrativas (UA). Pode encontrar scripts PowerShell de amostra https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0para tarefas comuns em .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Não sou capaz de criar uma unidade administrativa**

**A:** Apenas um Administrador Global ou Administrador de Funções Privilegiadas pode criar uma unidade administrativa em Azure AD. Verifique se o utilizador que tenta criar a unidade administrativa é atribuído quer ao Administrador Global quer ao papel de Administrador Privilegiado.

**P: Adicionei um grupo à unidade administrativa, mas os membros do grupo ainda não aparecem na unidade administrativa**

**A:** Quando se adiciona um grupo à unidade administrativa, isso não resulta na adição de todos os membros do grupo à unidade administrativa. Os utilizadores devem ser diretamente designados para a unidade administrativa.

**P: Acabei de adicionar/removido um membro da unidade administrativa e ainda aparece na UI**

**A:** Por vezes, o processamento da adição ou remoção de um ou mais membros da unidade administrativa pode demorar alguns minutos a refletir na página das **unidades administrativas.** Pode optar por esperar alguns minutos para que se reflita sob as unidades administrativas. Em alternativa, pode ir diretamente às propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre utilizadores e grupos em UsA, consulte lista [de unidades administrativas para um utilizador](roles-admin-units-add-manage-users.md) e lista [rum as unidades administrativas de um grupo](roles-admin-units-add-manage-groups.md).

**P: Como administrador de palavra-passe delegado numa unidade administrativa, não posso redefinir a palavra-passe de um utilizador específico**

**A:** Um administrador atribuído a uma unidade administrativa só pode redefinir a palavra-passe para os utilizadores atribuídos à sua unidade administrativa. Certifique-se de que o utilizador para o qual a palavra-passe está a falhar pertence às unidades administrativas sobre as quais lhe foi atribuída a função. Se o utilizador pertencer à mesma unidade administrativa e ainda não conseguir redefinir a palavra-passe do utilizador, verifique as funções atribuídas ao utilizador. Para evitar uma elevação de privilégios, um administrador de âmbito de unidade administrativa não pode redefinir a palavra-passe de um utilizador que é atribuído a um papel com um âmbito de aplicação em toda a organização.

**P: Por que as unidades administrativas são necessárias? Não podíamos ter usado grupos de segurança como forma de definir um âmbito?**

**A:** Os grupos de segurança têm um modelo de objetivo e autorização existente. Um administrador de utilizador, por exemplo, pode gerir a adesão de todos os grupos de segurança da organização Azure AD, como usar grupos para gerir o acesso a aplicações como a Salesforce. Um administrador utilizador não deve ter a capacidade de gerir o próprio modelo de delegação, o que seria o resultado se os grupos de segurança fossem alargados para apoiar cenários de "agrupamento de recursos". Unidades administrativas, como unidades organizacionais no Diretório Ativo do Windows Server, destinam-se a fornecer uma forma de administração de âmbito de aplicação de uma vasta gama de objetos de diretório. Os próprios grupos de segurança podem ser membros dos âmbitos de recursos. A utilização de grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerir pode tornar-se confusa.

**P: O que significa adicionar um grupo a uma unidade administrativa?**

**A:** A adição de um grupo a uma unidade administrativa coloca o próprio grupo no âmbito de gestão de qualquer administrador do Utilizador que também seja remeto a essa unidade de administração. Os administradores dos utilizadores da unidade administrativa podem gerir o nome e a adesão do próprio grupo. Não concede ao Utilizador qualquer permissão para gerir os utilizadores do grupo (por exemplo, redefinir as suas palavras-passe). Para conceder ao Administrador do Utilizador a capacidade de gerir os utilizadores, os utilizadores têm de ser membros diretos da unidade administrativa.

**P: Um recurso (utilizador ou grupo) pode ser membro de mais de uma unidade administrativa?**

**A:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerido por todos os administradores de aplicação de âmbito de aplicação da organização e administrativos que tenham permissões sobre o recurso.

**P: As unidades administrativas estão disponíveis nas organizações B2C?**

**A:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: As unidades administrativas aninhadas são apoiadas?**

**A:** As unidades administrativas aninhadas não são apoiadas.

**P: As unidades administrativas são suportadas na PowerShell e na API graph?**

**R:** Sim. O suporte para Unidades Administrativas existe na documentação e scripts de [amostras](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview) [da PowerShell,](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e o suporte está no Microsoft Graph para o tipo de [recursos da Unidade Administrativa](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Passos seguintes

- [Unidades administrativas para restringir o âmbito de aplicação das funções](directory-administrative-units.md)
- [Gerir unidades administrativas](roles-admin-units-manage.md)