---
title: Executar código em recipientes linux padrão
description: O Azure App Service pode executar o seu código em recipientes Linux pré-construídos. Descubra como pode executar as suas aplicações web Linux no Azure.
keywords: serviço de aplicações do azure, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 65352b8f8f85f5e7a2e25ae99d5ca3368ad78711
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126526"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicações do Azure no Linux

[O Azure App Service](../overview.md) é uma plataforma computacional totalmente gerida que está otimizada para hospedar websites e aplicações web. Os clientes podem utilizar o Serviço de Aplicações no Linux para alojar aplicações Web nativamente no Linux para pilhas de aplicações suportadas.

## <a name="languages"></a>Linguagens

O Serviço de Aplicações no Linux suporta um número de imagens Incorporadas para aumentar a produtividade do programador. As línguas incluem: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core e Ruby. Execute [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) para ver as últimas línguas e versões suportadas. Se o runtime pedido pela sua aplicação não for suportado nas imagens incorporadas, existem instruções sobre como [criar a sua própria imagem do Docker](tutorial-custom-docker-image.md) para implementar a Aplicação Web para Contentores.

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

O Serviço de Aplicações no Linux só é suportado com planos de serviço de aplicação [Gratuito, Básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem um nível [Partilhado.](https://azure.microsoft.com/pricing/details/app-service/plans/) Não é possível criar uma Aplicação Web Linux num plano de Serviço de Aplicações que já acolhe aplicações web não linux.  

Com base numa limitação atual, para o mesmo grupo de recursos não é possível misturar aplicações Windows e Linux na mesma região.

## <a name="troubleshooting"></a>Resolução de problemas

> [!NOTE]
> Há uma nova capacidade integrada de exploração madeireira com [a Monitorização Azure (pré-visualização)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) . 
>
>

Quando a aplicação não consegue iniciar ou pretende verificar o registo da sua aplicação, verifique os registos do Docker no diretório LogFiles. Pode aceder a este diretório através do seu site SCM ou do FTP. Para registar o `stdout` e `stderr` a partir do seu recipiente, é necessário ativar o **registo de registos de serviços** de aplicações em termos de serviços de **aplicações**. A regulação entra em vigor imediatamente. O Serviço de Aplicações deteta a alteração e reinicia automaticamente o recipiente.

Pode aceder ao site do SCM através das **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

![Utilizar o Kudu para ver registos do Docker][1]

## <a name="next-steps"></a>Passos seguintes

Os artigos seguintes ajudam-no a começar a utilizar o Serviço de Aplicações no Linux com aplicações Web escritas em várias linguagens:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplicação com vários contentores](quickstart-multi-container.md)

Para mais informações sobre o Serviço de Aplicações no Linux, consulte:

* [FAQ do Serviço de Aplicações para Linux](app-service-linux-faq.md)
* [Suporte SSH para o Serviço de Aplicações no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de teste no Serviço de Aplicações](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua do Hub do Docker](app-service-linux-ci-cd.md)

Pode publicar perguntas e problemas no [nosso fórum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
