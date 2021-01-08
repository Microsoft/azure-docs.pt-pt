---
title: 'Quickstart: Configurar uma app para aceder a uma API web Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, configura um registo de aplicações que representa uma API web na plataforma de identidade da Microsoft para permitir o acesso a recursos (permissões) a aplicações de clientes.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: fdaaf935dc458a6a5b2a87a81bb98a1392f1034a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012126"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Quickstart: Configurar uma aplicação do cliente para aceder a uma API web

Neste quickstart, fornece uma aplicação de cliente registada na plataforma de identidade da Microsoft com acesso baseado em permissões à sua própria API web. Também fornece acesso à aplicação do cliente ao Microsoft Graph.

Ao especificar os âmbitos de uma API web no registo da sua aplicação de clientes, a aplicação do cliente pode obter um token de acesso contendo esses âmbitos a partir da plataforma de identidade da Microsoft. Dentro do seu código, a API web pode então fornecer acesso baseado em permissão aos seus recursos com base nos âmbitos encontrados no token de acesso.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa - [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Registar uma candidatura](quickstart-register-app.md)
* Conclusão do [Quickstart: Configurar uma aplicação para expor uma API web](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Adicione permissões para aceder à sua API web

No primeiro cenário, concede a uma aplicação de clientes acesso à sua própria API web, ambas as quais deveria ter-se registado como parte dos pré-requisitos. Se ainda não tiver uma aplicação de cliente e uma API web registadas, complete os passos nos dois [artigos pré-requisitos.](#prerequisites)

Este diagrama mostra como os dois registos de aplicações se relacionam entre si. Nesta secção, adicione permissões ao registo da aplicação do cliente.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Diagrama de linha mostrando uma API web com âmbitos expostos à direita e uma aplicação de cliente à esquerda com esses âmbitos selecionados como permissões" border="false":::

Uma vez registado tanto a sua app de clientes como a API web e expôs a API criando âmbitos, pode configurar as permissões do cliente para a API seguindo estes passos:

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino que contém o registo da sua aplicação do cliente.
1. Selecione as inscrições da **Azure Ative Directory**  >  **App** e, em seguida, selecione a sua aplicação ao cliente *(não* a sua API web).
1. Selecione **permissões API**  >  **Adicione uma permissão**  >  **As minhas APIs**.
1. Selecione a API web que registou como parte dos pré-requisitos.

    **As permissões delegadas são selecionadas** por predefinição. As permissões delegadas são apropriadas para aplicações de clientes que acedam a uma API web como utilizador inscrito, e cujo acesso deve ser restrito às permissões que seleciona no passo seguinte. Deixe **as permissões delegadas selecionadas** para este exemplo.

    **As permissões de aplicação** são para aplicações de tipo serviço ou daemon que precisam de aceder a uma API web como eles mesmos, sem interação do utilizador para iniciar sação ou consentimento. A menos que tenha definido funções de aplicação para a sua API web, esta opção é desativada.
1. Sob **permissões Select**, expanda o recurso cujos âmbitos definiu para a sua API web e selecione as permissões que a aplicação do cliente deve ter em nome do utilizador inscrito.

    Se usou os nomes de âmbito de exemplo especificados no início rápido anterior, deverá ver **Empregados.Read.All** and **Employees.Write.All**.
    **Selecione Empregados.Leia.Tudo** ou outra permissão que possa ter criado ao completar os pré-requisitos.
1. **Selecione Adicionar permissões** para concluir o processo.

Depois de adicionar permissões à sua API, deverá consultar as permissões selecionadas sob **permissões configuradas.** A imagem que se segue mostra o exemplo *Funcionários.Read.Todas as* permissões delegadas adicionadas ao registo da aplicação do cliente.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Permissões configuradas painel no portal Azure mostrando a permissão recém-adicionada":::

Também pode notar a permissão *user.Read* para a API do Gráfico microsoft. Esta permissão é adicionada automaticamente quando regista uma aplicação no portal Azure.

## <a name="add-permissions-to-access-microsoft-graph"></a>Adicionar permissões para aceder ao Microsoft Graph

Além de aceder à sua própria API web em nome do utilizador inscrito, a sua aplicação também poderá ter de aceder ou modificar os dados (ou outros) do utilizador armazenados no Microsoft Graph. Ou pode ter um serviço ou app Daemon que precisa de aceder ao Microsoft Graph como ele próprio, realizando operações sem qualquer interação do utilizador.

### <a name="delegated-permission-to-microsoft-graph"></a>Permissão delegada no Microsoft Graph

Configure a permissão delegada no Microsoft Graph para permitir que a sua aplicação do cliente realize operações em nome do utilizador com sessão iniciada, por exemplo, lendo o seu e-mail ou modificando o seu perfil. Por predefinição, os utilizadores da sua aplicação cliente são questionados quando fazem login para consentir com as permissões delegadas que configuraram para a mesmo.

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino que contém o registo da sua aplicação do cliente.
1. Selecione as inscrições da **App Azure Ative Directory**  >  App e, em seguida, selecione a sua aplicação ao cliente.
1. Selecione **permissões API**  >  **Adicionar uma permissão**  >  **Microsoft Graph**
1. Selecione **permissões delegadas**. O Microsoft Graph expõe muitas permissões, com as mais utilizadas mostradas no topo da lista.
1. Sob **permissões Selecione,** selecione as seguintes permissões:

    | Permissão       | Descrição                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Ver o endereço de e-mail dos utilizadores                           |
    | `offline_access` | Manter o acesso aos dados a que lhe deu acesso |
    | `openid`         | Iniciar sessão dos utilizadores em                                       |
    | `profile`        | Ver o perfil básico dos utilizadores                           |
1. **Selecione Adicionar permissões** para concluir o processo.

Sempre que configurar permissões, os utilizadores da sua aplicação são solicitados no início do súpr para o seu consentimento para permitir que a sua app aceda ao recurso API em seu nome.

Como administrador, também pode conceder o consentimento em nome de *todos os* utilizadores para que não sejam solicitados a fazê-lo. O consentimento da administração é discutido mais tarde na secção [Mais sobre permissões da API e](#more-on-api-permissions-and-admin-consent) secção de consentimento administrativo deste artigo.

### <a name="application-permission-to-microsoft-graph"></a>Permissão de aplicação para Microsoft Graph

Configure permissões de aplicação para uma aplicação que precisa de autenticar-se como ela própria sem interação ou consentimento do utilizador. As permissões de aplicação são normalmente usadas por serviços de fundo ou aplicações daemon que acedem a uma API de forma "sem cabeça" e por APIs web que acedem a outra API (a jusante).

Nos seguintes passos, concede permissão aos Ficheiros do Microsoft *Graph.Read.All* como exemplo.

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino que contém o registo da sua aplicação do cliente.
1. Selecione as inscrições da **App Azure Ative Directory**  >  App e, em seguida, selecione a sua aplicação ao cliente.
1. Selecione **permissões API**  >  **Adicione uma permissão** as  >    >  **permissões da Aplicação de Gráficos do** Microsoft .
1. Todas as permissões expostas pelo Microsoft Graph são apresentadas sob **permissões Select**.
1. Selecione a permissão ou permissões que pretende conceder ao seu pedido. Como exemplo, pode ter uma aplicação daemon que digitaliza ficheiros na sua organização, alertando sobre um tipo ou nome de ficheiro específico.

    Sob **permissões Selecione,** expanda **ficheiros** e, em seguida, selecione a permissão *Ficheiros.Read.All.*
1. **Selecione Permissões de adicionar**.

Algumas permissões, como os Ficheiros do Microsoft *Graph.Read.All,* requerem o consentimento administrativo. Concede o consentimento administrativo selecionando o botão **de consentimento administrativo Grant,** discutido mais tarde na secção de [botão de consentimento](#admin-consent-button) de Administração.

### <a name="configure-client-credentials"></a>Configure credenciais de cliente

As aplicações que utilizam permissões de aplicação autenticam-se como elas próprias utilizando as suas próprias credenciais, sem exigir qualquer interação do utilizador. Antes da sua aplicação (ou API) poder aceder ao Microsoft Graph, à sua própria API web ou a qualquer outra API utilizando permissões de aplicação, precisa de configurar as credenciais da aplicação do cliente.

Para obter mais informações sobre a configuração das credenciais de uma aplicação, consulte a secção de [credenciais Add](quickstart-register-app.md#add-credentials) do [Quickstart: Registar uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Mais sobre permissões da API e consentimento administrativo

O painel de permissões da **API** de um registo de aplicações contém uma tabela [de permissões configurada,](#configured-permissions) e pode também conter uma tabela [de permissões outras permissões concedidas.](#other-permissions-granted) Ambas as tabelas e o [botão de consentimento](#admin-consent-button) de Administração são descritos nas seguintes secções.

### <a name="configured-permissions"></a>Permissões configuradas

A tabela **de permissões configuradas** no painel de permissões da **API** mostra a lista de permissões que a sua aplicação requer para o funcionamento básico - a lista *de acesso a recursos necessários* (RRA). Os utilizadores, ou os seus administradores, terão de consentir com estas permissões antes de utilizarem a sua aplicação. Outras permissões opcionais podem ser solicitadas mais tarde no tempo de execução (usando o consentimento dinâmico).

Esta é a lista mínima de permissões que as pessoas terão de consentir para a sua aplicação. Pode haver mais, mas estes serão sempre necessários. Para a segurança e para ajudar os utilizadores e administradores a sentirem-se mais confortáveis usando a sua app, nunca peçam nada de que não precisem.

Pode adicionar ou remover as permissões que aparecem nesta tabela utilizando os passos acima descritos ou de [Outras permissões concedidas (descritas](#other-permissions-granted) na secção seguinte). Como administrador, pode conceder o consentimento administrativo para o conjunto completo de permissões da API que aparecem na tabela e revogar o consentimento para permissões individuais.

### <a name="other-permissions-granted"></a>Outras permissões concedidas

Você também pode ver uma tabela intitulada **Outras permissões concedidas para {seu inquilino}** no painel de permissões da **API.** As **Outras permissões concedidas para a mesa {seu inquilino}** mostram permissões concedidas ao inquilino que não foram explicitamente configuradas no objeto de candidatura. Estas permissões foram solicitadas e consentidas dinamicamente. Esta secção só aparece se houver pelo menos uma permissão que se aplique.

Pode adicionar o conjunto completo das permissões de uma API ou permissões individuais que aparecem nesta tabela à tabela **de permissões configuradas.** Como administrador, pode revogar o consentimento administrativo para APIs ou permissões individuais nesta secção.

### <a name="admin-consent-button"></a>Botão de consentimento de administração

O **consentimento administrativo do botão {seu inquilino}** permite que um administrador conceda consentimento administrativo às permissões configuradas para o pedido. Quando seleciona o botão, é mostrado um diálogo solicitando que confirme a ação de consentimento.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Botão de consentimento administrativo de concessão destacado no painel de permissões configurados no portal Azure":::

Após a concessão do consentimento, as permissões que requerem o consentimento administrativo são mostradas como tendo o consentimento concedido:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Configure tabela de permissões no portal Azure mostrando o consentimento administrativo concedido para os Ficheiros.Leia.Toda a permissão":::

O botão **de consentimento administrativo Grant** é *desativado* se não for administrador ou se não tiver sido configurada nenhuma autorização para a aplicação. Se tiver permissões que tenham sido concedidas mas ainda não configuradas, o botão de consentimento administrativo solicita-lhe que manuseie estas permissões. Pode adicioná-las a permissões configuradas ou removê-las.

## <a name="next-steps"></a>Próximos passos

Avance para o próximo quickstart da série para aprender a configurar quais os tipos de conta que podem aceder à sua aplicação. Por exemplo, você pode querer limitar o acesso apenas aos utilizadores da sua organização (inquilino único) ou permitir utilizadores em outros inquilinos AZure AD (multi-inquilino) e aqueles com contas pessoais da Microsoft (MSA).

> [!div class="nextstepaction"]
> [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)