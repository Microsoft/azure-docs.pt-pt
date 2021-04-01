---
title: Tutorial - Adicionar a CDN do Azure a uma aplicação Web do Serviço de Aplicações do Azure | Microsoft Docs
description: Neste tutorial, a Rede de Entrega de Conteúdos (CDN) do Azure é adicionada a uma aplicação Web do Serviço de Aplicações do Azure para colocar em cache e entregar os seus ficheiros estáticos a partir de servidores próximos aos seus clientes em todo o mundo.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97562247"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Tutorial: Adicionar a CDN do Azure a uma aplicação Web do Serviço de Aplicações do Azure

Este tutorial mostra como adicionar a [Rede de Entrega de Conteúdo (CDN) do Azure](cdn-overview.md) a uma [aplicação Web no Serviço de Aplicações do Azure](../app-service/overview.md). As Aplicações Web são um serviço para o alojamento de aplicações Web, APIs REST e back-ends móveis. 

Segue a home page do site HTML estático de exemplo com que irá trabalhar:

![Home page da aplicação de exemplo](media/cdn-add-to-web-app/sample-app-home-page.png)

O que irá aprender:

> [!div class="checklist"]
> * Criar um ponto final da CDN.
> * Atualizar ativos em cache.
> * Utilize cadeias de consulta para controlar versões em cache.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Criar a aplicação Web

