---
author: baanders
description: incluir ficheiros para possíveis requisitos adicionais na configuração dos Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b490b4304dd74d8266b24d0ea5af58726b14d747
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125814"
---
É possível que a sua organização exija ações adicionais dos Proprietários de subscrição para configurar com sucesso um registo de aplicações (e, consequentemente, terminar a criação de uma instância Azure Digital Twins utilizável). Os passos necessários podem variar dependendo das definições específicas da sua organização.

Aqui estão algumas atividades potenciais comuns que um Proprietário pode precisar realizar. Estas e outras operações podem ser realizadas a partir da página de registos da [*App Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal Azure.
* Conceder consentimento administrativo para o registo da aplicação. A sua organização pode ter *o Consentimento Admin Exigido* globalmente ligado em Azure AD para todos os registos de aplicações dentro da sua subscrição. Em caso afirmativo, o Proprietário terá de selecionar este botão para a sua empresa na página de *permissões API* do registo da aplicação para que o registo da aplicação seja válido:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="Visão do portal do botão 'Conceder consentimento administrativo' sob permissões API":::
  - Se o consentimento tiver sido concedido com sucesso, a entrada para a Azure Digital Twins deve mostrar um valor de *Estado* de _Concessão para **(a sua empresa)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="Visão do portal do consentimento administrativo concedido à empresa ao abrigo de permissões da API":::
  - O Proprietário pode preferir repetir este processo para cada registo de aplicações que seja criado, ou fazê-lo uma vez e estabelecer um registo único de aplicações partilhadas para todos os casos Azure Digital Twins na subscrição a utilizar. No segundo cenário, o Proprietário deverá partilhar o *ID* do cliente e o *ID* do inquilino para o registo da aplicação com os desenvolvedores que terão de utilizar o registo da app. (É assim que se faz dentro do próprio inquilino da Microsoft).
* Ativar o acesso do cliente público
* Definir URLs de resposta específico para acesso web e desktop
* Permitir fluxos implícitos de autenticação OAuth2

Para obter mais informações sobre o registo de aplicações e as suas diferentes opções, consulte [*Registar uma aplicação com a plataforma de identidade microsoft.*](https://docs.microsoft.com/graph/auth-register-app-v2)

Tem agora uma instância Azure Digital Twins pronta a ser executada, atribuiu permissões para a gerir e criou permissões para uma aplicação do cliente aceder à sua.