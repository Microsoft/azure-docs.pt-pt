---
title: Implementar a sua primeira aplicação em Cloud Foundry no Microsoft Azure | Documentos da Microsoft
description: Implementar uma aplicação em Cloud Foundry no Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 9a0b12ec9d825fc665bd5beb89e911b80ed3889f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013868"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implementar a sua primeira aplicação em Cloud Foundry no Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) é uma plataforma de aplicações populares de código aberto disponíveis no Microsoft Azure. Neste artigo, vamos mostrar como implementar e gerir uma aplicação na Cloud Foundry no ambiente do Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente de Cloud Foundry

Existem várias opções para criação de um ambiente de Cloud Foundry no Azure:

- Utilize o [oferta de Cloud Foundry da Pivotal] [ pcf-azuremarketplace] no Azure Marketplace para criar um ambiente padrão, que inclui o PCF Ops Manager e o Azure Service Broker. Pode encontrar [concluir as instruções] [ pcf-azuremarketplace-pivotaldocs] para implementar o marketplace oferecem na documentação do Pivotal.
- Criar um ambiente personalizado por [implementar manualmente a Pivotal Cloud Foundry][pcf-custom].
- [Implementa os pacotes do código-fonte aberto Cloud Foundry diretamente] [ oss-cf-bosh] configurando um [BOSH](https://bosh.io) diretor, uma VM que coordena a implementação do ambiente Cloud Foundry.

> [!IMPORTANT] 
> Se estiver implantando o PCF no Azure Marketplace, anote o SYSTEMDOMAINURL e as credenciais de administrador necessárias para aceder ao Gestor de aplicações Pivotal, que são descritos no guia de implementação do marketplace. Eles são necessários para concluir este tutorial. Para implementações do marketplace, o SYSTEMDOMAINURL está no formato https://system. *endereço IP*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Ligar ao controlador de Cloud

O controlador de nuvem é o ponto de entrada principal num ambiente de Cloud Foundry para implantar e gerenciar aplicativos. O núcleo Cloud controlador API (CCAPI) é uma API REST, mas é acessível por meio de diversas ferramentas. Neste caso, iremos interagir com ele através do [Cloud Foundry CLI][cf-cli]. Pode instalar a CLI no Linux, MacOS ou Windows, mas se preferir não instalá-lo em todos os, está disponível pré-instalado no [Azure Cloud Shell][cloudshell-docs].

Para iniciar sessão, preceda `api` para o SYSTEMDOMAINURL que obteve da implementação do marketplace. Uma vez que a implementação do padrão utiliza um certificado autoassinado, também deve incluir o `skip-ssl-validation` mudar.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

São-lhe pedido para iniciar sessão no controlador de Cloud. Utilize as credenciais de conta de administrador que obteve a partir os passos de implementação do marketplace.

Cloud Foundry fornece *organizações* e *espaços* como espaços de nomes para isolar as equipes e os ambientes dentro de uma implantação partilhada. A implementação do PCF marketplace inclui a predefinição *sistema* org e um conjunto de espaços criado para conter os componentes de bases, como o serviço de dimensionamento automático e o Mediador de serviço do Azure. Por agora, escolha o *sistema* espaço.


## <a name="create-an-org-and-space"></a>Criar uma organização e o espaço

Se digitar `cf apps`, verá um conjunto de aplicativos do sistema que foram implementadas no espaço de sistema dentro da org system. 

Deve manter o *sistema* org reservado para aplicativos do sistema, pelo que pode criar uma organização e o espaço para abrigar o nosso exemplo de aplicativo.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Utilize o comando de destino para mudar para a nova organização e o espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando implanta um aplicativo, ele é criado automaticamente na nova organização e espaço. Para confirmar que atualmente não existem aplicações novo org/espaço, introduza `cf apps` novamente.

> [!NOTE] 
> Para obter mais informações sobre as organizações e espaços e como eles podem ser usados para controlo de acesso baseado em funções (RBAC), consulte a [documentação de Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implementar uma aplicação

Vamos usar um exemplo de aplicação de Cloud Foundry, chamado Hello Spring Cloud, que é escrito em Java e com base na [Spring Framework](https://spring.io) e [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clonar o repositório de Hello Spring Cloud

O aplicativo de exemplo Hello Spring Cloud está disponível no GitHub. Clone-o no seu ambiente e altere para o novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Criar a aplicação

Criar a aplicação com [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implementar aplicação cf Push

Pode implementar a maioria dos aplicativos para utilizar o Cloud Foundry o `push` comando:

```bash
cf push
```

Quando *push* um aplicativo, o Cloud Foundry Deteta o tipo de aplicativo (neste caso, uma aplicação Java) e identifica as respetivas dependências (no caso, o Spring framework). Em seguida, empacota tudo necessários para executar o código para uma imagem de contentor autónomo, conhecida como um *droplet*. Por fim, o Cloud Foundry agenda o aplicativo em uma das máquinas disponíveis no seu ambiente e cria um URL onde pode aceder ao mesmo, que está disponível na saída do comando.

![Saída do comando de push cf][cf-push-output]

Para ver a aplicação de spring-hello-cloud, abra o URL fornecido no seu browser:

![Interface do Usuário padrão para a Primavera de Hello Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Para saber mais sobre o que acontece durante `cf push`, consulte [como os aplicativos são pré-configurados] [ cf-push-docs] na documentação do Cloud Foundry.

## <a name="view-application-logs"></a>Ver registos de aplicação

Pode utilizar o Cloud Foundry CLI para ver os registos para uma aplicação pelo respetivo nome:

```bash
cf logs hello-spring-cloud
```

Por predefinição, os registos de comando utiliza *cauda*, que mostra os novos registos como elas são gravadas. Para ver os registos novos são apresentados, atualize a aplicação de spring-hello-cloud no browser.

Para ver os registos que já foram escritos, adicione o `recent` mudar:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Dimensionar a aplicação

Por predefinição, `cf push` apenas cria uma instância única do seu aplicativo. Para garantir a elevada disponibilidade e ativar o Escalamento horizontal para um débito mais elevado, em geral executar mais de uma instância de seus aplicativos. Pode facilmente aumentar horizontalmente as aplicações já implementadas utilizando o `scale` comando:

```bash
cf scale -i 2 hello-spring-cloud
```

A executar o `cf app` comando no aplicativo mostra que o Cloud Foundry é criar outra instância do aplicativo. Assim que tiver iniciado a aplicação, o Cloud Foundry inicia automaticamente a ele o tráfego de balanceamento de carga.


## <a name="next-steps"></a>Passos Seguintes

- [Leia a documentação de Cloud Foundry][cloudfoundry-docs]
- [Configurar o plug-in de serviços de DevOps do Azure para o Cloud Foundry][vsts-plugin]
- [Configurar o Nozzle do Microsoft Log Analytics para o Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
