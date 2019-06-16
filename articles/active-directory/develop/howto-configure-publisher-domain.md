---
title: Configurar o domínio de publicador de uma aplicação | Azure
description: Saiba como configurar o domínio de publicador de um aplicativo para que os utilizadores saibam onde as suas informações estão a ser enviadas.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47075f9e18b299341a98983ffb8a47389fd7063
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540217"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Como: Configurar o domínio de publicador de uma aplicação (pré-visualização)

Domínio de publicador de uma aplicação é apresentado aos utilizadores no [solicitação de consentimento da aplicação](application-consent-experience.md) para que os utilizadores saibam onde as suas informações estão a ser enviadas. Aplicativos de multilocação que estão registados após a 21 de Maio de 2019 que não têm um domínio de publicador são exibidos como **não verificados**. Aplicativos de multilocação são aplicações que suportam contas fora de um único diretório organizacional; Por exemplo, suportar todas as contas do Azure AD ou suportam todas as contas do Azure AD e as contas Microsoft pessoais.

## <a name="new-applications"></a>Novas aplicações

Ao registar uma nova aplicação, o domínio de publicador da sua aplicação pode ser definido para um valor predefinido. O valor depende de onde o aplicativo está registrado, especialmente se a aplicação é registada num inquilino e se o inquilino tem inquilino domínios verificados.

Se houver domínios verificados do inquilino, o domínio de publicador da aplicação será predefinido para o domínio verificado principal do inquilino. Se existem nenhum inquilino verificado domínios (que é o caso da aplicação não está registada num inquilino), o domínio de publicador da aplicação será definido como nulo.

A tabela seguinte resume o comportamento padrão do valor de domínio de publicador.  

| Domínios verificados do inquilino | Valor predefinido de domínio de publicador |
|-------------------------|----------------------------|
| Nulo | Nulo |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (primário) | domain2.com |

Se não estiver configurado o domínio de publicador de uma aplicação multi-inquilino, ou se estiver definido como um domínio que termina em. onmicrosoft.com, a solicitação de consentimento da aplicação mostrará **não verificados** em vez do domínio de publicador.

## <a name="grandfathered-applications"></a>Aplicativos grandfathered

Se a aplicação foi registrada antes de 21 de Maio de 2019, a solicitação de consentimento da sua aplicação não apresentará **não verificados** se não tiver definido um domínio de publicador. Recomendamos que defina o publicador de valor de domínio para que os utilizadores podem ver estas informações no pedido de consentimento da sua aplicação.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurar o domínio de publicador com o portal do Azure

Para definir o domínio de publicador da sua aplicação, siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.

1. Se a sua conta não existe mais do que um inquilino do Azure AD:
   1. Selecione o seu perfil no menu no canto superior direito da página e, em seguida **trocar diretório**.
   1. Altere a sua sessão para o inquilino do Azure AD onde pretende criar a sua aplicação.

1. Navegue para [do Azure Active Directory > registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) para localizar e selecionar a aplicação que pretende configurar.

   Depois de selecionar a aplicação, verá a aplicação **descrição geral** página.

1. A partir da aplicação **descrição geral** página, selecione a **marca** secção.

1. Encontrar o **domínio de publicador** campo e selecione uma das seguintes opções:

   - Selecione **configurar um domínio** se ainda não configurou um domínio já.
   - Selecione **domínio de atualização** se já tiver sido configurado um domínio.

Se a sua aplicação for registada num inquilino, verá duas guias para selecionar a partir de: **Selecione um domínio verificado** e **Certifique-se de um novo domínio**.

Se a sua aplicação não está registada num inquilino, apenas verá a opção de verificar um novo domínio para a sua aplicação.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar um novo domínio para a sua aplicação

1. Crie um ficheiro denominado `microsoft-identity-association.json` e cole o seguinte fragmento de código JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Substitua o marcador de posição *{seu-aplicação-ID-HERE}* com o ID da aplicação (cliente) que corresponde à sua aplicação.

1. Alojar o ficheiro em: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Substitua o marcador de posição *{seu-DOMAIN-HERE}* de acordo com o domínio verificado.

1. Clique nas **verificar e guardar o domínio** botão.

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se o seu inquilino tiver verificado os domínios, selecione um dos domínios a partir da **selecionar um domínio verificado** lista pendente.

## <a name="implications-on-the-app-consent-prompt"></a>Implicações na aplicação do pedido de consentimento

Configurar o domínio de publicador tem um impacto no que os utilizadores veem na linha de comandos de consentimento da aplicação. Para compreender totalmente os componentes da solicitação de consentimento, veja [Noções básicas sobre experiências de consentimento da aplicação](application-consent-experience.md).

A tabela seguinte descreve o comportamento para aplicativos criados antes de 21 de Maio de 2019.

![Solicitação de consentimento para as aplicações criadas antes de 21 de Maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento de aplicativos criados após a 21 de Maio de 2019 dependerá do domínio de publicador e o tipo de aplicação. A tabela seguinte descreve as alterações que deve esperar para ver com as diferentes combinações de configurações.

![Pedido de consentimento para as aplicações criadas após a 21 de Maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações de URIs de redirecionamento

Aplicativos que iniciar sessão dos utilizadores com contas pessoais da Microsoft ou conta escolar ou profissional ([multi-inquilino](single-and-multi-tenant-apps.md)) estão sujeitos a algumas restrições ao especificar os URIs de redirecionamento.

### <a name="single-root-domain-restriction"></a>Restrição de domínio de raiz única

Quando o valor de domínio de publicador para aplicações multi-inquilino está definido para aplicações de nulas, são restritos para partilhar um domínio de raiz única para os URIs de redirecionamento. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio de raiz, contoso.com, não corresponde ao fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Subdomínios são permitidos, mas tem de registar explicitamente o domínio de raiz. Por exemplo, enquanto os seguintes URIs de partilhar um domínio de raiz única, a combinação não é permitida.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

No entanto, se o desenvolvedor adiciona explicitamente o domínio de raiz, a combinação é permitida.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceções

Os seguintes casos não estão sujeitas a restrições de domínio de raiz única:

- Aplicações de inquilino único ou as aplicações que contas num único diretório de destino
- Utilização do host local como URIs de redirecionamento
- URIs de redirecionamento com esquemas personalizados (não-HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurar o domínio de publicador através de programação

Atualmente, não há nenhum suporte de REST API ou o PowerShell para configurar o domínio de publicador através de programação.
