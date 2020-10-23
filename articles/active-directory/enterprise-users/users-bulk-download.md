---
title: Faça o download de uma lista de utilizadores no portal Azure Ative Directory Microsoft Docs
description: Baixe os registos de utilizadores a granel no centro de administração Azure em Azure Ative Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 878f091ac6ac40f6ce434710e809c790f382c1ac
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376448"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Faça o download de uma lista de utilizadores no portal Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta operações de importação de utilizadores a granel (criar).

## <a name="required-permissions"></a>Permissões obrigatórias

Para descarregar a lista de utilizadores do centro de administração Admin AD Azure, tem de ser assinado com um utilizador atribuído a uma ou mais funções de administrador de nível de organização em Azure AD (O Administrador do Utilizador é a função mínima necessária). O convidado convidado e desenvolvedor de aplicações não são considerados funções de administrador.

## <a name="to-download-a-list-of-users"></a>Para baixar uma lista de utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Utilizador na organização.
2. Navegue para o Azure Ative Directory > Utilizadores. Em seguida, selecione os utilizadores que deseja incluir no download, assinalando a caixa na coluna esquerda ao lado de cada utilizador. Nota: Neste momento, não há forma de selecionar todos os utilizadores para exportação. Cada um deve ser selecionado individualmente.
3. Em Azure AD, **selecione Utilizadores**  >  **Descarregue utilizadores**.
4. Na página de **utilizadores do Download,** selecione **Começar** a receber um ficheiro CSV listando propriedades de perfil de utilizador. Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da operação Em Massa. O ficheiro contém a razão de cada erro.

   ![Selecione onde deseja a lista dos utilizadores que pretende descarregar](./media/users-bulk-download/bulk-download.png)

   O ficheiro de descarregamento conterá a lista filtrada dos utilizadores.

   Os seguintes atributos do utilizador estão incluídos:

   - userPrincipalName
   - displayName
   - surname
   - correio
   - nomeDado
   - objectId
   - userType
   - jobTitle
   - departamento
   - accountEnabled
   - utilizaçãoLocalização
   - streetAddress
   - state
   - país
   - físicoDeliveryOfficeName
   - city
   - código postal
   - número de telefone
   - dispositivo móvel
   - autenticaçãoPhoneNumber
   - autenticaçãoAlternativePhoneNumber
   - autenticaçãoEmail
   - alternateEmailAddress
   - ageGroup
   - consentimentoProvidedForMinor
   - legalAgeGroupClassificação

## <a name="check-status"></a>Verificar o estado

Pode ver o estado dos seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações a granel.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para criar uma lista de utilizadores pode funcionar até uma hora. Isto permite a criação e download de uma lista de pelo menos 500.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [Utilizadores adicionados a granel](users-bulk-add.md)
- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
