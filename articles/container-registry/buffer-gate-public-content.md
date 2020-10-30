---
title: Gerir o conteúdo público no registo privado de contentores
description: Práticas e fluxos de trabalho no Registo de Contentores de Azure para gerir dependências de imagens públicas de Docker Hub e outros conteúdos públicos
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 10/29/2020
ms.openlocfilehash: c7beddda0d344f6b7606f3e2d3624bee39009c66
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043517"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Gerir conteúdos públicos com registo de contentores Azure

Este artigo é uma visão geral de práticas e fluxos de trabalho para usar um registo local, como um registo de [contentores Azure](container-registry-intro.md) para manter cópias de conteúdo público, como imagens de contentores em Docker Hub. 


## <a name="risks-with-public-content"></a>Riscos com conteúdo público

O seu ambiente pode ter dependências de conteúdos públicos, tais como imagens de contentores públicos, [gráficos helm,](https://helm.sh/)políticas [de Agente de Política Aberta](https://www.openpolicyagent.org/) (OPA) ou outros artefactos. Por exemplo, você pode executar [nginx](https://hub.docker.com/_/nginx) para encaminhamento de serviço ou `docker build FROM alpine` puxando imagens diretamente de Docker Hub ou de outro registro público. 

Sem controlos adequados, ter dependências de conteúdos de registo público pode introduzir riscos para o desenvolvimento da sua imagem e fluxos de trabalho de implantação. Para mitigar os riscos, mantenha as cópias locais de conteúdo público sempre que possível. Para mais detalhes, consulte o [blog Open Container Initiative](https://opencontainers.org/posts/blog). 

## <a name="authenticate-with-docker-hub"></a>Autenticar com Docker Hub

Como primeiro passo, se atualmente retirar imagens públicas do Docker Hub como parte de um fluxo de trabalho de construção ou implantação, recomendamos que autente usando uma conta Docker Hub em vez de fazer um pedido de atração anónimo.

> [!NOTE]
> A partir de 2 de novembro de 2020, os limites de taxa de descarregamento aplicam-se a pedidos anónimos e autenticados para Docker Hub a partir de contas Docker Free Plan e são aplicados por endereço IP. 
>
> Ao estimar o seu número de pedidos de puxar, tenha em conta que ao utilizar serviços de fornecedor de nuvem ou trabalhar atrás de um NAT corporativo, vários utilizadores serão apresentados ao Docker Hub em conjunto como um subconjunto de endereços IP.  Adicionar a autenticação paga do Docker aos pedidos feitos ao Docker Hub evitará possíveis perturbações no serviço devido ao estrangulamento do limite de taxa.
>
> Para mais detalhes, consulte [os preços e subscrições](https://www.docker.com/pricing) do Docker e os [Termos de Serviço do Docker.](https://www.docker.com/legal/docker-terms-service)



Para exemplos e cenários de autenticação, consulte [o limite da taxa de descarregamento](https://docs.docker.com/docker-hub/download-rate-limit/).

### <a name="docker-hub-access-token"></a>Sinal de acesso do Docker Hub

O Docker Hub suporta [fichas de acesso pessoal](https://docs.docker.com/docker-hub/access-tokens/) como alternativas a uma senha do Docker ao autenticar o Docker Hub. Os tokens são recomendados para serviços automatizados que retiram imagens do Docker Hub. Pode gerar vários tokens para diferentes utilizadores ou serviços e revogar fichas quando já não for necessário.

Para autenticar com `docker login` a utilização de um token, omita a palavra-passe na linha de comando. Quando solicitado para uma senha, insira o token em vez disso. Se ativou a autenticação de dois fatores para a sua conta Docker Hub, deve utilizar um token de acesso pessoal ao iniciar sessão a partir do Docker CLI.

### <a name="authenticate-from-azure-services"></a>Autenticar dos serviços Azure

Vários serviços Azure, incluindo o App Service e o Azure Container Instances, suportam a retirada de imagens de registos públicos, como o Docker Hub, para implantações de contentores. Se precisar de implementar uma imagem do Docker Hub, recomendamos que configufique as definições para autenticar usando uma conta Docker Hub. Exemplos:

**Serviço de Aplicações**

* **Fonte de imagem** : Docker Hub
* **Acesso ao repositório** : Privado
* **Início de sessão:**\<Docker Hub username>
* **Senha:**\<Docker Hub token>

Para mais informações, consulte [os pulls autenticados do Docker Hub no Serviço de Aplicações](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Fonte de imagem** : Docker Hub ou outro registo
* **Tipo de imagem** : Privado
* **Servidor de login do registo de** imagem : docker.io
* **Nome do utilizador do registo de imagem:**\<Docker Hub username>
* **Senha de registo de imagem:**\<Docker Hub token>
* **Imagem:** \<repo name\> docker.io/:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importar imagens para um registo de contentores Azure
 
Para começar a gerir cópias de imagens públicas, pode criar um registo de contentores Azure se ainda não tiver uma. Crie um registo utilizando o [Azure CLI](container-registry-get-started-azure-cli.md), [portal Azure](container-registry-get-started-portal.md), [Azure PowerShell,](container-registry-get-started-powershell.md)ou outras ferramentas. 

Como um passo único recomendado, [importar](container-registry-import-images.md) imagens base e outros conteúdos públicos para o seu registo de contentores Azure. O comando [de importação az acr](/cli/azure/acr#az_acr_import) no Azure CLI suporta a importação de imagens de registos públicos como Docker Hub e Microsoft Container Registry e de outros registos privados de contentores. 

`az acr import` não requer uma instalação local do Docker. Você pode executá-lo com uma instalação local do Azure CLI ou diretamente em Azure Cloud Shell suportando qualquer tipo de imagens de SO, imagens multi-arquitetura ou artefactos OCI, como gráficos Helm.

Exemplo:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub password>
```

Dependendo das necessidades da sua organização, pode importar para um registo dedicado ou um repositório num registo partilhado.

## <a name="automate-application-image-updates"></a>Automatizar atualizações de imagem de aplicação

Os desenvolvedores de imagens de aplicações devem garantir que o seu código refere o conteúdo local sob o seu controlo. Por exemplo, uma `Docker FROM` declaração num Dockerfile deve fazer referência a uma imagem num registo de imagem base privada em vez de um registo público. 

Expandindo a importação de imagens, crie uma [tarefa de registo de contentores Azure](container-registry-tasks-overview.md) para automatizar as construções de imagem de aplicação quando as imagens base são atualizadas. Uma tarefa de construção automatizada pode rastrear as [atualizações de imagem base](container-registry-tasks-base-images.md) e [as atualizações de códigos de origem](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Para um exemplo detalhado, consulte [como consumir e manter o conteúdo público com as tarefas de registo de contentores Azure](https://github.com/SteveLasker/azure-docs/blob/consuming-public-content/articles/container-registry/container-registry-consuming-public-content.md). 

> [!NOTE]
> Uma única tarefa pré-configurada pode reconstruir automaticamente todas as imagens de aplicação que referenciam uma imagem base dependente. 
 
## <a name="next-steps"></a>Passos seguintes
 
* Saiba mais sobre [as tarefas ACR](container-registry-tasks-overview.md) para construir, executar, empurrar e remendar imagens de contentores em Azure.
* Consulte os [tutoriais ACR Tasks](container-registry-tutorial-quick-task.md) para mais exemplos para automatizar construções e atualizações de imagem.
