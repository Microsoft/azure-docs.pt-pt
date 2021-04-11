---
title: Azure Cosmos DB Emulator descarrega e lança notas
description: Obtenha as notas de lançamento do Emulador Azure Cosmos DB para diferentes versões e informações de descarregamento.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 442fa1c76d4667a54f1c13b411b1c052c9a5b134
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566647"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB Emulator - Divulgar notas e descarregar informações
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo mostra as notas de lançamento do Azure Cosmos DB Emulator com uma lista de atualizações de funcionalidades que foram feitas em cada lançamento. Também lista a versão mais recente do emulador para descarregar e usar.

## <a name="download"></a>Download

| | Ligação |
|---------|---------|
|**Download MSI**|[Centro de Transferências da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolva-se localmente com o Azure Cosmos DB Emulator](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="21111-22-february-2021"></a>2.11.11 (22 fevereiro 2021)

 - Esta versão atualiza o conteúdo local do Data Explorer para a versão mais recente do portal Azure.


### <a name="21110-5-january-2021"></a>2.11.10 (5 janeiro 2021)

 - Esta versão atualiza o conteúdo local do Data Explorer para a versão mais recente do portal Azure e adiciona uma nova opção pública, "/ExportPemCert", que permite ao utilizador emulador exportar diretamente o certificado do emulador público como um . Ficheiro PEM.

### <a name="2119-3-december-2020"></a>2.11.9 (3 de dezembro de 2020)

 - Esta versão aborda problemas de casal com a funcionalidade emulador Azure Cosmos DB, além da atualização geral de conteúdos que reflete as mais recentes funcionalidades e melhorias no Azure Cosmos DB:
 * Corrija para um problema em que os pedidos de carga útil de grandes documentos falham ao utilizar aplicações de clientes Direct e Java.
 * Correção para um problema de conectividade com a versão 3.6 do ponto final do MongoDB quando direcionada por aplicações baseadas em .NET.

### <a name="2118-6-november-2020"></a>2.11.8 (6 de novembro de 2020)

 - Esta versão inclui uma atualização para o Explorador de Dados do Emulador Cosmos e corrige um problema em que os clientes TLS 1.3 tentam abrir o Data Explorer.

### <a name="2116-6-october-2020"></a>2.11.6 (6 de outubro de 2020)

 - Esta versão aborda um problema relacionado com a conuncy quando vários contentores podem ser criados ao mesmo tempo. Nesses casos, os dados do emulador são deixados num estado corrompido e na sequência de pedidos da API ao ponto final do emulador podem falhar com erros de "serviço indisponível", exigindo um reinício e um reset dos dados locais do emulador.

### <a name="2115-23-august-2020"></a>2.11.5 (23 de agosto de 2020)

Esta versão adiciona duas novas opções de arranque emulador Cosmos: 

* "/EnablePreview" - permite funcionalidades de pré-visualização para o emulador. As funcionalidades de pré-visualização que ainda estão em desenvolvimento e podem ser acedidas via CI e escrita por amostras.
* "/EnableAadAuthentication" - permite ao emulador aceitar tokens personalizados do Azure Ative Directory como alternativa às teclas primárias do Azure Cosmos. Esta funcionalidade ainda está em desenvolvimento; as atribuições específicas de funções e outras definições relacionadas com a permissão não são suportadas atualmente.

### <a name="2112-07-july-2020"></a>2.11.2 (07 julho 2020)

- Esta versão altera a forma como os vestígios etl necessários quando se recolhiam os problemas do emulador Cosmos. WPR (ferramentas de runtime de desempenho do Windows) é agora a ferramenta predefinida para capturar vestígios baseados em ETL, enquanto a captura antiga baseada em LOGMAN foi depreciada. Esta alteração é necessária em parte porque as atualizações de segurança mais recentes do Windows tiveram um impacto inesperado no funcionamento do LOGMAN quando executados através do emulador Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 de junho de 2020)

- Esta versão corrige alguns bugs relacionados com o Explorador de Dados do Emulador. Em certos casos, ao utilizar o emulador Data Explorer através de um navegador web, não consegue ligar-se ao ponto final do emulador Cosmos e todas as ações relacionadas, como a criação de uma base de dados ou um contentor, resultarão em erro. O segundo problema corrigido está relacionado com a criação de um item a partir de um ficheiro JSON utilizando a ação de upload do Data Explorer.

### <a name="2110"></a>2.11.0

- Esta versão introduz suporte para a produção de autoescalação. Estas novas funcionalidades incluem a capacidade de definir um nível máximo de produção personalizado nas unidades de pedido (RU/s), permitir a autoescalação nas bases de dados e contentores existentes, e suporte programático através de Azure Cosmos DB SDKs.
- Corrija um problema enquanto consultava um grande número de documentos (mais de 1 GB) se o emulador falhará com o código de estado de erro interno 500.

### <a name="292"></a>2.9.2

- Esta versão corrige um bug enquanto permite suporte para a versão 3.2 do ponto final do MongoDb. Também adiciona suporte para gerar vestígios ETL para fins de resolução de problemas usando WPR em vez de LOGMAN.

### <a name="291"></a>2.9.1

- Esta versão corrige problemas de casal no suporte da API de consulta e restaura a compatibilidade com OSs mais antigos, como o Windows Server 2012.

### <a name="290"></a>2.9.0

- Esta versão adiciona a opção de definir a consistência para um prefixo consistente e aumentar os limites máximos para utilizadores e permissões.

### <a name="272"></a>2.7.2

- Esta versão adiciona suporte ao servidor da versão 3.6 do MongoDB ao Emulador Cosmos. Para iniciar um ponto final mongoDB que é a versão alvo 3.6 do serviço, inicie o emulador a partir de uma linha de comando do Administrador com a opção "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Esta versão corrige uma regressão, que impediu os utilizadores de executar consultas contra a conta API SQL do emulador quando utilizarem clientes baseados em .NET core ou x86 .NET.

### <a name="246"></a>2.4.6

- Esta versão proporciona paridade com as funcionalidades do serviço Azure Cosmos a partir de julho de 2019, com as exceções notadas em [Develop localmente com Azure Cosmos DB Emulator](local-emulator.md). Também corrige vários bugs relacionados com o emulador desligado quando invocado através da linha de comando e endereço IP interno sobrepõe-se a clientes SDK usando conectividade de modo direto.

### <a name="243"></a>2.4.3

- Desativado a iniciar o serviço MongoDB por predefinição. Apenas o ponto final SQL é ativado como padrão. O utilizador deve iniciar o ponto final manualmente utilizando a opção de linha de comando "/EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos finais de serviço, como Gremlin, Cassandra e Table.
- Corrigiu um bug no emulador quando começou com "/AllowNetworkAccess" onde os pontos finais gremlin, Cassandra e Table não estavam a lidar adequadamente com pedidos de clientes externos.
- Adicione portas de ligação direta às definições de Regras de Firewall.

### <a name="240"></a>2.4.0

- Corrigiu um problema com o emulador que não começou quando aplicações de monitorização de rede, como o Pulse Client, estão presentes no computador anfitrião.
