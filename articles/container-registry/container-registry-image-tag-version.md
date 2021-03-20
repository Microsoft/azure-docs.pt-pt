---
title: Etiqueta de imagem melhores práticas
description: Melhores práticas para marcar e verr versão imagens do contentor Docker ao empurrar imagens para e tirar imagens de um registo de contentores Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75445741"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Recomendações para a marcação e versão das imagens dos contentores

Ao empurrar as imagens do contentor para um registo de contentores e, em seguida, implantá-las, precisa de uma estratégia para a marcação de imagens e a versão. Este artigo discute duas abordagens e onde cada um se encaixa durante o ciclo de vida do contentor:

* **Etiquetas estáveis** - Etiquetas que reutiliza, por exemplo, para indicar uma versão importante ou menor, como *mycontainerimage:1.0*.
* **Tags únicas** - Uma etiqueta diferente para cada imagem que empurra para um registo, como *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Etiquetas estáveis

**Recomendação**: Utilize etiquetas estáveis para manter **as imagens base** para as construções do seu recipiente. Evite implantações com etiquetas estáveis, porque essas tags continuam a receber atualizações e podem introduzir inconsistências em ambientes de produção.

*Etiquetas estáveis* significam que um desenvolvedor, ou um sistema de construção, pode continuar a puxar uma etiqueta específica, que continua a receber atualizações. Estável não significa que o conteúdo esteja congelado. Pelo contrário, estável implica que a imagem deve ser estável para a intenção dessa versão. Para se manter "estável", pode ser servido para aplicar patches de segurança ou atualizações de quadros.

### <a name="example"></a>Exemplo

Uma equipa-quadro envia a versão 1.0. Sabem que vão enviar atualizações, incluindo pequenas atualizações. Para suportar etiquetas estáveis para uma determinada versão maior e menor, eles têm dois conjuntos de etiquetas estáveis.

* `:1` – uma etiqueta estável para a versão principal. `1` representa a versão "mais recente" ou "mais recente" 1.* .
* `:1.0`- uma etiqueta estável para a versão 1.0, permitindo que um programador se ligue às atualizações de 1.0 e não seja lançado para 1.1 quando for lançado.

A equipa também usa a `:latest` etiqueta, que aponta para a mais recente etiqueta estável, independentemente da versão principal atual.

Quando estão disponíveis atualizações de imagem base, ou qualquer tipo de desbloqueio de manutenção da estrutura, as imagens com as etiquetas estáveis são atualizadas para a mais recente digestão que representa a versão estável mais atual dessa versão.

Neste caso, tanto as etiquetas principais como as pequenas estão continuamente a ser servidas. A partir de um cenário de imagem base, isto permite ao proprietário da imagem fornecer imagens servidas.

### <a name="delete-untagged-manifests"></a>Eliminar manifestos não anotados

Se uma imagem com uma etiqueta estável for atualizada, a imagem previamente marcada não é marcada, resultando numa imagem órfã. Os dados manifestos e únicos da camada da imagem anterior permanecem no registo. Para manter o tamanho do registo, pode eliminar periodicamente manifestos não com ensaios resultantes de atualizações de imagem estáveis. Por exemplo, [a purga automática manifesta-se](container-registry-auto-purge.md) mais velha do que uma duração especificada, ou define uma política de [retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="unique-tags"></a>Etiquetas únicas

**Recomendação**: Utilize etiquetas únicas para **implantações,** especialmente num ambiente que possa escalar em múltiplos nós. Provavelmente quer implantações deliberadas de uma versão consistente dos componentes. Se o seu recipiente recomeçar ou um orquestrador escalonar mais instâncias, os seus anfitriões não puxarão acidentalmente uma versão mais recente, inconsistente com os outros nós.

A marcação única significa simplesmente que cada imagem empurrada para um registo tem uma etiqueta única. As etiquetas não são reutilizadas. Existem vários padrões que pode seguir para gerar tags únicas, incluindo:

* **Carimbo de data** - Esta abordagem é bastante comum, uma vez que se pode dizer claramente quando a imagem foi construída. Mas, como correlacioná-lo de volta ao seu sistema de construção? Tens de encontrar a construção que foi concluída ao mesmo tempo? Em que fuso horário está? Todos os seus sistemas de construção estão calibrados para a UTC?
* **Git commit**  – Esta abordagem funciona até começar a suportar atualizações de imagem base. Se uma atualização de imagem base acontecer, o seu sistema de construção começa com o mesmo Git comprometendo-se com a construção anterior. No entanto, a imagem base tem um novo conteúdo. Em geral, um compromisso git fornece uma etiqueta semi-estável.
* **Ressalto -** Cada imagem de recipiente empurrada para um registo de contentores está associada a um manifesto, identificado por um haxixe SHA-256 único, ou digerido. Embora única, a digestão é longa, difícil de ler e não correlacionada com o seu ambiente de construção.
* **Build ID** - Esta opção pode ser a melhor, uma vez que é provavelmente incremental, e permite-lhe correlacionar-se de volta à construção específica para encontrar todos os artefactos e registos. No entanto, como uma leitura manifesta, pode ser difícil para um humano ler.

  Se a sua organização tem vários sistemas de construção, pré-fixação da etiqueta com o nome do sistema de construção é uma variação desta opção: `<build-system>-<build-id>` . Por exemplo, pode diferenciar as construções do sistema de construção jenkins da equipa da API e do sistema de construção de Azure Pipelines da equipa web.

### <a name="lock-deployed-image-tags"></a>Bloqueie etiquetas de imagem implantadas

Como uma boa prática, recomendamos que [bloqueie](container-registry-image-lock.md) qualquer etiqueta de imagem implantada, definindo o seu `write-enabled` atributo para `false` . Esta prática impede-o de remover inadvertidamente uma imagem do registo e possivelmente perturbar as suas implementações. Pode incluir o passo de bloqueio no seu oleoduto de libertação.

Bloquear uma imagem implantada ainda permite remover outras imagens não desocupadas do seu registo utilizando funcionalidades de Registo de Contentores Azure para manter o seu registo. Por exemplo, [purgar automaticamente](container-registry-auto-purge.md) manifestos não marcados ou imagens desbloqueadas com mais tempo do que uma duração especificada, ou definir uma [política de retenção](container-registry-retention-policy.md) para manifestos não marcados.

## <a name="next-steps"></a>Passos seguintes

Para uma discussão mais detalhada sobre os conceitos neste artigo, consulte o post de blog [Docker Tagging: As melhores práticas para marcar e verter imagens de estivadores.](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)

Para ajudar a maximizar o desempenho e a utilização rentável do seu registo de contentores Azure, consulte [as melhores práticas para o Registo de Contentores Azure](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

