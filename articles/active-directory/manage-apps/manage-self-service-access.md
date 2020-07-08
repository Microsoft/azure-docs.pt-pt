---
title: Como configurar a atribuição de aplicações de self-service / Microsoft Docs
description: Permitir o acesso a aplicações self-service para permitir que os utilizadores encontrem as suas próprias aplicações
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
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a59343d4eafaa0e11f56636daa7471e748abf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763147"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicações de autosserviço

Antes que os seus utilizadores possam auto-descobrir aplicações a partir do painel de acesso my apps, é necessário permitir o acesso da **aplicação Self-service** a quaisquer aplicações que deseje permitir que os utilizadores se auto-descubram e solicitem acesso. Esta funcionalidade está disponível para aplicações que foram adicionadas a partir da [Galeria AD AZure](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou foram adicionadas através do [consentimento do utilizador ou administração.](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) 

Esta funcionalidade é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI, e é altamente recomendado como parte de uma implementação de aplicações modernas com o Azure Ative Directory.

Através desta funcionalidade, pode:

-   Deixe que os utilizadores auto-descubram as aplicações do painel de [acesso my Apps](https://myapps.microsoft.com/) sem incomodar o grupo de TI.

-   Adicione esses utilizadores a um grupo pré-configurado para que possa ver quem solicitou acesso, remover o acesso e gerir as funções que lhes foram atribuídas.

-   Opcionalmente, permitir que um aprovador de negócio aprove pedidos de acesso a candidaturas para que o grupo de TI não tenha de o fazer.

-   Configurar opcionalmente até 10 indivíduos que possam aprovar o acesso a esta aplicação.

-   Opcionalmente, permitir que um aprovador de negócios estabeleça as palavras-passe que os utilizadores podem utilizar para iniciar seduções à aplicação, a partir do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/)do aprovador da empresa.

-   Opcionalmente, atribua automaticamente os utilizadores autosserviço atribuídos a uma função de aplicação diretamente.

> [!NOTE]
> É necessária uma licença Azure Ative Directory Premium (P1 ou P2) para que os utilizadores solicitem aderir a uma aplicação de self-service e para que os proprietários aprovem ou neguem pedidos. Sem uma licença Azure Ative Directory Premium, os utilizadores não podem adicionar aplicações de self-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Permitir o acesso a aplicações self-service para permitir que os utilizadores encontrem as suas próprias aplicações

O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se auto-descubram, e opcionalmente permitem ao grupo empresarial aprovar o acesso a essas aplicações. Para o sinal único de palavra-passe nas aplicações, também pode permitir ao grupo empresarial gerir as credenciais atribuídas a esses utilizadores a partir dos seus próprios painéis de acesso My Apps.

Para permitir o acesso de aplicações self-service a uma aplicação, siga os passos abaixo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador Global.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.

3. Selecione a inscrição na lista. Se não vir a aplicação, comece a escrever o seu nome na caixa de pesquisa. Ou utilize os comandos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, **selecione Apply**.

4. No menu de navegação à esquerda, selecione **Self-service**.

5. Para permitir o acesso a aplicações self-service para esta aplicação, **Yes.** rode o **Allow users to request access to this application?**

6. Ao lado de qual grupo deve ser **Select group**adicionado **aos utilizadores designados?** Escolha um grupo e, em seguida, clique em **Selecionar**. Quando o pedido de um utilizador for aprovado, eles serão adicionados a este grupo. Ao visualizar a adesão deste grupo, poderá ver quem teve acesso à aplicação através do acesso ao self-service.
  
    > [!NOTE]
    > Esta definição não suporta grupos sincronizados a partir do local.

7. **Opcional:** Para exigir a aprovação do negócio antes de os utilizadores poderem aceder, detete **a aprovação do Requerimento antes de conceder acesso a esta aplicação?** **Yes**

8. **Opcional: Para aplicações que utilizem a palavra-passe de assinatura única apenas,** para permitir que os aprovadores de negócios especifiquem as palavras-passe enviadas para esta aplicação para utilizadores aprovados, detete **os aprovadores de Autorização para definir as palavras-passe do utilizador para esta aplicação?** **Yes**

9. **Opcional:** Para especificar os aprovadores empresariais que estão autorizados a aprovar o acesso a **Select approvers**esta aplicação, ao lado **de Quem está autorizado a aprovar o acesso a esta aplicação?** Em seguida, clique em **Selecionar**.

    >[!NOTE]
    >Os grupos não são apoiados. Pode selecionar até 10 aprovadores de negócios individuais. Se especificar vários aprovadores, qualquer um dos aprovadores pode aprovar um pedido de acesso.

10. **Opcional:** **Para aplicações que expõem funções,** atribuir utilizadores aprovados por autosserviço a uma função, **Select Role**ao lado da **função a que os utilizadores devem ser atribuídos nesta aplicação?** Em seguida, clique em **Selecionar**.

11. Clique no botão **Guardar** na parte superior do painel para terminar.

Assim que concluir a configuração da aplicação self-service, os utilizadores podem navegar para o painel de [acesso my Apps](https://myapps.microsoft.com/) e clicar no botão adicionar **aplicações de self-service** para encontrar as aplicações que estão habilitados com acesso ao self-service. Os aprovadores de negócios também vêem uma notificação no seu [painel de acesso às Minhas Apps.](https://myapps.microsoft.com/) Pode ativar um e-mail notificando-os quando um utilizador tiver solicitado acesso a uma aplicação que exija a sua aprovação.

## <a name="next-steps"></a>Próximos passos
[Configurar o Azure Active Directory para gestão de grupo personalizada](../users-groups-roles/groups-self-service-management.md)
