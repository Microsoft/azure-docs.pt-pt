---
title: Atualizações de imagem base - Tarefas
description: Saiba mais sobre as imagens base para imagens de contentores de aplicação e sobre como uma atualização de imagem base pode desencadear uma tarefa de Registo de Contentores Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617934"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Sobre atualizações de imagem base para Tarefas ACR

Este artigo fornece informações de fundo sobre atualizações à imagem base de uma aplicação e como estas atualizações podem desencadear uma tarefa de Registo de Contentores Azure.

## <a name="what-are-base-images"></a>O que são imagens base?

Os ficheiros de dockerque definem a maioria das imagens de contentores especificam uma imagem dos pais a partir da qual a imagem é baseada, muitas vezes referida como a sua *imagem base*. As imagens base normalmente contêm o sistema operativo, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server,][base-windows]no qual o resto das camadas do recipiente são aplicadas. Podem também incluir quadros de aplicação como [o Node.js][base-node] ou [o .NET Core][base-dotnet]. Estas imagens base são, em si, tipicamente baseadas em imagens públicas a montante. Várias das suas imagens de aplicação podem partilhar uma imagem de base comum.

Frequentemente, uma imagem de base é atualizada pelo responsável pela manutenção da imagem para incluir as novas funcionalidades ou os melhoramentos no SO ou na estrutura. Os patches de segurança são outra causa comum para a atualização da imagem de base. Quando estas atualizações a montante ocorrerem, também deve atualizar as imagens base para incluir a correção crítica. Cada imagem de aplicação deve então ser reconstruída para incluir estas correções a montante agora incluídas na sua imagem base.

Em alguns casos, como uma equipa de desenvolvimento privado, uma imagem base pode especificar mais do que o SISTEMA ou o enquadramento. Por exemplo, uma imagem base pode ser uma imagem de componente de serviço partilhado que precisa de ser rastreada. Os membros de uma equipa podem precisar de rastrear esta imagem base para testes, ou precisam de atualizar regularmente a imagem ao desenvolver imagens de aplicações.

## <a name="track-base-image-updates"></a>Acompanhar as atualizações de imagem base

O ACR Tasks permite compilar automaticamente as imagens quando é atualizada a imagem de base de um contentor.

A ACR Tasks descobre dinamicamente dependências de imagem base quando constrói uma imagem de recipiente. Como resultado, pode detetar quando a imagem base de uma imagem de aplicação é atualizada. Com uma tarefa de construção configurada, as Tarefas ACR podem reconstruir automaticamente todas as imagens de aplicação que referenciam a imagem base. Com esta deteção e reconstrução automáticas, as Tarefas ACR poupam-lhe o tempo e o esforço normalmente necessários para rastrear e atualizar manualmente cada imagem de aplicação que refere a sua imagem base atualizada.

## <a name="base-image-locations"></a>Localizações de imagem base

Para a imagem construída a partir de um Dockerfile, uma tarefa ACR deteta dependências de imagens base nos seguintes locais:

* O mesmo registo de contentores Azure onde a tarefa é executado
* Outro registo privado de contentores Azure na mesma ou numa região diferente 
* Um repo público em Docker Hub 
* Um repo público no Registo de Contentores da Microsoft

Se a imagem base especificada na declaração de `FROM` residir num desses locais, a tarefa ACR adiciona um gancho para garantir que a imagem é reconstruída sempre que a sua base é atualizada.

## <a name="additional-considerations"></a>Considerações adicionais

* **Imagens base para imagens** de aplicação - Atualmente, uma tarefa ACR apenas rastreia as atualizações de imagem base para imagens de aplicação (tempo de*execução).* Não rastreia atualizações de imagem base para imagens intermédias (tempo de*construção)* utilizadas em Ficheiros Dockerfiles em vários estágios.  

* **Ativada por padrão** - Quando cria uma tarefa ACR com a [tarefa az acr criar][az-acr-task-create] comando, por padrão a tarefa é *ativada* para o gatilho através de uma atualização de imagem base. Ou seja, a propriedade `base-image-trigger-enabled` está definida para True. Se quiser desativar este comportamento numa tarefa, atualize a propriedade para False. Por exemplo, executar o seguinte comando de [atualização de tarefas az acr:][az-acr-task-update]

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Gatilho para rastrear dependências** - Para permitir que uma tarefa ACR determine e rastreie as dependências de uma imagem de contentor -- que incluem a sua imagem base - primeiro deve desencadear a tarefa de construir a imagem **pelo menos uma vez**. Por exemplo, acionar a tarefa manualmente utilizando o comando de execução de [tarefas az acr.][az-acr-task-run]

* **Etiqueta estável para imagem base** - Para desencadear uma tarefa na atualização de imagem base, a imagem base deve ter uma etiqueta *estável,* como `node:9-alpine`. Esta marcação é típica para uma imagem base que é atualizada com OS e patches de enquadramento para uma última versão estável. Se a imagem base for atualizada com uma nova etiqueta de versão, não desencadeia uma tarefa. Para obter mais informações sobre a marcação de imagem, consulte a orientação das [melhores práticas.](container-registry-image-tag-version.md) 

* **Outros gatilhos** de tarefas - Numa tarefa desencadeada por atualizações de imagem base, também pode ativar os gatilhos com base no compromisso do [código fonte](container-registry-tutorial-build-task.md) ou [no calendário](container-registry-tasks-scheduled.md). Uma atualização de imagem base também pode desencadear uma [tarefa em várias etapas.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes tutoriais para cenários para automatizar imagens de aplicação depois de atualizada uma imagem base:

* [Automatizaa imagem de contentor constrói-se quando uma imagem base é atualizada no mesmo registo](container-registry-tutorial-base-image-update.md)

* [Automatizaa imagem de contentor constrói-se quando uma imagem base é atualizada num registo diferente](container-registry-tutorial-base-image-update.md)


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
