---
title: Azure Cosmos Emulator descarrega e lança notas
description: Obtenha as notas de lançamento do emulador Azure Cosmos para diferentes versões e informações de descarregamento.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: ab31daafe496f1aaad2bbc8075b2514d9e57e997
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84687179"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - Lançar notas e descarregar informações

Este artigo mostra as notas de lançamento do emulador Azure Cosmos com uma lista de atualizações de funcionalidades que foram feitas em cada lançamento. Também lista a versão mais recente do emulador para descarregar e usar.

## <a name="download"></a>Transferência

| | |
|---------|---------|
|**Download MSI**|[Centro de Transferências da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolva-se localmente com o emulador Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="2111"></a>2.11.1

- Esta versão corrige alguns bugs relacionados com o Explorador de Dados do Emulador. Em certos casos, ao utilizar o emulador Data Explorer através de um navegador web, não consegue ligar-se ao ponto final do emulador Cosmos e todas as ações relacionadas, como a criação de uma base de dados ou um contentor, irão falhar. O segundo problema corrigido está relacionado com a criação de um item a partir de um ficheiro JSON utilizando a ação de upload do Data Explorer.

### <a name="2110"></a>2.11.0

- Esta versão introduz suporte para a produção de autoescalação. Estas novas funcionalidades incluem a capacidade de definir um nível máximo de produção personalizado nas unidades de pedido (RU/s), permitir a autoescalação nas bases de dados e contentores existentes, e suporte programático através de Azure Cosmos DB SDKs.
- Corrija um problema enquanto consultava uma grande quantidade de documentos (mais de 1GB) se o emulador falhará com o código de estado de erro interno 500.

### <a name="292"></a>2.9.2

- Esta versão corrige um bug enquanto permite suporte para a versão 3.2 do ponto final do MongoDb. Também adiciona suporte para gerar vestígios ETL para fins de resolução de problemas usando WPR em vez de LOGMAN.

### <a name="291"></a>2.9.1

- Esta versão corrige problemas de casal no suporte da API de consulta e restaura a compatibilidade com OSs mais antigos, como o Windows Server 2012.

### <a name="290"></a>2.9.0

- Esta versão adiciona a opção de definir a consistência para um prefixo consistente e aumentar os limites máximos para utilizadores e permissões.

### <a name="272"></a>2.7.2

- Esta versão adiciona suporte ao servidor da versão 3.6 do MongoDB ao Emulador Cosmos. Para iniciar um ponto final mongoDB que é a versão alvo 3.6 do serviço, inicie o emulador a partir de uma linha de comando do Administrador com a opção "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Esta versão corrige uma regressão que impediu os utilizadores de executar consultas contra a conta API SQL do emulador quando utilizarem clientes baseados em .NET core ou x86 .NET.

### <a name="246"></a>2.4.6

- Esta versão proporciona paridade com as funcionalidades do serviço Azure Cosmos a partir de julho de 2019, com as exceções notadas em [Develop localmente com o emulador Azure Cosmos.](local-emulator.md) Também corrige vários bugs relacionados com a paragem do emulador quando invocado através da linha de comando e endereço IP interno sobrepõe-se a clientes SDK usando conectividade de modo direto.

### <a name="243"></a>2.4.3

- Desativado a iniciar o serviço MongoDB por predefinição. Apenas o ponto final SQL é ativado como padrão. O utilizador deve iniciar o ponto final manualmente utilizando a opção de linha de comando "/EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos finais de serviço, como Gremlin, Cassandra e Table.
- Corrigiu um bug no emulador quando começou com "/AllowNetworkAccess" onde os pontos finais gremlin, Cassandra e Table não estavam a lidar adequadamente com pedidos de clientes externos.
- Adicione portas de ligação direta às definições de Regras de Firewall.

### <a name="240"></a>2.4.0

- Corrigiu um problema com o emulador que não começou quando aplicações de monitorização de rede, como o Pulse Client, estão presentes no computador anfitrião.
