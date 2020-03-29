---
title: incluir ficheiro
description: incluir ficheiro
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325800"
---
1. Inicie sessão no [Portal do Azure].

2. Clique em **Criar um recurso**.

3. Na caixa de pesquisa, digite **web app**.
    
4. Na lista de resultados, selecione **Web App** do Marketplace.

5. Selecione o seu Grupo **de Subscrição** e **Recursos** (selecione um grupo de recursos existente _ou_ crie um novo (usando o mesmo nome que a sua app)).

6. Escolha um **nome** único da sua aplicação web.

7. Escolha a opção de **publicação** padrão como **Código**.

8. Na **pilha Runtime,** é necessário selecionar uma versão em **ASP.NET** ou **nó**. Se estiver a construir um backend .NET, selecione uma versão em ASP.NET. Caso contrário, se estiver a visar uma aplicação baseada no Nó, selecione uma versão do Nó.

9. Selecione o **sistema operativo**certo, quer o Linux quer o Windows. 

10. Selecione a **Região** onde gostaria que esta aplicação fosse implementada. 

11. Selecione o plano de serviço de **aplicações** apropriado e bata em **Review e crie**. 

12. No **Grupo de Recursos**, selecione um grupo de recursos existente _ou_ crie um novo (utilize o mesmo nome da sua aplicação).

13. Clique em **Criar**. Antes de prosseguir, aguarde alguns minutos até o serviço ser implementado com êxito. Observe o ícone de Notificações (sino) no cabeçalho de portal para ver se há atualizações de estado.

14. Assim que a implementação estiver concluída, clique na secção de detalhes de **implementação** e clique no Recurso do Tipo **Microsoft.Web/sites**. Irá navegar para a App Service Web App que acabou de criar. 

15. Clique na lâmina **de configuração** em **Definições** e nas definições de **Aplicação,** clique no botão de definição de **nova aplicação.**

16. Na página de definição de **aplicações Adicionar/Editar,** introduza **nome** como **MobileAppsManagement_EXTENSION_VERSION** e Valor como **mais recente** e atinja OK.

Estão prontos para usar esta aplicação Web de serviço de aplicação recém-criada como uma aplicação Mobile.

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com/