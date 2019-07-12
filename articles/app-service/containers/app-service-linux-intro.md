---
title: Introdução ao serviço de aplicações no Linux – Azure | Documentos da Microsoft
description: Saiba mais sobre o Serviço de Aplicações do Azure no Linux.
keywords: serviço de aplicações do azure, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6bca1b067f5ec667e8b5da92a182a5618582b2f3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617436"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicações do Azure no Linux

[Serviço de aplicações do Azure](../overview.md) é uma plataforma de computação totalmente gerida que está otimizada para o alojamento de sites e aplicações web. Os clientes podem utilizar o Serviço de Aplicações no Linux para alojar aplicações Web nativamente no Linux para pilhas de aplicações suportadas. O [idiomas](#languages) secção lista as pilhas de aplicação que são atualmente suportadas.

## <a name="languages"></a>Languages

O Serviço de Aplicações no Linux suporta um número de imagens Incorporadas para aumentar a produtividade do programador. Se o runtime pedido pela sua aplicação não for suportado nas imagens incorporadas, existem instruções sobre como [criar a sua própria imagem do Docker](tutorial-custom-docker-image.md) para implementar a Aplicação Web para Contentores.

| Idioma | Versões Suportadas |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, 14 de WildFly (tudo em execução JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

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

Serviço de aplicações no Linux só é suportado com [gratuito, básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) planos de serviço de aplicações e não tem um [partilhado](https://azure.microsoft.com/pricing/details/app-service/plans/) escalão. Não é possível criar uma aplicação Web do Linux num plano do serviço de aplicações já não - Linux Web Apps de alojamento.  

Com base numa limitação atual, para o mesmo grupo de recursos não é possível misturar aplicações Windows e Linux, na mesma região.

## <a name="troubleshooting"></a>Resolução de problemas

Quando a aplicação não consegue iniciar ou pretende verificar o registo da sua aplicação, verifique os registos do Docker no diretório LogFiles. Pode aceder a este diretório através do seu site SCM ou do FTP. Registo a `stdout` e `stderr` do seu contentor, tem de ativar **registo de contentor do Docker** sob **registos do serviço de aplicações**. A definição entra em vigor imediatamente. Serviço de aplicações Deteta a alteração e reinicia automaticamente o contentor.

Pode aceder ao site do SCM através das **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

![Utilizar o Kudu para ver registos do Docker][1]

## <a name="next-steps"></a>Passos Seguintes

Os artigos seguintes ajudam-no a começar a utilizar o Serviço de Aplicações no Linux com aplicações Web escritas em várias linguagens:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplicação com vários contentores](quickstart-multi-container.md)

Para obter mais informações sobre o serviço de aplicações no Linux, consulte:

* [FAQ do Serviço de Aplicações para Linux](app-service-linux-faq.md)
* [Suporte SSH para o Serviço de Aplicações no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de teste no Serviço de Aplicações](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua do Hub do Docker](app-service-linux-ci-cd.md)

Pode publicar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
