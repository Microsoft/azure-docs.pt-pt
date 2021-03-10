---
title: Resolução de problemas das unidades administrativas e FAQ - Azure Ative Directory | Microsoft Docs
description: Investigue unidades administrativas para conceder permissões com âmbito restrito no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565891"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Unidades administrativas AZure AD: Resolução de problemas e FAQ

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas. Para obter scripts PowerShell para tarefas comuns, consulte [Trabalhar com unidades administrativas](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Por que sou incapaz de criar uma unidade administrativa?**

**A:** Apenas um *Administrador Global* ou *Administrador privilegiado* pode criar uma unidade administrativa em Azure AD. Verifique se o utilizador que está a tentar criar a unidade administrativa é atribuído ao *Administrador Global* ou ao *Administrador privilegiado.*

**P: Adicionei um grupo a uma unidade administrativa. Porque é que os membros do grupo ainda não aparecem lá?**

**A:** Quando se adiciona um grupo a uma unidade administrativa, isso não resulta na adição de todos os membros do grupo. Os utilizadores devem ser diretamente designados para a unidade administrativa.

**P: Acabei de adicionar (ou removido) um membro da unidade administrativa. Porque é que o membro não aparece (ou continua a aparecer) na interface do utilizador?**

**A:** Por vezes, a adição ou remoção de um ou mais membros de uma unidade administrativa pode demorar alguns minutos a refletir-se no painel das **unidades administrativas.** Em alternativa, pode ir diretamente às propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre utilizadores e grupos em unidades administrativas, consulte [uma lista de unidades administrativas para um utilizador](admin-units-add-manage-users.md) e Veja uma lista de [unidades administrativas para um grupo](admin-units-add-manage-groups.md).

**P: Sou um administrador de senha delegado numa unidade administrativa. Por que não consigo redefinir a senha de um utilizador específico?**

**A:** Como administrador de uma unidade administrativa, só pode redefinir palavras-passe para utilizadores que estejam destacados para a sua unidade administrativa. Certifique-se de que o utilizador cuja redefinição de palavra-passe está a falhar pertence à unidade administrativa à qual foi designado. Se o utilizador pertencer à mesma unidade administrativa, mas ainda não conseguir repor a palavra-passe do utilizador, verifique as funções que são atribuídas ao utilizador. 

Para evitar uma elevação de privilégios, um administrador administrativo não pode redefinir a palavra-passe de um utilizador que está atribuído a um papel com um âmbito de organização.

**P: Por que as unidades administrativas são necessárias? Não podíamos ter usado grupos de segurança como forma de definir um âmbito?**

**A:** Os grupos de segurança têm um modelo de finalidade e autorização existente. Um *Administrador de Utilizador*, por exemplo, pode gerir a adesão de todos os grupos de segurança da organização Azure AD. A função poderá utilizar grupos para gerir o acesso a aplicações como a Salesforce. Um *administrador de utilizador* não deve ser capaz de gerir o próprio modelo de delegação, o que seria o resultado se os grupos de segurança fossem alargados para apoiar cenários de "agrupamento de recursos". 

As unidades administrativas, como as unidades organizacionais no Windows Server Ative Directory, destinam-se a fornecer uma forma de abranger a administração de uma vasta gama de objetos de diretório. Os próprios grupos de segurança podem ser membros de âmbitos de recursos. A utilização de grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerir pode tornar-se confuso.

**P: O que significa adicionar um grupo a uma unidade administrativa?**

**A:** A adição de um grupo a uma unidade administrativa coloca o próprio grupo no âmbito de gestão de qualquer Administrador de *Utilizador* que também seja abrangido por essa unidade administrativa. Os administradores de utilizadores da unidade administrativa podem gerir o nome e a adesão do próprio grupo. Não concede ao *Administrador de Utilizador* permissões para gerir os utilizadores do grupo (por exemplo, para redefinir as suas palavras-passe). Para conceder ao *Administrador utilizador* a capacidade de gerir os utilizadores, os utilizadores têm de ser membros diretos da unidade administrativa.

**P: Um recurso (utilizador ou grupo) pode ser membro de mais de uma unidade administrativa?**

**A:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerido por todos os administradores de toda a organização e administrativos que tenham permissões sobre o recurso.

**P: As unidades administrativas estão disponíveis em organizações B2C?**

**A:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: As unidades administrativas aninhadas são apoiadas?**

**A:** Não, as unidades administrativas aninhadas não são apoiadas.

**P: As unidades administrativas são suportadas na PowerShell e na API do gráfico?**

**R:** Sim. Encontrará suporte para unidades administrativas na [documentação do cmdlet PowerShell](/powershell/module/Azuread/) e [scripts de amostras.](/powershell/azure/active-directory/working-with-administrative-units)

Encontre suporte para o [tipo de recurso administrativoU no](/graph/api/resources/administrativeunit) Microsoft Graph.

## <a name="next-steps"></a>Passos seguintes

- [Restringir o âmbito de funções utilizando unidades administrativas](administrative-units.md)
- [Gerir unidades administrativas](admin-units-manage.md)
