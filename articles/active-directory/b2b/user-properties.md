---
title: Propriedades de um usuário convidado B2B-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Propriedades e Estados do usuário convidado B2B antes e após o resgate do convite
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa282afdf910c2449b5d5ea0bc5e38a396f3aa02
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608861"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um usuário de colaboração B2B Azure Active Directory

Este artigo descreve as propriedades e os Estados do objeto de usuário convidado B2B no Azure Active Directory (AD do Azure) antes e após o resgate do convite. Um usuário de colaboração B2B (entre empresas) do Azure AD é um usuário com UserType = convidado. Esse usuário convidado normalmente é de uma organização parceira e tem privilégios limitados no diretório de convite, por padrão.

Dependendo das necessidades da organização que convida, um usuário de colaboração B2B do Azure AD pode estar em um dos seguintes Estados de conta:

- Estado 1: hospedado em uma instância externa do Azure AD e representado como um usuário convidado na organização que está convidando. Nesse caso, o usuário B2B entra usando uma conta do Azure AD que pertence ao locatário convidado. Se a organização parceira não usar o Azure AD, o usuário convidado no Azure AD ainda será criado. Os requisitos são que eles reenviam seu convite e o Azure AD verifica seu endereço de email. Essa organização também é chamada de locação JIT (just-in-time) ou de um aluguel "viral".

- Estado 2: hospedado em uma conta da Microsoft ou outra e representada como um usuário convidado na organização host. Nesse caso, o usuário convidado entra com um conta Microsoft ou uma conta social (google.com ou semelhante). A identidade do usuário convidado é criada como um conta Microsoft no diretório da organização que está convidando durante o resgate da oferta.

- Estado 3: hospedado no Active Directory local da organização host e sincronizado com o Azure AD da organização host. Você pode usar Azure AD Connect para sincronizar as contas de parceiro para a nuvem como usuários B2B do Azure AD com UserType = convidado. Consulte [conceder acesso às contas de parceiros gerenciados localmente aos recursos de nuvem](hybrid-on-premises-to-cloud.md).

- Estado 4: hospedado no Azure AD da organização host com UserType = convidado e credenciais que a organização host gerencia.

  ![Diagrama ilustrando os quatro Estados do usuário](media/user-properties/redemption-diagram.png)


Agora, vejamos a aparência de um usuário de colaboração B2B do Azure AD no Azure AD.

### <a name="before-invitation-redemption"></a>Antes do resgate de convite

As contas State 1 e State 2 são o resultado de convidar os usuários convidados a colaborar usando as próprias credenciais dos usuários convidados. Quando o convite é enviado inicialmente para o usuário convidado, uma conta é criada no seu diretório. Essa conta não tem credenciais associadas a ela porque a autenticação é executada pelo provedor de identidade do usuário convidado. A propriedade **Source** da conta de usuário convidado em seu diretório é definida como **usuário convidado**. 

