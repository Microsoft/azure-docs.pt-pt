---
title: Download do emulador do Cosmos do Azure e notas de versão
description: Leia as notas de versão do emulador do Cosmos do Azure e transferi-lo.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332120"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Utilizar o emulador do Cosmos para o local de desenvolvimento e teste

Este artigo mostra as notas de versão de emulador do Cosmos do Azure com uma lista de atualizações de funcionalidades que foram feitas em cada lançamento. Ele também apresenta a versão mais recente do emulador para transferir e utilizar.

## <a name="download"></a>Transferência

| | |
|---------|---------|
|**Download MSI**|[Centro de Transferências da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolver localmente com o emulador do Cosmos do Azure](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="243"></a>2.4.3

- A iniciar o serviço de MongoDB, por predefinição desativada. Apenas o ponto de extremidade do SQL está ativado como predefinição. O utilizador tem de iniciar o ponto de extremidade manualmente utilizando o emulador "/ EnableMongoDbEndpoint" opção da linha de comandos. Agora, é como todos os outros pontos finais de serviço, como Gremlin, Cassandra e tabela.
- Foi corrigido um erro no emulador ao iniciar com "/ AllowNetworkAccess" em que os pontos de extremidade do Gremlin, Cassandra e a tabela não eram o processamento correto dos pedidos de clientes externos.
- Adicione as portas de ligação direta para as definições de regras de Firewall.

### <a name="240"></a>2.4.0

- Foi corrigido um problema com o emulador a falhar iniciar quando as aplicações de monitorização de rede, como o cliente do Pulse, estão presentes no computador anfitrião.
