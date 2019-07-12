---
title: Imagens de etiqueta e a versão no Azure Container Registry
description: Melhores práticas para etiquetagem e controlo de versões Docker imagens de contentor
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800396"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Recomendações para etiquetagem e controlo de versões de imagens de contentor

Quando enviar implementar imagens de contentor para um registo de contentor e, em seguida, implementá-las, precisa de uma estratégia para a marcação de imagem e o controlo de versões. Este artigo aborda as duas abordagens e onde cada um se encaixa durante o ciclo de vida do contentor:

* **Etiquetas de estável** -marcas que reutilize, por exemplo, para indicar a principal ou menor, tais como a versão *mycontainerimage:1.0*.
* **Etiquetas exclusivas** -uma etiqueta diferente para cada imagem enviar para um registo, como *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Etiquetas estáveis

**Recomendação**: Utilizar etiquetas estáveis para manter **basear imagens** para as compilações do seu contentor. Evite implementações com etiquetas estáveis, porque essas marcas continuam a receber atualizações e podem introduzir inconsistências em ambientes de produção.

*Estável etiquetas* significa que um desenvolvedor ou um sistema de compilação, pode continuar solicitar uma etiqueta específica, o que continua a obter atualizações. Estável não significa que os conteúdos são interrompidos. Em vez disso, estável implica que a imagem deve ser estável para a intenção de que a versão. Para se manter "estável", podem ser atendido para aplicar patches de segurança ou atualizações do framework.

### <a name="example"></a>Exemplo

Uma equipe de estrutura é fornecido a versão 1.0. Eles sabem que irá enviar atualizações, incluindo atualizações secundárias. Para suportar etiquetas estáveis para uma versão principal e secundária determinada, eles têm dois conjuntos de etiquetas estáveis.

* `:1` – uma marca estável para a versão principal. `1` representa a versão 1. * "mais recente" ou "mais recente".
* `:1.0`-uma marca estável para a versão 1.0, permitindo que o desenvolvedor se vincular às atualizações de 1.0 e não ser efetuado o rollforward para 1.1 quando ele for lançado.

A equipe também usa o `:latest` marca, que aponta para a marca estável mais recente, não importa é que a versão principal atual.

Quando estiverem disponíveis atualizações de imagem base, ou qualquer tipo de versão do framework, imagens com as etiquetas estáveis de manutenção são atualizadas para o resumo do mais recente que representa a versão estável mais recente do que a versão.

Neste caso, etiquetas principais e secundárias são continuamente em manutenção. A partir de um cenário de imagem base, isso permite que o proprietário de imagem para fornecer imagens executadas.

## <a name="unique-tags"></a>Etiquetas exclusivas

**Recomendação**: Utilizar etiquetas exclusivas para o **implementações**, especialmente num ambiente que pode dimensionar em vários nós. Provavelmente desejará deliberadas implementações de uma versão consistente de componentes. Se os reinícios de contentor ou um orquestrador aumenta horizontalmente mais instâncias, os anfitriões não acidentalmente solicitar uma versão mais recente, inconsistente com os outros nós.

A identificação exclusiva significa simplesmente que cada imagem enviada para um registo tem uma etiqueta exclusiva. As etiquetas não são reutilizadas. Existem vários padrões que pode seguir para gerar etiquetas exclusivas, incluindo:

* **Carimbo de data / hora** -essa abordagem é muito comum, uma vez que pode informar claramente aos quando a imagem foi criada. Mas, como correlacioná-los novamente para o sistema de compilação? Tem que localizar a compilação que foi concluída, ao mesmo tempo? O fuso horário é no? Todos os seus sistemas de compilação são calibrados UTC?
* **Consolidação de Git** – essa abordagem funciona quando começa a dar suporte a atualizações de imagem base. Se ocorrer uma atualização da imagem base, o sistema de compilação, inicia com a consolidação de Git mesmo como a compilação anterior. No entanto, a imagem base tem conteúdo novo. Em geral, uma consolidação de Git fornece uma *semiestruturados*-estável marca.
* **Resumo de manifesto** -cada imagem de contentor enviada para um registo de contentor está associada com um manifesto, identificado por um hash SHA-256 exclusivo ou digest. Embora exclusivo, o resumo é muito difícil de ler e uncorrelated com o seu ambiente de compilação.
* **Criar ID** -esta opção pode ser melhor, pois é provável que incremental, e permite-lhe correlacionar novamente para a compilação específica para localizar todos os artefatos e registos. No entanto, como um resumo de manifesto, poderão ser difíceis de leitura.

  Se a sua organização tiver vários sistemas de compilação, prefixando a etiqueta com o nome do sistema de compilação é uma variação esta opção: `<build-system>-<build-id>`. Por exemplo, pode diferenciar compilações de sistema de compilação do Jenkins a equipe de API e do Azure Pipelines a equipa de web de sistema de compilação.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma discussão mais detalhada dos conceitos neste artigo, consulte a mensagem de blogue [marcação do Docker: Melhores práticas para etiquetagem e controlo de versões de imagens do docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Para ajudar a maximizar o desempenho e a utilização rentável do seu registo de contentor do Azure, veja [melhores práticas do Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

