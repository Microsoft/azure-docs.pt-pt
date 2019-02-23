---
title: Propriedades de um utilizador de colaboração do B2B - Azure Active Directory | Documentos da Microsoft
description: Propriedades de utilizador de colaboração do Azure Active Directory B2B são configuráveis
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45e9553a3af8a09a6630efa771294661702feef5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670715"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um utilizador de colaboração do Azure Active Directory B2B

Um utilizador de colaboração do Azure Active Directory (Azure AD) empresa-empresa (B2B) é um utilizador com UserType = convidado. Normalmente, este utilizador convidado é de uma organização parceira e tem os privilégios no diretório do convite, limitados por predefinição.

Consoante as necessidades da organização de convite, um utilizador de colaboração B2B do Azure AD pode ter um dos seguintes Estados de conta:

- Estado de 1: Que se encontrem numa instância externa do Azure AD e representada como um utilizador convidado na organização de convite. Neste caso, o utilizador B2B inicia sessão utilizando uma conta do Azure AD que pertença ao inquilino de convidado. Se a organização de parceiro não utilizar o Azure AD, o utilizador convidado no Azure AD ainda é criado. Os requisitos são que eles resgatar o convite e do Azure AD verifica o respetivo endereço de e-mail. Essa organização também é denominada um inquilinos de (JIT) just-in-time ou um "viral" inquilinos.

- Estado 2: Que se encontrem no Microsoft ou outra conta e representada como um utilizador convidado na organização do anfitrião. Neste caso, o utilizador convidado entra com uma conta Microsoft ou uma conta de redes sociais (google.com ou semelhante). Identidade do utilizador convidado é criada como uma conta Microsoft no diretório da organização convidou durante o resgate de oferta.

- Estado de 3: Que se encontrem no Active Directory no local da organização de anfitrião e sincronizados com o Azure da organização de anfitrião AD. Pode utilizar o Azure AD Connect para sincronizar as contas de parceiros para a cloud como utilizadores do Azure AD B2B com UserType = convidado. Ver [conceder acesso de contas de parceiros gerido localmente a recursos na cloud](hybrid-on-premises-to-cloud.md).

- Estado de 4: Que se encontrem no Azure da organização de anfitrião AD com UserType = convidado e as credenciais que gere a organização de anfitrião.

  ![Exibindo iniciais na máquina](media/user-properties/redemption-diagram.png)


Agora, vamos ver o aspeto de um utilizador de colaboração B2B do Azure AD no Azure AD.

### <a name="before-invitation-redemption"></a>Antes de resgate de convite

Estado 1 e 2 do Estado contas são o resultado de convidar utilizadores convidados para colaborar com as credenciais dos utilizadores de convidado. Quando o convite é inicialmente enviado ao utilizador convidado, é criada uma conta no seu diretório. Esta conta não tem quaisquer credenciais associadas com a mesma, porque a autenticação é efetuada pelo fornecedor de identidade do utilizador convidado. O **origem** propriedade para a conta de utilizador convidado no seu diretório está definida como **Invited utilizador**. 

