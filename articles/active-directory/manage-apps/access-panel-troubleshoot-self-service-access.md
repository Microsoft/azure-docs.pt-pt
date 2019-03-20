---
title: Problema ao utilizar o acesso de aplicações self-service | Documentos da Microsoft
description: Resolver problemas relacionados com o acesso de aplicações self-service
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd3ae06c42452d425f041543715336827048ccb2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076450"
---
# <a name="problem-using-self-service-application-access"></a>Problema ao utilizar o acesso de aplicações self-service

Acesso de aplicações self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a esses aplicativos. Pode permitir que o grupo de empresas gerir as credenciais atribuídas a esses utilizadores para a direita da palavra-passe de início de sessão único em aplicações de seus painéis de acesso.

Antes dos utilizadores Self-podem detetar aplicações a partir de seu painel de acesso, tem de ativar **acesso da aplicação self-service** para todos os aplicativos que deseja permitir que os utilizadores Self-detetar e pedir acesso.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Certifique-se de acesso da aplicação self-service está configurado corretamente. Consulte "Como configurar o acesso de aplicações self-service".

-   Certifique-se de que o utilizador ou grupo tenha sido ativado para pedir acesso de aplicações self-service.

-   Certifique-se de que o utilizador é visitar o local correto para acesso de aplicações self-service. os usuários podem navegar para seus [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique nas **+ adicionar** botão para encontrar as aplicações a que tiver ativado o acesso self-service.

-   Se recentemente foi configurado o acesso de aplicações self-service, tente iniciar e terminar novamente no painel de acesso do utilizador após alguns minutos para ver se as alterações de acesso personalizado tem parecido.

## <a name="how-to-configure-self-service-application-access"></a>Como configurar o acesso de aplicações self-service

Para ativar o acesso de aplicações self-service a uma aplicação, siga os passos abaixo:

1. Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerda principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** do menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende ativar a gestão personalizada de acesso a partir da lista.

7. Assim que o aplicativo é carregado, clique em **self-service** no menu de navegação do lado esquerdo da aplicação.

8. Para ativar o acesso de aplicações self-service para esta aplicação, ative o **permitir que os utilizadores peçam acesso a esta aplicação?** alternar para **Sim.**

9. Em seguida, para selecionar o grupo a que os utilizadores que pedem acesso a esta aplicação deve ser adicionado, clique o Seletor de junto à etiqueta **a que grupo devem os utilizadores atribuídos ser adicionados?** e selecione um grupo.

10. **Opcional:** Se quiser pedir uma aprovação de negócios antes dos utilizadores têm permissão para aceder, defina o **exigem a aprovação antes de conceder acesso a esta aplicação?** alternar para **Sim**.

11. **Opcional: Para aplicativos usando apenas, palavra-passe início de sessão único no** se pretender permitir que os aprovadores empresariais especificar as palavras-passe que são enviadas para esta aplicação para os utilizadores aprovados, defina o **permitir que os aprovadores definam as palavras-passe do utilizador para isso aplicação?**  alternar para **Sim**.

12. **Opcional:** Para especificar os aprovadores empresariais que têm permissão para aprovar o acesso a esta aplicação, clique o Seletor de junto à etiqueta **quem tem permissão para aprovar o acesso a esta aplicação?** para selecionar até 10 aprovadores de negócios individuais.

    >[!NOTE]
    > Grupos não são suportados.
    >
    >

13. **Opcional:** **Para aplicações que expõem funções**, se pretender atribuir utilizadores aprovados self-service a uma função, clique o Seletor de junto aos **a que função devem ser atribuídos os utilizadores desta aplicação?** para selecionar a função para o qual Estes utilizadores devem ser atribuídos.

14. Clique nas **guardar** botão na parte superior do painel para concluir.

Depois de concluir a configuração de aplicações self-service, os usuários podem navegar para seus [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique nas **+ adicionar** botão para encontrar as aplicações a que tiver ativado o Self-Service acesso. Aprovadores empresariais também veem uma notificação no seu [painel de acesso de aplicação](https://myapps.microsoft.com/). Pode ativar um e-mail a notificar quando um utilizador pediu acesso a um aplicativo que requer a sua aprovação. 

Estas aprovações suportam aprovação única fluxos de trabalho apenas, que significa que, se especificar vários aprovadores, qualquer um único aprovador pode aprovar acesso à aplicação.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem o problema 

Abra um pedido de suporte com as seguintes informações se estiver disponível:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de navegador

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios do fiddler

## <a name="next-steps"></a>Passos Seguintes
[Configurar o Azure Active Directory para gestão de grupos self-service](../users-groups-roles/groups-self-service-management.md)
