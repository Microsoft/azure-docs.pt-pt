---
title: Implemente a sua primeira aplicação para Cloud Foundry no Microsoft Azure
description: Implementar uma aplicação para Cloud Foundry em Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 037ac972dca49484f7b8c0ad8eed6942c901b997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562933"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implemente a sua primeira aplicação para Cloud Foundry no Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) é uma popular plataforma de aplicações open-source disponível no Microsoft Azure. Neste artigo, mostramos como implementar e gerir uma aplicação na Cloud Foundry num ambiente Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente de cloud foundry

Existem várias opções para criar um ambiente cloud foundry em Azure:

- Utilize a [oferta de Pivotal Cloud Foundry][pcf-azuremarketplace] no Azure Marketplace para criar um ambiente padrão que inclua pcf Ops Manager e o Azure Service Broker. Pode encontrar [instruções completas][pcf-azuremarketplace-pivotaldocs] para a implementação da oferta de mercado na documentação Pivotal.
- Crie um ambiente personalizado [implantando manualmente a Criação de Nuvens Pivotal][pcf-custom].
- [Implemente os pacotes cloud foundry de código aberto diretamente][oss-cf-bosh] através da criação de um diretor [BOSH,](https://bosh.io) um VM que coordena a implantação do ambiente Cloud Foundry.

> [!IMPORTANT] 
> Se estiver a implementar o PCF a partir do Azure Marketplace, tome nota do SYSTEMDOMAINURL e das credenciais de administração necessárias para aceder ao Pivotal Apps Manager, ambos descritos no guia de implementação do mercado. São necessários para completar este tutorial. Para implementações no mercado, o SYSTEMDOMAINURL está no formulário `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Ligue-se ao Controlador de Nuvem

O Cloud Controller é o principal ponto de entrada para um ambiente cloud foundry para implantação e gestão de aplicações. O núcleo do Controlador de Nuvem API (CCAPI) é uma API REST, mas é acessível através de várias ferramentas. Neste caso, interagimos com ele através do [Cloud Foundry CLI.][cf-cli] Pode instalar o CLI no Linux, macOS ou Windows, mas se preferir não o instalar, está disponível pré-instalado na [Azure Cloud Shell.][cloudshell-docs]

Para iniciar sessão, `api` prepare-se para o SYSTEMDOMAINURL que obteve a partir da implementação do mercado. Uma vez que a implementação predefinida utiliza um certificado auto-assinado, também deverá incluir o `skip-ssl-validation` comutador.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

É-lhe pedido que faça login no Controlador de Nuvem. Utilize as credenciais de conta de administração que adquiriu a partir das etapas de implementação do mercado.

Cloud Foundry fornece *orgs* e *espaços* como espaços de nome para isolar as equipas e ambientes dentro de uma implementação partilhada. A implementação do mercado pcf inclui o *org do sistema* padrão e um conjunto de espaços criados para conter os componentes base, como o serviço de autoscalagem e o corretor de serviçoS Azure. Por enquanto, escolha o espaço do *sistema.*


## <a name="create-an-org-and-space"></a>Criar um org e espaço

Se `cf apps` escrever, vê um conjunto de aplicações do sistema que foram implementadas no espaço do sistema dentro do sistema org. 

Você deve manter o *sistema* org reservado para aplicações do sistema, por isso crie um org e espaço para alojar a nossa aplicação de amostra.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Utilize o comando alvo para mudar para o novo org e espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando implementa uma aplicação, é criada automaticamente no novo org e espaço. Para confirmar que atualmente não existem aplicações no novo org/espaço, `cf apps` escreva novamente.

> [!NOTE] 
> Para obter mais informações sobre orgs e espaços e como podem ser usados para o controlo de acesso baseado em funções cloud Foundry (Cloud Foundry RBAC), consulte a [documentação cloud foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implementar uma aplicação

Vamos usar uma aplicação cloud foundry chamada Hello Spring Cloud, que é escrita em Java e baseada na [Estrutura de primavera](https://spring.io) e Bota de [primavera.](https://projects.spring.io/spring-boot/)

### <a name="clone-the-hello-spring-cloud-repository"></a>Clone o repositório Hello Spring Cloud

A aplicação da amostra Hello Spring Cloud está disponível no GitHub. Clone-o para o seu ambiente e transforme-se no novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Criar a aplicação

Construa a aplicação utilizando [Apache Maven.](https://maven.apache.org)

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implementar a aplicação com cf push

Pode implementar a maioria das aplicações para Cloud Foundry utilizando o `push` comando:

```bash
cf push
```

Ao *empurrar* uma aplicação, a Cloud Foundry deteta o tipo de aplicação (neste caso, uma aplicação Java) e identifica as suas dependências (neste caso, o quadro da primavera). Em seguida, embala tudo o que é necessário para encaixar o seu código numa imagem de recipiente autónomo, conhecida como *uma gota*. Finalmente, o Cloud Foundry agenda a aplicação numa das máquinas disponíveis no seu ambiente e cria um URL onde pode alcançá-la, que está disponível na saída do comando.

![Saída do comando de push cf][cf-push-output]

Para ver a aplicação hello-spring-cloud, abra o URL fornecido no seu navegador:

![UI padrão para Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Para saber mais sobre o que acontece durante `cf push` , consulte como as [aplicações são encenadas][cf-push-docs] na documentação cloud foundry.

## <a name="view-application-logs"></a>Ver registos de aplicações

Pode utilizar o CLI cloud foundry para visualizar registos para uma aplicação com o seu nome:

```bash
cf logs hello-spring-cloud
```

Por predefinição, o comando de registos utiliza *cauda*, que mostra novos registos à medida que são escritos. Para ver novos registos, atualize a aplicação hello-spring-cloud no navegador.

Para ver registos que já foram escritos, adicione o `recent` interruptor:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Dimensione a aplicação

Por padrão, `cf push` apenas cria uma única instância da sua aplicação. Para garantir uma elevada disponibilidade e permitir uma maior produção, geralmente pretende executar mais do que uma instância das suas aplicações. Pode facilmente escalar as aplicações já implantadas utilizando o `scale` comando:

```bash
cf scale -i 2 hello-spring-cloud
```

Executar o `cf app` comando na aplicação mostra que cloud Foundry está a criar outro exemplo da aplicação. Uma vez iniciada a aplicação, a Cloud Foundry começa automaticamente a carregar o tráfego de equilíbrio.


## <a name="next-steps"></a>Passos seguintes

- [Leia a documentação da Cloud Foundry][cloudfoundry-docs]
- [Configurar o plugin de serviços Azure DevOps para Cloud Foundry][vsts-plugin]
- [Configure o bocal de análise do Microsoft Log para cloud foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
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