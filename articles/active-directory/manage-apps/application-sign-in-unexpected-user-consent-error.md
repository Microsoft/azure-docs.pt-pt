---
title: Erro inesperado ao consentir uma aplicação | Documentos da Microsoft
description: Aborda os erros que podem ocorrer durante o processo de consentir uma aplicação e o que pode fazer sobre eles
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff3be9a9bc7fd897f340e5fe6a4775a4914810
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824945"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erro inesperado ao consentir uma aplicação

Este artigo aborda os erros que podem ocorrer durante o processo de consentir uma aplicação. Se estiver a resolver solicitações de consentimento inesperado que não contêm quaisquer mensagens de erro, consulte [cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Muitos aplicativos que se integram com o Azure Active Directory requerem permissões para aceder a outros recursos para funcionar. Quando esses recursos também estão integrados no Azure Active Directory, permissões para aceder aos mesmos, muitas vezes, é solicitado usando a estrutura comum de consentimento. Um pedido de consentimento é apresentado, que geralmente ocorre na primeira vez que um aplicativo é usado, mas também pode ocorrer num uso subseqüente do aplicativo.

Tem de ser verdadeiras para um utilizador dar consentimento às permissões de que um aplicativo exigir determinadas condições. Se estas condições não forem cumpridas, podem ocorrer os seguintes erros.

## <a name="requesting-not-authorized-permissions-error"></a>Erro de permissões não autorizado a pedir
* **AADSTS90093:** &lt;clientAppDisplayName&gt; está a solicitar uma ou mais permissões que não tem autorização para conceder. Contacte um administrador, que pode consentir a esta aplicação em seu nome.

Este erro ocorre quando um utilizador que não seja um administrador da empresa tenta utilizar uma aplicação que está a pedir permissões que apenas um administrador pode conceder. Este erro pode ser resolvido por um administrador conceder acesso à aplicação em nome da sua organização.

## <a name="policy-prevents-granting-permissions-error"></a>Política impede a conceder permissões de erro
* **AADSTS90093:** Um administrador do &lt;tenantDisplayName&gt; definiu uma política que impede a concessão &lt;nome da aplicação&gt; as permissões que está a solicitar. Contacte um administrador do &lt;tenantDisplayName&gt;, que pode conceder permissões a esta aplicação em seu nome.

Este erro ocorre quando um administrador de empresa desativa a capacidade dos utilizadores dar consentimento a aplicações e, em seguida, um utilizador de não-administrador tenta utilizar uma aplicação que necessita de consentimento. Este erro pode ser resolvido por um administrador conceder acesso à aplicação em nome da sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** Parece que o processo de início de sessão encontrou um problema intermitente gravar as permissões que tentou conceder aos &lt;clientAppDisplayName&gt;. Tente novamente mais tarde.

Este erro indica que ocorreu um problema de lado do serviço. Puder ser resolvido, tentando autorizar a aplicação novamente.

## <a name="resource-not-available-error"></a>Recurso erro não está disponível
* **AADSTS65005:** A aplicação &lt;clientAppDisplayName&gt; solicitadas permissões para aceder a um recurso &lt;resourceAppDisplayName&gt; que não está disponível. 

Contacte o programador da aplicação.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não está disponível no erro de inquilino
* **AADSTS65005:** &lt;clientAppDisplayName&gt; está a solicitar acesso a um recurso &lt;resourceAppDisplayName&gt; que não está disponível na sua organização &lt;tenantDisplayName &gt;. 

Certifique-se de que este recurso está disponível ou contacte o administrador do &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Erro de correspondência de permissões
* **AADSTS65005:** A aplicação pediu o consentimento para recursos de acesso &lt;resourceAppDisplayName&gt;. Este pedido falhou porque não corresponde a como a aplicação foi previamente configurada durante o registo de aplicação. Entre em contato com a aplicação vendor.* *

Estes erros todos ocorrem quando o aplicativo de um usuário está tentando dar consentimento a está a pedir permissões para aceder a uma aplicação de recurso que não pode ser encontrada no diretório da organização (inquilino). Esta situação pode ocorrer por diversos motivos:

-   O desenvolvedor do aplicativo cliente tenha configurado seu aplicativo incorretamente, fazendo com que ele solicitar acesso a um recurso é inválida. Neste caso, o programador da aplicação tem de atualizar a configuração da aplicação cliente para resolver este problema.

-   Um Principal de serviço que representa o aplicativo de recurso de destino não existe na organização, ou existiu no passado, mas foi removido. Para resolver este problema, um Principal de serviço para a aplicação de recurso tem de ser aprovisionado na organização para que a aplicação cliente pode pedir permissões às mesmas. O Principal de serviço pode ser aprovisionado de diversas formas, consoante o tipo de aplicação, incluindo:

    -   Adquirir uma assinatura para a aplicação de recurso (aplicações publicadas da Microsoft)

    -   Que consinta a aplicação de recurso

    -   Conceder permissões de aplicação através do portal do Azure

    -   Adicionar a aplicação a partir da Galeria de aplicações do Azure AD

## <a name="next-steps"></a>Passos Seguintes 

[Aplicações, permissões e consentimento no Azure Active Directory (ponto de final v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Âmbitos, permissões e consentimento no Azure Active Directory (ponto final v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


