---
title: Propriedades de um utilizador convidado B2B - Azure Ative Directory / Microsoft Docs
description: Azure Ative Directory B2B propriedades de utilizadores convidados e estados antes e depois do resgate de convites
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b68a4d0c9c33f6a7eb1a2300955e9185bd52d8fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705474"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um utilizador de colaboração Azure Ative Directory B2B

Este artigo descreve as propriedades e estados do objeto de utilizador convidado B2B em Azure Ative Directory (Azure AD) antes e depois do resgate de convites. Um utilizador de colaboração Azure AD (B2B) é um utilizador com UserType = Guest. Este utilizador convidado é tipicamente de uma organização parceira e tem privilégios limitados no diretório convidativo, por padrão.

Dependendo das necessidades da organização convidativa, um utilizador de colaboração Azure AD B2B pode estar numa das seguintes contas:

- Estado 1: Autódido num caso externo de Azure AD e representado como utilizador convidado na organização convidativa. Neste caso, o utilizador B2B assina através de uma conta AZure AD que pertence ao inquilino convidado. Se a organização parceira não utilizar a Azure AD, o utilizador convidado em Azure AD ainda é criado. Os requisitos são que eles resgatam o seu convite e a Azure AD verifica o seu endereço de e-mail. Este arranjo também é chamado de arrendamento just-in-time (JIT) ou um arrendamento "viral".

   > [!IMPORTANT]
   > **A partir de 31 de março de 2021, a**Microsoft deixará de apoiar o resgate de convites através da criação de contas Ead AZure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

- Estado 2: Alojado numa conta microsoft ou outra e representado como utilizador convidado na organização anfitriã. Neste caso, o utilizador convidado assina com uma conta Microsoft ou uma conta social (google.com ou similares). A identidade do utilizador convidado é criada como uma conta Microsoft no diretório da organização convidativa durante o resgate da oferta.

- Estado 3: Alojado no ative directy da organização anfitriã e sincronizado com o AD Azure da organização anfitriã. Pode utilizar o Azure AD Connect para sincronizar as contas do parceiro na nuvem como utilizadores AD B2B Azure com UserType = Guest. Consulte [as contas de parceiros geridas localmente pelo Grant, acedendo a recursos na nuvem.](hybrid-on-premises-to-cloud.md)

- Estado 4: Alojado no AZure AD da organização anfitriã com UserType = Convidado e credenciais que a organização anfitriã gere.

  ![Diagrama que retrata os quatro estados de utilizador](media/user-properties/redemption-diagram.png)


Agora, vamos ver como é um utilizador de colaboração Azure AD B2B em Azure AD.

### <a name="before-invitation-redemption"></a>Antes da redenção do convite

As contas do Estado 1 e do Estado 2 são o resultado de convidar os utilizadores convidados a colaborar utilizando as credenciais dos utilizadores convidados. Quando o convite é inicialmente enviado ao utilizador convidado, é criada uma conta no seu diretório. Esta conta não tem quaisquer credenciais associadas porque a autenticação é realizada pelo fornecedor de identidade do utilizador convidado. A propriedade **Source** para a conta de utilizador convidado no seu diretório está definida para **utilizador convidado.** 

