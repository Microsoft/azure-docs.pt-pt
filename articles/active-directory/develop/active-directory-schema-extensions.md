---
title: Use atributos de extensão de esquema AD Azure em sinistros
titleSuffix: Microsoft identity platform
description: Descreve como utilizar atributos de extensão de esquema de diretório para enviar dados do utilizador para aplicações em sinistros simbólicos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755732"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Utilização de atributos de extensão de esquema de diretório em sinistros

Os atributos de extensão de esquema de diretório fornecem uma forma de armazenar dados adicionais no Azure Ative Directory em objetos de utilizador e outros objetos de diretório, tais como grupos, detalhes do inquilino, diretores de serviço.  Apenas os atributos de extensão em objetos do utilizador podem ser usados para emitir reclamações para aplicações. Este artigo descreve como utilizar atributos de extensão de esquema de diretório para o envio de dados do utilizador para aplicações em sinistros simbólicos.

> [!NOTE]
> O Microsoft Graph fornece dois outros mecanismos de extensão para personalizar objetos gráficos. Estas são conhecidas como extensões abertas do Microsoft Graph e extensões de esquema do Microsoft Graph. Consulte a documentação do [Microsoft Graph](/graph/extensibility-overview) para obter mais detalhes. Os dados armazenados em objetos do Microsoft Graph que utilizam estas capacidades não estão disponíveis como fontes para sinistros em tokens.

Os atributos de extensão do esquema de diretório estão sempre associados a uma aplicação no arrendatário e são referenciados pela *aplicaçãoid* da aplicação em seu nome.

O identificador de um atributo de extensão de esquema de diretório é do formulário *Extension_xxxxxxxxx_AttributeName*.  Onde *xxxxx* é a *aplicaçãoId* da aplicação para a qual a extensão foi definida.

## <a name="registering-and-using-directory-schema-extensions"></a>Registar e utilizar extensões de esquema de diretório
Os atributos de extensão do esquema de diretório podem ser registados e povoados de uma de duas maneiras:

- Configurando o AD Connect para criá-los e sincronizar os dados nos mesmos a partir das instalações AD. Ver [extensões de diretório de sincronização de ligação Azure AD](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Ao utilizar o Microsoft Graph para registar, descreva os valores e leia a partir de [extensões](/graph/extensibility-overview)de esquema . [Os cmdlets PowerShell](/powershell/azure/active-directory/using-extension-attributes-sample) também estão disponíveis.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Emissão de reclamações com dados de atributos de extensão de esquema de diretório criados com AD Connect
Os atributos de extensão de esquema de diretório criados e sincronizados usando o AD Connect estão sempre associados ao ID de aplicação utilizado pelo AD Connect. Podem ser utilizados como fonte de reclamações, configurando-as como reclamações na configuração **de Aplicações Empresariais** no Portal UI para aplicações SAML registadas usando a Galeria ou a experiência de configuração de aplicação não-Galeria ao abrigo **de Aplicações empresariais,** e através de uma política de mapeamento de reclamações para aplicações registadas através da experiência de registo de aplicações.  Uma vez que um atributo de extensão de diretório criado via AD Connect esteja no diretório, ele aparecerá na configuração de reclamações SAML SSO UI.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Emissão de reclamações com dados de atributos de extensão de esquema de diretório criados para uma aplicação usando Graph ou PowerShell
Se um atributo de extensão de esquema de diretório estiver registado para uma aplicação utilizando o Microsoft Graph ou PowerShell (através de uma configuração inicial de aplicações ou passo de provisionamento, por exemplo), a mesma aplicação pode ser configurada no Azure Ative Directory para receber dados nesse atributo de um objeto de utilizador numa reivindicação quando o utilizador se inscreve.  O pedido pode ser configurado para receber dados em extensões de esquema de diretório que estejam registadas nessa mesma aplicação usando [reclamações opcionais.](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)  Estes podem ser definidos no manifesto de candidatura.  Isto permite que uma aplicação multi-inquilino registe atributos de extensão de esquema de diretório para seu próprio uso. Quando a aplicação é disponibilizada a um inquilino, as extensões de esquema de diretório associadas ficam disponíveis para serem fixadas aos utilizadores desse inquilino e para serem consumidas.  Uma vez configurado no arrendatário e o consentimento concedido, pode ser usado para armazenar e recuperar dados através de gráfico e mapear para reclamações em fichas que a plataforma de identidade microsoft emite para aplicações.

Os atributos de extensão do esquema de diretório podem ser registados e povoados para qualquer aplicação.

Se uma aplicação precisar de enviar reclamações com dados de um atributo de extensão registado numa aplicação diferente, deve ser utilizada uma [política de mapeamento de sinistros](active-directory-claims-mapping.md) para mapear o atributo de extensão à reclamação.  Um padrão comum para gerir atributos de extensão de esquema de diretório é criar uma aplicação especificamente para ser o ponto de registo para todas as extensões de esquema que você precisa.  Não tem de ser uma aplicação real e esta técnica significa que todas as extensões têm o mesmo ID de aplicação em seu nome.

Por exemplo, aqui está uma política de mapeamento de reclamações para emitir uma única reclamação de um atributo de extensão de esquema de diretório num token OAuth/OIDC:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Onde *xxxxxxx* é o ID da aplicação com que a extensão foi registada.

> [!TIP]
> A consistência do caso é importante na definição de atributos de extensão do diretório em objetos.  Os nomes de atributos de extensão não são casos sensíveis quando são configurados, mas são sensíveis ao caso quando são lidos do diretório pelo serviço token.  Se um atributo de extensão for definido num objeto de utilizador com o nome "LegacyId" e noutro objeto de utilizador com o nome "legacyid", quando o atributo for mapeado para uma reclamação utilizando o nome "LegacyId" os dados serão recuperados com sucesso e a alegação incluída no símbolo para o primeiro utilizador, mas não a segunda.
>
> O parâmetro "ID" no esquema de alegações utilizado para atributos de diretório incorporado é "ExtensionID" para atributos de extensão de diretório.

## <a name="next-steps"></a>Próximos passos
- Saiba como [adicionar reclamações personalizadas ou adicionais aos tokens SAML 2.0 e JSON Web Tokens (JWT).](active-directory-optional-claims.md)
- Saiba como [personalizar as reclamações emitidas em fichas para uma aplicação específica.](active-directory-claims-mapping.md)