---
title: Executar recipientes embutidos FAQ
description: Encontre respostas às perguntas frequentes sobre os contentores Linux incorporados no Azure App Service.
keywords: serviço de aplicativos azure, web app, faq, linux, oss, web app para contentores, multi-contentor, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6faec27bf368b3eb45e05a91307df6027bda93b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100094003"
---
# <a name="azure-app-service-on-linux-faq"></a>FAQ do Serviço de Aplicações do Azure no Linux

Com o lançamento do App Service no Linux, estamos a trabalhar na adição de funcionalidades e na melhoria da nossa plataforma. Este artigo fornece respostas a perguntas que os nossos clientes nos têm feito recentemente.

Se tiver alguma pergunta, comente este artigo.

## <a name="built-in-images"></a>Imagens embutham

**Quero forrear os contentores estivadores embutidos que a plataforma fornece. Onde posso encontrar os ficheiros?**

Podes encontrar todos os ficheiros do Docker no [GitHub.](https://github.com/azure-app-service) Você pode encontrar todos os contentores Docker em [Docker Hub.](https://hub.docker.com/u/appsvc/)

<a id="#startup-file"></a>

**Quais são os valores esperados para a secção de Ficheiros de Arranque quando configurar a pilha de tempo de execução?**

| Pilha           | Valor Esperado                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | o comando para iniciar a sua aplicação JAR (por exemplo, `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | a localização de um script para executar as configurações necessárias (por exemplo, `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | o ficheiro de configuração PM2 ou o seu ficheiro de script                                |
| .NET Core       | o nome DLL compilado como `dotnet <myapp>.dll`                                 |
| Ruby            | o script Ruby que você quer inicializar a sua app com                     |

Estes comandos ou scripts são executados após o início do recipiente docker incorporado, mas antes do seu código de aplicação ser iniciado.

## <a name="management"></a>Gestão

**O que acontece quando carrego no botão de reinício no portal Azure?**

Esta ação é a mesma que um recomeço do Docker.

**Posso utilizar o Secure Shell (SSH) para ligar à máquina virtual do contentor de aplicações (VM)?**

Sim, pode fazê-lo através do site de gestão de controlo de fontes (SCM).

> [!NOTE]
> Também pode ligar ao contentor de aplicações diretamente a partir do seu computador de desenvolvimento local através de SSH, SFTP ou do Visual Studio Code (para aplicações Node.js de depuração em direto). Para obter mais informações, veja [Depuração remota e SSH no Serviço de Aplicações no Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Como posso criar um plano de Serviço de Aplicações Linux através de um SDK ou um modelo de Gestor de Recursos Azure?**

Desaprote o campo **reservado** do serviço de aplicações para *o verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

**A minha aplicação web ainda usa uma imagem antiga do estivador depois de ter atualizado a imagem no Docker Hub. Apoia a integração contínua e a implantação de recipientes personalizados?**

Sim, para configurar a integração/implantação contínua para o Registo de Contentores Azure ou DockerHub, seguindo a [implementação contínua com a Web App para contentores](./deploy-ci-cd-custom-container.md). Para registos privados, pode refrescar o recipiente parando e iniciando a sua aplicação web. Ou pode alterar ou adicionar uma definição de aplicação falsa para forçar uma atualização do seu recipiente.

**Apoia ambientes de encenação?**

Sim.

**Posso usar *webDeploy/MSDeploy* para implementar a minha aplicação web?**

Sim, precisa de definir uma definição de aplicação chamada `WEBSITE_WEBDEPLOY_USE_SCM` *falsa*.

**A implementação da aplicação do Git falha ao usar a aplicação web Linux. Como posso contornar o assunto?**

Se a implementação do Git falhar na sua aplicação web Linux, escolha uma das seguintes opções para implementar o seu código de aplicação:

- Utilize a função de Entrega Contínua (Pré-visualização): Pode armazenar o código fonte da sua aplicação num repo Azure DevOps Git ou GitHub para utilizar a Azure Continuous Delivery. Para obter mais informações, consulte [Como configurar a entrega contínua para a aplicação web Linux.](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)

- Utilize a [API de implementação ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Para utilizar esta API, [SSH na sua aplicação web](configure-linux-open-ssh-session.md) e vá para a pasta onde pretende implementar o seu código. Execute o seguinte código:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se tiver um erro no qual o `curl` comando não seja encontrado, certifique-se de que instala o curl utilizando `apt-get install curl` antes de executar o comando `curl` anterior.

## <a name="language-support"></a>Suporte de idiomas

**Quero usar tomadas web na minha aplicação Node.js, configurações especiais ou configurações para definir?**

Sim, desative `perMessageDeflate` o código de Node.js do lado do servidor. Por exemplo, se estiver a utilizar socket.io, utilize o seguinte código:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Suporta aplicações não-compensadas .NET Core?**

Sim.

**Apoia o Compositor como gestor de dependência para aplicações PHP?**

Sim, durante uma implementação de Git, Kudu deve detetar que você está implementando uma aplicação PHP (graças à presença de um ficheiro compositor.lock), e Kudu irá então desencadear uma instalação de compositor.

## <a name="custom-containers"></a>Personalizar contentores

**Estou a usar o meu próprio contentor personalizado. Quero que a plataforma monte uma participação da SMB no `/home/` diretório.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a definição não for **especificada** ou definida como *falsa,* o `/home/` diretório não será **partilhado** em instâncias de escala, e os ficheiros escritos **não persistirão** em todo o recomeço. A definição explícita `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para *o verdadeiro* ativará o suporte.

**O meu contentor personalizado demora muito tempo a começar, e a plataforma reinicia o contentor antes de terminar o arranque.**

Pode configurar o tempo que a plataforma espera antes de reiniciar o seu recipiente. Para tal, defina a definição da `WEBSITES_CONTAINER_START_TIME_LIMIT` aplicação para o valor que pretende. O valor predefinido é de 230 segundos e o valor máximo é de 1800 segundos.

**Qual é o formato para o URL do servidor de registo privado?**

Forneça o URL de registo completo, incluindo `http://` ou `https://` .

**Qual é o formato do nome de imagem na opção de registo privado?**

Adicione o nome de imagem completo, incluindo o URL de registo privado (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que utilizam uma porta personalizada [não podem ser introduzidos através do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para `docker-custom-image-name` definir, utilize a [ `az` ferramenta de linha de comando](/cli/azure/webapp/config/container#az-webapp-config-container-set).

**Posso expor mais do que uma porta na minha imagem personalizada do contentor?**

Não apoiamos expor mais do que um porto.

**Posso levar o meu próprio armazém?**

Sim, [traga o seu próprio armazenamento](./configure-connect-to-azure-storage.md) está em pré-visualização.

**Por que não posso navegar no sistema de ficheiros do meu contentor personalizado ou processar a partir do site SCM?**

O sítio SCM funciona num recipiente separado. Não é possível verificar o sistema de ficheiros ou executar processos do recipiente da aplicação.

**O meu contentor personalizado ouve um porto que não seja o porto 80. Como posso configurar a minha app para encaminhar pedidos para aquele porto?**

Temos deteção automática da porta. Também pode especificar uma configuração de aplicação chamada *WEBSITES_PORT* e dar-lhe o valor do número de porta esperado. Anteriormente, a plataforma utilizava a definição da aplicação *PORT.* Estamos a planear depreciar esta configuração da aplicação e *usá WEBSITES_PORT* exclusivamente.

**Preciso implementar HTTPS no meu recipiente personalizado?**

Não, a plataforma lida com a rescisão HTTPS nas extremidades dianteiras partilhadas.

## <a name="multi-container-with-docker-compose"></a>Multi-contentor com Docker Compose

**Como configurar o Registo de Contentores Azure (ACR) para utilizar com vários contentores?**

Para utilizar o ACR com multi-contentores, **todas as imagens** do contentor devem ser acolhidas no mesmo servidor de registo ACR. Uma vez que estejam no mesmo servidor de registo, terá de criar definições de aplicação e, em seguida, atualizar o ficheiro de configuração Do Docker Compose para incluir o nome de imagem ACR.

Criar as seguintes definições de aplicação:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completo, ex: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (permitir o acesso a administrador nas definições de ACR)

Dentro do ficheiro de configuração, faça referência à sua imagem ACR como o seguinte exemplo:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como sei qual é o recipiente acessível à Internet?**

- Apenas um recipiente pode estar aberto para acesso
- Apenas a porta 80 e 8080 está acessível (portas expostas)

Aqui estão as regras para determinar qual o recipiente acessível - por ordem de precedência:

- Definição de aplicação `WEBSITES_WEB_CONTAINER_NAME` definida para o nome do recipiente
- O primeiro recipiente a definir a porta 80 ou 8080
- Se nenhum dos acima referidos for verdadeiro, o primeiro recipiente definido no ficheiro será acessível (exposto)


## <a name="web-sockets"></a>Tomadas Web

As tomadas web são suportadas em aplicações Linux.

> [!IMPORTANT]
> As tomadas web não são suportadas atualmente para aplicações Linux em Planos de Serviço de Aplicações Gratuitas. Estamos a trabalhar na remoção desta limitação e planeamos suportar até 5 ligações web socket em planos de Serviço de Aplicações Gratuitas.

## <a name="pricing-and-sla"></a>Preços e SLA

**Qual é o preço, agora que o serviço está geralmente disponível?**

Os preços variam de acordo com a SKU e a região, mas pode ver mais detalhes na nossa página de preços: [Preço do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Outras questões

**O que significa "Recurso solicitado não disponível no grupo de recursos"?**

Pode ver esta mensagem ao criar uma aplicação web utilizando o Azure Resource Manager (ARM). Com base numa limitação atual, para o mesmo grupo de recursos, não é possível misturar aplicações Windows e Linux na mesma região.

**Quais são os caracteres suportados nos nomes de definições de aplicações?**

Pode utilizar apenas letras (A-Z, a-z), números (0-9) e o caráter de sublinhado (_) para configurações de aplicações.

**Onde posso pedir novas funcionalidades?**

Pode submeter a sua ideia no fórum de feedback das [Web Apps.](https://aka.ms/webapps-uservoice) Adicione "[Linux]" ao título da sua ideia.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Serviço de Aplicações Azure no Linux?](overview.md#app-service-on-linux)
- [Configurar ambientes de teste no Serviço de Aplicações do Azure](deploy-staging-slots.md)
- [Implementação contínua com aplicação web para contentores](./deploy-ci-cd-custom-container.md)
