---
title: Configure o domínio de editor de uma aplicação [ Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o domínio de editor de uma aplicação para que os utilizadores saibam onde as suas informações estão a ser enviadas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697137"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Como configurar o domínio de editor de uma aplicação

O domínio de editor de uma aplicação é apresentado aos utilizadores no [pedido de consentimento da aplicação](application-consent-experience.md) para informar os utilizadores de onde as suas informações estão a ser enviadas. Aplicações multi-arrendatárias que são registadas após 21 de maio de 2019 que não têm um domínio de editor aparece como **não verificada.** As candidaturas multi-arrendatárias são candidaturas que suportam contas fora de um único diretório organizacional; por exemplo, apoiar todas as contas da AD Azure ou suportar todas as contas da AD Azure e contas pessoais da Microsoft.

## <a name="new-applications"></a>Novas aplicações

Ao registar uma nova aplicação, o domínio da editora da sua aplicação pode ser definido para um valor predefinido. O valor depende do local onde a app está registada, nomeadamente se a app está registada num inquilino e se o arrendatário tem domínios verificados pelo arrendatário.

Se existirem domínios verificados pelos inquilinos, o domínio de editor da aplicação será indefinido para o domínio verificado primário do inquilino. Se não houver domínios verificados por inquilinos (o que acontece quando a aplicação não está registada num inquilino), o domínio de editor da aplicação será definido como nulo.

A tabela seguinte resume o comportamento padrão do valor de domínio da editora.  

| Domínios verificados pelo arrendatário | Valor padrão do domínio da editora |
|-------------------------|----------------------------|
| nulo | nulo |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - -.onmicrosoft.com<br/>domain1.com<br/>- domain2.com (primária) | domain2.com |

Se o domínio de editor de uma aplicação multi-arrendatária não estiver definido, ou se for definido para um domínio que termina em .onmicrosoft.com, o pedido de consentimento da aplicação mostrará **não verificado** no lugar do domínio da editora.

## <a name="grandfathered-applications"></a>Aplicações de avô

Se a sua aplicação foi registada antes de 21 de maio de 2019, o pedido de consentimento da sua aplicação não mostrará **não será verificado se** não tiver definido um domínio de editor. Recomendamos que detetete o valor de domínio da editora para que os utilizadores possam ver esta informação no pedido de consentimento da sua aplicação.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configure o domínio da editora usando o portal Azure

Para definir o domínio de editor da sua aplicação, siga estes passos.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.

1. Se a sua conta estiver presente em mais de um inquilino da AD Azure:
   1. Selecione o seu perfil a partir do menu no canto superior direito da página e, em seguida, **mude de diretório**.
   1. Mude a sua sessão para o inquilino da AD Azure onde pretende criar a sua candidatura.

1. Navegue para [o Diretório Ativo do Azure > registos](https://go.microsoft.com/fwlink/?linkid=2083908) da App para encontrar e selecionar a app que pretende configurar.

   Assim que tiver selecionado a aplicação, verá a página de **visão geral** da aplicação.

1. Na página **'Overview'** da aplicação, selecione a secção **Branding.**

1. Encontre o campo de **domínio da Editora** e selecione uma das seguintes opções:

   - **Selecione Configurar um domínio** se ainda não tiver configurado um domínio.
   - Selecione **domínio de atualização** se um domínio já tiver sido configurado.

Se a sua aplicação estiver registada num inquilino, verá dois separadores para selecionar: **Selecione um domínio verificado** e **verifique um novo domínio**.

Se a sua aplicação não estiver registada num inquilino, só verá a opção de verificar um novo domínio para a sua aplicação.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar um novo domínio para a sua aplicação

1. Crie um `microsoft-identity-association.json` ficheiro nomeado e colhe o seguinte corte de código JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Substitua o espaço reservado *{YOUR-APP-ID-HERE}* pelo ID da aplicação (cliente) que corresponde à sua aplicação.

1. Hospedar o `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`ficheiro em: . Substitua o espaço reservado *{YOUR-DOMAIN-HERE}* para corresponder ao domínio verificado.

1. Clique no botão Verificar e guardar o botão **de domínio.**

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se o seu inquilino tiver domínios verificados, selecione um dos domínios do Select um dropdown de **domínio verificado.**

>[!Note]
> O cabeçalho esperado 'Content-Type' `application/json`que deve ser devolvido é . Pode ter um erro como mencionado abaixo se usar qualquer outra coisa como`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicações no pedido de consentimento da aplicação

Configurar o domínio da editora tem um impacto no que os utilizadores vêem no pedido de consentimento da aplicação. Para compreender plenamente os componentes do pedido de consentimento, consulte compreender as experiências de [consentimento da aplicação.](application-consent-experience.md)

A tabela seguinte descreve o comportamento das aplicações criadas antes de 21 de maio de 2019.

![Pedido de consentimento para apps criadas antes de 21 de maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento de novas aplicações criadas após o dia 21 de maio de 2019 dependerá do domínio da editora e do tipo de aplicação. A tabela seguinte descreve as alterações que deve esperar ver com as diferentes combinações de configurações.

![Pedido de consentimento para apps criadas após 21 de maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações na redirecionamento de URIs

As aplicações que assinam em utilizadores com qualquer conta de trabalho ou escola, ou contas pessoais da Microsoft[(multi-inquilinos)](single-and-multi-tenant-apps.md)estão sujeitas a poucas restrições ao especificar REdirecionamento de URIs.

### <a name="single-root-domain-restriction"></a>Restrição de domínio de raiz única

Quando o valor de domínio da editora para aplicações multi-arrendatárias está definido para nulo, as aplicações são restritas a partilhar um domínio raiz único para os URIs redirecionados. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio raiz, contoso.com, não corresponde fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Os subdomínios são permitidos, mas deve registar explicitamente o domínio raiz. Por exemplo, enquanto os seguintes URIs partilham um único domínio de raiz, a combinação não é permitida.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

No entanto, se o desenvolvedor adicionar explicitamente o domínio raiz, a combinação é permitida.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceções

Os seguintes casos não estão sujeitos à restrição de domínio raiz única:

- Aplicativos de inquilino único, ou aplicativos que visam contas num único diretório
- Utilização do hospedeiro local como URIs redirecionais
- Redirecione os URIs com regimes personalizados (não HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configure o domínio da editora programáticamente

Atualmente, não existe suporte REST API ou PowerShell para configurar o domínio da editora programáticamente.
