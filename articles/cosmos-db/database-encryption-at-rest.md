---
title: Encriptação em repouso no Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece encriptação de dados em repouso e como ele é implementado.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f406f008e2c377b39deb8d151855ce7315616701
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616860"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Criptografia de dados no Azure Cosmos DB 

Encriptação inativa é uma expressão que normalmente refere-se para a encriptação de dados em dispositivos de armazenamento não volátil, como unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs). O cosmos DB armazena seus bancos de dados primários no SSDs. Seus anexos de suporte de dados e as cópias de segurança são armazenadas no armazenamento de Blobs do Azure, que geralmente está protegido por HDDs. Com o lançamento de encriptação em repouso para o Cosmos DB, todos os seus bancos de dados, anexos de suporte de dados e as cópias de segurança são encriptadas. Os dados agora são encriptados em trânsito (através da rede) e em repouso (não-volátil armazenamento), dando-lhe encriptação ponto a ponto.

Como um serviço PaaS, o Cosmos DB é muito fácil de usar. Como de todos os dados de utilizador armazenados no Cosmos DB são encriptados em descanso e em transporte, não precisa de realizar qualquer ação. Outra forma de colocar isso é que a encriptação inativa é "on" por predefinição. Não há nenhum controle para ativá-la ou desativar. Azure Cosmos DB usa a criptografia AES-256 em todas as regiões em que a conta está em execução. Fornecemos esta funcionalidade enquanto Continuamos atender aos nossos [SLAs de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação de encriptação em repouso para o Azure Cosmos DB

Encriptação inativa é implementada com um número de tecnologias de segurança, incluindo sistemas de armazenamento de chaves segura, redes encriptadas e APIs criptográficas. Sistemas que desencriptam e processam os dados de tem para comunicar com sistemas que gerem as chaves. O diagrama mostra como o armazenamento de dados encriptados e o gerenciamento de chaves são separados. 

![Diagrama de design](./media/database-encryption-at-rest/design-diagram.png)

O fluxo básico de um pedido de utilizador é o seguinte:
- A conta de base de dados de utilizador é efetuada pronta e chaves de armazenamento são obtidas através de um pedido para o fornecedor de recursos do serviço de gestão.
- Um utilizador cria uma ligação ao Cosmos DB por meio de transporte seguro/HTTPS. (Os SDKs abstraem os detalhes.)
- O usuário envia um documento JSON sejam armazenados através da ligação de segura criada anteriormente.
- O documento JSON é indexado, a menos que o usuário desativou a indexação.
- Ambos os dados JSON de índice e documentos são escritos para armazenamento seguro.
- Periodicamente, os dados são ler a partir do armazenamento seguro e cópia de segurança para o Store de Blobs do Azure encriptado.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P. Quanto mais custa o armazenamento do Azure se Criptografia do Serviço de Armazenamento estiver habilitado?
R: Não há nenhum custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P. Quem gerencia as chaves de criptografia?
R: As chaves são gerenciadas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P. Com que frequência as chaves de criptografia são giradas?
R: A Microsoft tem um conjunto de diretrizes internas para a rotação de chaves de criptografia, que Cosmos DB segue. As diretrizes específicas não são publicadas. Microsoft publicar os [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), que é visto como um subconjunto de orientação interno e tem práticas úteis para desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P. Posso usar minhas próprias chaves de criptografia?
R: Cosmos DB é um serviço de PaaS e trabalhamos duro para manter o serviço fácil de usar. Já Reparámos que essa pergunta, muitas vezes, é pedida como uma pergunta de proxy para atender um requisito de conformidade, como PCI-DSS. Como parte da criação desta funcionalidade, trabalhámos com auditores de conformidade para garantir que os clientes que utilizam o Cosmos DB cumprir os respetivos requisitos sem a necessidade de gerir chaves propriamente ditas.

### <a name="q-what-regions-have-encryption-turned-on"></a>P. Em quais regiões a criptografia está ativada?
R: Todas as regiões de Azure Cosmos DB têm a criptografia ativada para todos os dados do usuário.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P. A criptografia afeta os SLAs de taxa de transferência e latência de desempenho?
R: Não há nenhum impacto ou alteração nos SLAs de desempenho agora que a criptografia em repouso está habilitada para todas as contas existentes e novas. Pode ler mais sobre o [SLA para o Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) página para ver as garantias de mais recente.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P. O emulador local dá suporte à criptografia em repouso?
R: O emulador é uma ferramenta de desenvolvimento/teste independente e não usa os serviços de gerenciamento de chaves que o serviço gerenciado Cosmos DB usa. Nossa recomendação é ativar o BitLocker em unidades em que estiver armazenando dados de teste do emulador confidenciais. O [emulador oferece suporte a alterar o diretório de dados predefinido](local-emulator.md) , bem como através de um local conhecido.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral da segurança de Cosmos DB e os aprimoramentos mais recentes, consulte [segurança do banco de dados Cosmos do Azure](database-security.md).
Para obter mais informações sobre as certificações da Microsoft, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).
