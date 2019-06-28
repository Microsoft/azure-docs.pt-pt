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
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325800"
---
1. Inicie sessão no [Azure portal].

2. Clique em **Criar um recurso**.

3. Na caixa de pesquisa, escreva **aplicação Web**.
    
4. Na lista de resultados, selecione **aplicação Web** do Marketplace.

5. Selecione seu **subscrição** e **grupo de recursos** (selecione um grupo de recursos existente _ou_ criar um novo (utilizando o mesmo nome que a sua aplicação)).

6. Escolha um exclusivo **nome** da sua aplicação web.

7. Escolher a predefinição **Publish** opção como **código**.

8. Na **pilha de Runtime**, tem de selecionar uma versão sob **ASP.NET** ou **nó**. Se estiver a criar um back-end de .NET, selecione uma versão em ASP.NET. Caso contrário, se estiver a filtrar uma aplicação de nó com base, selecione um da versão do nó.

9. Selecione o direito **sistema operativo**, Linux ou Windows. 

10. Selecione o **região** onde pretende que esta aplicação a serem implantados. 

11. Selecione o adequado **plano do App Service** e prima **revisão e criar**. 

12. No **Grupo de Recursos**, selecione um grupo de recursos existente _ou_ crie um novo (utilize o mesmo nome da sua aplicação).

13. Clique em **Criar**. Antes de prosseguir, aguarde alguns minutos até o serviço ser implementado com êxito. Observe o ícone de Notificações (sino) no cabeçalho de portal para ver se há atualizações de estado.

14. Depois de concluída a implementação, clique nas **detalhes de implementação** secção e, em seguida, clique no recurso de tipo **Microsoft.Web/sites**. Ele vai navegar para a aplicação de Web do serviço de aplicação que acabou de criar. 

15. Clique no **Configuration** painel em **definições** e, no **as definições da aplicação**, clique no **nova definição de aplicação** botão.

16. Na **Adicionar/Editar definição de aplicação** página, introduza **nome** como **MobileAppsManagement_EXTENSION_VERSION** e o valor como **mais recente** e clico em OK.

São definidos para utilizar recentemente criada a aplicação de serviço Web da aplicação como uma aplicação móvel.

<!-- URLs. -->
[Azure portal]: https://portal.azure.com/