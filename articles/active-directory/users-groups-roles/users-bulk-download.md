---
title: Baixar uma lista de usuários (versão prévia) no portal de Azure Active Directory | Microsoft Docs
description: Baixe registros de usuário em massa no centro de administração do Azure em Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d106e803fdef4f054b7e4717ba59a43359d1d68a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720297"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Baixar uma lista de usuários (versão prévia) no portal Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte a operações de importação (criação) de usuário em massa.

## <a name="required-permissions"></a>Permissões obrigatórias

Para baixar a lista de usuários do centro de administração do Azure AD, você deve estar conectado com um usuário atribuído a uma ou mais funções de administrador de nível de organização no Azure AD. O convite para convidados e o desenvolvedor de aplicativos não são considerados funções de administrador.

## <a name="to-download-a-list-of-users"></a>Para baixar uma lista de usuários

1. [Entre em sua organização do Azure ad](https://aad.portal.azure.com) com uma conta de administrador de usuário na organização.
2. Navegue até Azure Active Directory > usuários. Em seguida, selecione os usuários que deseja incluir no download marcando a caixa na coluna esquerda ao lado de cada usuário. Observação: neste momento, não há como selecionar todos os usuários para exportação. Cada um deve ser selecionado individualmente.
3. No Azure AD, selecione **usuários** > **baixar usuários**.
4. Na página **baixar usuários** , selecione **Iniciar** para receber um arquivo CSV listando propriedades de perfil de usuário. Se houver erros, você poderá baixar e exibir o arquivo de resultados na página resultados da operação em massa. O arquivo contém o motivo de cada erro.

   ![Selecione onde você deseja que a lista os usuários que deseja baixar](./media/users-bulk-download/bulk-download.png)

   O arquivo de download conterá a lista filtrada de usuários.

   Os seguintes atributos de usuário serão incluídos: 

   - userPrincipalName
   - displayName
   - sobrenome
   - mescla
   - givenName
   - objectId
   - userType
   - jobTitle
   - inteiros
   - Manager
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - país
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - móveis
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Verificar o estado

Você pode ver o status de suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![Verificar o status do carregamento na página de resultados de operações em massa](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites do serviço de download em massa

Cada atividade em massa para criar uma lista de usuários pode ser executada por até uma hora. Isso permite a criação e o download de uma lista de pelo menos 500.000 usuários.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar usuários em massa](users-bulk-add.md)
- [Excluir usuários em massa](users-bulk-delete.md)
- [Usuários de restauração em massa](users-bulk-restore.md)
