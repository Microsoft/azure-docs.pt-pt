---
title: Opções de implantação para contentores Linux
description: Decida entre a implantação personalizada de contentores Docker, multi-contentores e um quadro de aplicação incorporado para o Serviço de Aplicações em Linux.
keywords: serviço de aplicativos azure, web app, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687503"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Imagem personalizada, multi-contentor ou imagem de plataforma incorporada?

[O Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece três caminhos diferentes para publicar a sua aplicação na web:

- **Implementação personalizada da imagem**: "Dockerize" a sua aplicação numa imagem do Docker que contenha todos os seus ficheiros e dependências num pacote pronto a ser executado.
- **Implantação de vários contentores**: "Dockerize" a sua aplicação em vários contentores utilizando um ficheiro de configuração Docker Compose.
- **Implementação de aplicativos com uma imagem**de plataforma incorporada : As nossas imagens de plataforma incorporada contêm tempos e dependências comuns da web, tais como Nó e PHP. Utilize qualquer um dos métodos de implementação do Serviço de [Aplicações Azure](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para implementar a sua aplicação no armazenamento da sua aplicação web e, em seguida, use uma imagem de plataforma incorporada para executá-la.

## <a name="which-method-is-right-for-your-app"></a>Qual é o método certo para a sua aplicação? 

Os principais fatores a ter em conta são:

- **Disponibilidade do Docker no seu fluxo de trabalho**de desenvolvimento : O desenvolvimento personalizado da imagem requer conhecimentos básicos sobre o fluxo de trabalho de desenvolvimento do Docker. A implementação de uma imagem personalizada para uma aplicação web requer a publicação da sua imagem personalizada a um anfitrião de repositóriocomo o Docker Hub. Se está familiarizado com o Docker e pode adicionar tarefas docker ao seu fluxo de trabalho de construção, ou se já está publicando a sua aplicação como uma imagem do Docker, uma imagem personalizada é quase certamente a melhor escolha.
- **Arquitetura multi-camadas**: Implante vários recipientes, tais como uma camada de aplicação web e uma camada de API para separar as capacidades utilizando multi-recipientes. 
- Desempenho da **aplicação**: Aumente o desempenho da sua aplicação multi-contentor usando uma camada de cache como redis. Selecione multi-recipiente para o conseguir.
- **Requisitos únicos**de tempo de execução : As imagens da plataforma incorporada são projetadas para atender às necessidades da maioria das aplicações web, mas são limitadas na sua personalizabilidade. A sua aplicação pode ter dependências únicas ou outros requisitos de tempo de execução que excedam o que as imagens incorporadas são capazes.
- **Requisitos de construção**: Com [a implementação contínua,](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)pode obter a sua aplicação em funcionamento diretamente a partir do código fonte. Não é necessário nenhum processo externo de construção ou publicação. No entanto, existe um limite para a personalizabilidade e disponibilidade de ferramentas de construção dentro do motor de implantação [Kudu.](https://github.com/projectkudu/kudu/wiki) A sua aplicação pode superar as capacidades da Kudu à medida que cresce nas suas dependências ou requisitos para a lógica de construção personalizada.
- **Requisitos de leitura/escrita**de disco : Todas as aplicações web são atribuídas a um volume de armazenamento para conteúdo web. Este volume, apoiado pelo Azure Storage, `/home` está montado no sistema de ficheiros da aplicação. Ao contrário dos ficheiros no sistema de ficheiros de contentores, os ficheiros no volume de conteúdo são acessíveis em todas as instâncias de escala de uma aplicação, e as modificações persistirão em todos os reinícios da aplicação. No entanto, a latência do disco do volume de conteúdos é maior e mais variável do que a latência do sistema de ficheiros de contentores local, e o acesso pode ser impactado por atualizações da plataforma, tempo de inatividade não planeado e problemas de conectividade da rede. As aplicações que requerem acesso apenas a ficheiros de conteúdo podem beneficiar da implementação personalizada de imagens, que coloca ficheiros no sistema de ficheiros de imagem em vez de no volume de conteúdo.
- **Construir o uso**de recursos : Quando uma aplicação é implantada a partir da fonte, os scripts de implementação executados pela Kudu usam os mesmos recursos de cálculo e armazenamento do Plano de Serviço de Aplicações como a aplicação em execução. Grandes implementações de aplicações podem consumir mais recursos ou tempo do que o desejado. Em particular, muitos fluxos de trabalho de implementação geram atividade de disco pesado no volume de conteúdo da aplicação, o que não é otimizado para tal atividade. Uma imagem personalizada entrega todos os ficheiros e dependências da sua aplicação ao Azure num único pacote sem necessidade de transferências de ficheiros adicionais ou ações de implementação.
- **Necessidade de uma iteração rápida**: A dockerizing de uma app requer passos adicionais de construção. Para que as alterações entrem em vigor, tem de empurrar a sua nova imagem para um repositório com cada atualização. Estas atualizações são então puxadas para o ambiente Azure. Se um dos recipientes incorporados satisfaz as necessidades da sua aplicação, a implantação a partir da fonte pode oferecer um fluxo de trabalho de desenvolvimento mais rápido.

## <a name="next-steps"></a>Passos seguintes

Recipiente personalizado:
* [Executar um contentor personalizado](quickstart-docker-go.md)

Multi-contentor:
* [Criar aplicativo multi-contentores](quickstart-multi-container.md)

Os seguintes artigos começam com o App Service no Linux com uma imagem de plataforma incorporada:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Nó.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Pitão](quickstart-python.md)
* [Ruby](quickstart-ruby.md)