---
title: Erro inesperado ao efetuar o consentimento para uma aplicação Microsoft Docs
description: Discute erros que podem ocorrer durante o processo de consentir com uma aplicação e o que pode fazer sobre eles
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 558c6dc24f6d0d17c9a82bbc79f39649f63dc7f4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658490"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erro inesperado ao efetuar o consentimento de uma aplicação

Este artigo discute erros que podem ocorrer durante o processo de consentimento para uma aplicação. Se estiver a resolver pedidos de consentimento inesperados que não contenham mensagens de erro, consulte [Cenários de Autenticação para Azure AD](../develop/authentication-vs-authorization.md).

Muitas aplicações que se integram com o Azure Ative Directory requerem permissões para aceder a outros recursos para funcionar. Quando estes recursos também são integrados com o Azure Ative Directory, as permissões de acesso aos mesmos são frequentemente solicitadas usando o quadro comum de consentimento. É apresentado um pedido de consentimento, que geralmente ocorre na primeira vez que uma aplicação é usada, mas também pode ocorrer numa utilização subsequente da aplicação.

Certas condições devem ser verdadeiras para que um utilizador consinta nas permissões que uma aplicação requer. Se estas condições não forem satisfeitas, podem ocorrer os seguintes erros.

## <a name="requesting-not-authorized-permissions-error"></a>Solicitando erro de permissões não autorizadas
* **AADSTS90093:** &lt; clienteAppDisplayName &gt; está a solicitar uma ou mais permissões que não está autorizado a conceder. Contacte um administrador que possa consentir com este pedido em seu nome.
* **AADSTS90094:** &lt; clienteAppDisplayName &gt; precisa de permissão para aceder a recursos na sua organização que só um administrador pode conceder. Peça a um administrador para conceder permissão para esta aplicação antes de poder utilizá-la.

Este erro ocorre quando um utilizador que não é administrador da empresa tenta usar uma aplicação que está a solicitar permissões que só um administrador pode conceder. Este erro pode ser resolvido por um administrador que concede acesso ao pedido em nome da sua organização.

Este erro também pode ocorrer quando um utilizador é impedido de consentir com uma aplicação devido à Microsoft detetar que o pedido de permissões é arriscado. Neste caso, será também registado um evento de auditoria com uma categoria de "Gestação de Aplicações", Tipo de Atividade de "Consentimento à aplicação" e Razão de Estado de "Aplicação arriscada detetada".

Outro cenário em que este erro pode ocorrer é quando a atribuição do utilizador é necessária para a aplicação, mas não foi fornecido nenhum consentimento do administrador. Neste caso, o administrador deve primeiro fornecer o consentimento do administrador.   

## <a name="policy-prevents-granting-permissions-error"></a>A política impede a concessão de permissões de erro
* **AADSTS90093:** Um administrador do &lt; InquilinaDisplayMe &gt; definiu uma política que o impede de conceder &lt; o nome da app as &gt; permissões que está a solicitar. Contacte um administrador do &lt; TenantDisplayName, &gt; que pode conceder permissões a esta aplicação em seu nome.

Este erro ocorre quando um administrador da empresa desliga a capacidade de os utilizadores consentirem com as aplicações, então um utilizador não administrador tenta usar uma aplicação que requer consentimento. Este erro pode ser resolvido por um administrador que concede acesso ao pedido em nome da sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** Parece que o processo de login encontrou um problema intermitente a registar as permissões que tentou conceder ao &lt; clienteAppDisplayName &gt; . tente novamente mais tarde.

Este erro indica que ocorreu um problema intermitente no lado do serviço. Pode ser resolvido tentando consentir novamente com o pedido.

## <a name="resource-not-available-error"></a>Erro não disponível de recursos
* **AADSTS65005:** O cliente da &lt; aplicaçãoAppDisplayName &gt; solicitou permissões para aceder a um &lt; recurso de recursosAppDisplayName &gt; que não está disponível. 

