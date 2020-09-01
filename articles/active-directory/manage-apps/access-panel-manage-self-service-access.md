---
title: Como utilizar o acesso a aplicações self-service em Azure AD
description: Ativar o self-service para que os utilizadores possam encontrar aplicações em Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: c789a5fdd2aed7a904dc71065340b3c921ac9abb
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146611"
---
# <a name="how-to-use-self-service-application-access"></a>Como utilizar o acesso a aplicações self-service

Antes de os seus utilizadores se auto-descobrirem a partir da sua página My Apps, é necessário permitir o acesso da **aplicação self-service** a quaisquer aplicações que deseje permitir que os utilizadores se auto-descubram e solicitem acesso.

Esta funcionalidade é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI, e é altamente recomendado como parte de uma implementação de aplicações modernas com o Azure Ative Directory.

Para aprender a utilizar as Minhas Apps numa perspetiva de utilizador final, consulte [o portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

Através desta funcionalidade, pode:

-   Deixe que os utilizadores auto-descubram as aplicações do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) sem incomodar o grupo de TI.
-   Adicione esses utilizadores a um grupo pré-configurado para que possa ver quem solicitou acesso, remover o acesso e gerir as funções que lhes foram atribuídas.
-   Opcionalmente, permitir que alguém aprove pedidos de acesso a aplicações para que o grupo de TI não tenha de o fazer.
-   Configurar opcionalmente até 10 indivíduos que possam aprovar o acesso a esta aplicação.
-   Opcionalmente, permita que alguém descreva as palavras-passe que os utilizadores podem usar para iniciar sôm na aplicação.
-   Opcionalmente, atribua automaticamente os utilizadores autosserviço atribuídos a uma função de aplicação diretamente.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Permitir o acesso a aplicações self-service para permitir que os utilizadores encontrem as suas próprias aplicações

O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se auto-descubram, permitindo opcionalmente ao grupo empresarial aprovar o acesso a essas aplicações. Pode permitir que o grupo empresarial gere as credenciais atribuídas a esses utilizadores para o signo único da palavra-passe nas aplicações a partir da sua página my Apps.

Para permitir o acesso de aplicações self-service a uma aplicação, siga os passos abaixo:
1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2. Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4. Selecione **Aplicações empresariais** do menu de navegação à esquerda do Azure Ative.
5. Selecione **Todas as Aplicações** para ver uma lista de todas as suas aplicações.
   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**
6. Selecione a aplicação a que pretende ativar o acesso self-service a partir da lista.
7. Uma vez que a aplicação seja carregada, selecione **Self-service** do menu de navegação à esquerda da aplicação.
8. Para permitir o acesso a aplicações self-service para esta aplicação, **Yes.** rode o **Allow users to request access to this application?**
9. Em seguida, para selecionar o grupo a que devem ser adicionados os utilizadores que solicitam acesso a esta aplicação, selecione o seletor ao lado da etiqueta **Para que grupo deve ser adicionado os utilizadores?**
10. **Opcional:** Se desejar uma aprovação do negócio antes de os utilizadores poderem aceder, desacere a **Yes** **aprovação do Requerer antes de conceder acesso a esta aplicação?**
11. **Opcional: Para aplicações que utilizem a palavra-passe apenas,** se pretender permitir que esses aprovadores de negócios especifiquem as palavras-passe enviadas **Yes**para esta aplicação para utilizadores aprovados, detete **os aprovadores de Autorização para definir as palavras-passe do utilizador para esta aplicação?**
12. **Opcional:** Especifique os aprovadores empresariais que estão autorizados a aprovar o acesso a esta aplicação. Selecione **Quem pode aprovar o acesso a esta aplicação?** Em seguida, selecione até 10 aprovadores de negócios individuais.
    * Os grupos não são apoiados.
13. **Opcional:** **Para aplicações que expõem funções**, se pretender atribuir utilizadores aprovados por autosserviço a uma função, selecione o seletor ao lado da **função a que os utilizadores devem ser atribuídos nesta aplicação?**
14. **Selecione** o botão Guardar na parte superior para terminar.

Assim que concluir a configuração da aplicação self-service, os utilizadores podem navegar para o seu Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) e selecionar o botão **+Adicionar** para encontrar as aplicações às quais ativou o acesso ao self-service. Os aprovadores empresariais também vêem uma notificação no seu [Painel de Acesso a Aplicações](https://myapps.microsoft.com/). Pode ativar um e-mail notificando-os quando um utilizador tiver solicitado acesso a uma aplicação que exija a sua aprovação. 

Estas aprovações suportam apenas fluxos de trabalho de homologação únicas, o que significa que se especificar vários aprovadores, qualquer aprovante pode aprovar o acesso à aplicação.

## <a name="things-to-check-if-self-service-isnt-working"></a>Coisas para verificar se o self-service não está funcionando
-   Certifique-se de que o utilizador ou grupo foi habilitado a solicitar o acesso à aplicação de autosserviço.
-   Certifique-se de que o utilizador está a visitar o local correto para o acesso à aplicação de autosserviço. os utilizadores podem navegar para o seu Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) e selecionar o botão **+Adicionar** para encontrar as aplicações às quais permitiu o acesso ao self-service.
-   Se o acesso à aplicação de autosserviço foi recentemente configurado, tente entrar e sair novamente no Painel de Acesso do utilizador após alguns minutos para ver se as alterações de acesso ao autosserviço apareceram.

## <a name="next-steps"></a>Passos seguintes
[Configurar o Azure Active Directory para gestão de grupo personalizada](../users-groups-roles/groups-self-service-management.md)
