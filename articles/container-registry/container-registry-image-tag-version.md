---
title: Etiqueta de imagem boas práticas
description: As melhores práticas para marcar e versonizar imagens de contentores docker ao empurrar imagens para e puxar imagens de um registo de contentores Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445741"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Recomendações para a marcação e versão de imagens de contentores

Ao empurrar imagens de contentores para um registo de contentores e, em seguida, implantá-las, precisa de uma estratégia para a marcação de imagem e versão. Este artigo discute duas abordagens e onde cada uma se encaixa durante o ciclo de vida do recipiente:

* **Etiquetas estáveis** - Tags que reutiliza, por exemplo, para indicar uma versão importante ou menor, como *mycontainerimage:1.0*.
* **Tags únicas** - Uma etiqueta diferente para cada imagem que empurra para um registo, como *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Etiquetas estáveis

**Recomendação**: Utilize etiquetas estáveis para manter **imagens base** para a construção do seu recipiente. Evite implantações com etiquetas estáveis, porque essas etiquetas continuam a receber atualizações e podem introduzir inconsistências em ambientes de produção.

*Etiquetas estáveis* significam que um desenvolvedor, ou um sistema de construção, pode continuar a puxar uma etiqueta específica, que continua a receber atualizações. Estável não significa que o conteúdo esteja congelado. Pelo contrário, estável implica que a imagem deve ser estável para a intenção dessa versão. Para se manter "estável", pode ser servido para aplicar patches de segurança ou atualizações de quadros.

### <a name="example"></a>Exemplo

Uma equipa-quadro envia a versão 1.0. Sabem que vão enviar atualizações, incluindo pequenas atualizações. Para suportar etiquetas estáveis para uma determinada versão maior e menor, eles têm dois conjuntos de etiquetas estáveis.

* `:1`– uma etiqueta estável para a versão principal. `1`representa a versão "mais recente" ou "mais recente" 1.*
* `:1.0`- uma etiqueta estável para a versão 1.0, permitindo que um desenvolvedor se ligue a atualizações de 1.0, e não seja lançada para a frente para 1.1 quando for lançada.

A equipa também `:latest` usa a etiqueta, que aponta para a última marca estável, independentemente da versão principal atual.

Quando as atualizações de imagem base estão disponíveis, ou qualquer tipo de libertação de manutenção do quadro, as imagens com as etiquetas estáveis são atualizadas para a mais recente digestão que representa a versão estável mais atual dessa versão.

Neste caso, as etiquetas principais e menores estão continuamente a ser servida. A partir de um cenário de imagem base, isto permite ao proprietário da imagem fornecer imagens serresidas.

### <a name="delete-untagged-manifests"></a>Eliminar manifestos não marcados

Se uma imagem com uma etiqueta estável for atualizada, a imagem previamente marcada é desmarcada, resultando numa imagem órfã. Os dados de camadas manifestos e únicos da imagem anterior permanecem no registo. Para manter o tamanho do registo, pode eliminar periodicamente manifestos não marcados resultantes de atualizações de imagem estáveis. Por exemplo, [a purga automática](container-registry-auto-purge.md) manifesta manifesta-se com mais de uma duração especificada ou estabelece uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="unique-tags"></a>Tags únicas

**Recomendação**: Utilize etiquetas únicas para **implantações,** especialmente num ambiente que possa escalar em vários nós. É provável que queira implantação deliberada de uma versão consistente de componentes. Se o seu contentor reiniciar ou um orquestrador escalonar mais instâncias, os seus anfitriões não puxarão acidentalmente uma versão mais recente, inconsistente com os outros nós.

A marcação única significa simplesmente que cada imagem empurrada para um registo tem uma etiqueta única. As etiquetas não são reutilizadas. Existem vários padrões que pode seguir para gerar tags únicas, incluindo:

* **Carimbo de data** - Esta abordagem é bastante comum, uma vez que se pode dizer claramente quando a imagem foi construída. Mas como correlacionar de volta ao seu sistema de construção? Tens de encontrar a construção que foi concluída ao mesmo tempo? Em que fuso horário está? Todos os seus sistemas de construção estão calibrados para utc?
* **Git compromete** - Esta abordagem funciona até começar a suportar atualizações de imagem base. Se uma atualização de imagem base acontecer, o seu sistema de construção começa com o mesmo compromisso Git que a construção anterior. No entanto, a imagem base tem novos conteúdos. Em geral, um compromisso git fornece uma etiqueta *semi-estável.*
* **Manifesta digestão** - Cada imagem do recipiente empurrada para um registo de contentores está associada a um manifesto, identificado por um hash SHA-256 único, ou digestão. Embora única, a digestão é longa, difícil de ler e não está relacionada com o seu ambiente de construção.
* **Build ID** - Esta opção pode ser melhor já que é provavelmente incremental, e permite-lhe correlacionar de volta à construção específica para encontrar todos os artefactos e registos. No entanto, como uma digestão manifesta, pode ser difícil para um humano ler.

  Se a sua organização tiver vários sistemas de construção, a `<build-system>-<build-id>`prefixação da etiqueta com o nome do sistema de construção é uma variação desta opção: . Por exemplo, você poderia diferenciar as construções do sistema de construção Jenkins da equipa API e o sistema de construção de Pipelines Azure da equipa web.

### <a name="lock-deployed-image-tags"></a>Bloqueio de etiquetas de imagem implantadas

Como uma boa prática, recomendamos que [bloqueie](container-registry-image-lock.md) qualquer etiqueta `write-enabled` de `false`imagem implantada, definindo o seu atributo para . Esta prática impede que remova inadvertidamente uma imagem do registo e possivelmente perturbe as suas implementações. Pode incluir o passo de bloqueio no seu oleoduto de libertação.

Bloquear uma imagem implementada permite-lhe ainda remover outras imagens não implantadas do seu registo utilizando funcionalidades do Registo de Contentores Azure para manter o seu registo. Por exemplo, [purgar automaticamente](container-registry-auto-purge.md) manifestos não marcados ou imagens desbloqueadas com uma duração especificada, ou definir uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="next-steps"></a>Passos seguintes

Para uma discussão mais detalhada sobre os conceitos neste artigo, consulte o post de blog [Docker Tagging: As melhores práticas para marcar e versonar imagens](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)de estivadores.

Para ajudar a maximizar o desempenho e a utilização rentável do seu registo de contentores Azure, consulte [as melhores práticas para](container-registry-best-practices.md)o Registo de Contentores Azure .

<!-- IMAGES -->


<!-- LINKS - Internal -->

