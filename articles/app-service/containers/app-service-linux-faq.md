---
title: Serviço de aplicações no Linux FAQ - Azure | Documentos da Microsoft
description: Serviço de aplicações do Azure no Linux FAQ.
keywords: serviço de aplicações do Azure, aplicação web, perguntas frequentes, linux, oss, aplicação web para contentores, com vários contentores, multicontainer
services: app-service
documentationCenter: ''
author: yili
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4c2ed5fa65528a690d618e45c118d2433820ddc6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57871498"
---
# <a name="azure-app-service-on-linux-faq"></a>Serviço de aplicações do Azure no Linux FAQ

Com o lançamento do serviço de aplicações no Linux, estamos a trabalhar na adição de recursos e a fazer melhorias para a nossa plataforma. Este artigo fornece respostas a perguntas que nossos clientes têm pedido nos recentemente.

Se tiver dúvidas, comentários sobre este artigo.

## <a name="built-in-images"></a>Imagens incorporadas

**Quero bifurcar os contentores de Docker incorporados que a plataforma oferece. Onde posso encontrar esses arquivos?**

Pode encontrar todos os ficheiros de Docker no [GitHub](https://github.com/azure-app-service). Encontrará todos os contentores do Docker [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Quais são os valores esperados para a secção do ficheiro de arranque quando posso configurar a pilha de tempo de execução?**

| Pilha     | Valor esperado                                                                |
|-----------|-------------------------------------------------------------------------------|
| Java SE   | um comando para iniciar seu `.jar` aplicação                                    |
| Tomcat    | a localização de um script para executar qualquer configruations necessário para a sua aplicação |
| Node.js   | o ficheiro de configuração PM2 ou do seu ficheiro de script                                |          
| .Net Core | o nome DLL compilado como `dotnet <myapp>.dll`                                 |
| Ruby      | o script de Ruby que deseja inicializar a sua aplicação com                     

## <a name="management"></a>Gestão

**O que acontece quando pressiono o botão de reinício no portal do Azure?**

Esta ação é o mesmo que um reinício do Docker.

**Posso utilizar o Secure Shell (SSH) para ligar à máquina de virtual de contentores de aplicação (VM)?**

Sim, pode fazê-lo através do site de gestão (SCM) do controle de origem.

> [!NOTE]
> Também pode ligar ao contentor de aplicações diretamente a partir do seu computador de desenvolvimento local através de SSH, SFTP ou do Visual Studio Code (para aplicações Node.js de depuração em direto). Para obter mais informações, veja [Depuração remota e SSH no Serviço de Aplicações no Linux](https://aka.ms/linux-debug).
>

**Como posso criar um plano de serviço de aplicações do Linux através de um SDK ou um modelo Azure Resource Manager?**

Deve definir os **reservado** campo do serviço de aplicações para *verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

**Meu aplicativo web ainda utiliza uma imagem de contentor do Docker antiga depois atualizei a imagem no Docker Hub. Suporta a integração contínua e implementação de contentores personalizados?**

Sim, para configurar a integração/implementação contínua para Azure Container Registry ou DockerHub, seguir [implementação contínua com a aplicação Web para contentores](./app-service-linux-ci-cd.md). Para registos privados, pode atualizar o contentor ao parar e, em seguida, iniciar a aplicação web. Ou pode alterar ou adicionar uma definição de aplicativo fictício para forçar uma atualização do seu contentor.

**Suporta ambientes de teste?**

Sim.

**Pode utilizar *WebDeploy/MSDeploy* para implementar a minha aplicação web?**

Sim, precisa definir uma aplicação chamada `WEBSITE_WEBDEPLOY_USE_SCM` para *false*.

**Implementação de Git do meu aplicativo falhar ao utilizar a aplicação web do Linux. Como posso solucionar o problema?**

Se falhar a implementação do Git para a sua aplicação web do Linux, escolha uma das seguintes opções para implementar o código da aplicação:

- Utilize a funcionalidade de entrega contínua (pré-visualização): Pode armazenar o código-fonte da sua aplicação num repositório de Git de DevOps do Azure ou o repositório do GitHub para utilizar a entrega contínua do Azure. Para obter mais informações, consulte [como configurar a entrega contínua para aplicação web do Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Utilize o [ZIP implementar API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Para utilizar esta API, [SSH na sua aplicação web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e aceda à pasta onde pretende implementar o seu código. Execute o seguinte código:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se obtiver um erro que o `curl` comando não for encontrado, certifique-se de instalar o curl, utilizando `apt-get install curl` antes de executar o anterior `curl` comando.

## <a name="language-support"></a>Suporte de idiomas

**Quer utilize sockets web na minha aplicação node. js, quaisquer definições especiais ou configurações para definir?**

Sim, desativar `perMessageDeflate` em seu código do lado do servidor node. js. Por exemplo, se estiver a utilizar o socket.io, utilize o seguinte código:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Suporta aplicações de .NET Core não compiladas?**

Sim.

**Suporta o compositor como um Gestor de dependências para aplicações PHP?**

Sim, durante a implementação de Git, Kudu deve detetar que estiver implantando um aplicativo PHP (graças a presença de um ficheiro de composer.lock) e Kudu, em seguida, irá disparar uma instalação do compositor.

## <a name="custom-containers"></a>Contentores personalizados

**Uso meu próprio contentor personalizado. Quero que a plataforma para montar uma partilha SMB para o `/home/` diretório.**

Pode fazer isso definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição de aplicação para *true*. Tenha em mente que isso fará com que os reinícios de contentor quando o armazenamento de plataforma passa por uma alteração.

>[!NOTE]
>Se o `WEBSITES_ENABLE_APP_SERVICE_STORAGE` definição está especificada ou definida como *false*, o `/home/` diretório não será compartilhado entre instâncias de dimensionamento e ficheiros que são escritos lá não serão incluídos em reinícios.

**Meu contentor personalizado demora muito tempo para iniciar, e a plataforma reinicia o contentor, antes de concluir a iniciar.**

Pode configurar a quantidade de tempo, que a plataforma aguardará antes de reinicia o contentor. Para tal, defina o `WEBSITES_CONTAINER_START_TIME_LIMIT` definição de aplicação para o valor desejado. O valor predefinido é 230 segundos e o valor máximo é 1800 segundos.

**O que é o formato para o URL do servidor de registo privado?**

Indique o URL de registo completo, incluindo `http://` ou `https://`.

**O que é o formato para o nome da imagem na opção de registo privado?**

Adicione o nome da imagem inteira, incluindo o URL de registo privado (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes que utilizam uma porta personalizada de imagem [não é possível inserir através do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, utilize o [ `az` ferramenta da linha de comandos](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Pode expor mais de uma porta na minha imagem de contentor personalizado?**

Nós não suportam atualmente expor mais de uma porta.

**Posso usar o meu próprio armazenamento?**

Sim, [traga seu próprio armazenamento](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) está em pré-visualização.

**Por que não pode procurar os ficheiro sistema ou a executar processos meu contentor personalizado do SCM site?**

O site do SCM é executado num contentor separado. Não é possível verificar os processos de sistema ou de execução do arquivo do contêiner do aplicativo.

**Meu contentor personalizado fica à escuta numa porta diferente da 80. Como configurar a minha aplicação para encaminhar pedidos para essa porta?**

Temos a deteção automática de porta. Também pode especificar uma aplicação chamada *WEBSITES_PORT* e dê a ele o valor do número de porta esperado. Anteriormente, a plataforma utilizada a *porta* definição de aplicação. Estamos a planear preterir esta definição de aplicação e utilizar *WEBSITES_PORT* exclusivamente.

**É necessário implementar o HTTPS no meu contentor personalizado?**

Não, a plataforma lida com a terminação HTTPS nos front-ends partilhados.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Composição de vários contentores com o Docker e Kubernetes

**Como posso configurar o Azure Container Registry (ACR) para utilizar com vários contentores?**

Para utilizar o ACR com vários contentores, **todas as imagens de contentor** precisam ser hospedados no mesmo servidor de registo do ACR. Quando estiverem no mesmo servidor de registo, terá de criar as definições da aplicação e, em seguida, atualizar o ficheiro de configuração do Docker Compose ou o Kubernetes para incluir o nome de imagem do ACR.

Crie as seguintes definições de aplicação:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (total de URL, por exemplo: https://<server-name>.azurecr.io)
- DOCKER_REGISTRY_SERVER_PASSWORD (ativar administrador de acesso nas definições de ACR)

Dentro do arquivo de configuração, fazem referência a imagem do ACR semelhante ao seguinte exemplo:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como posso saber que o contentor está acessível pela internet?**

- Apenas um contentor pode ser aberto para acesso
- Apenas a porta 80 e 8080 está acessíveis (portas expostas)

Seguem-se as regras para determinar o contentor que está acessível - na ordem de precedência:

- Definição de aplicação `WEBSITES_WEB_CONTAINER_NAME` definido como o nome do contentor
- O primeiro contentor para definir a porta 80 ou 8080
- Se nenhum dos acima for VERDADEIRO, o primeiro contentor definido no arquivo estará acessível (exposto)

## <a name="pricing-and-sla"></a>Preços e SLA

**Quais são os preços, agora que o serviço está disponível em geral?**

É cobrado o preço de serviço de aplicações do Azure normal para o número de horas durante as quais a aplicação for executada.

## <a name="other-questions"></a>Outras perguntas

**Quais são os carateres suportados em nomes de definições de aplicação?**

Pode utilizar apenas letras (A-Z, a-z), números (0-9) e o caráter de sublinhado (_) para configurações do aplicativo.

**Onde posso pedir novas funcionalidades?**

Pode enviar a sua ideia no [fórum de comentários de aplicações Web](https://aka.ms/webapps-uservoice). Adicione "[Linux]" para o título da sua ideia.

## <a name="next-steps"></a>Passos Seguintes

- [O que é o serviço de aplicações do Azure no Linux?](app-service-linux-intro.md)
- [Configurar ambientes de teste no Serviço de Aplicações do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Implementação contínua com a aplicação Web para contentores](./app-service-linux-ci-cd.md)
