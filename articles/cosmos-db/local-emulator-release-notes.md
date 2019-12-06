---
title: Notas de versão e download do emulador Cosmos do Azure
description: Obtenha as notas de versão do emulador Cosmos do Azure para diferentes versões e informações de download.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: eb73551c4226df8bc20428558720c0ebbfff96d9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873595"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulador Cosmos do Azure – notas de versão e informações de download

Este artigo mostra as notas de versão do emulador Cosmos do Azure com uma lista de atualizações de recursos que foram feitas em cada versão. Ele também lista a versão mais recente do emulador para baixar e usar.

## <a name="download"></a>Transferir

| | |
|---------|---------|
|**Download do MSI**|[Centro de Transferências da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Get started**|[Desenvolver localmente com o emulador Cosmos do Azure](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="270"></a>2.7.0

- Esta versão corrige uma regressão que impediu os usuários de executar consultas na conta da API do SQL do emulador ao usar clientes .NET Core ou x86 baseados em .NET.

### <a name="246"></a>2.4.6

- Esta versão fornece paridade com os recursos do serviço Cosmos do Azure a partir de julho de 2019, com as exceções indicadas em [desenvolver localmente com o emulador Cosmos do Azure](local-emulator.md). Ele também corrige vários bugs relacionados ao desligamento do emulador quando invocado por linha de comando e substituições de endereço IP interno para clientes SDK usando conectividade de modo direto.

### <a name="243"></a>2.4.3

- Desabilitado a inicialização do serviço MongoDB por padrão. Somente o ponto de extremidade SQL está habilitado como padrão. O usuário deve iniciar o ponto de extremidade manualmente usando a opção de linha de comando "/EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos de extremidade de serviço, como Gremlin, Cassandra e Table.
- Corrigido um bug no emulador ao começar com "/AllowNetworkAccess", em que os pontos de extremidade Gremlin, Cassandra e table não estavam lidando corretamente com solicitações de clientes externos.
- Adicione portas de conexão direta às configurações de regras de firewall.

### <a name="240"></a>2.4.0

- Foi corrigido um problema com falha na inicialização do emulador quando aplicativos de monitoramento de rede, como o cliente Pulse, estão presentes no computador host.