![Antes de resgate da oferta](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Após o resgate de convite

Depois do utilizador convidado aceita o convite, o **origem** propriedade é atualizada com base no fornecedor de identidade do utilizador convidado.

Para os utilizadores convidados no estado 1, o **origem** é **externos do Azure Active Directory**.

![1 utilizador convidado de estado após o resgate de oferta](media/user-properties/after-redemption-state1.png)

Para os utilizadores convidados no estado 2, o **origem** é **Account Microsoft**.

![Utilizador convidado de estado 2 após o resgate de oferta](media/user-properties/after-redemption-state2.png)

Para os utilizadores convidados no Estado 3 e 4 de estado, o **origem** estiver definida como **Azure Active Directory** ou **Windows Server Active Directory**, conforme descrito na secção seguinte.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Principais propriedades de utilizador de colaboração B2B do Azure AD
### <a name="usertype"></a>UserType
Esta propriedade indica a relação do utilizador para os inquilinos do anfitrião. Esta propriedade pode ter dois valores:
- Membro: Este valor indica um funcionário da organização anfitrião e um utilizador na folha de pagamento da organização. Por exemplo, este utilizador espera que tenham acesso aos sites de apenas internos. Este utilizador não é considerado um funcionário externo.

- Convidado: Este valor indica que um utilizador que não seja considerado interno para a empresa, como um funcionário externo, parceiro ou cliente. Não se espera que esse usuário receber memorando interno de um CEO ou receber os benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType tem sem relação com a forma como o utilizador inicia sessão, a função de diretório do utilizador e assim por diante. Esta propriedade simplesmente indica a relação do utilizador para a organização de anfitrião e permite que a organização impor políticas que dependem desta propriedade.

### <a name="source"></a>Origem
Esta propriedade indica como o utilizador inicia sessão.

- Utilizador convidado: Este utilizador foi convidado, mas ainda não tiver resgatado um convite.

- Externo do Active Directory: Este utilizador está hospedado numa organização externa e autentica utilizando uma conta do Azure AD que pertence a outra organização. Este tipo de início de sessão corresponde a 1 de estado.

- Conta da Microsoft: Este utilizador está hospedado numa conta Microsoft e é autenticado ao utilizar uma conta Microsoft. Este tipo de início de sessão corresponde a estado 2.

- Windows Server Active Directory: Este utilizador é iniciou sessão a partir do Active Directory no local e que pertence a esta organização. Este tipo de início de sessão corresponde a 3 de estado.

- Azure Active Directory: Este utilizador é autenticado com uma conta do Azure AD que pertence a esta organização. Este tipo de início de sessão corresponde a 4 de estado.
  > [!NOTE]
  > Origem e UserType são propriedades independentes. Um valor de origem não implica um valor específico para UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Utilizadores B2B do Azure AD podem ser adicionados como membros em vez de convidados?
Normalmente, um utilizador do Azure AD B2B e o utilizador convidado são sinônimos. Por conseguinte, um utilizador de colaboração B2B do Azure AD é adicionado como um utilizador com UserType = convidados por predefinição. No entanto, em alguns casos, a organização do parceiro é um membro de uma grande organização a que a organização de anfitrião também pertence. Se assim for, a organização de anfitrião possa querer tratar os utilizadores na organização do parceiro como membros em vez de convidados. Utilize as APIs do Azure AD B2B convite Manager para adicionar ou Convide um utilizador da organização parceira para a organização do anfitrião como membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtro para os utilizadores convidados no diretório

![Filtrar utilizadores convidados](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter UserType
É possível converter UserType de membro para o convidado e vice-versa com o PowerShell. No entanto, a propriedade UserType representa a relação do utilizador para a organização. Portanto, deve alterar esta propriedade apenas se for a relação do utilizador para as alterações da organização. Se mudar a relação do utilizador, deve alterar ao nome principal de utilizador (UPN)? O utilizador deve continuar a ter acesso aos mesmos recursos? Deve ser atribuída uma caixa de correio? Não é recomendada a alterar o UserType com o PowerShell como uma atividade atômica. Além disso, no caso desta propriedade se torna imutável com o PowerShell, não recomendamos que tenha uma dependência nesse valor.

## <a name="remove-guest-user-limitations"></a>Remover limitações de utilizador convidado
Pode haver casos em que pretende atribuir aos seus usuários de convidado privilégios mais altos. Pode adicionar um utilizador convidado a qualquer função e até remover as restrições de utilizador de convidado predefinidas no diretório para dar os mesmos privilégios como membros de um utilizador.

É possível desativar as limitações de padrão para que um utilizador convidado no diretório da empresa tem as mesmas permissões que um utilizador de membro.

![Remover limitações de utilizador convidado](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso fazer os utilizadores convidados visível na lista de endereços Global do Exchange?
Sim. Por predefinição, os objetos de convidado não são visíveis na lista de endereços global da sua organização, mas pode utilizar o Azure Active Directory PowerShell para que fiquem visíveis. Para obter detalhes, consulte **posso fazer objetos de convidado visível na lista de endereços global?** na [gerir o acesso de convidado no grupos do Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq). 

## <a name="next-steps"></a>Passos Seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Tokens de utilizador de colaboração B2B](user-token.md)
* [Mapeamento de afirmações de utilizador de colaboração de B2B](claims-mapping.md)
