---
title: Implementar imagem personalizada, com vários contentores, incorporada ou imagem - serviço de aplicações do Azure | Documentos da Microsoft
description: Como decidir entre a implementação do contentor de Docker personalizada, com vários contentores e uma estrutura de aplicação incorporada para o serviço de aplicações no Linux
keywords: serviço de aplicações do Azure, aplicação web, linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 079bfae19a4960ef5ab95c9d48d5603423407a9e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772879"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Imagem personalizada, com vários contentores ou imagem de plataforma incorporada?

[Serviço de aplicações no Linux](app-service-linux-intro.md) oferece três caminhos diferentes para apresentar a sua aplicação publicada na web:

- **Implantação de imagem personalizada**: "Colocar" a aplicação numa imagem do Docker que contém todos os seus ficheiros e dependências num pacote de prontos para executar.
- **Implementação de vários contentor**: "Colocar" seu aplicativo em vários contentores com um Docker Compose ou um ficheiro de configuração do Kubernetes. Para obter mais informações, consulte [aplicação de vários contentor](#multi-container-apps-supportability).
- **Implementação de aplicações com uma imagem de plataforma interna**: As nossas imagens de plataforma interna contenham runtimes de aplicação web comuns e dependências, como Node e PHP. Utilizar qualquer um da [métodos de implantação do App Service do Azure](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implementar a sua aplicação para o armazenamento da sua aplicação web e, em seguida, utilizar uma imagem de plataforma interna para executá-lo.

## <a name="which-method-is-right-for-your-app"></a>Que método é adequado para a sua aplicação? 

Fatores principais a serem considerados são:

- **Disponibilidade do Docker no seu fluxo de trabalho de desenvolvimento**: Desenvolvimento de imagem personalizada requer um conhecimento básico do fluxo de trabalho de desenvolvimento do Docker. Implementação de uma imagem personalizada para uma aplicação web precisa de publicação da sua imagem personalizada para um anfitrião de repositório, como o Docker Hub. Se estiver familiarizado com o Docker e pode adicionar tarefas de Docker para seu fluxo de trabalho de compilação, ou se já está a publicar a aplicação como uma imagem do Docker, uma imagem personalizada é quase certamente a melhor opção.
- **Arquitetura com várias camada**: Implemente vários contentores como uma camada de aplicativo web e uma camada de API para separar recursos através de vários contentores. 
- **Desempenho do aplicativo**: Aumente o desempenho da sua aplicação de vários contentor usando uma camada de cache, como o Redis. Selecione vários contentores para atingir esse objetivo.
- **Requisitos do runtime exclusivo**: As imagens de plataforma interna foram concebidas para satisfazer as necessidades da maioria das aplicações web, mas estão limitadas na sua capacidade de personalização. A aplicação pode ter dependências exclusivas ou outros requisitos de tempo de execução que excedem o que são capazes de imagens incorporadas.
- **Criar requisitos**: Com o [implementação contínua](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), pode obter a aplicação em execução no Azure diretamente a partir do código-fonte. Nenhum processo de compilação ou de publicação externo é necessário. No entanto, existe um limite para a capacidade de personalização e a disponibilidade de ferramentas de compilação dentro do [Kudu](https://github.com/projectkudu/kudu/wiki) motor de implementação. A aplicação pode ser excedida capacidades do Kudu conforme ela cresce em suas dependências ou requisitos para a lógica de compilação personalizados.
- **Requisitos de leitura/escrita de disco**: Todas as aplicações web são alocadas um volume de armazenamento para o conteúdo da web. Este volume, apoiada pelo armazenamento do Azure, está montado para `/home` no sistema de ficheiros da aplicação. Ao contrário dos arquivos do sistema de ficheiros de contentor, ficheiros do volume de conteúdo estão acessíveis em todas as instâncias de dimensionamento de uma aplicação e modificações serão mantidos entre reinícios da aplicação. No entanto, a latência de disco do volume de conteúdo é maior e mais variável que a latência do sistema de ficheiros de contentor local e o acesso pode ser afetado por atualizações de plataforma, período de indisponibilidade não planeado e problemas de conectividade de rede. Aplicações que necessitam de acesso só de leitura pesado para ficheiros de conteúdo podem se beneficiar da implantação de imagem personalizada, que coloca os ficheiros no sistema de ficheiros de imagem a, em vez de no volume de conteúdo.
- **Criar a utilização de recursos**: Quando uma aplicação é implementada a partir da origem, os scripts de implantação executam através da utilização de Kudu os mesmo plano do serviço de aplicações de computação e armazenamento de recursos como o aplicativo em execução. Implementações de aplicações grande podem consumir mais recursos ou o tempo que o desejado. Em particular, muitos fluxos de trabalho de implantação geram atividade de disco intensivo sobre o volume de conteúdo de aplicação, que não está otimizada para essa atividade. Uma imagem personalizada fornece todos os ficheiros e dependências da sua aplicação para o Azure num único pacote sem ser necessário para as transferências de ficheiros adicionais ou ações de implementação.
- **Precisa de uma rápida iteração**: Dockerizing uma aplicação requer passos de compilação adicional. Para que as alterações entrem em vigor, tem de enviar sua nova imagem para um repositório com cada atualização. Estas atualizações são obtidas para o ambiente do Azure. Se um dos contentores incorporados atende às necessidades da sua aplicação, a implementação a partir de origem pode oferecer um fluxo de trabalho de desenvolvimento mais rápido.

## <a name="multi-container-apps-supportability"></a>Capacidade de suporte de aplicações de vários contentores

### <a name="supported-docker-compose-configuration-options"></a>Opções de configuração Docker Compose suportadas
- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

### <a name="unsupported-docker-compose-configuration-options"></a>Opções de configuração do Docker Compose não suportadas
- build (não é permitida)
- depends_on (ignorada)
- networks (ignorada)
- secrets (ignorada)
- portas que não seja a 80 e 8080 (ignorados)

> [!NOTE]
> Quaisquer outras opções não explicitamente referidas também são ignoradas na Pré-visualização Pública.

### <a name="supported-kubernetes-configuration-options"></a>Opções de configuração suportadas do Kubernetes
- args
- command
- containers
- image
- nome
- ports
- spec

> [!NOTE]
>Quaisquer outras opções do Kubernetes não explicitamente referidas não são suportadas na Pré-visualização Pública.
>
