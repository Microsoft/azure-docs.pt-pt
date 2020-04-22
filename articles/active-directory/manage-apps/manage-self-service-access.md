---
title: Como configurar a atribuição de pedidos de self-service [ Microsoft Docs
description: Ativar o acesso à aplicação self-service para permitir que os utilizadores encontrem as suas próprias aplicações
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
ms.date: 04/20/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e09fd63ee6121ac9bf7f3c2be00f0ac22f752f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731697"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Como configurar a atribuição de aplicações de self-service

Antes de os seus utilizadores poderem autodescobrir aplicações a partir do painel de acesso das Minhas Aplicações, é necessário permitir o acesso à **aplicação self-service** a quaisquer aplicações que deseje permitir aos utilizadores auto-descobrirem e solicitarem acesso. Esta funcionalidade está disponível para aplicações adicionadas a partir da [Galeria AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), Procuração de [Aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ou foram adicionadas através do consentimento do [utilizador ou administrador.](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) 

Esta funcionalidade é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI, e é altamente recomendado como parte de uma implementação de aplicações modernas com o Azure Ative Directory.

Através desta funcionalidade, pode:

-   Deixe os utilizadores auto-descobrirem aplicações do painel de [acesso My Apps](https://myapps.microsoft.com/) sem incomodar o grupo de TI.

-   Adicione esses utilizadores a um grupo pré-configurado para que possa ver quem solicitou acesso, remover o acesso e gerir as funções que lhes foram atribuídas.

-   Opcionalmente, permita que um aprovador de negócios aprove pedidos de acesso a aplicações para que o grupo de TI não tenha de o fazer.

-   Configure opcionalmente até 10 indivíduos que possam aprovar o acesso a esta aplicação.

-   Opcionalmente, permita que um utilizador de negócios detetete as palavras-passe que os utilizadores podem utilizar para iniciar sessão na aplicação, a partir do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/)do utilizador.

-   Opcionalmente atribua automaticamente os utilizadores autosserviço atribuídos a uma função de aplicação diretamente.

> [!NOTE]
> É necessária uma licença Azure Ative Directory Premium (P1 ou P2) para que os utilizadores solicitem aderir a uma aplicação de self-service e que os proprietários aprovem ou negem pedidos. Sem uma licença Azure Ative Directory Premium, os utilizadores não podem adicionar aplicações self-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Ativar o acesso à aplicação self-service para permitir que os utilizadores encontrem as suas próprias aplicações

O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se autodescubram aplicações e, opcionalmente, permitem que o grupo empresarial aprove o acesso a essas aplicações. Para um sinal único de senha nas aplicações, também pode permitir que o grupo empresarial gere as credenciais atribuídas aos utilizadores a partir dos seus próprios painéis de acesso My Apps.

Para permitir o acesso à aplicação self-service a uma aplicação, siga os passos abaixo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador Global.

2. Selecione **Azure Active Directory**. No menu de navegação à esquerda, selecione **aplicações Enterprise**.

3. Selecione a aplicação da lista. Se não vir a aplicação, comece a digitar o seu nome na caixa de pesquisa. Ou utilize os controlos do filtro para selecionar o tipo de aplicação, o estado ou a visibilidade e, em seguida, selecione **Aplicar**.

4. No menu de navegação à esquerda, selecione **Self-service**.

5. Para permitir o acesso à aplicação self-service para esta aplicação, **Yes.** rode os **utilizadores de Permitir em solicitar acesso a esta aplicação?**

6. Ao lado de qual grupo deve ser **Select group**adicionado os **utilizadores designados?** Escolha um grupo e, em seguida, clique em **Selecionar**. Quando o pedido de um utilizador for aprovado, serão adicionados a este grupo. Ao ver a adesão deste grupo, poderá ver quem teve acesso à aplicação através do acesso ao self-service.
  
    > [!NOTE]
    > Esta configuração não suporta grupos sincronizados a partir do local.

7. **Opcional:** Para exigir a aprovação do negócio antes de os utilizadores poderem aceder, **Yes**detetete a aprovação do Requiren antes de **conceder acesso a esta aplicação?**

8. **Opcional: Para aplicações que utilizem apenas um sinal único de senha,** para permitir que os aprovadores de negócios especifiquem as palavras-passe enviadas para esta aplicação para utilizadores aprovados, definir os aprovadores de permitir definir as **palavras-passe do utilizador para esta aplicação?** **Yes**

9. **Opcional:** Para especificar os aprovadores de negócios que estão autorizados a aprovar o acesso a **Select approvers**esta aplicação, ao lado de **Quem está autorizado a aprovar o acesso a esta aplicação?** Em seguida, clique em **Selecionar**.

    >[!NOTE]
    >Os grupos não são apoiados. Pode selecionar até 10 aprovadores individuais de negócios. Se especificar vários aprovadores, qualquer único aprovador pode aprovar um pedido de acesso.

10. **Opcional:** **Para aplicações que exponham funções,** atribuir aos utilizadores aprovados por self-service uma função, ao **Select Role**lado do To qual a função que os utilizadores devem atribuir **nesta aplicação?** Em seguida, clique em **Selecionar**.

11. Clique no botão **Guardar** na parte superior do painel para terminar.

Assim que completar a configuração da aplicação self-service, os utilizadores podem navegar para o painel de acesso das [Minhas Aplicações](https://myapps.microsoft.com/) e clicar no botão **adicionar aplicações self-service** para encontrar as aplicações que estão ativas com acesso self-service. Os aprovadores de negócios também vêem uma notificação no seu painel de [acesso my apps.](https://myapps.microsoft.com/) Pode ativar um e-mail notificando-os quando um utilizador solicitou acesso a uma aplicação que requer a sua aprovação.

## <a name="next-steps"></a>Passos seguintes
[Configurar o Azure Active Directory para gestão de grupo personalizada](../users-groups-roles/groups-self-service-management.md)