Para criar a aplicação Web com que irá trabalhar, siga o [início rápido HTML estático](../app-service/quickstart-html.md), através do passo **Navegar para a aplicação**.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra um browser e navegue para o [portal do Azure](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Otimização de aceleração de site dinâmico
Se quiser otimizar o seu ponto final de CDN para a aceleração de site dinâmica (DSA), deve utilizar o [portal da CDN](cdn-create-new-endpoint.md) para criar o seu perfil e o ponto final. Com [otimização DSA](cdn-dynamic-site-acceleration.md), o desempenho das páginas web com o conteúdos dinâmicos foi consideravelmente melhorado. Para obter instruções sobre como otimizar um ponto final de CDN para DSA a partir do portal de CDN, veja [Configuração do ponto final de CDN para acelerar a entrega de ficheiros dinâmicos](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Caso contrário, se não quiser otimizar o seu novo ponto final, pode utilizar o portal da aplicação Web para criá-lo ao seguir os passos da próxima secção. Note que para os perfis de **CDN do Azure da Verizon**, não pode alterar a otimização de um ponto final de CDN após ter sido criado.

## <a name="create-a-cdn-profile-and-endpoint"></a>Criar um perfil da CDN e ponto final

No painel de navegação esquerdo, selecione **Serviço de Aplicações** e, em seguida, selecione a aplicação que criou no [início rápido HTML estático](../app-service/quickstart-html.md).

![Selecione a aplicação de Serviço de Aplicações no portal](media/cdn-add-to-web-app/portal-select-app-services.png)

Na página **Serviço de Aplicações**, na secção **Definições**, selecione **Redes > Configurar a CDN do Azure para a sua aplicação**.

![Selecione a CDN no portal](media/cdn-add-to-web-app/portal-select-cdn.png)

Na página **Azure Content Delivery Network**(Rede de Entrega de Conteúdos do Azure), indique as definições **Novo ponto final** conforme especificado na tabela.

![Criar perfil e ponto final no portal](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Definição | Valor sugerido | Descrição |
| ------- | --------------- | ----------- |
| **Perfil da CDN** | myCDNProfile | Um perfil da CDN é uma coleção de pontos finais da CDN com o mesmo escalão de preços. |
| **Escalão de preço** | Standard Akamai | O [escalão de preço](cdn-features.md) especifica o fornecedor e as funcionalidades disponíveis. Este tutorial utiliza o *Standard da Akamai*. |
| **Nome do ponto final da CDN** | Qualquer nome que é exclusivo no domínio azureedge.net | Aceda aos seus recursos em cache no nome final do *&lt; ponto &gt; de* domínio .azureedge.net.

Selecione **Criar** para criar um perfil de CDN.

O Azure cria o perfil e o ponto final. O novo ponto final é apresentado na lista **Pontos finais** e, quando está aprovisionado, o estado é **A executar**.

![Novo ponto final na lista](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testar o ponto final da CDN

 Dado que a propagação do registo demora algum tempo, o ponto final não está imediatamente disponível para utilização: 
   - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
   - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
   - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 

A aplicação de exemplo tem um ficheiro *index.html* e pastas *css*, *img* e *js* que contêm outros elementos estáticos. Os caminhos de conteúdo para todos estes ficheiros são os mesmos no ponto final da CDN. Por exemplo, ambos os seguintes URLs acedem ao ficheiro *bootstrap.css* na pasta *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Navegue até um browser para o seguinte URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Home page da aplicação de exemplo servida a partir de CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Será apresentada a mesma página que executou anteriormente numa aplicação Web do Azure. A CDN do Azure obteve os ativos da aplicação Web de origem e está a servi-los a partir do ponto final da CDN

Para se certificar de que esta página está colocada em cache no CDN, atualize-a. Por vezes são necessários dois pedidos para o mesmo recurso, para o CDN colocar em cache os conteúdos solicitados.

Para obter mais informações sobre a criação de perfis de CDN do Azure e os pontos finais, consulte [Introdução ao Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Remover a CDN

A CDN atualiza periodicamente os respetivos recursos da aplicação Web de origem consoante a configuração de tempo de duração (TTL). O TTL predefinido é de sete dias.

Por vezes, poderá precisar de atualizar a CDN antes de expirar o TTL; por exemplo, ao implementar conteúdo atualizado na aplicação Web. Para acionar uma atualização, remova manualmente os recursos da CDN. 

Nesta secção do tutorial, implemente uma alteração na aplicação Web e remova o CDN para acionar o CDN para atualizar a respetiva cache.

### <a name="deploy-a-change-to-the-web-app"></a>Implementar uma alteração na aplicação Web

Abra o ficheiro *index.html* e adicione *-V2* ao cabeçalho H1, conforme mostrado no seguinte exemplo: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Consolide as alterações e implemente-as na aplicação Web.

```bash
git commit -am "version 2"
git push azure main
```

Depois de concluída a implementação, navegue para o URL da aplicação Web para ver a alteração.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 no título na aplicação Web](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Se navegar para o URL de ponto final da CDN da home page, não verá a alteração porque a versão em cache na CDN ainda não expirou. 

```
http://<endpointname>.azureedge.net/index.html
```

![Nenhum V2 no título na CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Remova a CDN no portal

Para acionar a CDN para atualizar a versão em cache, remova a CDN.

Na navegação à esquerda do portal, selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos que criou a sua aplicação Web (myResourceGroup).

![Selecionar o grupo de recursos](media/cdn-add-to-web-app/portal-select-group.png)

Na lista de recursos, selecione o ponto final de CDN.

![Selecione o ponto final](media/cdn-add-to-web-app/portal-select-endpoint.png)

Na parte superior da página **Ponto final**, selecione **Remover**.

![Selecione Remover](media/cdn-add-to-web-app/portal-select-purge.png)

Introduza os caminhos do conteúdo que quer remover. Pode transmitir um caminho de ficheiro completo para remover um ficheiro individual ou um segmento de caminho para remover e atualizar todo o conteúdo numa pasta. Como alterou o *index.html*, certifique-se de que está num dos caminhos.

Na parte inferior da página, selecione **Remover**.

![Remover página](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Certifique-se de que a CDN é atualizada

Aguarde até que o pedido de remoção conclua o processamento, que demora normalmente alguns minutos. Para ver o estado atual, selecione o ícone da campainha na parte superior da página. 

![Remover notificação](media/cdn-add-to-web-app/portal-purge-notification.png)

Ao navegar para o URL de ponto final da CDN para *index.html*, verá o *V2* que adicionou ao título na home page, que indica que a cache da CDN foi atualizada.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 no título na CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Para obter mais informações, consulte [Remover um ponto final do Azure CDN](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Utilizar cadeias de consulta para o conteúdo da versão

A CDN do Azure oferece as seguintes opções de comportamento de colocação em cache:

* Ignorar cadeias de consulta
* Ignorar a colocação em cache de cadeias de consulta
* Colocar em cache todos os URLs exclusivos 

A primeira opção é a predefinida, o que significa que existe apenas uma versão em cache de um ativo, independentemente da cadeia de consulta no URL. 

Nesta secção do tutorial, pode alterar o comportamento de colocação em cache para colocar em cache todos os URL exclusivos.

### <a name="change-the-cache-behavior"></a>Alterar o comportamento de cache

Na página **Ponto Final CDN** do portal do Azure, selecione **Cache**.

Selecione **Colocar em cache todos os URL exclusivos** relativo ao **comportamento de colocação em cache da Cadeia de consulta** na lista pendente.

Selecione **Guardar**.

![Selecione o comportamento de colocação em cache de cadeia de consulta](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Certifique-se de que os URL exclusivos são colocados em cache em separado

Num browser, navegue para a home page no ponto final da CDN e inclua uma cadeia de consulta: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

A CDN do Azure devolve o conteúdo da aplicação Web atual, que inclui *V2* no cabeçalho. 

Para se certificar de que esta página está colocada em cache no CDN, atualize-a. 

Abra *index.html*, altere *V2* para *V3* e, em seguida, implemente a alteração. 

```bash
git commit -am "version 3"
git push azure main
```

Num browser, aceda ao URL de ponto final da CDN com uma nova cadeia de consulta, como `q=2`. A CDN do Azure obtém o ficheiro *index.html* atual e apresenta *V3*. No entanto, se navegar para o ponto final da CDN com a cadeia de consulta `q=1`, verá *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 no título na CDN, cadeia de consulta 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 no título na CDN, cadeia de consulta 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Este resultado mostra que cada cadeia de consulta é tratada de forma diferente:

* q=1 foi utilizado antes, pelo que os conteúdos armazenados em cache são devolvidos (V2).
* q=2 é novo, pelo que os conteúdos mais recentes da aplicação Web são obtidos e devolvidos (V3).

Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](../cdn/cdn-query-string.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar um ponto final da CDN.
> * Atualizar ativos em cache.
> * Utilize cadeias de consulta para controlar versões em cache.

Saiba como otimizar o desempenho da CDN nos seguintes artigos:

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao ponto final da CDN do Azure](cdn-map-content-to-custom-domain.md)