---
title: Pré-visualização de nível básico - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: O único nível básico do nó para a base de dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024047"
---
# <a name="basic-tier-preview"></a>Nível básico (pré-visualização)

> [!IMPORTANT]
> O nível básico de Hiperescala (Citus) está atualmente em pré-visualização.  Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

O nível básico na Base de Dados Azure para PostgreSQL - Hiperescala (Citus) é uma forma simples de criar um pequeno grupo de servidores que pode escalar mais tarde. Enquanto os grupos de servidores no nível padrão têm um nó coordenador e pelo menos dois nós de trabalhadores, o nível básico executa tudo num único nó de base de dados.

Além de utilizar menos nós, o nível básico tem todas as características do nível padrão. Tal como o nível padrão, suporta alta disponibilidade, leu réplicas e armazenamento de mesas colunar, entre outras funcionalidades.

## <a name="choosing-basic-vs-standard-tier"></a>Escolher o nível básico vs padrão

O nível básico pode ser uma opção de implantação económica e conveniente para o desenvolvimento inicial, teste e integração contínua. Utiliza um único nó de base de dados e apresenta o mesmo SQL API que o nível padrão. Pode testar aplicações com o nível básico e depois [formar-se no nível padrão](howto-hyperscale-scale-grow.md#add-worker-nodes) com confiança de que a interface permanece a mesma.

O nível básico também é adequado para cargas de trabalho mais pequenas na produção (uma vez que sai da pré-visualização para a disponibilidade geral). Há espaço para escalar verticalmente *dentro* do nível básico, aumentando o número de vCores do servidor.

Quando for necessária uma maior escala, utilize o nível padrão. O seu menor grupo de servidores permitido tem um nó coordenador e dois trabalhadores. Pode optar por utilizar mais nós com base no seu caso de utilização, conforme descrito no nosso [tamanho inicial](howto-hyperscale-scale-initial.md) como fazer.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [providenciar o nível básico](quickstart-create-hyperscale-basic-tier.md)
* Quando estiver pronto, veja [como se formar](howto-hyperscale-scale-grow.md#add-worker-nodes) do nível básico para o nível padrão
* A opção [de armazenamento colunar](concepts-hyperscale-columnar.md) está disponível tanto no nível básico como padrão
