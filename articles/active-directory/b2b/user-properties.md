---
title: Propriedades de um utilizador convidado B2B - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: Propriedades e estados de hóspedes do Diretório Ativo Azure B2B antes e depois do resgate do convite
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050801"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um utilizador de colaboração Azure Ative Directory B2B

Este artigo descreve as propriedades e estados do objeto de utilizador convidado B2B no Azure Ative Directory (Azure AD) antes e depois da redenção do convite. Um utilizador de colaboração Azure AD business-to-business (B2B) é um utilizador com UserType = Guest. Este utilizador hóspede é tipicamente de uma organização parceira e tem privilégios limitados no diretório convidativo, por padrão.

Dependendo das necessidades da organização convidativa, um utilizador de colaboração Azure AD B2B pode estar num dos seguintes estados de conta:

- Estado 1: Alojado numa instância externa de Azure AD e representado como utilizador convidado na organização convidativa. Neste caso, o utilizador B2B assina através da utilização de uma conta Azure AD que pertence ao inquilino convidado. Se a organização parceira não utilizar o Azure AD, o utilizador convidado em Azure AD ainda é criado. Os requisitos são que eles resgatam o seu convite e a Azure AD verifica o seu endereço de e-mail. Este arranjo também é chamado de arrendamento just-in-time (JIT) ou um arrendamento "viral".

   > [!IMPORTANT]
   > A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas AD Azure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optarem pela autenticação de código de acesso único por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

- Estado 2: Alojado numa conta Microsoft ou outra e representado como utilizador convidado na organização anfitriã. Neste caso, o utilizador convidado entra em si com uma conta Microsoft ou uma conta social (google.com ou similar). A identidade do utilizador convidado é criada como uma conta Microsoft no diretório da organização convidativa durante o resgate da oferta.

- Estado 3: Alojado no Diretório Ativo da organização anfitriã e sincronizado com o Azure AD da organização anfitriã. Pode utilizar o Azure AD Connect para sincronizar as contas do parceiro na nuvem como utilizadores Azure AD B2B com UserType = Guest. Consulte grant contas [de parceiro geridos localmente acesso a recursos na nuvem](hybrid-on-premises-to-cloud.md).

- Estado 4: Alojado no Anúncio Azure da organização anfitriã com UserType = Hóspede e credenciais que a organização anfitriã gere.

  ![Diagrama que retrata os quatro estados do utilizador](media/user-properties/redemption-diagram.png)


Agora, vamos ver como é um utilizador de colaboração Azure AD B2B em Azure AD.

### <a name="before-invitation-redemption"></a>Antes da redenção do convite

As contas do Estado 1 e do Estado 2 são o resultado de convidar os utilizadores convidados a colaborar usando as credenciais dos próprios utilizadores convidados. Quando o convite é inicialmente enviado ao utilizador convidado, é criada uma conta no seu diretório. Esta conta não tem credenciais associadas à sua conta porque a autenticação é realizada pelo fornecedor de identidade do utilizador convidado. A propriedade **Source** para a conta de utilizador convidado no seu diretório está definida para **o utilizador convidado**. 

