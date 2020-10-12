---
title: Atualizações de imagem base - Tarefas
description: Saiba mais sobre as imagens base para imagens de contentores de aplicação e sobre como uma atualização de imagem base pode desencadear uma tarefa de Registo de Contentores Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 35933c4cdbbf2762f7a54bd945f8a8ffa55b9f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85918504"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Sobre atualizações de imagem base para tarefas ACR

Este artigo fornece informações de fundo sobre atualizações à imagem base de uma aplicação e como estas atualizações podem desencadear uma tarefa de Registo de Contentores Azure.

## <a name="what-are-base-images"></a>O que são imagens base?

Os ficheiros de estivadores que definem a maioria das imagens do contentor especificam uma imagem-mãe a partir da qual a imagem é baseada, muitas vezes referida como a sua *imagem base*. As imagens de base, normalmente, contêm o sistema operativo, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], no qual são aplicadas as restantes camadas do contentor. As imagens de base também podem incluir estruturas de aplicações, tais como [Node.js][base-node] ou [.NET Core][base-dotnet]. Estas imagens base são elas próprias tipicamente baseadas em imagens a montante do público. Várias das suas imagens de aplicação podem partilhar uma imagem base comum.

Frequentemente, uma imagem de base é atualizada pelo responsável pela manutenção da imagem para incluir as novas funcionalidades ou os melhoramentos no SO ou na estrutura. Os patches de segurança são outra causa comum para a atualização da imagem de base. Quando estas atualizações a montante ocorrerem, também deve atualizar as suas imagens base para incluir a correção crítica. Cada imagem de aplicação também deve ser reconstruída para incluir estas correções a montante agora incluídas na sua imagem base.

Em alguns casos, como uma equipa de desenvolvimento privado, uma imagem base pode especificar mais do que o SO ou o enquadramento. Por exemplo, uma imagem base pode ser uma imagem de componente de serviço partilhada que precisa de ser rastreada. Os membros de uma equipa podem precisar de rastrear esta imagem base para testes, ou precisam de atualizar regularmente a imagem ao desenvolver imagens de aplicações.

## <a name="track-base-image-updates"></a>Acompanhar atualizações de imagem base

O ACR Tasks permite compilar automaticamente as imagens quando é atualizada a imagem de base de um contentor.

ACR Tasks descobre dinamicamente as dependências de imagem base quando constrói uma imagem de contentor. Como resultado, pode detetar quando a imagem base de uma imagem de aplicação é atualizada. Com uma tarefa de construção pré-configurada, as Tarefas ACR podem reconstruir automaticamente todas as imagens de aplicação que referenciam a imagem base. Com esta deteção e reconstrução automática, a ACR Tasks poupa-lhe o tempo e esforço normalmente necessários para rastrear e atualizar manualmente cada imagem de aplicação que faz referência à sua imagem base atualizada.

## <a name="base-image-locations"></a>Localizações de imagem base

Para a construção de imagens a partir de um Dockerfile, uma tarefa ACR deteta dependências de imagens base nos seguintes locais:

* O mesmo registo de contentores Azure onde a tarefa é executada
* Outro registo privado de contentores Azure na mesma ou numa região diferente 
* Um repo público em Docker Hub 
* Um repo público no registo de contentores da Microsoft

Se a imagem base especificada na `FROM` declaração residir num desses locais, a tarefa ACR adiciona um gancho para garantir que a imagem é reconstruída sempre que a sua base for atualizada.

## <a name="base-image-notifications"></a>Notificações de imagem base

O tempo entre o momento em que uma imagem base é atualizada e quando a tarefa dependente é desencadeada depende da localização da imagem base:

* **Imagens base de um repo público em Docker Hub ou MCR** - Para imagens base em repositórios públicos, uma tarefa ACR verifica as atualizações de imagem num intervalo aleatório entre 10 e 60 minutos. As tarefas dependentes são executadas em conformidade.
* **Imagens base de um registo de contentores Azure** - Para imagens base nos registos de contentores Azure, uma tarefa ACR ativa imediatamente uma execução quando a sua imagem base é atualizada. A imagem base pode estar no mesmo ACR onde a tarefa é executado ou num ACR diferente em qualquer região.

## <a name="additional-considerations"></a>Considerações adicionais

* **Imagens base para imagens de aplicações** - Atualmente, uma tarefa ACR apenas rastreia atualizações de imagem base para imagens de aplicação *(tempo de execução).* Não rastreia atualizações de imagem base para imagens intermédias *(tempo de construção)* usadas em ficheiros dockers em várias fases.  

* **Ativado por predefinição** - Quando cria uma tarefa ACR com o comando [az acr criar][az-acr-task-create] comando, por predefinição a tarefa é *ativada* para desencadear através de uma atualização de imagem base. Ou seja, a `base-image-trigger-enabled` propriedade está definida para True. Se pretender desativar este comportamento numa tarefa, atualize a propriedade para Falso. Por exemplo, executar o seguinte comando [de atualização de tarefas az acr:][az-acr-task-update]

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Gatilho para rastrear as dependências** - Para permitir que uma tarefa de ACR determine e rastreie as dependências de uma imagem de um recipiente -- que incluem a sua imagem base - primeiro deve desencadear a tarefa de construir a imagem **pelo menos uma vez**. Por exemplo, desacione a tarefa manualmente utilizando o comando de execução de [tarefas az acr.][az-acr-task-run]

* **Etiqueta estável para a imagem base** - Para desencadear uma tarefa na atualização da imagem base, a imagem base deve ter uma etiqueta *estável,* tal como `node:9-alpine` . Esta marcação é típica de uma imagem base que é atualizada com os patches de SISTEMA e estrutura para uma versão estável mais recente. Se a imagem base for atualizada com uma nova etiqueta de versão, não desencadeia uma tarefa. Para obter mais informações sobre a marcação de imagens, consulte as [melhores práticas de orientação.](container-registry-image-tag-version.md) 

* **Outros detonadores de tarefas** - Numa tarefa desencadeada por atualizações de imagem base, também pode ativar os gatilhos baseados no [código fonte](container-registry-tutorial-build-task.md) ou [num horário](container-registry-tasks-scheduled.md). Uma atualização de imagem base também pode desencadear uma [tarefa em várias etapas](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes tutoriais para cenários para automatizar as construções de imagem de aplicação após a atualização de uma imagem base:

* [Automatizar a imagem do contentor constrói-se quando uma imagem base é atualizada no mesmo registo](container-registry-tutorial-base-image-update.md)

* [Automatizar a imagem do contentor constrói-se quando uma imagem base é atualizada num registo diferente](container-registry-tutorial-base-image-update.md)


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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
