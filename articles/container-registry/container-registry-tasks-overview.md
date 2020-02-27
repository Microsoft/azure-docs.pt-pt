---
title: Descrição geral das Tarefas do ACR
description: Uma introdução às Tarefas ACR, um conjunto de funcionalidades no Registo de Contentores Azure que fornece construção de imagem de contentores, gestão e remendos seguros e automatizados na nuvem.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: cb5f0a71c31c26d679efd8a17b360dab2ad0862b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615957"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar construções e manutenção de contentores com Tarefas ACR

Os contentores proporcionam novos níveis de virtualização, isolando a aplicação e as dependências dos desenvolvedores das infraestruturas e requisitos operacionais. O que resta, no entanto, é a necessidade de abordar a forma como esta aplicação virtualização é gerida e remendada ao longo do ciclo de vida do contentor.

## <a name="what-is-acr-tasks"></a>O que são Tarefas ACR?

**A CR Tasks** é um conjunto de funcionalidades dentro do Registo de Contentores Azure. Fornece um edifício de imagem de contentores baseado em nuvem para [plataformas](#image-platforms) como Linux, Windows e ARM, e pode automatizar [os SISTEMAe patching de enquadramento](#automate-os-and-framework-patching) para os seus recipientes Docker. As Tarefas ACR não só alarga o seu ciclo de desenvolvimento "inner-loop" à nuvem com imagens de contentores a pedido, como também permite construções automatizadas desencadeadas por atualizações de código fonte, atualizações para a imagem base de um recipiente ou temporizadores. Por exemplo, com os gatilhos de atualização de imagem base, pode automatizar o seu SISTEMA e a estrutura de aplicação remendando o fluxo de trabalho, mantendo ambientes seguros enquanto adere aos princípios dos recipientes imutáveis.

## <a name="task-scenarios"></a>Cenários de tarefa

A ACR Tasks apoia vários cenários para construir e manter imagens de contentores e outros artefactos. Consulte as seguintes secções deste artigo para mais detalhes.

* **[Tarefa rápida](#quick-task)** - Construa e empurre uma única imagem de contentor para um registo de contentores a pedido, em Azure, sem precisar de uma instalação local do Motor Docker. Pense `docker build`, `docker push` na nuvem.
* **Tarefas ativadas automaticamente** - Ative um ou mais *gatilhos* para construir uma imagem:
  * **[Desencadear na atualização do código fonte](#trigger-task-on-source-code-update)** 
  * **[Desencadear na atualização da imagem base](#automate-os-and-framework-patching)** 
  * **[Gatilho em um horário](#schedule-a-task)** 
* **[Tarefa em várias etapas](#multi-step-tasks)** - Alargar a capacidade única de construção e impulso de imagem das Tarefas ACR com fluxos de trabalho multi-etapas e multi-contentores. 

Cada Tarefa ACR tem um contexto de [código fonte](#context-locations) associado - a localização de um conjunto de ficheiros de origem usados para construir uma imagem de recipiente ou outro artefacto. Os contextos de exemplo incluem um repositório Git ou um sistema de ficheiros local.

As tarefas também podem tirar partido das variáveis de [execução,](container-registry-tasks-reference-yaml.md#run-variables)para que possa reutilizar definições de tarefas e normalizar etiquetas para imagens e artefactos.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento do ciclo interno, o processo iterativo de escrita de código, construção e teste da sua aplicação antes de se comprometer com o controlo de fontes, é realmente o início da gestão do ciclo de vida dos contentores.

Antes de comprometer a sua primeira linha de código, a funcionalidade de [tarefa rápida](container-registry-tutorial-quick-task.md) da ACR Tasks pode proporcionar uma experiência de desenvolvimento integrada, descarregando as construções de imagem do seu recipiente para o Azure. Com tarefas rápidas, pode verificar as definições de construção automatizadas e apanhar potenciais problemas antes de comprometer o seu código.

Utilizando o formato familiar `docker build`, o comando de [construção az acr][az-acr-build] no Azure CLI assume um [contexto](#context-locations) (o conjunto de ficheiros para construir), envia-lhe Tarefas ACR e, por padrão, empurra a imagem construída para o seu registo após a conclusão.

Para uma introdução, consulte o quickstart para [construir e executar uma imagem](container-registry-quickstart-task-cli.md) de contentor no Registo de Contentores Azure.  

A ACR Tasks é projetado como um ciclo de vida de recipiente primitivo. Por exemplo, integre as Tarefas ACR na sua solução CI/CD. Ao executar [login az][az-login] com um diretor de [serviço,][az-login-service-principal]a sua solução CI/CD poderia então emitir comandos de [construção az acr][az-acr-build] para iniciar construções de imagem.

Saiba como utilizar tarefas rápidas no primeiro tutorial de Tarefas ACR, [construa imagens de contentores na nuvem com tarefas](container-registry-tutorial-quick-task.md)de registo de contentores Azure .

> [!TIP]
> Se quiser construir e empurrar uma imagem diretamente a partir do código fonte, sem um Dockerfile, o Registo de Contentores Azure fornece o comando de construção de [pacote az acr][az-acr-pack-build] (pré-visualização). Esta ferramenta constrói e empurra uma imagem do código fonte da aplicação utilizando [cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Tarefa de gatilho na atualização do código fonte

Desencadear uma construção de imagem de recipiente ou tarefa em várias etapas quando o código for comprometido, ou um pedido de puxão é feito ou atualizado, para um repositório git público ou privado em GitHub ou Azure DevOps. Por exemplo, configurar uma tarefa de construção com o comando Azure CLI [az acr task criar][az-acr-task-create] especificando um repositório Git e opcionalmente um ramo e Dockerfile. Quando a sua equipa atualiza o código no repositório, um webhook criado por ACR Tasks desencadeia uma construção da imagem do recipiente definida no repo. 

A Cr Tasks suporta os seguintes gatilhos quando define um repo Git como contexto da tarefa:

| Acionador | Ativado por predefinição |
| ------- | ------------------ |
| Consolidação | Sim |
| Pedido de puxar | Não |

Para configurar um gatilho de atualização de código fonte, é necessário fornecer à tarefa um token de acesso pessoal (PAT) para definir o webhook no repto de GitHub ou Azure DevOps público ou privado.

> [!NOTE]
> Atualmente, a ACR Tasks não suporta comprometer ou puxar gatilhos de pedido em repos gitHub Enterprise.

Aprenda a desencadear as construções no código fonte cometido no segundo tutorial de Tarefas ACR, a imagem de [contentor automatizar constrói com tarefas](container-registry-tutorial-build-task.md)de registo de contentores Azure .

## <a name="automate-os-and-framework-patching"></a>Automatizar os os e patching de quadro

O poder das Tarefas ACR para melhorar verdadeiramente o fluxo de trabalho de construção do seu recipiente provém da sua capacidade de detetar uma atualização para uma *imagem base*. Uma característica da maioria das imagens de contentores, uma imagem base é uma imagem dos pais na qual se baseiam uma ou mais imagens de aplicação. As imagens base normalmente contêm o sistema operativo, e às vezes os quadros de aplicação. 

Pode configurar uma tarefa ACR para rastrear uma dependência de uma imagem base quando constrói uma imagem de aplicação. Quando a imagem de base atualizada é empurrada para o seu registo, ou uma imagem base é atualizada num repo público como no Docker Hub, as Tarefas ACR podem automaticamente construir quaisquer imagens de aplicação com base no mesmo.
Com esta deteção e reconstrução automáticas, as Tarefas ACR poupam-lhe o tempo e o esforço normalmente necessários para rastrear e atualizar manualmente cada imagem de aplicação que refere a sua imagem base atualizada.

Saiba mais sobre [os gatilhos](container-registry-tasks-base-images.md) de atualização de imagem base para tarefas ACR. E aprender a desencadear uma construção de imagem quando uma imagem de base é empurrada para um registo de contentores no tutorial [automate imagem do contentor constrói quando uma imagem base é atualizada num registo de contentores Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Agendar uma tarefa

Agendar opcionalmente uma tarefa configurando um ou mais *gatilhos temporizadores* quando criar ou atualizar a tarefa. Agendar uma tarefa é útil para executar cargas de trabalho de contentores num horário definido, ou executar operações de manutenção ou testes em imagens empurradas regularmente para o seu registo. Para mais detalhes, consulte [Executar uma tarefa ACR num horário definido](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas com vários passos

As tarefas em várias etapas fornecem definição e execução de tarefas baseadas em etapas para construir, testar e remendar imagens de contentores na nuvem. Os passos de tarefa definidos num [ficheiro YAML](container-registry-tasks-reference-yaml.md) especificam operações individuais de construção e pressão para imagens de contentores ou outros artefactos. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

Por exemplo, pode criar uma tarefa em várias etapas que automatiza o seguinte:

1. Construir uma imagem de aplicação web
1. Executar o recipiente de aplicação web
1. Construir uma imagem de teste de aplicação web
1. Executar o recipiente de teste de aplicação web, que realiza testes contra o recipiente de aplicação em execução
1. Se os testes passarem, construa um pacote de arquivo de gráfico helm
1. Execute uma `helm upgrade` usando o novo pacote de arquivo de gráficos Helm

Tarefas em várias etapas permitem dividir o edifício, correr e testar uma imagem em passos mais comportáveis, com suporte inter-passo de dependência. Com tarefas em várias etapas em Tarefas ACR, você tem mais controlo granular sobre a construção de imagem, testes e SISTEMA e fluxos de trabalho de correção de quadros.

Conheça as tarefas em várias etapas em [executar tarefas de construção, teste e patch em tarefas ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Localizações de contexto

A tabela que se segue apresenta alguns exemplos de localizações de contexto suportados para tarefas ACR:

| Localização do contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de ficheiros local | Ficheiros dentro de um diretório no sistema de ficheiros local. | `/home/user/projects/myapp` |
| Ramo mestre GitHub | Ficheiros dentro do ramo principal (ou outro padrão) de um repositório GitHub público ou privado.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub filial | Ramo específico de um repo gitHub público ou privado.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta GitHub | Ficheiros dentro de uma subpasta num repo gitHub público ou privado. O exemplo mostra a combinação de uma especificação de ramo e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub compromete-se | Compromisso específico num repo gitHub público ou privado. O exemplo mostra a combinação de um hash (SHA) e especificação de subpasta. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Subpasta Azure DevOps | Ficheiros dentro de uma subpasta num repo azure público ou privado. O exemplo mostra a combinação da especificação do ramo e da subpasta. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Bola de alcatrão remota | Ficheiros num arquivo comprimido num webserver remoto. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Ao utilizar um repo git privado como contexto para uma tarefa, você precisa fornecer um token de acesso pessoal (PAT).

## <a name="image-platforms"></a>Plataformas de imagem

Por padrão, a ACR Tasks constrói imagens para o Sistema Linux OS e para a arquitetura amd64. Especifique a etiqueta `--platform` para construir imagens do Windows ou imagens Linux para outras arquiteturas. Especifique o S e opcionalmente uma arquitetura apoiada em formato OS/arquitetura (por exemplo, `--platform Linux/arm`). Para as arquiteturas ARM, especifique opcionalmente uma variante em formato OS/arquitetura/variante (por exemplo, `--platform Linux/arm64/v8`):

| SO | Arquitetura|
| --- | ------- | 
| Linux | amd64<br/>braço<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>Ver registos de tarefas

Cada execução de tarefa gera saída de registo que pode inspecionar para determinar se os passos de tarefa foram executados com sucesso. Se utilizar o comando de execução [az acr](/cli/azure/acr#az-acr-build), [az acr,](/cli/azure/acr#az-acr-run)ou [az acr](/cli/azure/acr/task#az-acr-task-run) task run para desencadear a tarefa, a saída de log para o executo de tarefas é transmitida para a consola e também armazenada para posterior recuperação. Quando uma tarefa é automaticamente desencadeada, por exemplo por um compromisso de código fonte ou por uma atualização de imagem base, os registos de tarefas são armazenados apenas. Consulte os registos para uma execução de tarefa no portal Azure ou utilize o comando de registos de [tarefas az acr.](/cli/azure/acr/task#az-acr-task-logs)

Por predefinição, os dados e registos de tarefas executados num registo são retidos durante 30 dias e, em seguida, purgados automaticamente. Se pretender arquivar os dados para uma execução de tarefa, ative o arquivamento utilizando o comando de [atualização de tarefas az acr.](/cli/azure/acr/task#az-acr-task-update-run) O exemplo seguinte permite o arquivamento da execução de *tarefas cf11* no registo *do registo .*

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Passos seguintes

Quando estiver pronto para automatizar a imagem do contentor e a manutenção na nuvem, confira a [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).

Instale opcionalmente a [extensão do Docker para código](https://code.visualstudio.com/docs/azure/docker) de estúdio visual e a extensão [da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com os registos de contentores Azure. Puxe e empurre as imagens para um registo de contentores Azure, ou execute tarefas ACR, todas dentro do Código do Estúdio Visual.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
