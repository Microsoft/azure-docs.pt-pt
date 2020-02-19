---
title: Executar contentores embutidos FAQ
description: Encontre respostas às perguntas frequentes sobre os contentores Linux embutidos no Azure App Service.
keywords: serviço de aplicações azure, web app, faq, linux, oss, web app para recipientes, multi-contentores, multicontentor
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c590a27f61c1a555ae30828332e4140a6116f95f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443861"
---
# <a name="azure-app-service-on-linux-faq"></a>Serviço de Aplicações Azure no Linux FAQ

Com o lançamento do App Service no Linux, estamos a trabalhar na adição de funcionalidades e na melhoria da nossa plataforma. Este artigo fornece respostas a perguntas que os nossos clientes nos têm feito recentemente.

Se tiver alguma pergunta, comente sobre este artigo.

## <a name="built-in-images"></a>Imagens incorporadas

**Quero bifurcar os contentores docker embutidos que a plataforma fornece. Onde posso encontrar os ficheiros?**

Podes encontrar todos os ficheiros do Docker no [GitHub.](https://github.com/azure-app-service) Pode encontrar todos os contentores do Docker no [Docker Hub.](https://hub.docker.com/u/appsvc/)

<a id="#startup-file"></a>

**Quais são os valores esperados para a secção 'Ficheiro de Arranque' quando configurar a pilha de tempo de execução?**

| Pilha           | Valor Esperado                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | o comando para iniciar a sua aplicação JAR (por exemplo, `java -jar /home/site/wwwroot/app.jar --server.port=80`) |
| Tomcat          | a localização de um script para executar quaisquer configurações necessárias (por exemplo, `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | o ficheiro de configuração PM2 ou o seu ficheiro script                                |
| .Núcleo De Rede       | o nome DLL compilado como `dotnet <myapp>.dll`                                 |
| Ruby            | o script Ruby que você quer inicializar a sua app com                     |

Estes comandos ou scripts são executados após o início do recipiente Docker incorporado, mas antes do seu código de aplicação ser iniciado.

## <a name="management"></a>Gestão

**O que acontece quando carregar no botão de reinício no portal Azure?**

Esta ação é a mesma que um Docker reinicia.

**Posso usar a Secure Shell (SSH) para ligar à máquina virtual do contentor de aplicações (VM)?**

Sim, pode fazê-lo através do site de gestão de controlo de fontes (SCM).

> [!NOTE]
> Também pode ligar ao contentor de aplicações diretamente a partir do seu computador de desenvolvimento local através de SSH, SFTP ou do Visual Studio Code (para aplicações Node.js de depuração em direto). Para obter mais informações, veja [Depuração remota e SSH no Serviço de Aplicações no Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Como posso criar um plano de serviço de aplicações Linux através de um modelo SDK ou de Um Gestor de Recursos Azure?**

Deve definir o campo **reservado** do serviço de aplicações como *verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

**A minha aplicação ainda usa uma imagem antiga do contentor do Docker depois de ter atualizado a imagem no Docker Hub. Apoia a integração contínua e a implantação de recipientes personalizados?**

Sim, para configurar a integração/implantação contínua para o Registo de Contentores Azure ou DockerHub, seguindo a [implantação contínua com web app para recipientes](./app-service-linux-ci-cd.md). Para registos privados, pode refrescar o recipiente parando e, em seguida, iniciando a sua aplicação web. Ou pode alterar ou adicionar uma definição de aplicação de boneco para forçar uma atualização do seu recipiente.

**Apoia ambientes de encenação?**

Sim.

**Posso usar *webDeploy/MSDeploy* para implementar a minha aplicação web?**

Sim, você precisa definir uma definição de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` a *falso*.

**A implementação da git da minha aplicação falha ao usar a aplicação web linux. Como posso resolver o assunto?**

Se a implementação da Git falhar na sua aplicação web Linux, escolha uma das seguintes opções para implementar o seu código de aplicação:

- Utilize a função De entrega contínua (pré-visualização): Pode armazenar o código fonte da sua aplicação num repo DevOps Git ou GitHub para utilizar a Azure Continuous Delivery. Para mais informações, consulte Como configurar a Entrega Contínua para a [aplicação web linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Utilize o [ZIP implementar API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Para utilizar este API, [SSH na sua aplicação web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e ir para a pasta onde pretende implementar o seu código. Execute o seguinte código:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se tiver um erro de que o comando `curl` não seja encontrado, certifique-se de que instala o caracol utilizando `apt-get install curl` antes de executar o comando `curl` anterior.

## <a name="language-support"></a>Suporte de idiomas

**Quero usar tomadas web na minha aplicação Node.js, quaisquer configurações especiais ou configurações para definir?**

Sim, desative `perMessageDeflate` no seu código Node.js do lado do servidor. Por exemplo, se estiver a utilizar socket.io, utilize o seguinte código:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Suporta aplicações não compiladas .NET Core?**

Sim.

**Apoia o Compositor como gestor de dependência para aplicações PHP?**

Sim, durante uma implantação git, Kudu deve detetar que você está implementando uma aplicação PHP (graças à presença de um ficheiro compositor.lock), e Kudu irá então desencadear uma instalação de compositor.

## <a name="custom-containers"></a>Personalizar contentores

**Estou a usar o meu próprio contentor personalizado. Quero que a plataforma monte uma quota SMB para o diretório `/home/`.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição **for não especificada** ou definida como *verdadeira,* o `/home/` diretório **será partilhado** em instâncias de escala, e os ficheiros escritos **persistirão** em reinícios. A definição explícita de `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a *falso* irá desativar o suporte.

**O meu recipiente personalizado demora muito tempo a começar, e a plataforma reinicia o contentor antes de terminar de arrancar.**

Pode configurar o tempo que a plataforma irá esperar antes de reiniciar o seu recipiente. Para isso, defina a definição de aplicações `WEBSITES_CONTAINER_START_TIME_LIMIT` para o valor que pretende. O valor padrão é de 230 segundos, e o valor máximo é de 1800 segundos.

**Qual é o formato para o URL do servidor de registo privado?**

Forneça o URL completo do registo, incluindo `http://` ou `https://`.

**Qual é o formato para o nome de imagem na opção de registo privado?**

Adicione o nome de imagem completo, incluindo o URL de registo privado (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que utilizam uma porta personalizada [não podem ser introduzidos através do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, utilize a [ferramenta`az` linha de comando](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Posso expor mais do que uma porta na minha imagem de recipiente personalizada?**

Não apoiamos a exposição de mais de um porto.

**Posso trazer o meu próprio armazém?**

Sim, [traga o seu próprio armazenamento](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) está em pré-visualização.

**Por que não posso navegar no sistema de ficheiros do meu contentor personalizado ou processos de execução a partir do site SCM?**

O local SCM funciona num recipiente separado. Não é possível verificar o sistema de ficheiros ou os processos de execução do contentor da aplicação.

**O meu contentor personalizado ouve uma porta que não seja a porta 80. Como posso configurar a minha aplicação para encaminhar pedidos para aquela porta?**

Temos deteção automática de portas. Também pode especificar uma definição de aplicação chamada *WEBSITES_PORT* e dar-lhe o valor do número de porta esperado. Anteriormente, a plataforma usava a definição de aplicações *PORT.* Estamos a planear depreciar esta definição de aplicações e *usáWEBSITES_PORT* exclusivamente.

**Preciso implementar HTTPS no meu recipiente personalizado?**

Não, a plataforma trata da rescisão HTTPS nas extremidades dianteiras partilhadas.

## <a name="multi-container-with-docker-compose"></a>Multi-contentor com Docker Compose

**Como configurar o Registo de Contentores Azure (ACR) para utilizar com multi-contentores?**

Para utilizar o ACR com multi-contentores, **todas as imagens** de contentores devem ser alojadas no mesmo servidor de registo ACR. Uma vez que estejam no mesmo servidor de registo, terá de criar definições de aplicação e, em seguida, atualizar o ficheiro de configuração Docker Compose para incluir o nome de imagem ACR.

Criar as seguintes definições de aplicação:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completo, ex: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (permitir o acesso à administração nas definições de ACR)

Dentro do ficheiro de configuração, consulte a sua imagem ACR como o seguinte exemplo:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como sei qual é o recipiente acessível à Internet?**

- Apenas um recipiente pode estar aberto para acesso
- Apenas o porto 80 e 8080 está acessível (portas expostas)

Aqui estão as regras para determinar qual o recipiente acessível - por ordem de precedência:

- Definição de aplicação `WEBSITES_WEB_CONTAINER_NAME` definida para o nome do recipiente
- O primeiro recipiente a definir o porto 80 ou 8080
- Se nenhum dos acima for verdadeiro, o primeiro recipiente definido no ficheiro será acessível (exposto)

## <a name="pricing-and-sla"></a>Preços e SLA

**Qual é o preço, agora que o serviço está geralmente disponível?**

É-lhe cobrado o preço normal do Serviço de Aplicações Azure pelo número de horas que a sua aplicação executa.

## <a name="other-questions"></a>Outras questões

**Quais são os caracteres suportados nos nomes das definições de aplicações?**

Só pode utilizar letras (A-Z, a-z), números (0-9) e o caráter de sublinhado (_) para as definições de aplicação.

**Onde posso pedir novas funcionalidades?**

Pode submeter a sua ideia no fórum de feedback de [Aplicações Web](https://aka.ms/webapps-uservoice). Adicione "[Linux]" ao título da sua ideia.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Serviço de Aplicações Azure no Linux?](app-service-linux-intro.md)
- [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Implantação contínua com aplicação web para contentores](./app-service-linux-ci-cd.md)
