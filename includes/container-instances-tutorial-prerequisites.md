---
title: incluir ficheiro
description: incluir ficheiro
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 10fb9e8169b7f4159ccbf4a0ff36021f6033f811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75552427"
---
Tem de cumprir os requisitos seguintes para concluir este tutorial:

**CLI do Azure**: Tem de ter a CLI do Azure versão 2.0.29 ou posterior instalada no computador local. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

**Docker**: Este tutorial pressupõe conhecimentos básicos dos principais conceitos do Docker, como contentores, imagens de contentor e comandos básicos do `docker`. Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker][docker-get-started].

Para completar este tutorial, precisa do Docker instalado localmente. O Docker oferece pacotes que configuram o ambiente do Docker no [macOS][docker-mac], no [Windows][docker-windows] e no [Linux][docker-linux].

> [!IMPORTANT]
> Uma vez que a shell do Azure Cloud não inclui o daemon do Docker, *tem* de instalar a CLI do Azure e o Motor do Docker no seu *computador local* para concluir este tutorial. Não pode utilizar o Azure Cloud Shell para este tutorial.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
