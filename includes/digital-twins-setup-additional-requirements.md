---
author: baanders
description: incluir ficheiros para possíveis requisitos adicionais na configuração dos Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: c2668bdda5002ebd2a34b8a2ffa5885263aec0c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009671"
---
É possível que a sua organização exija ações adicionais de proprietários/administradores de subscrição para configurar com sucesso um registo de aplicações (e assim, para terminar a criação de uma instância Azure Digital Twins utilizável). Os passos necessários podem variar dependendo das definições específicas da sua organização.

Aqui estão algumas atividades potenciais comuns que um Proprietário/administrador pode precisar realizar. Estas e outras operações podem ser realizadas a partir da página de registos da [*App Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal Azure.
* Conceder consentimento administrativo para o registo da aplicação. A sua organização pode ter *o Consentimento Admin Exigido* globalmente ligado em Azure AD para todos os registos de aplicações dentro da sua subscrição. Em caso afirmativo, o Proprietário/administrador terá de selecionar este botão para a sua empresa na página de *permissões API* do registo da aplicação para que o registo da aplicação seja válido:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Visão do portal do botão 'Conceder consentimento administrativo' sob permissões API":::
  - Se o consentimento tiver sido concedido com sucesso, a entrada para a Azure Digital Twins deve então mostrar um valor de *Estado* de _Concedido para **(a sua empresa)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Visão do portal do botão 'Conceder consentimento administrativo' sob permissões API":::
* Ativar o acesso do cliente público
* Definir URLs de resposta específico para acesso web e desktop
* Permitir fluxos implícitos de autenticação OAuth2

Para obter mais informações sobre o registo de aplicações e as suas diferentes opções de configuração, consulte [*Registar uma aplicação com a plataforma de identidade microsoft.*](https://docs.microsoft.com/graph/auth-register-app-v2)

Tem agora uma instância Azure Digital Twins pronta a ser executada, atribuiu permissões para a gerir e criou permissões para uma aplicação do cliente aceder à sua.