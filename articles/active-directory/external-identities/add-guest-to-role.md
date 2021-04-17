---
title: Adicione um utilizador de colaboração B2B a um papel - Azure Ative Directory
description: Adicione um utilizador convidado a um papel no Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575671"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Conceda permissões a utilizadores de organizações parceiras no seu inquilino Azure Ative Directory

Os utilizadores de colaboração B2B do Azure Ative Directory (Azure AD) são adicionados como utilizadores convidados ao diretório, e as permissões dos hóspedes no diretório são restritas por padrão. O seu negócio pode precisar de alguns utilizadores convidados para preencher funções de maior privilégio na sua organização. Para apoiar a definição de papéis de maior privilégio, os utilizadores convidados podem ser adicionados a quaisquer funções que desejem, com base nas necessidades da sua organização.

Se um papel de diretório for atribuído a um utilizador convidado, o utilizador convidado será concedido com permissões adicionais que venham com o papel, incluindo permissões básicas de leitura. Ver [funções incorporadas a Azure AD](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference).

## <a name="default-role"></a>Papel predefinido

![Screenshot mostrando o papel de diretório padrão](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Papel de Administrador Global

![Screenshot mostrando o papel de administrador global](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Função de administrador limitado

![Screenshot mostrando o papel de administrador limitado](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Propriedades do utilizador de colaboração B2B](user-properties.md)
