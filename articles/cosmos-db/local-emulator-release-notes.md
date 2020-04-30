---
title: Notas de descarregamento e lançamento do Emulador Azure Cosmos
description: Obtenha as notas de lançamento do emulador Azure Cosmos para diferentes versões e descarregamento de informações.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77168653"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulador Azure Cosmos - Notas de lançamento e descarregamento de informações

Este artigo mostra as notas de lançamento do emulador Azure Cosmos com uma lista de atualizações de funcionalidades que foram feitas em cada lançamento. Também lista a versão mais recente do emulador para descarregar e usar.

## <a name="download"></a>Transferência

| | |
|---------|---------|
|**Download de MSI**|[Centro de Transferências da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolver localmente com o emulador Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="291"></a>2.9.1

- Esta versão corrige problemas de casal no suporte a API de consulta e restaura a compatibilidade com OSs mais antigos, como o Windows Server 2012.

### <a name="290"></a>2.9.0

- Esta versão adiciona a opção de definir a consistência para prefixar consistentemente e aumentar os limites máximos para utilizadores e permissões.

### <a name="272"></a>2.7.2

- Esta versão adiciona suporte ao servidor MongoDB versão 3.6 ao Emulador Cosmos. Para iniciar um ponto final mongoDB que visa a versão 3.6 do serviço, inicie o emulador a partir de uma linha de comando administrador com a opção "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Esta versão corrige uma regressão que impediu os utilizadores de executarem consultas contra a conta SQL API do emulador ao utilizar emprementem .NET core ou x86 .NET clientes baseados.

### <a name="246"></a>2.4.6

- Este lançamento proporciona paridade com as características do serviço Azure Cosmos a partir de julho de 2019, com as exceções observadas no Desenvolvimento Local com o [emulador Azure Cosmos.](local-emulator.md) Também corrige vários bugs relacionados com a paragem do emulador quando invocado através da linha de comando e do endereço IP interno para os clientes SDK usando conectividade de modo direto.

### <a name="243"></a>2.4.3

- Desativado iniciando o serviço MongoDB por defeito. Apenas o ponto final SQL está ativado como padrão. O utilizador deve iniciar manualmente o ponto final utilizando a opção de linha de comando "/EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos finais de serviço, como Gremlin, Cassandra e Table.
- Fixou um bug no emulador ao começar pelo "/AllowNetworkAccess" onde os pontos finais gremlin, Cassandra e Table não estavam a lidar adequadamente com pedidos de clientes externos.
- Adicione portas de ligação direta às definições das Regras de Firewall.

### <a name="240"></a>2.4.0

- Corrigiu um problema com o emulador não ter começado quando as aplicações de monitorização da rede, como o Pulse Client, estão presentes no computador anfitrião.
