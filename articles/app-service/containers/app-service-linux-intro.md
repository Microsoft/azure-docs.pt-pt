---
title: Introdução ao serviço de aplicações no Linux – Azure | Documentos da Microsoft
description: Saiba mais sobre o Serviço de Aplicações do Azure no Linux.
keywords: serviço de aplicações do azure, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/09/2018
ms.author: wesmc
ms.custom: seodec18
ms.openlocfilehash: 2dc12c7250e6747359c8f3813a84537f98bc69d6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993882"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicações do Azure no Linux

A [Aplicação Web](../overview.md) é uma plataforma de computação completamente gerida, está otimizada para o alojamento de sites e de Aplicações Web. Os clientes podem utilizar o Serviço de Aplicações no Linux para alojar aplicações Web nativamente no Linux para pilhas de aplicações suportadas. As secções seguintes listam as pilhas de aplicação atualmente suportadas.

## <a name="languages"></a>Linguagens

O Serviço de Aplicações no Linux suporta um número de imagens Incorporadas para aumentar a produtividade do programador. Se o runtime pedido pela sua aplicação não for suportado nas imagens incorporadas, existem instruções sobre como [criar a sua própria imagem do Docker](tutorial-custom-docker-image.md) para implementar a Aplicação Web para Contentores.

| Idioma | Versões Suportadas |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8,8, 8,9, 8.11, 9.4, 10.1,10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, 14 de WildFly (tudo em execução JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (Pré-visualização) | 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

Consulte [Criar uma aplicação Web Java no Serviço de Aplicações no Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-java) para mais detalhes.

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

Algumas funcionalidades, como a integração da rede virtual, a autenticação do Azure Active Directory/terceiros ou as extensões de site do Kudu, ainda não estão disponíveis. Assim que estas funcionalidades estiverem disponíveis, atualizaremos a nossa documentação e o blogue com as alterações.

O Serviço de Aplicações no Linux é suportado apenas com os planos do Serviço de Aplicações [Básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem um escalão [Gratuito ou Partilhado](https://azure.microsoft.com/pricing/details/app-service/plans/). Não pode criar a Aplicação Web para Contentores num plano do Serviço de Aplicações que já aloja Aplicações Web que não são do Linux. Também existe uma limitação atual no que diz respeito a não misturar aplicações Windows e Linux no mesmo grupo de recursos.

## <a name="troubleshooting"></a>Resolução de problemas

Quando a aplicação não consegue iniciar ou pretende verificar o registo da sua aplicação, verifique os registos do Docker no diretório LogFiles. Pode aceder a este diretório através do seu site SCM ou do FTP.
Para registar o `stdout` e `stderr` a partir do seu contentor, tem de ativar o **Registo do Contentor do Docker** em **Registos de Diagnóstico**.

![Ativar o Registo][2]

![Utilizar o Kudu para ver registos do Docker][1]

Pode aceder ao site do SCM através das **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

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

Veja também os artigos seguintes para obter mais detalhes sobre o Serviço de Aplicações no Linux:

* [FAQ do Serviço de Aplicações para Linux](app-service-linux-faq.md)
* [Suporte SSH para o Serviço de Aplicações no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de teste no Serviço de Aplicações](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementação contínua do Hub do Docker](app-service-linux-ci-cd.md)

Pode publicar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
