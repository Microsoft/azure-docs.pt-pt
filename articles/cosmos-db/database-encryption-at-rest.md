---
title: Encriptação em repouso em Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece encriptação de dados em repouso e como é implementado.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: 541c6f4a6c728844524af794f5e2063f4e352cce
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592139"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Encriptação de dados no Azure Cosmos DB 

A encriptação em repouso é uma frase que geralmente se refere à encriptação de dados em dispositivos de armazenamento não voláteis, tais como unidades de estado sólido (SSDs) e discos rígidos (HDDs). Cosmos DB armazena as suas bases de dados primárias em SSDs. Os seus anexos e backups de mídia são armazenados no armazenamento Azure Blob, que é geralmente apoiado por HDDs. Com a libertação de encriptação em repouso para cosmos DB, todas as suas bases de dados, anexos de mídia e backups estão encriptados. Os seus dados estão agora encriptados em trânsito (sobre a rede) e em repouso (armazenamento não volátil), dando-lhe encriptação de ponta a ponta.

Como serviço PaaS, o Azure Cosmos DB é muito fácil de usar. Como todos os dados dos utilizadores armazenados no Azure Cosmos DB estão encriptados em repouso e em transporte, não é necessário tomar qualquer medida. Outra forma de colocar isto é que a encriptação em repouso é "on" por padrão. Não há controlos para desligá-lo ou ligar. A Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está em execução. Fornecemos esta funcionalidade enquanto continuamos a cumprir a nossa [disponibilidade e desempenho SLAs.](https://azure.microsoft.com/support/legal/sla/cosmos-db) Os dados armazenados na sua conta Azure Cosmos são automaticamente e sem problemas encriptados com chaves geridas pela Microsoft (chaves geridas pelo serviço). Opcionalmente, pode optar por adicionar uma segunda camada de encriptação com as suas próprias chaves, conforme descrito no artigo de [chaves gerido pelo cliente.](how-to-setup-cmk.md)

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação da encriptação em repouso para o Azure Cosmos DB

A encriptação em repouso é implementada utilizando uma série de tecnologias de segurança, incluindo sistemas de armazenamento de chaves seguras, redes encriptadas e APIs criptográficos. Os sistemas que desencriptam e processam os dados têm de comunicar com sistemas que gerem chaves. O diagrama mostra como o armazenamento de dados encriptados e a gestão das teclas são separados. 

![Diagrama de design](./media/database-encryption-at-rest/design-diagram.png)

O fluxo básico de um pedido de utilizador é o seguinte:
- A conta de base de dados do utilizador está pronta e as chaves de armazenamento são recuperadas através de um pedido ao Fornecedor de Recursos do Serviço de Gestão.
- Um utilizador cria uma ligação ao Cosmos DB via TRANSPORTE HTTPS/Seguro. (Os SDKs abstraem os detalhes.)
- O utilizador envia um documento JSON para ser armazenado sobre a ligação segura previamente criada.
- O documento JSON é indexado a menos que o utilizador tenha desligado a indexação.
- Tanto o documento JSON como os dados do índice são escritos para garantir o armazenamento.
- Periodicamente, os dados são lidos a partir do armazenamento seguro e apoiados na Loja Blob Encriptada Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: Quanto mais o Armazenamento Azure custa se a encriptação do serviço de armazenamento estiver ativada?
R: Não há custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gere as chaves de encriptação?
R: As chaves são geridas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Com que frequência as chaves de encriptação são giradas?
R: A Microsoft tem um conjunto de diretrizes internas para a rotação da chave de encriptação, que cosmos DB segue. As diretrizes específicas não são publicadas. A Microsoft publica o Ciclo de Vida de [Desenvolvimento de Segurança (SDL),](https://www.microsoft.com/sdl/default.aspx)que é visto como um subconjunto de orientação interna e tem boas práticas úteis para os desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar as minhas próprias chaves de encriptação?
R: Sim agora esta funcionalidade está disponível para as novas contas cosmos e isso deve ser feito no momento da criação de contas. Por favor, consulte o documento [De chaves gerido pelo Cliente](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) para obter mais informações.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: Que regiões ligaram a encriptação?
R: Todas as regiões do Azure Cosmos DB têm encriptação ligada para todos os dados dos utilizadores.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: A encriptação afeta a latência do desempenho e os SLAs de entrada?
R: Não há impacto ou alterações no desempenho slAs agora que a encriptação em repouso está ativada para todas as contas existentes e novas. Pode ler mais na página [SLA for Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) para ver as mais recentes garantias.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: O emulador local suporta encriptação em repouso?
R: O emulador é uma ferramenta autónoma de dev/teste e não utiliza os principais serviços de gestão que o serviço Cosmos DB gerido utiliza. A nossa recomendação é ativar o BitLocker nas unidades onde está a armazenar dados de teste de emuladores sensíveis. O [emulador suporta a alteração do diretório de dados predefinido,](local-emulator.md) bem como a utilização de uma localização bem conhecida.

## <a name="next-steps"></a>Passos seguintes

* Pode optar por adicionar uma segunda camada de encriptação com as suas próprias chaves, para saber mais, ver o artigo de [chaves gerido pelo cliente.](how-to-setup-cmk.md)
* Para uma visão geral da segurança da Cosmos DB e as últimas melhorias, consulte a [segurança da base de dados Azure Cosmos.](database-security.md)
* Para obter mais informações sobre as certificações da Microsoft, consulte o [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