Contacte o programador da aplicação.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não disponível em erro do inquilino
* **AADSTS65005:** &lt; clienteAppDisplayName &gt; está solicitando acesso a um &lt; recurso de recursosAppDisplayName &gt; que não está disponível na sua organização &lt; inquilinaDisplayName. &gt; 

Certifique-se de que este recurso está disponível ou contacte um administrador do &lt; InquilinaDisplay. &gt;

## <a name="permissions-mismatch-error"></a>Permissões erro incompatível
* **AADSTS65005:** A aplicação solicitou consentimento para aceder aos &lt; recursosAppDisplayName &gt; . Este pedido falhou porque não corresponde à forma como a app foi pré-configurada durante o registo da app. Contacte o fornecedor de aplicações.**

Todos estes erros ocorrem quando a aplicação que um utilizador está a tentar consentir está a solicitar permissões para aceder a uma aplicação de recursos que não pode ser encontrada no diretório da organização (inquilino). Esta situação pode ocorrer por várias razões:

-   O desenvolvedor de aplicações do cliente configura a sua aplicação incorretamente, fazendo com que solicite o acesso a um recurso inválido. Neste caso, o desenvolvedor de aplicações deve atualizar a configuração da aplicação do cliente para resolver este problema.

-   Um Diretor de Serviço que represente a aplicação de recursos-alvo não existe na organização, ou existiu no passado, mas foi removido. Para resolver este problema, um Diretor de Serviço para o pedido de recurso deve ser aprovisionado na organização para que o pedido do cliente possa solicitar permissões. O Diretor de Serviço pode ser a provisionado de várias formas, dependendo do tipo de aplicação, incluindo:

    -   Aquisição de uma subscrição para a aplicação de recursos (aplicações publicadas pela Microsoft)

    -   Consentimento para a aplicação de recursos

    -   Concessão das permissões de candidatura através do portal Azure

    -   Adicionar o pedido da Galeria de Aplicações AD Azure

## <a name="risky-app-error-and-warning"></a>Erro de aplicação arriscado e aviso
* **AADSTS900941:** É necessário o consentimento do administrador. A aplicação é considerada arriscada. (AdminConsentRequiredDueToRiskyApp)
* Esta aplicação pode ser arriscada. Se confia nesta aplicação, por favor peça ao seu administrador que lhe conceda acesso.
* **AADSTS900981:** Foi recebido um pedido de consentimento administrativo para uma aplicação de risco. (AdminConsentRequestRiskyAppWarning)
* Esta aplicação pode ser arriscada. Só continuará se confiar nesta aplicação.

Ambas as mensagens serão exibidas quando a Microsoft determinar que o pedido de consentimento pode ser arriscado. Entre uma série de outros fatores, tal pode ocorrer se um [editor verificado](../develop/publisher-verification-overview.md) não tiver sido adicionado ao registo da app. O primeiro código de erro e mensagem será mostrado aos utilizadores finais quando o fluxo de trabalho de consentimento do [Administrador](configure-admin-consent-workflow.md) estiver desativado. O segundo código e mensagem será mostrado aos utilizadores finais quando o fluxo de trabalho de consentimento administrativo estiver ativado e aos administradores. 

Os utilizadores finais não poderão conceder o consentimento a apps que tenham sido detetadas como arriscadas. Os administradores são capazes de o fazer, mas devem avaliar a app com muito cuidado e proceder com cautela. Se a aplicação parecer suspeita após nova revisão, pode ser reportada à Microsoft a partir do ecrã de consentimento. 

## <a name="next-steps"></a>Próximos passos 

[Aplicativos, permissões e consentimento no Azure Ative Directory (v1 endpoint)](../develop/quickstart-register-app.md)<br>

[Âmbitos, permissões e consentimento no Diretório Ativo do Azure (v2.0 ponto final)](../develop/v2-permissions-and-consent.md)