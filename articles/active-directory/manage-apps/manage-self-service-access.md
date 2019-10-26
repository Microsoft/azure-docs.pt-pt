---
title: Como configurar a atribuição de aplicativo de autoatendimento | Microsoft Docs
description: Habilitar o acesso de aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos
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
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbeb25f1190754b4264cfbab9d8a03a6b65c4dff
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895970"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicativo de autoatendimento

Antes que os usuários possam detectar aplicativos automaticamente no painel de acesso, você precisa habilitar o **acesso de aplicativo de autoatendimento** a todos os aplicativos que você deseja permitir que os usuários autodescubram e solicitem acesso ao. Essa funcionalidade está disponível para aplicativos que foram adicionados da [Galeria do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), o [Azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou foram adicionados por meio do [consentimento do usuário ou do administrador](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Esse recurso é uma ótima maneira de economizar tempo e dinheiro como um grupo de ti e é altamente recomendável como parte de uma implantação de aplicativos modernos com o Azure Active Directory.

Usando esse recurso, você pode:

-   Permita que os usuários autodescubram aplicativos do [painel de acesso do aplicativo](https://myapps.microsoft.com/) sem incomodar o grupo de ti.

-   Adicione esses usuários a um grupo pré-configurado para que você possa ver quem solicitou acesso, remover o acesso e gerenciar as funções atribuídas a eles.

-   Opcionalmente, permita que um Aprovador de negócios aprove solicitações de acesso do aplicativo para que o grupo de ti não precise fazê-lo.

-   Opcionalmente, configure até 10 pessoas que podem aprovar o acesso a esse aplicativo.

-   Opcionalmente, permita que um Aprovador de negócios defina as senhas que esses usuários podem usar para entrar no aplicativo, diretamente no painel de acesso do [aplicativo](https://myapps.microsoft.com/)do aprovador de negócios.

-   Opcionalmente, atribua automaticamente usuários de autoatendimento atribuídos a uma função de aplicativo diretamente.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilitar o acesso de aplicativo de autoatendimento para permitir que os usuários encontrem seus próprios aplicativos

O acesso ao aplicativo de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos, permitindo, opcionalmente, que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para aplicativos de logon único com senha diretamente em seus painéis de acesso.

Para habilitar o acesso de aplicativo de autoatendimento a um aplicativo, siga as etapas abaixo:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4.  clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5.  clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

    * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6.  Selecione o aplicativo ao qual você deseja habilitar o acesso de autoatendimento na lista.

7.  Depois que o aplicativo for carregado, clique em **autoatendimento** no menu de navegação esquerdo do aplicativo.

8.  Para habilitar o acesso de aplicativo de autoatendimento para este aplicativo, ative a opção **permitir que os usuários solicitem acesso a este aplicativo?** alterne para **Sim.**

9.  Em seguida, para selecionar o grupo ao qual os usuários que solicitarem acesso a esse aplicativo devem ser adicionados, clique no seletor ao lado do rótulo **ao qual os usuários atribuídos devem ser adicionados?** e selecione um grupo.
  
    > [!NOTE]
    > Os grupos sincronizados do local não têm suporte para serem usados para o grupo ao qual os usuários que solicitam acesso a esse aplicativo devem ser adicionados.
  
10. **Opcional:** Se você quiser exigir uma aprovação de negócios antes que os usuários tenham permissão de acesso, defina a opção **exigir aprovação antes de conceder acesso a este aplicativo?** alterne para **Sim**.

11. **Opcional: para aplicativos que usam somente logon único de senha,** se você quiser permitir que os aprovadores de negócios especifiquem as senhas que são enviadas para este aplicativo para usuários aprovados, defina permitir que os **aprovadores definam as senhas do usuário para isso aplicativo?** Alterne para **Sim**.

12. **Opcional:** Para especificar os aprovadores de negócios que têm permissão para aprovar o acesso a esse aplicativo, clique no seletor ao lado do rótulo **que tem permissão para aprovar o acesso a este aplicativo?** para selecionar até 10 aprovadores de negócios individuais.

     > [!NOTE]
     > Não há suporte para grupos.
     >
     >

13. **Opcional:** **para aplicativos que expõem funções**, se você quiser atribuir usuários aprovados por autoatendimento a uma função, clique no seletor ao lado do **a qual função os usuários devem ser atribuídos neste aplicativo?** para selecionar a função à qual esses usuários deve ser atribuído.

14. Clique no botão **salvar** na parte superior da folha para concluir.

Depois de concluir a configuração de aplicativo de autoatendimento, os usuários podem navegar até o [painel de acesso do aplicativo](https://myapps.microsoft.com/) e clicar no botão **+ Adicionar** para localizar os aplicativos aos quais você habilitou o acesso de autoatendimento. Os aprovadores de negócios também veem uma notificação no [painel de acesso do aplicativo](https://myapps.microsoft.com/). Você pode habilitar um email notificando-os quando um usuário tiver solicitado o acesso a um aplicativo que exige sua aprovação. 

Essas aprovações dão suporte apenas a fluxos de trabalho de aprovação única, o que significa que, se você especificar vários Aprovadores, qualquer aprovador poderá aprovar o acesso ao aplicativo.

## <a name="next-steps"></a>Passos seguintes
[Configurando Azure Active Directory para gerenciamento de grupo de autoatendimento](../users-groups-roles/groups-self-service-management.md)
