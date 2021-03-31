---
title: Descrição geral das Tarefas do ACR
description: Uma introdução ao ACR Tasks, um conjunto de funcionalidades no Registo de Contentores Azure que proporciona uma construção, gestão e remendos de imagem de contentores seguros e automatizados na nuvem.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 1cf9faf934cebfb5abe0d2e1b26ffd7da2d6c549
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606791"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizar a imagem do contentor constrói e manutenção com tarefas ACR

Os contentores fornecem novos níveis de virtualização, isolando a aplicação e as dependências dos desenvolvedores das infraestruturas e dos requisitos operacionais. O que resta, no entanto, é a necessidade de abordar a forma como esta aplicação é gerida e remendada sobre o ciclo de vida do contentor.

## <a name="what-is-acr-tasks"></a>O que é ACR Tasks?

**ACR Tasks** é um conjunto de funcionalidades dentro do Registo de Contentores Azure. Fornece um edifício de imagem de contentores baseado em nuvem para [plataformas](#image-platforms) como Linux, Windows e ARM, e pode automatizar [o sistema operativo e a correção de estruturas](#automate-os-and-framework-patching) para os seus contentores Docker. As tarefas ACR não só alargam o seu ciclo de desenvolvimento "inner-loop" à nuvem com a imagem de contentor a pedido, como também permitem construções automatizadas desencadeadas por atualizações de códigos fontes, atualizações para a imagem base de um recipiente, ou temporizadores. Por exemplo, com os gatilhos de atualização de imagem base, pode automatizar o fluxo de trabalho de correção de sistemas de sistema operativo e de aplicação, mantendo ambientes seguros, cumprindo os princípios dos recipientes imutáveis.

## <a name="task-scenarios"></a>Cenários de tarefa

A ACR Tasks suporta vários cenários para construir e manter imagens de contentores e outros artefactos. Consulte as seguintes secções neste artigo para mais detalhes.

* **[Tarefa rápida](#quick-task)** - Construa e empurre uma única imagem de contentor para um registo de contentores a pedido, em Azure, sem necessidade de uma instalação local do Motor Docker. Pense, `docker build` `docker push` na nuvem.
* **Tarefas ativadas automaticamente** - Ative um ou mais *gatilhos* para construir uma imagem:
  * **[Gatilho na atualização do código fonte](#trigger-task-on-source-code-update)** 
  * **[Gatilho na atualização da imagem base](#automate-os-and-framework-patching)** 
  * **[Gatilho em um horário](#schedule-a-task)** 
* **[Tarefa em várias etapas](#multi-step-tasks)** - Alargar a capacidade única de construção e pressão de imagens de tarefas ACR com fluxos de trabalho multi-passo, baseados em vários contentores. 

Cada Tarefa ACR tem um [contexto de código fonte](#context-locations) associado - a localização de um conjunto de ficheiros de origem usados para construir uma imagem de contentor ou outro artefacto. Os contextos de exemplo incluem um repositório git ou um sistema de ficheiros local.

As tarefas também podem tirar partido de variáveis de [execução,](container-registry-tasks-reference-yaml.md#run-variables)para que possa reutilizar definições de tarefas e normalizar tags para imagens e artefactos.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento do ciclo interno, o processo iterativo de escrever código, construção e teste da sua aplicação antes de se comprometer com o controlo de origem, é realmente o início da gestão do ciclo de vida do contentor.

Antes de comprometer a sua primeira linha de código, a funcionalidade [de tarefa rápida](container-registry-tutorial-quick-task.md) da ACR Tasks pode proporcionar uma experiência de desenvolvimento integrada, descarregando a sua imagem de contentor para Azure. Com tarefas rápidas, pode verificar as definições de construção automatizada e apanhar potenciais problemas antes de comprometer o seu código.

Utilizando o `docker build` formato familiar, o comando [de construção az acr][az-acr-build] no CLI Azure tem um [contexto](#context-locations) (o conjunto de ficheiros a construir), envia-o para tarefas ACR e, por padrão, empurra a imagem construída para o seu registo após a conclusão.

Para uma introdução, consulte o quickstart para [construir e executar uma imagem de recipiente](container-registry-quickstart-task-cli.md) no Registo do Contentor Azure.  

As tarefas ACR são concebidas como um ciclo de vida de contentores primitivos. Por exemplo, integrar as Tarefas ACR na sua solução CI/CD. Ao executar [o login az][az-login] com um [principal de serviço,][az-login-service-principal]a sua solução ci/CD poderia então emitir comandos [de construção az acr][az-acr-build] para iniciar construções de imagem.

Aprenda a utilizar tarefas rápidas no primeiro tutorial de tarefas ACR, [Construa imagens de contentores na nuvem com tarefas de registo de contentores Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se pretender construir e empurrar uma imagem diretamente do código fonte, sem um Dockerfile, o Registo de Contentores Azure fornece o comando de construção de [pacote az acr][az-acr-pack-build] (pré-visualização). Esta ferramenta constrói e empurra uma imagem a partir do código fonte de aplicação utilizando [buildpacks nativos da nuvem](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Tarefa de gatilho na atualização do código fonte

Desencadeie uma construção de imagem de contentor ou uma tarefa em várias etapas quando o código é comprometido, ou um pedido de puxar é feito ou atualizado, para um repositório de GitHub ou Azure DevOps público ou privado. Por exemplo, configurar uma tarefa de construção com o comando Azure CLI [acr tarefa criar][az-acr-task-create] especificando um repositório Git e opcionalmente um ramo e Dockerfile. Quando a sua equipa atualiza o código no repositório, um webhook criado por ACR ativa uma construção da imagem do recipiente definida no repo. 

A ACR Tasks suporta os seguintes gatilhos quando define um git repo como o contexto da tarefa:

| Acionador | Ativado por predefinição |
| ------- | ------------------ |
| Consolidação | Yes |
| Pedido Pull | No |

Para configurar um gatilho de atualização de código fonte, é necessário fornecer à tarefa um token de acesso pessoal (PAT) para definir o webhook no repo do GitHub ou do Azure DevOps.

> [!NOTE]
> Atualmente, a ACR Tasks não suporta o commit ou pull request triggers nos repos da GitHub Enterprise.

Aprenda a desencadear as construções do código fonte comprometendo-se no segundo tutorial de tarefas ACR, a imagem do [recipiente de automatização constrói com as Tarefas de Registo de Contentores Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar o SISTEMA e a correção de quadros

O poder das Tarefas ACR para melhorar verdadeiramente o fluxo de trabalho de construção de contentores provém da sua capacidade de detetar uma atualização para uma *imagem base*. Uma característica da maioria das imagens do contentor, uma imagem base é uma imagem dos pais na qual uma ou mais imagens de aplicação são baseadas. As imagens base normalmente contêm o sistema operativo, e às vezes as estruturas de aplicação. 

Pode configurar uma tarefa ACR para rastrear uma dependência de uma imagem base quando constrói uma imagem de aplicação. Quando a imagem base atualizada é empurrada para o seu registo, ou uma imagem base é atualizada num repo público, como em Docker Hub, as Tarefas ACR podem automaticamente construir quaisquer imagens de aplicação com base nele.
Com esta deteção e reconstrução automática, a ACR Tasks poupa-lhe o tempo e esforço normalmente necessários para rastrear e atualizar manualmente cada imagem de aplicação que faz referência à sua imagem base atualizada.

Saiba mais sobre [os gatilhos de atualização de imagem base](container-registry-tasks-base-images.md) para tarefas ACR. E aprenda a desencadear uma construção de imagem quando uma imagem base é empurrada para um registo de contentores na imagem do recipiente de automatização tutorial [constrói-se quando uma imagem base é atualizada num registo de contentores Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Agendar uma tarefa

Agendar opcionalmente uma tarefa configurando um ou mais *gatilhos temporeiros* quando criar ou atualizar a tarefa. Agendar uma tarefa é útil para executar cargas de trabalho de contentores num horário definido, ou executar operações de manutenção ou testes em imagens empurradas regularmente para o seu registo. Para mais detalhes, consulte [executar uma tarefa ACR num horário definido](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas com vários passos

As tarefas em várias etapas fornecem definição de tarefa baseada em passos e execução para a construção, teste e remendação de imagens de contentores na nuvem. Os passos de tarefa definidos num [ficheiro YAML](container-registry-tasks-reference-yaml.md) especificam operações individuais de construção e pressão para imagens de contentores ou outros artefactos. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

Por exemplo, pode criar uma tarefa em várias etapas que automatiza o seguinte:

1. Construa uma imagem de aplicação web
1. Executar o recipiente de aplicação web
1. Construa uma imagem de teste de aplicação web
1. Execute o recipiente de teste de aplicação web, que realiza testes contra o recipiente de aplicação em execução
1. Se os testes passarem, construa um pacote de arquivo helm gráfico
1. Execute um `helm upgrade` uso do novo pacote de arquivo de gráficos Helm

As tarefas em várias etapas permitem dividir o edifício, correr e testar uma imagem em passos mais composíveis, com suporte de dependência inter-passo. Com tarefas em várias etapas em Tarefas ACR, você tem mais controlo granular sobre construção de imagem, testes e fluxos de trabalho de correção de estruturas.

Saiba mais sobre tarefas em várias etapas em [Executar tarefas de construção, teste e patch em tarefas ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Localizações de contexto

A tabela que se segue mostra exemplos de locais de contexto suportados para tarefas ACR:

| Localização de contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de ficheiros local | Ficheiros dentro de um diretório no sistema de ficheiros local. | `/home/user/projects/myapp` |
| Ramo principal do GitHub | Ficheiros dentro do ramo principal (ou outro predefinido) de um repositório gitHub público ou privado.  | `https://github.com/gituser/myapp-repo.git` |
| Filial do GitHub | Ramo específico de um repo GitHub público ou privado.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sub-dobrador GitHub | Ficheiros dentro de uma sub-página num repo GitHub público ou privado. O exemplo mostra a combinação de uma especificação de ramo e sub-dobragem. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub compromete-se | Compromisso específico num repo GitHub público ou privado. O exemplo mostra a combinação de um haxixe comprometido (SHA) e especificação de sub-dobra. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Subpagadora Azure DevOps | Ficheiros dentro de uma sub-página num repo Azure público ou privado. O exemplo mostra a combinação de ramificação e especificação de sub-dobragem. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball remoto | Ficheiros num arquivo comprimido num webserver remoto. | `http://remoteserver/myapp.tar.gz` |
| Artefacto no registo de contentores | [Arquivos de artefactos OCI](container-registry-oci-artifacts.md) num repositório de registo de contentores. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Ao utilizar um git repo privado como um contexto para uma tarefa, você precisa fornecer um token de acesso pessoal (PAT).

## <a name="image-platforms"></a>Plataformas de imagem

Por padrão, a ACR Tasks constrói imagens para o Linux OS e para a arquitetura amd64. Especifique a `--platform` etiqueta para construir imagens do Windows ou imagens Linux para outras arquiteturas. Especifique o SISTEMA e opcionalmente uma arquitetura suportada em formato OS/arquitetura (por exemplo, `--platform Linux/arm` ). Para as arquiteturas ARM, especificar opcionalmente uma variante no formato OS/arquitetura/variante (por exemplo, `--platform Linux/arm64/v8` ):

| SO | Arquitetura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Ver resultado das tarefas

Cada execução de tarefa gera saída de log que pode inspecionar para determinar se os passos de tarefa foram executados com sucesso. Quando ativa uma tarefa manualmente, a saída de registo para a execução da tarefa é transmitida para a consola e também armazenada para posterior recuperação. Quando uma tarefa é automaticamente ativada, por exemplo, por um código fonte ou por uma atualização de imagem base, os registos de tarefas são armazenados apenas. Ver os registos de execução no portal Azure ou utilizar o comando [de registos de tarefas az acr.](/cli/azure/acr/task#az-acr-task-logs)

Ver mais sobre [visualização e gestão de registos de tarefas](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Passos seguintes

Quando estiver pronto para automatizar a imagem do contentor e a manutenção na nuvem, confira a [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).

Instale opcionalmente a [extensão do Docker para o Código do Estúdio Visual](https://code.visualstudio.com/docs/azure/docker) e a extensão da Conta [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com os registos dos seus contentores Azure. Puxe e empurre as imagens para um registo de contentores Azure, ou execute tarefas ACR, tudo dentro do Código do Estúdio Visual.

<!-- LINKS - External -->
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
