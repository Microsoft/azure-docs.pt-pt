---
title: Encriptação em repouso no Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece encriptação de dados em repouso e como ele é implementado.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: b88b32b3ae2d4939d26fc268e25c87b1fb70eee5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237214"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Encriptação de dados no Azure Cosmos DB 

Encriptação inativa é uma expressão que normalmente refere-se para a encriptação de dados em dispositivos de armazenamento não volátil, como unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs). O cosmos DB armazena seus bancos de dados primários no SSDs. Seus anexos de suporte de dados e as cópias de segurança são armazenadas no armazenamento de Blobs do Azure, que geralmente está protegido por HDDs. Com o lançamento de encriptação em repouso para o Cosmos DB, todos os seus bancos de dados, anexos de suporte de dados e as cópias de segurança são encriptadas. Os dados agora são encriptados em trânsito (através da rede) e em repouso (não-volátil armazenamento), dando-lhe encriptação ponto a ponto.

Como um serviço PaaS, o Cosmos DB é muito fácil de usar. Como de todos os dados de utilizador armazenados no Cosmos DB são encriptados em descanso e em transporte, não precisa de realizar qualquer ação. Outra forma de colocar isso é que a encriptação inativa é "on" por predefinição. Não há nenhum controle para ativá-la ou desativar. O Azure Cosmos DB utiliza encriptação AES-256 em todas as regiões onde a conta está em execução. Fornecemos esta funcionalidade enquanto Continuamos atender aos nossos [SLAs de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/cosmos-db).

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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P. Quanto mais custa armazenamento do Azure se estiver ativada a encriptação do serviço de armazenamento?
R: Não existe nenhum custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P. Quem gere as chaves de encriptação?
R: As chaves são geridas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P. A frequência com que são revezadas de chaves de encriptação?
R: A Microsoft tem um conjunto de diretrizes internas para encriptação rotação de chaves, que segue o Cosmos DB. As diretrizes específicas não são publicadas. Microsoft publicar os [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), que é visto como um subconjunto de orientação interno e tem práticas úteis para desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P. Pode utilizar as minhas chaves de encriptação?
R: O cosmos DB é um serviço PaaS e Trabalhamos duro para manter o serviço fácil de usar. Já Reparámos que essa pergunta, muitas vezes, é pedida como uma pergunta de proxy para atender um requisito de conformidade, como PCI-DSS. Como parte da criação desta funcionalidade, trabalhámos com auditores de conformidade para garantir que os clientes que utilizam o Cosmos DB cumprir os respetivos requisitos sem a necessidade de gerir chaves propriamente ditas.

### <a name="q-what-regions-have-encryption-turned-on"></a>P. O que regiões tem a encriptação ativada?
R: Todas as regiões do Azure Cosmos DB tem a encriptação ativada para todos os dados de utilizador.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P. Encriptação afeta a latência de desempenho e débito SLAs?
R: Não existe impacto ou alterações no desempenho SLAs agora que a encriptação inativa está ativada para todas as contas novas e existentes. Pode ler mais sobre o [SLA para o Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) página para ver as garantias de mais recente.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P. O emulador local suporta a encriptação em repouso?
R: O emulador é uma ferramenta de desenvolvimento/teste autónoma e não utiliza os serviços de gestão de chaves que utiliza o serviço gerido do Cosmos DB. Nossa recomendação é ativar o BitLocker em unidades em que estiver armazenando dados de teste do emulador confidenciais. O [emulador oferece suporte a alterar o diretório de dados predefinido](local-emulator.md) , bem como através de um local conhecido.

## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral de segurança do Cosmos DB e as melhorias mais recentes, consulte [segurança de base de dados do Azure Cosmos DB](database-security.md).
Para obter mais informações sobre as certificações da Microsoft, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).
