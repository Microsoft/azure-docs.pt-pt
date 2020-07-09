---
title: Opções de implantação para contentores Linux
description: Decida entre a colocação personalizada do contentor Docker, multi-contentor e um quadro de aplicação incorporado para o Serviço de Aplicações em Linux.
keywords: serviço de aplicativos azure, web app, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687503"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Imagem personalizada, multi-contentor ou imagem de plataforma incorporada?

[O Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece três caminhos diferentes para que a sua aplicação é publicada na web:

- **Implementação de imagem personalizada**: "Dockerize" a sua aplicação numa imagem do Docker que contém todos os seus ficheiros e dependências num pacote pronto a executar.
- **Implantação de vários contentores**: "Dockerize" a sua aplicação em vários contentores utilizando um ficheiro de configuração Docker Compose.
- **Implementação de aplicativos com uma imagem de plataforma incorporada**: As nossas imagens de plataforma incorporadas contêm tempos e dependências comuns de aplicações web, como o Nó e o PHP. Utilize qualquer um dos métodos de implementação do [Azure App Service](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implementar a sua aplicação no armazenamento da sua aplicação web e, em seguida, use uma imagem de plataforma incorporada para executá-la.

## <a name="which-method-is-right-for-your-app"></a>Que método é adequado para a sua aplicação? 

Os principais fatores a ter em conta são:

- **Disponibilidade de Docker no seu fluxo de trabalho de desenvolvimento:** O desenvolvimento de imagem personalizada requer conhecimento básico do fluxo de trabalho de desenvolvimento do Docker. A implementação de uma imagem personalizada para uma aplicação web requer a publicação da sua imagem personalizada a um anfitrião de repositório como o Docker Hub. Se você está familiarizado com Docker e pode adicionar tarefas Docker ao seu fluxo de trabalho de construção, ou se você já está publicando a sua app como uma imagem Docker, uma imagem personalizada é quase certamente a melhor escolha.
- **Arquitetura em várias camadas**: Desdobre vários recipientes, tais como uma camada de aplicação web e uma camada de API para separar as capacidades utilizando vários recipientes. 
- **Desempenho da aplicação**: Aumente o desempenho da sua aplicação multi-contentores utilizando uma camada de cache como o Redis. Selecione vários recipientes para o conseguir.
- **Requisitos exclusivos de tempo**de execução : As imagens da plataforma incorporadas são projetadas para atender às necessidades da maioria das aplicações web, mas são limitadas na sua personalização. A sua aplicação pode ter dependências únicas ou outros requisitos de tempo de execução que excedam o que as imagens incorporadas são capazes de fazer.
- **Requisitos de construção**: Com [a implementação contínua,](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)pode colocar a sua aplicação em funcionamento no Azure diretamente do código fonte. Não é necessário qualquer processo externo de construção ou publicação. No entanto, existe um limite para a personalização e disponibilidade de ferramentas de construção dentro do motor de implantação [Kudu.](https://github.com/projectkudu/kudu/wiki) A sua aplicação pode superar as capacidades da Kudu à medida que cresce nas suas dependências ou requisitos para a lógica de construção personalizada.
- **Requisitos de leitura/escrita em disco**: Todas as aplicações web são atribuídas um volume de armazenamento para conteúdo web. Este volume, apoiado pelo Azure Storage, está montado `/home` no sistema de ficheiros da aplicação. Ao contrário dos ficheiros do sistema de ficheiros de contentores, os ficheiros no volume de conteúdo são acessíveis em todas as instâncias de escala de uma aplicação, e as modificações persistirão em todo o recomeço da aplicação. No entanto, a latência do disco do volume de conteúdo é maior e mais variável do que a latência do sistema de ficheiros de contentores locais, e o acesso pode ser impactado por atualizações da plataforma, tempo de inatividade não planeado e problemas de conectividade de rede. As aplicações que requerem acesso pesado apenas a ficheiros de conteúdo podem beneficiar da implementação personalizada de imagens, que coloca ficheiros no sistema de ficheiros de imagem em vez de no volume de conteúdo.
- **Construa a utilização**de recursos : Quando uma aplicação é implementada a partir de fonte, os scripts de implementação executados pela Kudu usam os mesmos recursos de computação e armazenamento do Plano de Aplicação que a aplicação de execução. Grandes implementações de aplicações podem consumir mais recursos ou tempo do que o desejado. Em particular, muitos fluxos de trabalho de implementação geram atividade de disco pesado no volume de conteúdo da aplicação, que não é otimizado para tal atividade. Uma imagem personalizada fornece todos os ficheiros e dependências da sua aplicação ao Azure num único pacote sem necessidade de transferências adicionais de ficheiros ou ações de implementação.
- **Necessidade de iteração rápida**: Dockerizing uma aplicação requer passos adicionais de construção. Para que as alterações produzam efeitos, deve empurrar a sua nova imagem para um repositório a cada atualização. Estas atualizações são então puxadas para o ambiente Azure. Se um dos recipientes incorporados satisfaça as necessidades da sua aplicação, a implantação a partir da fonte pode oferecer um fluxo de trabalho de desenvolvimento mais rápido.

## <a name="next-steps"></a>Próximos passos

Recipiente personalizado:
* [Executar um contentor personalizado](quickstart-docker-go.md)

Multi-contentor:
* [Criar aplicação com vários contentores](quickstart-multi-container.md)

Os seguintes artigos iniciam-no com o Serviço de Aplicações em Linux com uma imagem de plataforma incorporada:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)