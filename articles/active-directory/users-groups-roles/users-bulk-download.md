---
title: Faça o download de uma lista de utilizadores no portal de Diretório Sonâmon Ativo Do Azure [ Microsoft Docs
description: Descarregue os registos dos utilizadores a granel no centro de administração Azure em Azure Ative Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532658"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Faça o download de uma lista de utilizadores no portal azure Ative Directy

O Azure Ative Directory (Azure AD) suporta operações de importação de utilizadores a granel (criar) operações.

## <a name="required-permissions"></a>Permissões obrigatórias

Para descarregar a lista de utilizadores do centro de administração da AD Azure, deve ser contratado com um utilizador atribuído a uma ou mais funções de administrador de nível de organização no Azure AD (Administrador de Utilizador é a função mínima exigida). Convidado convidado e desenvolvedor de aplicações não são considerados papéis de administrador.

## <a name="to-download-a-list-of-users"></a>Para descarregar uma lista de utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta de administrador de utilizador na organização.
2. Navegue para o Diretório Ativo do Azure > Utilizadores. Em seguida, selecione os utilizadores que pretende incluir no download, marcando a caixa na coluna esquerda ao lado de cada utilizador. Nota: Neste momento, não há forma de selecionar todos os utilizadores para exportação. Cada um deve ser selecionado individualmente.
3. No Azure AD, selecione **Utilizadores** > **Descarregue os utilizadores**.
4. Na página de **Utilizadores de Descarregamento,** selecione **Iniciar** a receber um ficheiro CSV listando propriedades de perfil de utilizador. Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da operação Bulk. O ficheiro contém a razão de cada erro.

   ![Selecione onde deseja a lista os utilizadores que pretende descarregar](./media/users-bulk-download/bulk-download.png)

   O ficheiro de descarregamento conterá a lista filtrada dos utilizadores.

   Estão incluídos os seguintes atributos do utilizador:

   - userPrincipalName
   - displayName
   - surname
   - correio
   - nomeDado
   - objectId
   - userType
   - empregoTítulo
   - departamento
   - contaEnabled
   - utilizaçãoLocalização
   - streetAddress
   - state
   - país
   - physicalDeliveryOfficeName
   - city
   - Código postal
   - telefoneNúmero
   - dispositivo móvel
   - autenticaçãoNúmero de Telefone
   - autenticaçãoAlternativePhoneNumber
   - autenticaçãoEmail
   - endereço de email alternativo
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Verificar o estado

Pode ver o estado dos seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para criar uma lista de utilizadores pode funcionar até uma hora. Isto permite a criação e o download de uma lista de pelo menos 500.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [A granel adicionar utilizadores](users-bulk-add.md)
- [A granel, apaga os utilizadores](users-bulk-delete.md)
- [A granel restaura utentes](users-bulk-restore.md)