![Screenshot mostrando propriedades do utilizador antes de oferecer redenção](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Após a redenção do convite

Após o utilizador convidado aceitar o convite, a propriedade **Source** é atualizada com base no fornecedor de identidade do utilizador convidado.

Para os utilizadores convidados no Estado 1, o **Source** é **External Azure Ative Directory**.

![Utilizador convidado do Estado 1 após o resgate da oferta](media/user-properties/after-redemption-state1.png)

Para utilizadores convidados no Estado 2, a **Fonte** é **a Conta Microsoft.**

![Utilizador convidado do Estado 2 após resgate de oferta](media/user-properties/after-redemption-state2.png)

Para utilizadores convidados no Estado 3 e Estado 4, a propriedade **Source** está definida para **o Diretório Ativo Azure** ou o **Diretório Ativo**do Servidor Windows , como descrito na secção seguinte.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Propriedades-chave do utilizador de colaboração Azure AD B2B
### <a name="usertype"></a>UserType
Esta propriedade indica a relação do utilizador com o arrendamento hospedeiro. Esta propriedade pode ter dois valores:
- Membro: Este valor indica um funcionário da organização anfitriã e um utilizador na folha de pagamentos da organização. Por exemplo, este utilizador espera ter acesso a sites apenas internos. Este utilizador não é considerado um colaborador externo.

- Hóspede: Este valor indica um utilizador que não é considerado interno para a empresa, como um colaborador externo, parceiro ou cliente. Não se espera que este utilizador receba um memorando interno de um CEO ou receba benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType não tem qualquer relação com a forma como o utilizador entra, a função de diretório do utilizador, e assim por diante. Esta propriedade simplesmente indica a relação do utilizador com a organização anfitriã e permite que a organização aplique políticas que dependem desta propriedade.

### <a name="source"></a>Origem
Esta propriedade indica como o utilizador entra.

- Utilizador Convidado: Este utilizador foi convidado mas ainda não redimiu um convite.

- Diretório Ativo Azure Externo: Este utilizador está alojado numa organização externa e autentica através da utilização de uma conta Azure AD que pertence à outra organização. Este tipo de inscrição corresponde ao Estado 1.

- Conta Microsoft: Este utilizador está alojado numa conta da Microsoft e autentica-se utilizando uma conta Microsoft. Este tipo de inscrição corresponde ao Estado 2.

- Diretório Ativo do Windows Server: Este utilizador é assinado a partir do Diretório Ativo no local que pertence a esta organização. Este tipo de inscrição corresponde ao Estado 3.

- Diretório Ativo Azure: Este utilizador autentica utilizando uma conta Azure AD que pertence a esta organização. Este tipo de inscrição corresponde ao Estado 4.
  > [!NOTE]
  > Fonte e UserType são propriedades independentes. Um valor de Fonte não implica um valor específico para o UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Os utilizadores do Azure AD B2B podem ser adicionados como membros em vez de convidados?
Tipicamente, um utilizador E Utilizador B2B Azure AD e utilizador convidado são sinónimo. Portanto, um utilizador de colaboração Azure AD B2B é adicionado como um utilizador com UserType = Hóspede por padrão. No entanto, em alguns casos, a organização parceira é membro de uma organização maior à qual a organização anfitriã também pertence. Em caso afirmativo, a organização anfitriã poderá querer tratar os utilizadores da organização parceira como membros em vez de convidados. Utilize as APIs do Gestor de Convites Azure AD B2B para adicionar ou convidar um utilizador da organização parceira para a organização anfitriã como membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrar para utilizadores convidados no diretório

![Screenshot mostrando o filtro para os utilizadores convidados](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter tipo de utilizador
É possível converter o UserType de Membro para Convidado e vice-versa utilizando o PowerShell. No entanto, a propriedade UserType representa a relação do utilizador com a organização. Por isso, só deverá alterar esta propriedade se a relação do utilizador com a organização mudar. Se a relação do utilizador mudar, o nome principal do utilizador (UPN) deve mudar? Deve o utilizador continuar a ter acesso aos mesmos recursos? Deve ser atribuída uma caixa de correio? Não recomendamos alterar o UserType utilizando o PowerShell como uma atividade atómica. Além disso, no caso de esta propriedade se tornar imutável usando powerShell, não recomendamos tomar uma dependência deste valor.

## <a name="remove-guest-user-limitations"></a>Remover as limitações dos utilizadores dos hóspedes
Pode haver casos em que deseja dar aos seus utilizadores convidados privilégios mais elevados. Pode adicionar um utilizador convidado a qualquer função e até mesmo remover as restrições padrão de utilizador no diretório para dar a um utilizador os mesmos privilégios que os membros.

É possível desligar as limitações predefinidas para que um utilizador convidado no diretório da empresa tenha as mesmas permissões que um utilizador membro.

![Screenshot mostrando a opção de utilizadores externos nas definições do utilizador](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os utilizadores convidados visíveis na Lista de Endereços Globais do Exchange?
Sim. Por padrão, os objetos de hóspedes não são visíveis na lista de endereços globais da sua organização, mas pode usar o Azure Ative Directory PowerShell para torná-los visíveis. Para mais detalhes, consulte **Posso tornar os objetos de hóspedes visíveis na lista de endereços globais?** [Manage guest access in Office 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) 

## <a name="next-steps"></a>Passos seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Fichas de utilizador de colaboração B2B](user-token.md)
* [B2B colaboração utilizador reclama mapeamento](claims-mapping.md)
