---
title: Resolver problemas de gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba mais sobre alguns itens que deve verificar que o ajudarão a resolver problemas de gestão de direitos do Azure Active Directory (pré-visualização).
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
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473817"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Resolver problemas de gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve alguns itens que deve verificar que o ajudarão a resolver problemas de gestão de direitos do Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Lista de verificação para a administração de gestão de direitos

* Se receber uma mensagem de acesso negado quando configurar a gestão de direitos e for um Administrador Global, certifique-se de que o seu diretório tem um [licença do Azure AD Premium P2 (ou do EMS E5)](entitlement-management-overview.md#license-requirements).  
* Se receber um acesso negado a mensagem ao criar ou ver pacotes de acesso e for membro de um grupo de criador do catálogo, tem de criar um catálogo antes de criar seu primeiro pacote de acesso.

## <a name="checklist-for-adding-a-resource"></a>Lista de verificação para adicionar um recurso

* Para um aplicativo ser um recurso num pacote de acesso, tem de ter pelo menos uma função de recursos que pode ser atribuída. As funções são definidas pela aplicação em si e são geridas no Azure AD. Tenha em atenção que o portal do Azure também pode mostrar os principais de serviço para os serviços que não podem ser selecionados como aplicativos.  Em particular, **Exchange Online** e **SharePoint Online** são os serviços, não os aplicativos que têm funções de recursos no diretório, pelo que não pode ser incluídos num pacote de acesso.  Em vez disso, utilize o licenciamento baseado em grupo para estabelecer uma licença adequada para um utilizador que tem de aceder a esses serviços.

* Para um grupo ser um recurso num pacote de acesso, tem de ser capaz de ser passíveis de modificação no Azure AD.  Não não possível atribuir grupos que têm origem num diretório de Active Directory no local como recursos porque o respetivo proprietário ou atributos de membro não podem ser alterados no Azure AD.  

* Bibliotecas de documentos do SharePoint Online e os documentos individuais não não possível adicionar como recursos.  Em vez disso, crie um grupo de segurança do Azure AD, incluir desse grupo e uma função de site no pacote de acesso e no SharePoint Online utilizar o grupo para controlar o acesso à biblioteca de documentos ou documento.

* Se existirem utilizadores que já foram atribuídos a um recurso que pretende gerir com um pacote de acesso, certifique-se de que os utilizadores são atribuídos ao pacote de acesso com uma política adequada. Por exemplo, pode querer incluir um grupo num pacote de acesso que já tenha os utilizadores no grupo. Se esses utilizadores na requerem grupo de acesso continuaram, eles tem de ter uma política adequada para os pacotes de acesso para que eles não perdem o acesso ao grupo. Pode atribuir o pacote de acesso, fazendo os que os utilizadores peçam o pacote de acesso que contém esse recurso, ou ao atribuí-los diretamente para o pacote de acesso. Para obter mais informações, consulte [editar e gerir um pacote de acesso existente](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Lista de verificação para fornecer acesso a utilizadores externos

* Se houver um B2B [lista de permissões](../b2b/allow-deny-list.md), em seguida, os utilizadores cujos diretórios não são permitidos não será possível pedir acesso.

* Certifique-se de que existem sem [políticas de acesso condicional](../conditional-access/require-managed-devices.md) que impediria que os utilizadores externos pedir o acesso ou a capacidade de usar os aplicativos dos pacotes de acesso.

## <a name="checklist-for-request-issues"></a>Lista de verificação para problemas do pedido

* Quando um usuário deseja solicitar acesso a um pacote de acesso, certifique-se de que estão a utilizar o **hiperligação do portal do meu acesso** para o pacote de acesso. Para obter mais informações, consulte [hiperligação do portal de acesso de minha cópia](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Quando um utilizador inicia sessão no portal de acesso My para pedir um pacote de acesso, certifique-se de que se autenticar com a respetiva conta organizacional. A conta institucional pode ser uma conta no diretório de recursos ou num diretório que está incluído em uma das políticas do pacote de acesso. Se a conta de utilizador não é uma conta organizacional ou o diretório não está incluído na política, em seguida, o utilizador não verá o pacote de acesso. Para obter mais informações, consulte [solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um utilizador está bloqueado de iniciar sessão diretório de recursos, não poderão solicitar acesso no portal do meu acesso. Antes do utilizador pode pedir o acesso, tem de remover o bloco de início de sessão do perfil do usuário. Para remover o bloco de início de sessão, no portal do Azure, clique em **do Azure Active Directory**, clique em **utilizadores**, clique o utilizador e, em seguida, clique em **perfil**. Editar a **configurações** secção e altere **bloquear início de sessão** para **não**. Para obter mais informações, consulte [adicionar ou atualizar as informações de perfil de um utilizador com o Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Também pode verificar se o utilizador foi bloqueado devido a um [política de proteção de identidade](../identity-protection/howto-unblock-user.md).

* No portal do meu acesso, se um utilizador é um requerente e um aprovador, não verá que o pedido para um pacote de acesso na **aprovações** página. Este comportamento é intencional – um utilizador não pode aprovar o pedido de seus próprios. Certifique-se de que o pacote de acesso que estão a pedir tem aprovadores adicionais configurados na política. Para obter mais informações, consulte [editar uma política existente](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Se um novo utilizador externo, que não anteriormente iniciou sessão no seu diretório, recebe um pacote de acesso, incluindo um site do SharePoint Online, o seu pacote de acesso mostrará como não totalmente oferecido até a respetiva conta está aprovisionada no SharePoint Online.

## <a name="next-steps"></a>Passos Seguintes

- [Ver relatórios de como os utilizadores obtêm acesso na gestão de direitos](entitlement-management-reports.md)
