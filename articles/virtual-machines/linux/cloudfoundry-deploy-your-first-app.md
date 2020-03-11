---
title: Implemente a sua primeira aplicação para Cloud Foundry no Microsoft Azure
description: Implementar uma aplicação para Cloud Foundry em Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 45ae8979a2617d4f380e417e3f0910182ebe145e
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970063"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Implemente a sua primeira aplicação para Cloud Foundry no Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) é uma popular plataforma de aplicações de código aberto disponível no Microsoft Azure. Neste artigo, mostramos como implementar e gerir uma aplicação na Cloud Foundry num ambiente Azure.

## <a name="create-a-cloud-foundry-environment"></a>Criar um ambiente de fundição de nuvens

Existem várias opções para criar um ambiente de cloud foundry em Azure:

- Utilize a [oferta de fundição][pcf-azuremarketplace] de nuvem fundamental no Mercado Azure para criar um ambiente padrão que inclua pcf ops manager e o Corretor de Serviços Azure. Pode encontrar [instruções completas][pcf-azuremarketplace-pivotaldocs] para a implementação da oferta de mercado na documentação pivotal.
- Crie um ambiente personalizado [implantando a Fundação de Nuvem Pivotal manualmente][pcf-custom].
- [Implemente os pacotes cloud foundry de código aberto diretamente][oss-cf-bosh] através da criação de um diretor [BOSH,](https://bosh.io) um VM que coordena a implantação do ambiente cloud foundry.

> [!IMPORTANT] 
> Se estiver a implementar PCF a partir do Mercado Azure, tome nota do SYSTEMDOMAINURL e das credenciais de administração necessárias para aceder ao Pivotal Apps Manager, ambos descritos no guia de implementação do mercado. São necessários para completar este tutorial. Para implementações no mercado, o SYSTEMDOMAINURL está na forma https://system. *ip-address*.cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Ligue-se ao Controlador de Nuvem

O Controlador de Nuvem é o principal ponto de entrada para um ambiente de fundição em nuvem para implementação e gestão de aplicações. O Núcleo do Controlador de Nuvem API (CCAPI) é um Rest API, mas é acessível através de várias ferramentas. Neste caso, interagimos com ele através do [Cloud Foundry CLI][cf-cli]. Pode instalar o CLI no Linux, macOS ou Windows, mas se preferir não o instalar de todo, está disponível pré-instalado na [Cloud Shell Azure][cloudshell-docs].

Para iniciar sessão, prepare-se `api` ao SYSTEMDOMAINURL que obteve a partir da implementação do mercado. Uma vez que a implementação predefinida utiliza um certificado auto-assinado, deve incluir também o interruptor `skip-ssl-validation`.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

É solicitado que faça login no Controlador de Nuvem. Utilize as credenciais de conta de administração que adquiriu a partir das etapas de implantação do mercado.

Cloud Foundry fornece *orgs* e *espaços* como espaços de nome para isolar as equipas e ambientes dentro de uma implantação partilhada. A implementação do mercado do PCF inclui o *sistema* padrão org e um conjunto de espaços criados para conter os componentes base, como o serviço de autoscalcificação e o corretor de serviços Azure. Por enquanto, escolha o espaço do *sistema.*


## <a name="create-an-org-and-space"></a>Criar um org e espaço

Se escrever `cf apps`, vê um conjunto de aplicações do sistema que foram implementadas no espaço do sistema dentro do sistema org. 

Você deve manter o *sistema* org reservado para aplicações do sistema, por isso crie um org e espaço para alojar a nossa aplicação de amostra.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Utilize o comando-alvo para mudar para o novo org e espaço:

```bash
cf target -o testorg -s dev
```

Agora, quando se implementa uma aplicação, é automaticamente criada no novo org e espaço. Para confirmar que não existem atualmente aplicações no novo org/space, escreva `cf apps` novamente.

> [!NOTE] 
> Para obter mais informações sobre orgs e espaços e como podem ser usados para controlo de acesso baseado em papéis (RBAC), consulte a [documentação Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Implementar uma aplicação

Vamos usar uma aplicação de cloud foundry chamada Hello Spring Cloud, que é escrita em Java e baseada no Quadro de [primavera](https://spring.io) e [NaBota de primavera](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clone do repositório Hello Spring Cloud

A aplicação de amostra hello Spring Cloud está disponível no GitHub. Clone-o para o seu ambiente e transforme-o no novo diretório:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Criar a aplicação

Construa a aplicação usando [Apache Maven.](https://maven.apache.org)

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Implementar a aplicação com impulso cf

Pode implementar a maioria das aplicações para a Cloud Foundry utilizando o comando `push`:

```bash
cf push
```

Ao *impulsionar* uma aplicação, a Cloud Foundry deteta o tipo de aplicação (neste caso, uma aplicação Java) e identifica as suas dependências (neste caso, a estrutura da primavera). Em seguida, embala tudo o que é necessário para executar o seu código numa imagem de recipiente autónoma, conhecida como *gota.* Por fim, a Cloud Foundry programa a aplicação numa das máquinas disponíveis no seu ambiente e cria um URL onde pode alcançá-la, que está disponível na saída do comando.

![Saída do comando de impulso cf][cf-push-output]

Para ver a aplicação hello-spring-cloud, abra o URL fornecido no seu navegador:

![UI padrão para Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Para saber mais sobre o que acontece durante `cf push`, veja como as [aplicações são encenadas][cf-push-docs] na documentação da Cloud Foundry.

## <a name="view-application-logs"></a>Ver registos de aplicações

Pode utilizar o CLI de Difusão de Nuvem para visualizar registos para uma aplicação pelo seu nome:

```bash
cf logs hello-spring-cloud
```

Por predefinição, o comando de registos utiliza *a cauda,* que mostra novos registos à medida que são escritos. Para ver novos registos, refresque a aplicação hello-spring-cloud no navegador.

Para ver os registos que já foram escritos, adicione o interruptor `recent`:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Escala ré seleção da aplicação

Por predefinição, `cf push` apenas cria uma única instância da sua aplicação. Para garantir uma elevada disponibilidade e ativar a escala para uma maior saída, geralmente pretende executar mais do que uma instância das suas aplicações. Pode facilmente eliminar aplicações já implantadas utilizando o comando `scale`:

```bash
cf scale -i 2 hello-spring-cloud
```

Executar o comando `cf app` na aplicação mostra que a Cloud Foundry está a criar outra instância da aplicação. Uma vez iniciada a aplicação, a Cloud Foundry inicia automaticamente o tráfego de equilíbrio de carga.


## <a name="next-steps"></a>Passos seguintes

- [Leia a documentação da Cloud Foundry][cloudfoundry-docs]
- [Configurar o plugin azure DevOps Services para a Cloud Foundry][vsts-plugin]
- [Configure o bocal de análise de registo da Microsoft para a foundry em nuvem][loganalytics-nozzle]

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
