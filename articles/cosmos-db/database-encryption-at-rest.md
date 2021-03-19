---
title: Encriptação em repouso em Azure Cosmos DB
description: Saiba como a Azure Cosmos DB fornece encriptação de dados em repouso e como é implementado.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93089870"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Encriptação de dados no Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A encriptação em repouso é uma frase que geralmente se refere à encriptação de dados em dispositivos de armazenamento nãovoláteis, tais como unidades de estado sólido (SSDs) e discos rígidos (HDDs). A Cosmos DB armazena as suas bases de dados primárias em SSDs. Os seus anexos e backups de mídia são armazenados no armazenamento Azure Blob, que é geralmente apoiado por HDDs. Com a libertação de encriptação em repouso para a Cosmos DB, todas as suas bases de dados, anexos de mídia e backups estão encriptados. Os seus dados estão agora encriptados em trânsito (sobre a rede) e em repouso (armazenamento nãovolátelido), dando-lhe encriptação de ponta a ponta.

Como serviço PaaS, a Azure Cosmos DB é muito fácil de usar. Como todos os dados do utilizador armazenados no Azure Cosmos DB estão encriptados em repouso e no transporte, não é necessário tomar nenhuma medida. Outra forma de colocar isto é que a encriptação em repouso está "on" por padrão. Não há controlos para desligá-lo ou ligar. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está a funcionar. Fornecemos esta funcionalidade enquanto continuamos a atender à nossa [disponibilidade e desempenho SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db). Os dados armazenados na sua conta Azure Cosmos são automaticamente e sem problemas encriptados com teclas geridas pela Microsoft (teclas geridas pelo serviço). Opcionalmente, pode optar por adicionar uma segunda camada de encriptação com as suas próprias chaves, conforme descrito no artigo [de chaves gerido pelo cliente.](how-to-setup-cmk.md)

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação da encriptação em repouso para Azure Cosmos DB

A encriptação em repouso é implementada utilizando uma série de tecnologias de segurança, incluindo sistemas de armazenamento de chaves seguras, redes encriptadas e APIs criptográficos. Os sistemas que desencriptam e processam dados têm de comunicar com sistemas que gerem as chaves. O diagrama mostra como o armazenamento de dados encriptados e a gestão das chaves são separados. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Diagrama de design" border="false":::

O fluxo básico de um pedido de utilizador é o seguinte:
- A conta de base de dados do utilizador está pronta e as chaves de armazenamento são recuperadas através de um pedido ao Fornecedor de Recursos de Serviço de Gestão.
- Um utilizador cria uma ligação à Cosmos DB através de HTTPS/transporte seguro. (Os SDKs abstratam os detalhes.)
- O utilizador envia um documento JSON para ser armazenado sobre a ligação segura previamente criada.
- O documento JSON é indexado a menos que o utilizador tenha desligado a indexação.
- Tanto o documento JSON como os dados do índice são escritos para garantir o armazenamento.
- Periodicamente, os dados são lidos a partir do armazenamento seguro e reservados até à Azure Encrypted Blob Store.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: Quanto mais custa o Azure Storage se a Encriptação do Serviço de Armazenamento estiver ativada?
R: Não há custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gere as chaves de encriptação?
R: As chaves são geridas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Com que frequência são giradas as chaves de encriptação?
R: A Microsoft tem um conjunto de diretrizes internas para a rotação da chave de encriptação, que a Cosmos DB segue. As diretrizes específicas não são publicadas. A Microsoft publica o Ciclo de [Vida para Desenvolvimento de Segurança (SDL),](https://www.microsoft.com/sdl/default.aspx)que é visto como um subconjunto de orientação interna e tem boas práticas úteis para os desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar as minhas próprias chaves de encriptação?
R: Sim, esta funcionalidade já está disponível para novas contas DB da Azure Cosmos e isso deve ser feito no momento da criação de conta. Consulte o documento [Keys gerido pelo Cliente](./how-to-setup-cmk.md) para obter mais informações.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: Que regiões ligaram a encriptação?
R: Todas as regiões DB do Azure Cosmos têm encriptação ligada para todos os dados do utilizador.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: A encriptação afeta a latência de desempenho e o desempenho SLAs?
R: Não há qualquer impacto ou alterações no desempenho SLAs agora que a encriptação em repouso está ativada para todas as contas existentes e novas. Pode ler mais na página [SLA for Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) para ver as últimas garantias.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: O emulador local suporta a encriptação em repouso?
R: O emulador é uma ferramenta autónoma dev/teste e não utiliza os serviços de gestão chave que o serviço Cos DB gerido utiliza. A nossa recomendação é permitir que o BitLocker em unidades onde esteja a armazenar dados sensíveis de teste do emulador. O [emulador suporta alterar o diretório de dados predefinido,](local-emulator.md) bem como usar uma localização bem conhecida.

## <a name="next-steps"></a>Passos seguintes

* Pode optar por adicionar uma segunda camada de encriptação com as suas próprias chaves, para saber mais, ver o artigo [de chaves gerido pelo cliente.](how-to-setup-cmk.md)
* Para uma visão geral da segurança da Cosmos DB e das mais recentes melhorias, consulte [a segurança da base de dados Azure Cosmos.](database-security.md)
* Para obter mais informações sobre as certificações da Microsoft, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).