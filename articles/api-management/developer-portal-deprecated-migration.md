---
title: Migrar para o novo portal de desenvolvedores a partir do portal de desenvolvedores legado
titleSuffix: Azure API Management
description: Saiba como migrar do portal de desenvolvimento legado para o novo portal de desenvolvimento na API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326026"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrar para o novo portal de desenvolvedores

Este artigo descreve os passos que precisa de tomar para migrar do portal legado deprecado para o novo portal de desenvolvedores na API Management.

> [!IMPORTANT]
> O portal de desenvolvedores legados está agora deprecado e receberá apenas atualizações de segurança. Pode continuar a usá-lo, como de costume, até à sua reforma em outubro de 2023, altura em que será removido de todos os serviços de Gestão da API.

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Melhorias no novo portal de desenvolvimento

O novo portal de desenvolvedores aborda muitas limitações do portal deprecado. Apresenta um [editor visual de drag-and-drop para editar conteúdos](api-management-howto-developer-portal-customize.md) e um painel dedicado para os designers modelarem o site. As páginas, personalizações e configuração são guardadas como recursos do Azure Resource Manager no seu serviço de Gestão de API, que permite [automatizar implementações de portais](api-management-howto-developer-portal.md#automate). Por último, a base de código do portal é de código aberto, para [que possa alargá-la com funcionalidades personalizadas.](api-management-howto-developer-portal.md#managed-vs-self-hosted)

## <a name="how-to-migrate-to-new-developer-portal"></a>Como migrar para novo portal de desenvolvedores

O novo portal de desenvolvedores é incompatível com o portal deprecado e a migração automatizada não é possível. É necessário recriar manualmente o conteúdo (páginas, texto, ficheiros de mídia) e personalizar a aparência do novo portal. Os passos precisos variarão consoante as personalizações e complexidade do seu portal. Consulte [o tutorial do portal do desenvolvedor](api-management-howto-developer-portal-customize.md) para obter orientação. A configuração remanescente, como a lista de APIs, produtos, utilizadores, fornecedores de identidade, é automaticamente partilhada em ambos os portais.

> [!IMPORTANT]
> Se já lançou o novo portal do programador, mas ainda não fez alterações, [reinicie o conteúdo padrão](api-management-howto-developer-portal.md#preview-to-ga) para o atualizar para a versão mais recente.

Quando migrar do portal deprecado, lembre-se das seguintes alterações:

- Se expor o seu portal de desenvolvedores através de um domínio personalizado, [atribua um domínio](configure-custom-domain.md) ao novo portal de desenvolvedores. Utilize a opção **portal Developer** a partir do dropdown no portal Azure.
- [Aplique uma política CORS](api-management-howto-developer-portal.md#cors) nas suas APIs para ativar a consola de teste interativa.
- Se injetar CSS personalizado para modelar o portal, tem de [replicar o estilo utilizando o painel de design incorporado.](api-management-howto-developer-portal-customize.md) A injeção de CSS não é permitida no novo portal.
- Pode injetar JavaScript personalizado apenas na [versão auto-hospedada do novo portal](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Se a sua Gestão API estiver numa rede virtual e estiver exposta à Internet através do Application Gateway, [consulte este artigo de documentação](api-management-howto-integrate-internal-vnet-appgateway.md) para obter etapas precisas de configuração. Tem de:

    - Permitir a conectividade com o ponto final de gestão da API Management.
    - Ativar a conectividade com o novo ponto final do portal.
    - Desative as regras selecionadas de Firewall de Aplicação Web.

- Se alterou os modelos de notificação por e-mail predefinidos para incluir um URL de portal preterido explicitamente definido, altere-os para utilizar o parâmetro URL do portal ou apontar para o novo URL do portal. Se os modelos utilizarem o parâmetro URL do portal incorporado, não são necessárias alterações.
- *As questões* e *aplicações* não são suportadas no novo portal de desenvolvedores.
- A integração direta com o Facebook, Microsoft, Twitter e Google como fornecedores de identidade não é suportada no novo portal de desenvolvedores. Pode integrar-se com esses fornecedores através do Azure AD B2C.
- Se utilizar a delegação, altere o URL de retorno nas suas aplicações e use o ponto final [ *get Shared Token API* ](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) em vez do ponto final *do URL Generate SSO.*
- Se utilizar a Azure AD como fornecedor de identidade:

    - Altere o URL de retorno na sua aplicação para indicar o novo domínio do portal do desenvolvedor.
    - Modifique o sufixo do URL de devolução na sua aplicação a partir de `/signin-aad` `/signin` .

- Se utilizar o Azure AD B2C como fornecedor de identidade:

    - Altere o URL de retorno na sua aplicação para indicar o novo domínio do portal do desenvolvedor.
    - Modifique o sufixo do URL de devolução na sua aplicação a partir de `/signin-aad` `/signin` .
    - Incluir *O Nome Dado,* *Apelido*e *ID de Objeto do Utilizador* nas reclamações da aplicação.

- Se utilizar o OAuth 2.0 na consola de teste interativa, altere o URL de retorno na sua aplicação para apontar para o novo domínio do portal do desenvolvedor e modificar o sufixo:

    - De `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` para o fluxo de `/signin-oauth/code/callback/[serverName]` concessão de código de autorização.
    - De `/docs/services/[serverName]/console/oauth2/implicit/callback` para o fluxo implícito de `/signin-oauth/implicit/callback` concessão.
- Se utilizar o OpenID Connect na consola de teste interativa, altere o URL de retorno na sua aplicação para apontar para o novo domínio do portal do desenvolvedor e modificar o sufixo:

    - De `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` para o fluxo de `/signin-oauth/code/callback/[serverName]` concessão de código de autorização.
    - De `/docs/services/[serverName]/console/openidconnect/implicit/callback` para o fluxo implícito de `/signin-oauth/implicit/callback` concessão.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Visão geral do portal de desenvolvimento da AZure API Management](api-management-howto-developer-portal.md)
- [Aceda e personalize o portal do desenvolvedor](api-management-howto-developer-portal-customize.md)