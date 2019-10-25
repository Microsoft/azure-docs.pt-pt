---
title: Introdução ao serviço de aplicativo no Linux – Azure | Microsoft Docs
description: Saiba mais sobre o Serviço de Aplicações do Azure no Linux.
keywords: serviço de aplicações do azure, linux, oss
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819678"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicações do Azure no Linux

[Azure app serviço](../overview.md) é uma plataforma de computação totalmente gerenciada que é otimizada para hospedar sites e aplicativos Web. Os clientes podem utilizar o Serviço de Aplicações no Linux para alojar aplicações Web nativamente no Linux para pilhas de aplicações suportadas.

## <a name="languages"></a>Idiomas

O Serviço de Aplicações no Linux suporta um número de imagens Incorporadas para aumentar a produtividade do programador. Os idiomas incluem: node. js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core e Ruby. Execute [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) para exibir os idiomas mais recentes e as versões com suporte. Se o runtime pedido pela sua aplicação não for suportado nas imagens incorporadas, existem instruções sobre como [criar a sua própria imagem do Docker](tutorial-custom-docker-image.md) para implementar a Aplicação Web para Contentores.

## <a name="deployments"></a>Implementações

* FTP
* Git Local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambientes de teste
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Consola, Publicação e Depuração

* Ambientes
* Implementações
* Consola básica
* SSH

## <a name="scaling"></a>Dimensionamento

* Os clientes podem aumentar ou reduzir aplicações Web ao alterar o escalão do respetivo [plano do Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Localizações

Verifique o [Dashboard de Estado do Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitações

O portal do Azure mostra apenas as funcionalidades que funcionam atualmente para a Aplicação Web para Contentores. À medida que ativamos mais funcionalidades, estas tornam-se visíveis no portal.

O serviço de aplicativo no Linux só tem suporte com planos de serviço de aplicativo [gratuito, básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem uma camada [compartilhada](https://azure.microsoft.com/pricing/details/app-service/plans/) . Você não pode criar um aplicativo Web do Linux em um plano do serviço de aplicativo que já hospeda aplicativos Web não Linux.  

Com base em uma limitação atual, para o mesmo grupo de recursos, você não pode misturar aplicativos Windows e Linux na mesma região.

## <a name="troubleshooting"></a>Resolução de problemas

Quando a aplicação não consegue iniciar ou pretende verificar o registo da sua aplicação, verifique os registos do Docker no diretório LogFiles. Pode aceder a este diretório através do seu site SCM ou do FTP. Para registrar o `stdout` e `stderr` do contêiner, você precisa habilitar o **log de contêiner do Docker** em **logs do serviço de aplicativo**. A configuração entra em vigor imediatamente. O serviço de aplicativo detecta a alteração e reinicia o contêiner automaticamente.

Pode aceder ao site do SCM através das **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

![Utilizar o Kudu para ver registos do Docker][1]

## <a name="next-steps"></a>Passos seguintes

Os artigos seguintes ajudam-no a começar a utilizar o Serviço de Aplicações no Linux com aplicações Web escritas em várias linguagens:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplicação com vários contentores](quickstart-multi-container.md)

Para obter mais informações sobre o serviço de aplicativo no Linux, consulte:

* [FAQ do Serviço de Aplicações para Linux](app-service-linux-faq.md)
* [Suporte SSH para o Serviço de Aplicações no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de teste no Serviço de Aplicações](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua do Hub do Docker](app-service-linux-ci-cd.md)

Pode publicar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
