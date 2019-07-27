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
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67184817"
---
Tem de cumprir os requisitos seguintes para concluir este tutorial:

**CLI do Azure**: Você deve ter CLI do Azure versão 2.0.29 ou posterior instalada no computador local. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

**Docker**: Este tutorial pressupõe uma compreensão básica dos principais conceitos do Docker, como contêineres, imagens de `docker` contêiner e comandos básicos. Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker][docker-get-started].

**Mecanismo**do Docker: Para concluir este tutorial, você precisa do mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS][docker-mac], no [Windows][docker-windows]e no [Linux][docker-linux].

> [!IMPORTANT]
> Uma vez que a shell do Azure Cloud não inclui o daemon do Docker, *tem* de instalar a CLI do Azure e o Motor do Docker no seu *computador local* para concluir este tutorial. Não pode utilizar o Azure Cloud Shell para este tutorial.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
