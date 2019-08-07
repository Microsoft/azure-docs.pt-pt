---
title: Implante seu primeiro aplicativo para Cloud Foundry em Microsoft Azure | Microsoft Docs
description: Implantar um aplicativo para Cloud Foundry no Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
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
ms.openlocfilehash: fe510865e687b6a44538627e4ef9025b41416841
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "67668351"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implante seu primeiro aplicativo para Cloud Foundry em Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) é uma plataforma de aplicativo de software livre popular disponível em Microsoft Azure. Neste artigo, mostraremos como implantar e gerenciar um aplicativo no Cloud Foundry em um ambiente do Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente de Cloud Foundry

Há várias opções para criar um ambiente de Cloud Foundry no Azure:

- Use a [oferta de Cloud Foundry dinâmica][pcf-azuremarketplace] no Azure Marketplace para criar um ambiente padrão que inclui o PCF Ops Manager e o Service Broker do Azure. Você pode encontrar [instruções completas][pcf-azuremarketplace-pivotaldocs] para implantar a oferta do Marketplace na documentação do Pivotal.
- Crie um ambiente personalizado implantando a [Cloud Foundry dinâmica manualmente][pcf-custom].
- [Implante os pacotes de Cloud Foundry de código-fonte aberto diretamente][oss-cf-bosh] Configurando um diretor de [Bosh](https://bosh.io) , uma VM que coordena a implantação do ambiente de Cloud Foundry.

> [!IMPORTANT] 
> Se você estiver implantando o PCF do Azure Marketplace, anote o SYSTEMDOMAINURL e as credenciais de administrador necessárias para acessar o Pivotal apps Manager, ambos descritos no guia de implantação do Marketplace. Eles são necessários para concluir este tutorial. Para implantações do Marketplace, o SYSTEMDOMAINURL está no https://system formato. *IP-address*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Conectar-se ao controlador de nuvem

O controlador de nuvem é o ponto de entrada primário para um ambiente de Cloud Foundry para implantar e gerenciar aplicativos. A API do controlador de nuvem principal (CCAPI) é uma API REST, mas pode ser acessada por meio de várias ferramentas. Nesse caso, interagimos com ele por meio da [CLI do Cloud Foundry][cf-cli]. Você pode instalar a CLI no Linux, no MacOS ou no Windows, mas se preferir não instalá-lo, ele estará disponível pré-instalado no [Azure cloud Shell][cloudshell-docs].

Para fazer logon, preceda `api` o SYSTEMDOMAINURL que você obteve da implantação do Marketplace. Como a implantação padrão usa um certificado autoassinado, você também deve incluir a `skip-ssl-validation` opção.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Você será solicitado a fazer logon no controlador de nuvem. Use as credenciais de conta de administrador que você adquiriu das etapas de implantação do Marketplace.

Cloud Foundry fornece *organizações* e *espaços* como namespaces para isolar as equipes e os ambientes em uma implantação compartilhada. A implantação do Marketplace do PCF inclui a organização padrão do *sistema* e um conjunto de espaços criados para conter os componentes base, como o serviço de dimensionamento automático e o Azure Service Broker. Por enquanto, escolha o espaço do *sistema* .


## <a name="create-an-org-and-space"></a>Criar uma organização e um espaço

Se você digitar `cf apps`, verá um conjunto de aplicativos do sistema que foram implantados no espaço do sistema na organização do sistema. 

Você deve manter a organização do *sistema* reservada para aplicativos de sistema; portanto, crie uma organização e espaço para alojar nosso aplicativo de exemplo.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Use o comando de destino para alternar para a nova organização e o espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando você implanta um aplicativo, ele é criado automaticamente na nova organização e no mesmo espaço. Para confirmar que atualmente não há aplicativos na nova organização/espaço, digite `cf apps` novamente.

> [!NOTE] 
> Para obter mais informações sobre organizações e espaços e como eles podem ser usados para RBAC (controle de acesso baseado em função), consulte a [documentação do Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implementar uma aplicação

Vamos usar um aplicativo de Cloud Foundry de exemplo chamado Hello Spring Cloud, que é escrito em Java e baseado no [Spring Framework](https://spring.io) e [Spring boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clonar o repositório do Hello Spring Cloud

O aplicativo de exemplo Hello Spring Cloud está disponível no GitHub. Clone-o para o seu ambiente e altere para o novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Criar a aplicação

Compile o aplicativo usando o [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implantar o aplicativo com o CF push

Você pode implantar a maioria dos aplicativos para Cloud Foundry `push` usando o comando:

```bash
cf push
```

Quando você *envia um aplicativo por push* , Cloud Foundry detecta o tipo de aplicativo (nesse caso, um aplicativo Java) e identifica suas dependências (nesse caso, a estrutura Spring). Em seguida, ele empacota tudo o que é necessário para executar seu código em uma imagem de contêiner independente, conhecida como *droplet*. Por fim, Cloud Foundry agenda o aplicativo em um dos computadores disponíveis em seu ambiente e cria uma URL na qual você pode acessá-lo, que está disponível na saída do comando.

![Saída do comando de push do CF][cf-push-output]

Para ver o aplicativo Hello-Spring-Cloud, abra a URL fornecida no seu navegador:

![Interface do usuário padrão para Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Para saber mais sobre o que acontece `cf push`durante, consulte [como os aplicativos são preparados][cf-push-docs] na documentação do Cloud Foundry.

## <a name="view-application-logs"></a>Exibir logs de aplicativo

Você pode usar a CLI Cloud Foundry para exibir os logs de um aplicativo por seu nome:

```bash
cf logs hello-spring-cloud
```

Por padrão, o comando logs usa *cauda*, que mostra os novos logs conforme eles são gravados. Para ver novos logs aparecerem, atualize o aplicativo Hello-Spring-Cloud no navegador.

Para exibir os logs que já foram gravados, adicione `recent` a opção:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Dimensionar o aplicativo

Por padrão, `cf push` o cria apenas uma única instância do seu aplicativo. Para garantir a alta disponibilidade e habilitar o scale out para maior taxa de transferência, geralmente você desejará executar mais de uma instância de seus aplicativos. Você pode expandir facilmente os aplicativos já implantados `scale` usando o comando:

```bash
cf scale -i 2 hello-spring-cloud
```

A execução `cf app` do comando no aplicativo mostra que Cloud Foundry está criando outra instância do aplicativo. Depois que o aplicativo for iniciado, Cloud Foundry iniciará automaticamente o tráfego de balanceamento de carga para ele.


## <a name="next-steps"></a>Passos Seguintes

- [Leia a documentação do Cloud Foundry][cloudfoundry-docs]
- [Configurar o plug-in Azure DevOps Services para Cloud Foundry][vsts-plugin]
- [Configurar o bocal do Microsoft Log Analytics para Cloud Foundry][loganalytics-nozzle]

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
