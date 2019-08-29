---
title: Implantar uma imagem personalizada, vários contêineres ou um serviço de Azure App de imagem interno | Microsoft Docs
description: Como decidir entre a implantação personalizada do contêiner do Docker, o contêiner de múltiplos e uma estrutura de aplicativo interna para o serviço de aplicativo no Linux
keywords: serviço de aplicativo do Azure, aplicativo Web, Linux, OSS
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066780"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Imagem personalizada, de vários contêineres ou de plataforma interna?

O [serviço de aplicativo no Linux](app-service-linux-intro.md) oferece três caminhos diferentes para colocar o aplicativo publicado na Web:

- **Implantação de imagem personalizada**: "Encaixe" seu aplicativo em uma imagem do Docker que contém todos os seus arquivos e dependências em um pacote pronto para execução.
- **Implantação de vários contêineres**: "Encaixe" seu aplicativo em vários contêineres usando um arquivo de configuração Docker Compose.
- **Implantação de aplicativo com uma imagem de plataforma interna**: Nossas imagens de plataforma internas contêm tempos de execução e dependências comuns do aplicativo Web, como node e PHP. Use qualquer um dos [métodos de implantação de serviço Azure app](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implantar seu aplicativo no armazenamento de seu aplicativo Web e, em seguida, use uma imagem de plataforma interna para executá-lo.

## <a name="which-method-is-right-for-your-app"></a>Qual método é ideal para seu aplicativo? 

Os principais fatores a serem considerados são:

- **Disponibilidade do Docker em seu fluxo de trabalho de desenvolvimento**: O desenvolvimento de imagens personalizadas requer conhecimento básico do fluxo de trabalho de desenvolvimento do Docker. A implantação de uma imagem personalizada em um aplicativo Web requer a publicação de sua imagem personalizada em um host de repositório como o Hub do Docker. Se você estiver familiarizado com o Docker e puder adicionar tarefas do Docker ao seu fluxo de trabalho de compilação, ou se você já estiver publicando seu aplicativo como uma imagem do Docker, uma imagem personalizada é quase certamente a melhor opção.
- **Arquitetura em várias camadas**: Implante vários contêineres, como uma camada de aplicativo Web e uma camada de API para separar recursos usando vários contêineres. 
- **Desempenho do aplicativo**: Aumente o desempenho do seu aplicativo de vários contêineres usando uma camada de cache, como Redis. Selecione vários contêineres para conseguir isso.
- **Requisitos exclusivos de tempo de execução**: As imagens de plataforma internas são projetadas para atender às necessidades da maioria dos aplicativos Web, mas são limitadas em sua personalização. Seu aplicativo pode ter dependências exclusivas ou outros requisitos de tempo de execução que excedem o que as imagens internas são capazes.
- **Requisitos de compilação**: Com a [implantação contínua](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), você pode colocar seu aplicativo em funcionamento no Azure diretamente do código-fonte. Nenhum processo de compilação ou publicação externo é necessário. No entanto, há um limite para a personalização e a disponibilidade das ferramentas de compilação no mecanismo de implantação [kudu](https://github.com/projectkudu/kudu/wiki) . Seu aplicativo pode superar os recursos do kudu conforme ele cresce em suas dependências ou requisitos para a lógica de compilação personalizada.
- **Requisitos de leitura/gravação de disco**: Todos os aplicativos Web são alocados um volume de armazenamento para conteúdo da Web. Esse volume, apoiado pelo armazenamento do Azure, é montado `/home` no sistema de arquivos do aplicativo. Ao contrário dos arquivos no sistema de arquivos de contêiner, os arquivos no volume de conteúdo podem ser acessados em todas as instâncias de escala de um aplicativo, e as modificações persistirão entre as reinicializações do aplicativo. No entanto, a latência de disco do volume de conteúdo é maior e mais variável do que a latência do sistema de arquivos de contêiner local, e o acesso pode ser afetado por atualizações de plataforma, tempo de inatividade não planejado e problemas de conectividade de rede. Aplicativos que exigem acesso de alta disponibilidade a arquivos de conteúdo podem se beneficiar da implantação de imagem personalizada, que coloca arquivos no sistema de arquivos de imagem em vez de no volume de conteúdo.
- **Compilar o uso de recursos**: Quando um aplicativo é implantado da origem, os scripts de implantação executados pelo kudu usam os mesmos recursos de computação e armazenamento do plano do serviço de aplicativo como o aplicativo em execução. Implantações de aplicativo grande podem consumir mais recursos ou tempo do que o desejado. Em particular, muitos fluxos de trabalho de implantação geram atividade de disco pesada no volume de conteúdo do aplicativo, que não é otimizado para essa atividade. Uma imagem personalizada entrega todos os arquivos e dependências do seu aplicativo no Azure em um único pacote, sem a necessidade de transferências de arquivos adicionais ou ações de implantação.
- **Necessidade de iteração rápida**: Transformar um aplicativo requer etapas de compilação adicionais. Para que as alterações entrem em vigor, você deve enviar por push a nova imagem para um repositório com cada atualização. Essas atualizações são então obtidas para o ambiente do Azure. Se um dos contêineres internos atender às necessidades do seu aplicativo, a implantação a partir da origem poderá oferecer um fluxo de trabalho de desenvolvimento mais rápido.

## <a name="next-steps"></a>Passos seguintes

Contêiner personalizado:
* [Executar contêiner personalizado](quickstart-docker-go.md)

Vários contêineres:
* [Criar aplicativo de vários contêineres](quickstart-multi-container.md)

Os artigos a seguir o ajudarão a começar a usar o serviço de aplicativo no Linux com uma imagem de plataforma interna:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)