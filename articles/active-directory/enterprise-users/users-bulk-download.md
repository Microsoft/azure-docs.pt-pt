---
title: Faça o download de uma lista de utilizadores no portal Azure Ative Directory | Microsoft Docs
description: Baixe os registos de utilizadores a granel no centro de administração Azure em Azure Ative Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861588"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Faça o download de uma lista de utilizadores no portal Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta operações de importação de utilizadores a granel (criar).

## <a name="required-permissions"></a>Permissões obrigatórias

Para transferir a lista de utilizadores a partir do centro de administração do Azure AD, tem de iniciar sessão com um utilizador atribuído a uma ou mais funções de administrador ao nível da organização no Azure AD (Administrador de Utilizadores é a função mínima necessária). Emitente de convites e programador de aplicações não são considerados funções de administrador.

## <a name="to-download-a-list-of-users"></a>Para baixar uma lista de utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Utilizador na organização.
2. Navegue até Azure Active Directory > Utilizadores. Em seguida, selecione os utilizadores que quer incluir na transferência ao selecionar a caixa na coluna esquerda junto a cada utilizador. Nota: neste momento, não existe forma de selecionar todos os utilizadores para exportação. É necessário selecionar cada um individualmente.
3. Em Azure AD, **selecione Utilizadores**  >  **Descarregue utilizadores**.
4. Na página de **utilizadores do Download,** selecione **Começar** a receber um ficheiro CSV listando propriedades de perfil de utilizador. Se existirem erros, pode transferir e ver o ficheiro de resultados na página Resultados da operação em massa. O ficheiro contém o motivo de cada erro.

   ![Selecione onde deseja a lista dos utilizadores que pretende descarregar](./media/users-bulk-download/bulk-download.png)

   O ficheiro de transferência irá conter a lista filtrada de utilizadores.

   Estão incluídos os seguintes atributos de utilizador:

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
   - usageLocation
   - streetAddress
   - state
   - país
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - dispositivo móvel
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Verificar o estado

Pode ver o estado dos seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações a granel.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para criar uma lista de utilizadores pode funcionar até uma hora. Isto permite a criação e download de uma lista de pelo menos 500.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [Utilizadores adicionados a granel](users-bulk-add.md)
- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