![Captura de tela mostrando as propriedades do usuário antes da oferta de resgate](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Após resgate de convite

Depois que o usuário convidado aceitar o convite, a propriedade **Source** será atualizada com base no provedor de identidade do usuário convidado.

Para usuários convidados no estado 1, a **origem** é **externa Azure Active Directory**.

![Estado 1 usuário convidado após resgate da oferta](media/user-properties/after-redemption-state1.png)

Para usuários convidados no estado 2, a **origem** é a **conta da Microsoft**.

![Estado 2 usuário convidado após resgate de oferta](media/user-properties/after-redemption-state2.png)

Para usuários convidados no estado 3 e no estado 4, a propriedade **Source** é definida como **Azure Active Directory** ou **Windows Server Active Directory**, conforme descrito na próxima seção.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Propriedades de chave do usuário de colaboração B2B do Azure AD
### <a name="usertype"></a>UserType
Essa propriedade indica a relação do usuário com o aluguel do host. Essa propriedade pode ter dois valores:
- Membro: esse valor indica um funcionário da organização host e um usuário na folha de pagamento da organização. Por exemplo, esse usuário espera ter acesso a sites somente internos. Esse usuário não é considerado um colaborador externo.

- Convidado: esse valor indica um usuário que não é considerado interno para a empresa, como um colaborador externo, parceiro ou cliente. Um usuário como esse não se espera receber um memorando interno do CEO ou receber os benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType não tem nenhuma relação com o modo como o usuário entra, a função de diretório do usuário e assim por diante. Essa propriedade simplesmente indica a relação do usuário com a organização host e permite que a organização aplique políticas que dependem dessa propriedade.

### <a name="source"></a>Origem
Essa propriedade indica como o usuário entra.

- Usuário convidado: este usuário foi convidado, mas ainda não resgatou um convite.

- Active Directory externos: esse usuário é hospedado em uma organização externa e é autenticado usando uma conta do Azure AD que pertence a outra organização. Esse tipo de entrada corresponde ao estado 1.

- Conta Microsoft: esse usuário é hospedado em um conta Microsoft e é autenticado usando uma conta Microsoft. Esse tipo de entrada corresponde ao estado 2.

- Windows Server Active Directory: este usuário está conectado do Active Directory local que pertence a esta organização. Esse tipo de entrada corresponde ao estado 3.

- Azure Active Directory: esse usuário é autenticado usando uma conta do Azure AD que pertence a esta organização. Esse tipo de entrada corresponde ao estado 4.
  > [!NOTE]
  > Source e UserType são propriedades independentes. Um valor de source não implica um valor específico para UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Os usuários B2B do Azure AD podem ser adicionados como membros em vez de convidados?
Normalmente, um usuário do Azure AD B2B e um usuário convidado são sinônimos. Portanto, um usuário de colaboração B2B do Azure AD é adicionado como um usuário com UserType = convidado por padrão. No entanto, em alguns casos, a organização parceira é membro de uma organização maior à qual a organização host também pertence. Nesse caso, a organização host pode querer tratar os usuários na organização parceira como membros em vez de convidados. Use as APIs do Gerenciador de convites B2B do Azure AD para adicionar ou convidar um usuário da organização parceira para a organização host como um membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrar usuários convidados no diretório

![Captura de tela mostrando o filtro para usuários convidados](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter UserType
É possível converter UserType de Member para Guest e vice-versa usando o PowerShell. No entanto, a propriedade UserType representa a relação do usuário com a organização. Portanto, você deve alterar essa propriedade somente se a relação do usuário com a organização for alterada. Se a relação do usuário for alterada, o nome principal do usuário (UPN) será alterado? O usuário deve continuar tendo acesso aos mesmos recursos? Uma caixa de correio deve ser atribuída? Não é recomendável alterar o UserType usando o PowerShell como uma atividade atômica. Além disso, caso essa propriedade se torne imutável usando o PowerShell, não é recomendável assumir uma dependência desse valor.

## <a name="remove-guest-user-limitations"></a>Remover limitações do usuário convidado
Pode haver casos em que você deseja conceder a seus usuários convidados privilégios mais altos. Você pode adicionar um usuário convidado a qualquer função e até mesmo remover as restrições de usuário convidado padrão no diretório para dar a um usuário os mesmos privilégios que os membros.

É possível desativar as limitações padrão para que um usuário convidado no diretório da empresa tenha as mesmas permissões que um usuário membro.

![Captura de tela mostrando a opção usuários externos nas configurações do usuário](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os usuários convidados visíveis na lista de endereços global do Exchange?
Sim. Por padrão, os objetos convidados não são visíveis na lista de endereços global da sua organização, mas você pode usar Azure Active Directory PowerShell para torná-los visíveis. Para obter detalhes, consulte posso **tornar os objetos convidados visíveis na lista de endereços global?** em [gerenciar o acesso de convidado nos grupos do Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list). 

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Tokens de usuário de colaboração B2B](user-token.md)
* [Mapeamento de declarações do usuário de colaboração B2B](claims-mapping.md)