![Screenshot mostrando propriedades do utilizador antes de oferecer resgate](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Após a redenção do convite

Após o utilizador convidado aceitar o convite, a propriedade **Source** é atualizada com base no fornecedor de identidade do utilizador convidado.

Para os utilizadores convidados no Estado 1, a **Fonte** é **Diretório Ativo Azure Externo**.

![Utilizador convidado do Estado 1 após a oferta de resgate](media/user-properties/after-redemption-state1.png)

Para os utilizadores convidados no Estado 2, a **Fonte** é **a Conta Microsoft.**

![Utilizador convidado do Estado 2 após a oferta de resgate](media/user-properties/after-redemption-state2.png)

Para os utilizadores convidados no Estado 3 e Estado 4, a propriedade **Source** está definida para **Azure Ative Directory** ou **Windows Server Ative Directory**, conforme descrito na secção seguinte.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Principais propriedades do utilizador de colaboração Azure AD B2B
### <a name="usertype"></a>UserType
Esta propriedade indica a relação do utilizador com o arrendamento do anfitrião. Esta propriedade pode ter dois valores:
- Membro: Este valor indica um funcionário da organização anfitriã e um utilizador na folha de pagamento da organização. Por exemplo, este utilizador espera ter acesso a sites apenas internos. Este utilizador não é considerado um colaborador externo.

- Hóspede: Este valor indica um utilizador que não é considerado interno para a empresa, como um colaborador externo, parceiro ou cliente. Não se espera que um utilizador receba o memorando interno de um CEO ou receba benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType não tem qualquer relação com a forma como o utilizador se inscreve, o papel do diretório do utilizador, e assim por diante. Esta propriedade simplesmente indica a relação do utilizador com a organização anfitriã e permite que a organização aplique políticas que dependem desta propriedade.

### <a name="source"></a>Origem
Esta propriedade indica como o utilizador assina.

- Utilizador Convidado: Este utilizador foi convidado mas ainda não resgatou um convite.

- Diretório Ativo Externo: Este utilizador está a ser ajadado numa organização externa e autentica-se utilizando uma conta AZure AD que pertence à outra organização. Este tipo de inscrição corresponde ao Estado 1.

- Conta Microsoft: Este utilizador está a ser adoeado numa conta microsoft e autentica-se utilizando uma conta Microsoft. Este tipo de inscrição corresponde ao Estado 2.

- Windows Server Ative Directory: Este utilizador é assinado a partir de diretório ativo no local que pertence a esta organização. Este tipo de inscrição corresponde ao Estado 3.

- Azure Ative Directory: Este utilizador autentica utilizando uma conta Azure AD que pertence a esta organização. Este tipo de inscrição corresponde ao Estado 4.
  > [!NOTE]
  > Origem e UserType são propriedades independentes. Um valor de Origem não implica um valor específico para o UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Os utilizadores Azure AD B2B podem ser adicionados como membros em vez de hóspedes?
Normalmente, um utilizador AD B2B Azure e utilizador convidado são sinónimos. Portanto, um utilizador de colaboração Azure AD B2B é adicionado como um utilizador com UserType = Convidado por padrão. No entanto, em alguns casos, a organização parceira é membro de uma organização maior à qual a organização anfitriã também pertence. Em caso afirmativo, a organização anfitriã pode querer tratar os utilizadores da organização parceira como membros em vez de convidados. Utilize as APIs do Azure AD B2B Invitation Manager para adicionar ou convidar um utilizador da organização parceira para a organização anfitriã como membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtro para utilizadores convidados no diretório

![Screenshot mostrando o filtro para utilizadores convidados](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter UserType
É possível converter o UserType de Membro para Convidado e vice-versa utilizando o PowerShell. No entanto, a propriedade UserType representa a relação do utilizador com a organização. Portanto, você deve alterar esta propriedade apenas se a relação do utilizador com a organização mudar. Se a relação do utilizador mudar, o nome principal do utilizador (UPN) deve mudar? O utilizador deve continuar a ter acesso aos mesmos recursos? Deve ser atribuída uma caixa de correio? Não recomendamos que se mude o UserType utilizando o PowerShell como uma atividade atómica. Além disso, caso esta propriedade se torne imutável usando o PowerShell, não recomendamos que se assuma uma dependência deste valor.

## <a name="remove-guest-user-limitations"></a>Remova as limitações do utilizador do hóspede
Pode haver casos em que queira dar aos seus utilizadores convidados privilégios mais elevados. Pode adicionar um utilizador convidado a qualquer função e até remover as restrições predefinidos do utilizador do utilizador no diretório para dar a um utilizador os mesmos privilégios que os membros.

É possível desativar as limitações por defeito para que um utilizador convidado no diretório da empresa tenha as mesmas permissões que um utilizador membro.

![Screenshot mostrando a opção de utilizadores externos nas definições do utilizador](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os utilizadores convidados visíveis na Lista de Endereços Globais do Exchange?
Sim. Por predefinição, os objetos convidados não estão visíveis na lista global de endereços da sua organização, mas pode utilizar o Azure Ative Directory PowerShell para os tornar visíveis. Para mais informações, consulte **Posso tornar os objetos convidados visíveis na lista global de endereços?** [Manage guest access in Microsoft 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups)

## <a name="can-i-update-a-guest-users-email-address"></a>Posso atualizar o endereço de e-mail de um utilizador convidado?

Se um utilizador convidado aceitar o seu convite e posteriormente alterar o seu endereço de e-mail, o novo e-mail não sincroniza automaticamente o objeto do utilizador convidado no seu diretório. A propriedade de correio é criada através da [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0). Pode atualizar a propriedade de correio através da Microsoft Graph API, do centro de administração Exchange ou [do Exchange Online PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/set-mailuser?view=exchange-ps). A alteração será refletida no objeto de utilizador convidado Azure AD.

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Fichas de utilização de colaboração B2B](user-token.md)
* [Utilizador de colaboração B2B reivindica mapeamento](claims-mapping.md)
