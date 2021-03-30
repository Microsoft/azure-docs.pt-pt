---
title: Tutorial - Empurre atualização para o registo geo-replicado
description: Empurre uma imagem de Docker atualizada para o seu registo geo-replicado do contentor Azure e, em seguida, veja as alterações automaticamente implementadas para aplicações web em execução em várias regiões. Parte três de uma série com três partes.
ms.topic: tutorial
ms.date: 04/30/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 9222ac31e067cba6a0ffa71143c90f906ba6ff7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74454702"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Tutorial: Empurre uma imagem de recipiente atualizada para um registo de contentores geo-replicados para implementações regionais de aplicações web

Esta é a parte três de um tutorial de três partes. No [tutorial anterior](container-registry-tutorial-deploy-app.md), a georreplicação foi configurada para duas implementações regionais de Aplicações Web. Neste tutorial, comece por modificar a aplicação e, em seguida, crie uma nova imagem de contentor e envie-a para o seu registo georreplicado. Finalmente, verá a alteração, implementada automaticamente pelos webhooks do Azure Container Registry, em ambas as instâncias de Aplicações Web.

Neste tutorial, a última parte da série:

> [!div class="checklist"]
> * Modificar o HTML da aplicação Web
> * Criar e etiquetar a imagem do Docker
> * Enviar a alteração para o Azure Container Registry
> * Ver a aplicação atualizada em duas regiões diferentes

Se ainda não configurou as duas implementações regionais da *Aplicação Web para Contentores*, regresse ao tutorial anterior na série, [Implementar a aplicação Web a partir do Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modificar a aplicação Web

Neste passo, efetue uma alteração à aplicação Web que será altamente visível quando enviar a imagem de contentor atualizada para o Azure Container Registry.

Localize o ficheiro `AcrHelloworld/Views/Home/Index.cshtml` na origem de aplicação que [clonou do GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) num tutorial anterior e abra-o no seu editor de texto favorito. Adicione a seguinte linha abaixo da linha `<h1>` existente:

```html
<h1>MODIFIED</h1>
```

O seu `Index.cshtml` modificado deverá ter um aspeto semelhante a:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Recriar a imagem

Agora que atualizou a aplicação Web, recrie a respetiva imagem de contentor. Conforme anteriormente, utilize o nome de imagem completamente qualificado, incluindo o nome de domínio completamente qualificado (FQDN) do servidor de início de sessão, para a etiqueta:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

Em seguida, envie a imagem de contentor *acr-helloworld* atualizada para o seu registo georreplicado. Aqui, vai executar um único comando `docker push` para implementar a imagem atualizada nas réplicas de registo nas regiões *E.U.A. Oeste* e *E.U.A. Leste*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

O resultado `docker push` deve ser semelhante ao seguinte:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Ver os registos de webhooks

Enquanto a imagem está a ser replicada, pode ver os webhooks do Azure Container Registry a serem acionados.

Para ver os webhooks regionais que foram criados quando implementou o container nas *Aplicações Web para Contentores* num tutorial anterior, navegue para o seu registo de contentor no portal do Azure e, em seguida, selecione **Webhooks** em **SERVIÇOS**.

![Webhooks de registo de contentor no portal do Azure][tutorial-portal-01]

Selecione cada Webhook para ver o histórico de chamadas e respostas do mesmo. Deverá ver uma linha para a ação de **envio** nos registos de ambos os Webhooks. Neste caso, o registo do Webhook localizado na região *E.U.A. Oeste* mostra a ação de **envio** acionada pelo `docker push` no passo anterior:

![Registo de Webhooks de registo de contentor no portal do Azure (E.U.A. Oeste)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Ver a aplicação Web atualizada

Os Webhooks notificam as Aplicações Web de que uma nova imagem foi enviada para o registo, o que implementa automaticamente o contentor atualizado para duas aplicações Web regionais.

Certifique-se de que a aplicação foi atualizada em ambas as implementações ao navegar para ambas as implementações de Aplicações Web regionais no seu browser. Lembre-se de que pode encontrar o URL da aplicação Web implementada no canto superior direito de cada separador de descrição geral do Serviço de Aplicações do Azure.

![Descrição geral do Serviço de Aplicações do Azure no portal do Azure][tutorial-portal-03]

Para ver a atualização aplicada, selecione a ligação na descrição geral do Serviço de Aplicações do Azure. Eis uma vista de exemplo da aplicação executada na região *E.U.A. Oeste*:

![Vista de browser da aplicação Web modificada executada na região E.U.A. Oeste][deployed-app-westus-modified]

Verifique se a imagem de contentor atualizada também foi implementada na implementação *E.U.A. Leste* ao vê-la no seu browser.

![Vista de browser da aplicação Web modificada executada na região E.U.A. Leste][deployed-app-eastus-modified]

Com um único `docker push`, atualizou automaticamente a aplicação Web em execução em ambas as implementações de Aplicações Web regionais. Além disso, o Azure Container Registry apresentou as imagens de contentor dos repositórios mais próximos de cada implementação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou e enviou uma nova versão do contentor de aplicações Web para o seu registo georreplicado. Os Webhooks no Azure Container Registry notificaram as Aplicações Web para Contentores da atualização, o que acionou uma solicitação local da réplica do registo mais próximo.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build: compilação e correção automáticas de imagens

Além da georreplicação, o ACR Build é outra funcionalidade do Azure Container Registry que pode ajudar a otimizar o pipeline de implementação do contentor. Comece com a descrição geral do ACR Build para ter uma ideia das respetivas capacidades:

[Automate OS and framework patching with ACR Build](container-registry-tasks-overview.md) (Automatizar a aplicação de patches do SO e de aplicações com o ACR Build)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png