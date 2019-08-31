---
title: Automatizar a criação e aplicação de patch de imagens de contêiner com tarefas de registro de contêiner do Azure (tarefas ACR)
description: Uma introdução às tarefas do ACR, um conjunto de recursos no registro de contêiner do Azure que fornece criação de imagem de contêiner segura e automatizada, gerenciamento e aplicação de patches na nuvem.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: bc32ce59a7ec99278fb193f375d4ca945c227d2f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172181"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar compilações de imagem de contêiner e manutenção com tarefas ACR

Os contêineres fornecem novos níveis de virtualização, isolando dependências do aplicativo e do desenvolvedor de requisitos operacionais e de infraestrutura. No entanto, o que resta é a necessidade de abordar como essa virtualização de aplicativos é gerenciada e corrigida no ciclo de vida do contêiner.

## <a name="what-is-acr-tasks"></a>O que são tarefas de ACR?

**As tarefas ACR** são um conjunto de recursos no registro de contêiner do Azure. Ele fornece criação de imagem de contêiner baseada em nuvem para Linux, Windows e ARM e pode automatizar a aplicação de patches do [sistema operacional e da estrutura](#automate-os-and-framework-patching) para seus contêineres do Docker. As tarefas ACR não apenas estende o ciclo de desenvolvimento de "loop interno" para a nuvem com compilações de imagem de contêiner sob demanda, mas também permite compilações automatizadas na confirmação do código-fonte ou quando a imagem base de um contêiner é atualizada. Com gatilhos de atualização de imagem base, você pode automatizar o fluxo de trabalho do sistema operacional e aplicação de patch de estrutura de aplicativo, mantendo ambientes seguros enquanto obedece às entidades de contêineres imutáveis.

Crie e teste imagens de contêiner com tarefas ACR de quatro maneiras:

* [Tarefa rápida](#quick-task): Crie e envie por push imagens de contêiner sob demanda, no Azure, sem precisar de uma instalação de mecanismo do Docker local. Imagine `docker build` ,`docker push` na nuvem. Crie a partir do código-fonte local ou de um repositório git.
* [Compilar na confirmação do código-fonte](#automatic-build-on-source-code-commit): Disparar uma criação de imagem de contêiner automaticamente quando o código for confirmado em um repositório git.
* [Compilar na atualização da imagem base](#automate-os-and-framework-patching): Dispare uma compilação de imagem de contêiner quando a imagem base dessa imagem tiver sido atualizada.
* [Tarefas de várias etapas](#multi-step-tasks): Defina tarefas de várias etapas que criam imagens, executam contêineres como comandos e enviam imagens por push a um registro. Esse recurso de tarefas ACR dá suporte à execução de tarefas sob demanda e às operações de compilação, teste e envio de imagens paralelas.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, criar e testar seu aplicativo antes de confirmar o controle do código-fonte, é, na verdade, o início do gerenciamento do ciclo de vida do contêiner.

Antes de confirmar sua primeira linha de código, o recurso de [tarefa rápida](container-registry-tutorial-quick-task.md) de tarefas de ACR pode fornecer uma experiência de desenvolvimento integrada descarregando suas compilações de imagem de contêiner para o Azure. Com tarefas rápidas, você pode verificar suas definições de compilação automatizadas e detectar possíveis problemas antes de confirmar seu código.

Usando o formato `docker build` conhecido, o comando [AZ ACR Build][az-acr-build] no CLI do Azure usa um *contexto* (o conjunto de arquivos a serem compilados), envia as tarefas de ACR e, por padrão, empurra a imagem interna para o registro após a conclusão.

Para obter uma introdução, consulte o guia de início rápido para [criar e executar uma imagem de contêiner](container-registry-quickstart-task-cli.md) no registro de contêiner do Azure.  

A tabela a seguir mostra alguns exemplos de locais de contexto com suporte para tarefas ACR:

| Local do contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro da ramificação mestre (ou outro padrão) de um repositório do GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Branch do GitHub | Branch específico de um repositório GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório GitHub. O exemplo mostra a combinação de uma especificação de ramificação e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor webremoto. | `http://remoteserver/myapp.tar.gz` |

Por padrão, as tarefas ACR criam imagens para o SO Linux e a arquitetura AMD64. Especifique a `--platform` marca para criar imagens do Windows ou imagens do Linux para outras arquiteturas. Especifique o sistema operacional e, opcionalmente, uma arquitetura com suporte no formato de sistema operacional `--platform Linux/arm`/arquitetura (por exemplo,). Para arquiteturas ARM, especifique opcionalmente uma variante no formato do sistema operacional/arquitetura/variante (por `--platform Linux/arm64/v8`exemplo,):

| OS | Arquitetura|
| --- | ------- | 
| Linux | AMD64<br/>braço<br/>arm64<br/>386 |
| Windows | AMD64 |

As tarefas ACR são projetadas como um primitivo de ciclo de vida do contêiner. Por exemplo, integre tarefas de ACR à sua solução de CI/CD. Ao executar o [AZ login][az-login] com uma [entidade de serviço][az-login-service-principal], sua solução de CI/CD poderá emitir comandos [AZ ACR Build][az-acr-build] para iniciar a criação de imagens.

Saiba como usar tarefas rápidas no primeiro tutorial de tarefas do ACR, [criar imagens de contêiner na nuvem com as tarefas do registro de contêiner do Azure](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Compilação automática na confirmação do código-fonte

Use tarefas ACR para disparar automaticamente uma compilação de imagem de contêiner quando o código for confirmado em um repositório git no GitHub ou no Azure DevOps. Tarefas de compilação, configuráveis com o comando CLI do Azure [AZ ACR Task][az-acr-task], permitem que você especifique um repositório git e, opcionalmente, uma ramificação e Dockerfile. Quando sua equipe confirma o código para o repositório, um webhook criado por tarefas de ACR dispara uma compilação da imagem de contêiner definida no repositório.

> [!IMPORTANT]
> Se você tiver criado previamente tarefas durante a visualização com `az acr build-task` o comando, essas tarefas precisarão ser recriadas usando o comando [AZ ACR Task][az-acr-task] .

Saiba como disparar compilações na confirmação do código-fonte no segundo tutorial de tarefas do ACR, automatizar compilações de [imagem de contêiner com tarefas de registro de contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatize a aplicação de patch do sistema operacional e da estrutura

O poder das tarefas do ACR para aprimorar verdadeiramente seu fluxo de trabalho de Build de contêiner vem da sua capacidade de detectar uma atualização para uma imagem de base. Quando a imagem base atualizada é enviada para o registro ou uma imagem base é atualizada em um repositório público, como no Hub do Docker, as tarefas do ACR podem criar automaticamente qualquer imagem de aplicativo baseada nela.

As imagens de contêiner podem ser amplamente categorizadas em imagens de *base* e imagens de *aplicativo* . As imagens básicas normalmente incluem o sistema operacional e as estruturas de aplicativo sobre as quais seu aplicativo é criado, juntamente com outras personalizações. Essas imagens básicas normalmente são baseadas em imagens de upstream públicas, por exemplo: [Alpine Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]ou [node. js][base-node]. Várias imagens de aplicativo podem compartilhar uma imagem de base comum.

Quando uma imagem do sistema operacional ou do App Framework é atualizada pelo mantenedor upstream, por exemplo, com um patch crítico de segurança do sistema operacional, você também deve atualizar suas imagens base para incluir a correção crítica. Cada imagem do aplicativo também deve ser recriada para incluir essas correções upstream agora incluídas na sua imagem base.

Como as tarefas do ACR detectam dinamicamente as dependências da imagem base quando ele cria uma imagem de contêiner, ele pode detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma [tarefa de compilação](container-registry-tutorial-base-image-update.md#create-a-task)pré-configurada, as tarefas de ACR recompilam **automaticamente cada imagem de aplicativo** para você. Com essa detecção e recriação automáticas, as tarefas ACR poupam o tempo e o esforço normalmente necessários para rastrear e atualizar manualmente cada uma das imagens de aplicativo que fazem referência à imagem base atualizada.

Uma tarefa ACR controla uma atualização de imagem base quando a imagem base está em um dos seguintes locais:

* O mesmo registro de contêiner do Azure em que a tarefa é executada
* Outro registro de contêiner do Azure na mesma região 
* Um repositório público no Hub do Docker
* Um repositório público no registro de contêiner da Microsoft

Saiba mais sobre aplicação de patches do sistema operacional e da estrutura no terceiro tutorial de tarefas do ACR, [Automatize a criação de imagens na atualização da imagem base com as tarefas do registro de contêiner do Azure](container-registry-tutorial-base-image-update.md).

## <a name="multi-step-tasks"></a>Tarefas com vários passos

As tarefas de várias etapas fornecem uma definição de tarefa baseada em etapas e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapas que automatiza o seguinte:

1. Criar uma imagem de aplicativo Web
1. Executar o contêiner do aplicativo Web
1. Criar uma imagem de teste de aplicativo Web
1. Executar o contêiner de teste de aplicativo Web que executa testes no contêiner do aplicativo em execução
1. Se os testes forem aprovados, crie um pacote de arquivo gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo de gráfico Helm

As tarefas de várias etapas permitem dividir a construção, a execução e o teste de uma imagem em etapas mais combináveis, com suporte à dependência entre etapas. Com tarefas de várias etapas em tarefas de ACR, você tem um controle mais granular sobre criação de imagens, teste e fluxos de trabalho de aplicação de patch de estrutura e sistema operacional.

Saiba mais sobre as tarefas de várias etapas em [executar tarefas de compilação, teste e patch de várias etapas em tarefas de ACR](container-registry-tasks-multi-step.md).

## <a name="view-task-logs"></a>Exibir logs de tarefa

Cada execução de tarefa gera a saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com êxito. Se você usar o comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)ou [AZ ACR Task execute](/cli/azure/acr/task#az-acr-task-run) para disparar a tarefa, a saída do log para a execução da tarefa será transmitida ao console e também será armazenada para recuperação posterior. Exiba os logs de uma tarefa executada no portal do Azure ou use o comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) .

A partir de julho de 2019, os dados e logs para tarefas executadas em um registro serão mantidos por padrão por 30 dias e, em seguida, limpos automaticamente. Se você quiser arquivar os dados para uma execução de tarefa, habilite o arquivamento usando o comando [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . O exemplo a seguir habilita o arquivamento para a tarefa executar *CF11* no registro myregistry.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Passos Seguintes

Quando estiver pronto para automatizar a aplicação de patch do sistema operacional e da estrutura criando suas imagens de contêiner na nuvem, confira a [série de tutoriais de tarefas ACR](container-registry-tutorial-quick-task.md)de três partes.

Opcionalmente, instale a [extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão de [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetuar pull e enviar imagens por push para um registro de contêiner do Azure ou executar tarefas de ACR, tudo no Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
