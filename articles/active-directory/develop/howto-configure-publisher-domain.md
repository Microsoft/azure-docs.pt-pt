---
title: Configure o domínio de uma aplicação | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o domínio de uma aplicação para informar os utilizadores para onde as suas informações estão a ser enviadas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104183"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Como: Configurar o domínio de uma aplicação

O domínio de uma aplicação é apresentado aos utilizadores no [consentimento da aplicação, a fim de](application-consent-experience.md) informar os utilizadores para onde as suas informações estão a ser enviadas. As aplicações multi-arrendadas que estão registadas após 21 de maio de 2019 que não têm um domínio de editor aparecem como **não verificadas.** As aplicações multi-arrendantes são aplicações que suportam contas fora de um único diretório organizacional; por exemplo, suporte todas as contas AD do Azure ou suporte todas as contas AD do Azure e contas pessoais da Microsoft.

## <a name="new-applications"></a>Novas aplicações

Quando regista uma nova aplicação, o domínio da editora da sua aplicação pode ser definido como um valor padrão. O valor depende do local onde a app está registada, nomeadamente se a app está registada num arrendatário e se o arrendatário tem domínios verificados pelo arrendatário.

Se existirem domínios verificados pelo inquilino, o domínio do editor da aplicação irá predefinir ao domínio verificado primário do inquilino. Se não existirem domínios verificados pelo arrendatário (o que acontece quando a aplicação não estiver registada num arrendatário), o domínio da editora da aplicação será definido como nulo.

A tabela seguinte resume o comportamento padrão do valor do domínio da editora.  

| Domínios verificados pelo arrendatário | Valor padrão do domínio do editor |
|-------------------------|----------------------------|
| nulo | nulo |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primário) | domain2.com |

Se o domínio de uma aplicação multi-arrendante não estiver definido, ou se estiver definido para um domínio que termina em .onmicrosoft.com, o pedido de consentimento da aplicação mostrará **não verificado** no lugar do domínio da editora.

## <a name="grandfathered-applications"></a>Aplicações de avô

Se a sua aplicação foi registada antes de 21 de maio de 2019, o pedido de consentimento da sua aplicação não **será** verificado se não tiver definido um domínio de editor. Recomendamos que desempenhe o valor do domínio da editora para que os utilizadores possam ver estas informações na solicitação de consentimento da sua aplicação.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configure o domínio do editor usando o portal Azure

Para definir o domínio de editor da sua aplicação, siga estes passos.

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual a aplicação está registada.
1. Navegue para [o Azure Ative Directory > as inscrições](https://go.microsoft.com/fwlink/?linkid=2083908) da App para encontrar e selecionar a aplicação que pretende configurar.

   Uma vez selecionada a aplicação, verá a página **geral** da aplicação.
1. Under **Manage**, selecione o **Branding**.
1. Encontre o campo **de domínio Publisher** e selecione uma das seguintes opções:

   - Selecione **Configurar um domínio** se ainda não tiver configurado um domínio.
   - Selecione **o domínio 'Update'** se um domínio já tiver sido configurado.

Se a sua aplicação estiver registada num inquilino, verá dois separadores para selecionar a partir de: **Selecione um domínio verificado** e **Verifique um novo domínio**.

Se a sua aplicação não estiver registada num inquilino, só verá a opção de verificar um novo domínio para a sua aplicação.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar um novo domínio para a sua aplicação

1. Crie um ficheiro com o nome `microsoft-identity-association.json` e cole o seguinte corte de código JSON.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Substitua o espaço reservado *{YOUR-APP-ID-HERE}* pelo ID de aplicação (cliente) que corresponda à sua aplicação.

1. Hospedar o ficheiro em: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . . Substitua o espaço reservado *{YOUR-DOMAIN-HERE}* para corresponder ao domínio verificado.

1. Clique no botão **Verificar e guardar o domínio.**

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se o seu inquilino tiver domínios verificados, selecione um dos domínios do **Select a descoduse de domínio verificado.**

>[!Note]
> O cabeçalho "Tipo de Conteúdo" esperado que deve ser devolvido é `application/json` . Pode obter um erro como mencionado abaixo se usar qualquer outra coisa como `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicações no pedido de consentimento da aplicação

Configurar o domínio da editora tem um impacto no que os utilizadores vêem na solicitação de consentimento da aplicação. Para compreender completamente os componentes do pedido de consentimento, consulte [a compreensão das experiências de consentimento do pedido.](application-consent-experience.md)

A tabela seguinte descreve o comportamento das aplicações criadas antes de 21 de maio de 2019.

![Pedido de consentimento para apps criadas antes de 21 de maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento das novas aplicações criadas após o dia 21 de maio de 2019 dependerá do domínio da editora e do tipo de aplicação. A tabela seguinte descreve as alterações que deve esperar ver com as diferentes combinações de configurações.

![Pedido de consentimento para apps criadas após 21 de maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações no redirecionamento de URIs

As aplicações que assinam em utilizadores com qualquer conta de trabalho ou escola, ou contas pessoais da Microsoft[(multi-inquilinos](single-and-multi-tenant-apps.md)) estão sujeitas a poucas restrições ao especificar URIs de redirecionamento.

### <a name="single-root-domain-restriction"></a>Restrição de domínio de raiz única

Quando o valor de domínio da editora para aplicações multi-inquilinos é definido como nulo, as aplicações são restritas a partilhar um único domínio de raiz para os URIs redirecionados. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio raiz, contoso.com, não corresponde fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Subdomínios são permitidos, mas você deve registar explicitamente o domínio raiz. Por exemplo, enquanto os URIs seguintes partilham um único domínio de raiz, a combinação não é permitida.

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

Os seguintes casos não estão sujeitos à restrição de domínio de raiz única:

- Aplicativos de inquilino único, ou aplicativos que visam contas em um único diretório
- Utilização de localhost como URIs redirecionando
- Redirecionar URIs com esquemas personalizados (não-HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurar o domínio do editor programáticamente

Atualmente, não existe suporte de API ou PowerShell para configurar programáticamente o domínio da editora.
