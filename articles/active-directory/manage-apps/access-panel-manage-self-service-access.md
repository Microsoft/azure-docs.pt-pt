---
title: Como utilizar o acesso à aplicação self-service [ Microsoft Docs
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "65784543"
---
# <a name="how-to-use-self-service-application-access"></a>Como utilizar o acesso à aplicação self-service

Antes de os seus utilizadores poderem autodescobrir as aplicações a partir do seu painel de acesso, é necessário permitir o acesso à **aplicação self-service** a quaisquer aplicações a que deseje permitir aos utilizadores auto-descobrire solicitar acesso.

Esta funcionalidade é uma ótima maneira de economizar tempo e dinheiro como um grupo de TI, e é altamente recomendado como parte de uma implementação de aplicações modernas com o Azure Ative Directory.

Através desta funcionalidade, pode:

-   Deixe os utilizadores auto-descobrirem as aplicações do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) sem incomodar o grupo de TI.

-   Adicione esses utilizadores a um grupo pré-configurado para que possa ver quem solicitou acesso, remover o acesso e gerir as funções que lhes foram atribuídas.

-   Opcionalmente, permita que um aprovador de negócios aprove pedidos de acesso a aplicações para que o grupo de TI não tenha de o fazer.

-   Configure opcionalmente até 10 indivíduos que possam aprovar o acesso a esta aplicação.

-   Opcionalmente, permita que um utilizador de negócios detetete as palavras-passe que os utilizadores podem utilizar para iniciar sessão na aplicação, a partir do Painel de Acesso à [Aplicação](https://myapps.microsoft.com/)do utilizador.

-   Opcionalmente atribua automaticamente os utilizadores autosserviço atribuídos a uma função de aplicação diretamente.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Ativar o acesso à aplicação self-service para permitir que os utilizadores encontrem as suas próprias aplicações

O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se autodescubram aplicações, permitindo opcionalmente ao grupo empresarial aprovar o acesso a essas aplicações. Pode permitir que o grupo empresarial gere as credenciais atribuídas aos utilizadores para aplicações de assinatura única de password a partir dos seus painéis de acesso.

Para permitir o acesso à aplicação self-service a uma aplicação, siga os passos abaixo:

1. Abra o [**Portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à mão esquerda do Diretório Ativo Azure.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende ativar o acesso ao Self-service a partir da lista.

7. Assim que a aplicação estiver carregada, clique em **Self-service** a partir do menu de navegação à mão esquerda da aplicação.

8. Para permitir o acesso à aplicação self-service para esta aplicação, **Yes.** rode os **utilizadores de Permitir em solicitar acesso a esta aplicação?**

9. Em seguida, para selecionar o grupo ao qual devem ser adicionados os utilizadores que solicitam o acesso a esta aplicação, clique no seletor ao lado da etiqueta **Para que grupo deve ser adicionado os utilizadores?**

10. **Opcional:** Se desejar uma aprovação de negócio antes de os utilizadores poderem aceder, detetete a **aprovação do Requires antes de conceder acesso a esta aplicação?** **Yes**

11. **Opcional: Para aplicações** que utilizem apenas uma única placa de senha, se pretender permitir que esses aprovadores de negócios especifiquem as palavras-passe enviadas **Yes**para esta aplicação para utilizadores aprovados, detete te permite que os aprovadores de permitam definir as **palavras-passe do utilizador para esta aplicação?**

12. **Opcional:** Para especificar os aprovadores de negócios autorizados a aprovar o acesso a esta aplicação, clique no seletor ao lado do rótulo **Quem está autorizado a aprovar o acesso a esta aplicação?**

    * Os grupos não são apoiados.

13. **Opcional:** **Para aplicações que exponham funções,** caso deseje atribuir aos utilizadores aprovados por self-service uma função, clique no seletor ao lado do **To que deve ser atribuído aos utilizadores nesta aplicação?**

14. Clique no botão **Guardar** na parte superior da lâmina para terminar.

Assim que completar a configuração da aplicação self-service, os utilizadores podem navegar para o seu Painel de Acesso a [Aplicações](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar as aplicações às quais ativou o acesso ao self-service. Os aprovadores de negócios também vêem uma notificação no seu Painel de Acesso a [Aplicações.](https://myapps.microsoft.com/) Pode ativar um e-mail notificando-os quando um utilizador solicitou acesso a uma aplicação que requer a sua aprovação. 

Estas aprovações suportam apenas fluxos de trabalho de aprovação individuais, o que significa que se especificar vários aprovadores, qualquer único aprovador pode aprovar o acesso à aplicação.

## <a name="next-steps"></a>Passos seguintes
[Configurar o Azure Active Directory para gestão de grupo personalizada](../users-groups-roles/groups-self-service-management.md)
