---
title: Implantação offline
description: A implementação offline permite-lhe extrair imagens de um registo de contentores privados em vez de retirar do Registo de Contentores da Microsoft.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 575903654a165bef0d09ac6abf0793af3f6784e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940609"
---
# <a name="offline-deployment-overview"></a>Visão geral da implementação offline

Tipicamente, as imagens de contentores utilizadas na criação do controlador de dados Azure Arc, as instâncias geridas pelo SQL e os grupos de servidores de hiperescala PostgreSQL são diretamente retiradas do Registo de Contentores da Microsoft (MCR). Em alguns casos, o ambiente para o quais está a implementar não terá conectividade com o Registo de Contentores da Microsoft.  Para situações como esta, pode retirar as imagens do contentor utilizando um computador, que _tem_ acesso ao Registo de Contentores da Microsoft e, em seguida, marcá-las e empurrá-las para um registo privado de contentores que _seja_ conecível do ambiente em que pretende implantar serviços de dados habilitados a Azure Arc.

Como são fornecidas atualizações mensais para serviços de dados habilitados a Azure Arc e há um grande número de imagens de contentores, o melhor é realizar este processo de puxar, marcar e empurrar as imagens do contentor para um registo de contentores privados usando um script.  O script pode ser automatizado ou executado manualmente.

Um [guião de amostra](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) pode ser encontrado no repositório Azure Arc GitHub.

> [!NOTE]
> Este script requer a instalação de python e o [Docker CLI](https://docs.docker.com/install/).

O script irá interativamente solicitar as seguintes informações.  Em alternativa, se pretender que o script seja executado sem indicações interativas, pode definir as variáveis ambientais correspondentes antes de executar o script.

|Prompt|Variável ambiente|Notas|
|---|---|---|
|Fornecer registo de contentores de origem - prima ENTER para utilização `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Normalmente, você retiraria as imagens do Registo de Contentores da Microsoft, mas se estiver a participar numa pré-visualização privada com um registo diferente, pode utilizar as informações que lhe são fornecidas como parte do programa de pré-visualização.|
|Fornecer repositório de registo de contentores de origem - prima ENTER para utilizar `arcdata` :|SOURCE_DOCKER_REPOSITORY|Se estiver a retirar do Registo de Contentores da Microsoft, o repositório será `arcdata` .|
|Fornecer o nome de utilizador para o registo do contentor de origem - prima ENTER para não utilizar nenhum:|SOURCE_DOCKER_USERNAME|Só forneça um valor se estiver a retirar imagens de um contentor de uma fonte que requer login.  O Registo de Contentores da Microsoft não requer um login.|
|Fornecer senha para o registo do contentor de origem - prima ENTER para não utilizar nenhum:|SOURCE_DOCKER_PASSWORD|Só forneça um valor se estiver a retirar imagens de um contentor de uma fonte que requer login.  O Registo de Contentores da Microsoft não requer um login. Esta é uma senha mascarada.  Não verá a palavra-passe se a escrever ou colar.|
|Fornecer etiqueta de imagem de recipiente para as imagens na fonte - prima ENTER para utilizar `<current monthly release tag>` '|SOURCE_DOCKER_TAG|O nome da etiqueta predefinida será atualizado mensalmente para refletir o mês e o ano da versão atual no Registo de Contentores da Microsoft.|
|Fornecer o nome dns do registo do contentor-alvo ou endereço IP:|TARGET_DOCKER_REGISTRY|O nome DNS do registo-alvo ou endereço IP.  Este é o registo para o qual as imagens serão empurradas _para_.|
|Fornecer o repositório de registo de contentores-alvo:|TARGET_DOCKER_REPOSITORY|O repositório no registo alvo para empurrar as imagens para.|
|Fornecer nome de utilizador para o registo do contentor-alvo - prima a entrada para não utilizar nenhum:|TARGET_DOCKER_USERNAME|O nome de utilizador, se houver, é utilizado para iniciar sessão no registo do contentor-alvo.|
|Fornecer senha para o registo do contentor-alvo - prima a introdução para não utilizar nenhum:|TARGET_DOCKER_PASSWORD|A palavra-passe, se for, é usada para iniciar sessão no registo do contentor-alvo. Esta é uma senha mascarada.  Não verá a palavra-passe se a escrever ou colar.|
|Fornecer etiqueta de imagem de recipiente para as imagens no alvo:|TARGET_DOCKER_TAG|Normalmente, usaria a mesma etiqueta que a fonte para evitar confusões.|