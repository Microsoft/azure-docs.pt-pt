---
title: FAQ do serviço de aplicativo no Linux – Azure | Microsoft Docs
description: Perguntas frequentes sobre o serviço Azure App no Linux.
keywords: serviço de aplicativo do Azure, aplicativo Web, perguntas frequentes, Linux, OSS, aplicativo Web para contêineres, vários contêineres, multirecipiente
services: app-service
documentationCenter: ''
author: msangapu-msft
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 10452590c9415291cb2a5913aeef5c8a00cdfe12
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562991"
---
# <a name="azure-app-service-on-linux-faq"></a>Perguntas frequentes sobre o serviço Azure App no Linux

Com o lançamento do serviço de aplicativo no Linux, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Este artigo fornece respostas a perguntas que nossos clientes têm feito recentemente.

Se você tiver uma pergunta, comente sobre este artigo.

## <a name="built-in-images"></a>Imagens internas

**Quero bifurcar os contêineres internos do Docker que a plataforma fornece. Onde posso encontrar esses arquivos?**

Você pode encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). Você pode encontrar todos os contêineres do Docker no [Hub](https://hub.docker.com/u/appsvc/)do Docker.

<a id="#startup-file"></a>

**Quais são os valores esperados para a seção arquivo de inicialização quando configuro a pilha de tempo de execução?**

| Pilha           | Valor esperado                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | o comando para iniciar seu aplicativo JAR (por exemplo, `java -jar my-app.jar --server.port=80`) |
| Tomcat, Wildfly | o local de um script para executar as configurações necessárias (por exemplo, `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | o arquivo de configuração PM2 ou o arquivo de script                                |
| .Net Core       | o nome da DLL compilada como`dotnet <myapp>.dll`                                 |
| Ruby            | o script Ruby com o qual você deseja inicializar seu aplicativo                     |

Esses comandos ou scripts são executados depois que o contêiner interno do Docker é iniciado, mas antes do código do aplicativo ser iniciado.

## <a name="management"></a>Gestão

**O que acontece quando eu pressiono o botão reiniciar na portal do Azure?**

Essa ação é igual à reinicialização do Docker.

**Posso usar Secure Shell (SSH) para se conectar à VM (máquina virtual) do contêiner de aplicativo?**

Sim, você pode fazer isso por meio do site SCM (gerenciamento de controle do código-fonte).

> [!NOTE]
> Também pode ligar ao contentor de aplicações diretamente a partir do seu computador de desenvolvimento local através de SSH, SFTP ou do Visual Studio Code (para aplicações Node.js de depuração em direto). Para obter mais informações, veja [Depuração remota e SSH no Serviço de Aplicações no Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Como posso criar um plano do serviço de aplicativo do Linux por meio de um SDK ou um modelo de Azure Resource Manager?**

Você deve definir o campo **reservado** do serviço de aplicativo como *true*.

## <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

**Meu aplicativo Web ainda usa uma imagem de contêiner do Docker antiga depois de atualizar a imagem no Hub do Docker. Você dá suporte à integração e à implantação contínuas de contêineres personalizados?**

Sim, para configurar a integração/implantação contínua para o registro de contêiner do Azure ou DockerHub, seguindo [a implantação contínua com aplicativo Web para contêineres](./app-service-linux-ci-cd.md). Para registros privados, você pode atualizar o contêiner interrompendo e, em seguida, iniciando seu aplicativo Web. Ou você pode alterar ou adicionar uma configuração de aplicativo fictícia para forçar uma atualização do seu contêiner.

**Há suporte para ambientes de preparo?**

Sim.

**Posso usar *WebDeploy/MSDeploy* para implantar meu aplicativo Web?**

Sim, você precisa definir uma configuração de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` como *false*.

**A implantação do git do meu aplicativo falha ao usar o aplicativo Web do Linux. Como posso contornar o problema?**

Se a implantação do git falhar em seu aplicativo Web do Linux, escolha uma das seguintes opções para implantar o código do aplicativo:

- Use o recurso entrega contínua (versão prévia): Você pode armazenar o código-fonte do aplicativo em um repositório Git do Azure DevOps ou no repositório GitHub para usar a entrega contínua do Azure. Para obter mais informações, consulte [como configurar a entrega contínua para o aplicativo Web do Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Usar a [API de implantação de zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Para usar essa API, use [SSH em seu aplicativo Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e vá para a pasta onde você deseja implantar seu código. Execute o seguinte código:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se você receber um erro informando que o `curl` comando não foi encontrado, certifique-se de instalar a ondulação usando `curl` `apt-get install curl` antes de executar o comando anterior.

## <a name="language-support"></a>Suporte de idiomas

**Desejo usar o Web Sockets no meu aplicativo node. js, configurações especiais ou definições a serem definidas?**

Sim, desabilitar `perMessageDeflate` no seu código node. js do lado do servidor. Por exemplo, se você estiver usando socket.io, use o seguinte código:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Há suporte para aplicativos .NET Core não compilados?**

Sim.

**Há suporte para o Composer como um Gerenciador de dependência para aplicativos PHP?**

Sim, durante uma implantação do git, o kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo Composer. Lock) e o kudu irá disparar uma instalação do Composer.

## <a name="custom-containers"></a>Contêineres personalizados

**Estou usando meu próprio contêiner personalizado. Quero que a plataforma monte um compartilhamento `/home/` SMB no diretório.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a configuração não for **especificada** ou definida como *true*, `/home/` o diretório **será compartilhado** entre as instâncias de escala e os arquivos gravados **persistirão** entre as reinicializações. Definir `WEBSITES_ENABLE_APP_SERVICE_STORAGE` explicitamente como *false* desabilitará a montagem.

**Meu contêiner personalizado leva muito tempo para ser iniciado e a plataforma reinicia o contêiner antes de concluir a inicialização.**

Você pode configurar a quantidade de tempo que a plataforma aguardará antes de reiniciar o contêiner. Para fazer isso, defina a `WEBSITES_CONTAINER_START_TIME_LIMIT` configuração do aplicativo para o valor desejado. O valor padrão é 230 segundos e o valor máximo é 1800 segundos.

**Qual é o formato da URL do servidor de registro particular?**

Forneça a URL completa do registro, `http://` incluindo `https://`ou.

**Qual é o formato do nome da imagem na opção de registro particular?**

Adicione o nome completo da imagem, incluindo a URL particular do registro (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que usam uma porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, use a [ `az` ferramenta de linha de comando](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Posso expor mais de uma porta em minha imagem de contêiner personalizada?**

Não há suporte para expor mais de uma porta.

**Posso colocar meu próprio armazenamento?**

Sim, [traga seu próprio armazenamento](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) para visualização.

**Por que não consigo procurar meu sistema de arquivos do contêiner personalizado ou executar processos do site do SCM?**

O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner do aplicativo.

**Meu contêiner personalizado escuta uma porta diferente da porta 80. Como posso configurar meu aplicativo para rotear solicitações para essa porta?**

Temos a detecção automática de porta. Você também pode especificar uma configuração de aplicativo chamada *WEBSITES_PORT* e dar a ela o valor do número da porta esperada. Anteriormente, a plataforma usava a configuração do aplicativo de *porta* . Estamos planejando substituir essa configuração de aplicativo e usar *WEBSITES_PORT* exclusivamente.

**Preciso implementar HTTPS em meu contêiner personalizado?**

Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="multi-container-with-docker-compose"></a>Vários contêineres com Docker Compose

**Como fazer configurar o ACR (registro de contêiner do Azure) para usar com vários contêineres?**

Para usar o ACR com vários contêineres, **todas as imagens de contêiner** precisam ser hospedadas no mesmo servidor de registro do ACR. Quando estiverem no mesmo servidor do registro, você precisará criar configurações do aplicativo e, em seguida, atualizar o arquivo de configuração Docker Compose para incluir o nome da imagem ACR.

Crie as seguintes configurações de aplicativo:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completa, ex: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (habilitar o acesso de administrador em configurações de ACR)

No arquivo de configuração, referencie sua imagem ACR como o exemplo a seguir:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como fazer saber qual contêiner é acessível pela Internet?**

- Somente um contêiner pode ser aberto para acesso
- Somente a porta 80 e 8080 está acessível (portas expostas)

Aqui estão as regras para determinar qual contêiner está acessível-na ordem de precedência:

- Configuração `WEBSITES_WEB_CONTAINER_NAME` de aplicativo definida como o nome do contêiner
- O primeiro contêiner para definir a porta 80 ou 8080
- Se nenhuma das opções acima for verdadeira, o primeiro contêiner definido no arquivo estará acessível (exposto)

## <a name="pricing-and-sla"></a>Preços e SLA

**Qual é o preço, agora que o serviço está disponível para o público geral?**

Você é cobrado pelo preço normal do serviço de Azure App pelo número de horas em que seu aplicativo é executado.

## <a name="other-questions"></a>Outras perguntas

**Quais são os caracteres com suporte em nomes de configurações do aplicativo?**

Você pode usar apenas letras (A-Z, a-z), números (0-9) e o caractere de sublinhado (_) para configurações do aplicativo.

**Onde posso solicitar novos recursos?**

Você pode enviar sua ideia no [Fórum de comentários de aplicativos Web](https://aka.ms/webapps-uservoice). Adicione "[Linux]" ao título da sua ideia.

## <a name="next-steps"></a>Passos seguintes

- [O que é o serviço Azure App no Linux?](app-service-linux-intro.md)
- [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Implantação contínua com Aplicativo Web para Contêineres](./app-service-linux-ci-cd.md)
