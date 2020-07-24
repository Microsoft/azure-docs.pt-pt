---
title: Quickstart - Desdobre o contentor Docker para a instância do contentor - Docker CLI
description: Neste arranque rápido, você usa o Docker CLI para implementar rapidamente uma aplicação web contentorizada que funciona em uma instância isolada do recipiente Azure
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: ''
ms.openlocfilehash: 684bb3537d26b60afb0fc0796a94e1a134aa50c8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102280"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Quickstart: Implementar uma instância de contentor em Azure utilizando o Docker CLI

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente para uma instância de contentor a pedido quando desenvolver aplicações nativas da nuvem e pretende mudar perfeitamente do desenvolvimento local para a implementação em nuvem.

Neste arranque rápido, você usa comandos nativos do Docker CLI para implantar um recipiente Docker e disponibilizar a sua aplicação em Instâncias de Contentores Azure. Esta capacidade é ativada pela [integração entre Docker e Azure](https://docs.docker.com/engine/context/aci-integration/) (beta). Alguns segundos depois de executar um `docker run` comando, pode navegar para a aplicação em execução no recipiente:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplicação implementada com o Azure Container Instances vista no browser":::

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

Para este arranque rápido, tem de instalar a versão 2.3.2.0 ou mais tarde do Docker Desktop Edge, disponível para [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) ou [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Ou instale o [CLI de Integração do Docker ACI para o Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (beta). 

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização e requer funcionalidades beta (pré-visualização) em Docker. Leia mais sobre [as versões Stable and Edge do Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions). Nem todas as características de Azure Container Instances são suportadas. Fornecer feedback sobre a integração Docker-Azure criando um problema no repositório [aci-integração-beta](https://github.com/docker/aci-integration-beta) GitHub.

## <a name="create-azure-context"></a>Criar contexto azul

Para utilizar comandos Docker para executar contentores em Instâncias de Contentores Azure, primeiro faça login em Azure:

```bash
docker login azure
```

Quando solicitado, insira ou selecione as suas credenciais Azure.


Corra `docker context create aci` para criar um contexto ACI. Este contexto associa o Docker à sua subscrição Azure para que possa criar casos de contentores. Por exemplo, para criar um contexto chamado *myacicontext*:

```
docker context create aci myacicontext
```

Quando solicitado, selecione o seu ID de subscrição Azure e, em seguida, selecione um grupo de recursos existente ou **crie um novo grupo de recursos**. Se escolher um novo grupo de recursos, é criado com um nome gerado pelo sistema. O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.


Corra `docker context ls` para confirmar que adicionou o contexto ACI aos seus contextos Docker:

```
docker context ls
```

## <a name="create-a-container"></a>Criar um contentor

Depois de criar um contexto Docker, pode criar um recipiente em Azure. Neste arranque rápido, usa-se a `mcr.microsoft.com/azuredocs/aci-helloworld` imagem pública. Esta imagem embala uma pequena aplicação web escrita em Node.js que serve uma página html estática.

Em primeiro lugar, mude para o contexto da ACI. Todos os comandos subsequentes do Docker são executados neste contexto.

```
docker context use myacicontext
```

Executar o seguinte `docker run` comando para criar a instância do contentor Azure com a porta 80 exposta à internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Saída de amostra para uma implantação bem sucedida:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Corra `docker ps` para obter detalhes sobre o recipiente de funcionamento, incluindo o endereço IP público:

```
docker ps
```


A produção da amostra mostra um endereço IP público, neste caso *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Agora aceda ao endereço IP no seu browser. Se lhe for apresentada uma página Web semelhante à seguinte, parabéns! Implementou com êxito uma aplicação em execução num contentor de Docker para Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Aplicação implementada com o Azure Container Instances vista no browser":::

## <a name="pull-the-container-logs"></a>Extrair os registos de contentor

Quando precisar de resolver problemas de um contentor ou da aplicação nele executada (ou apenas ver o respetivo resultado), comece por visualizar os registos da instância de contentor.

Por exemplo, executar o `docker logs` comando para ver os registos do recipiente *esfomeado-kirch* no contexto ACI:

```azurecli-interactive
docker logs hungry-kirch
```

A saída apresenta os registos para o contentor e deve mostrar os pedidos GET de HTTP gerados quando visualizou a aplicação no seu browser.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar o recipiente, corra `docker rm` para removê-lo. Este comando para e elimina a instância do recipiente Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um recipiente Azure a partir de uma imagem pública, usando a integração entre Docker e Azure. Saiba mais sobre cenários de integração na documentação do [Docker.](https://docs.docker.com/engine/context/aci-integration/) 

Também pode utilizar a [extensão Docker](https://aka.ms/VSCodeDocker) para o Código do Estúdio Visual para uma experiência integrada para desenvolver, executar e gerir contentores, imagens e contextos.

Para utilizar ferramentas Azure para criar e gerir casos de contentores, consulte outros quickstarts utilizando o [Azure CLI](container-instances-quickstart.md), [Azure PowerShell,](container-instances-quickstart-powershell.md) [Portal Azure](container-instances-quickstart-portal.md)e [modelo Azure Resource Manager](container-instances-quickstart-template.md).

Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

