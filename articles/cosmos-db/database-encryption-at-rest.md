---
title: Criptografia em repouso no Azure Cosmos DB
description: Saiba como Azure Cosmos DB fornece criptografia de dados em repouso e como ele é implementado.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: a9e89336973b0b13544c5bc0bccec41652c6952e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755093"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Criptografia de dados no Azure Cosmos DB 

A criptografia em repouso é uma frase que normalmente se refere à criptografia de dados em dispositivos de armazenamento não volátil, como SSDs (unidades de estado sólido) e HDDs (unidades de disco rígido). O Cosmos DB armazena seus bancos de dados primários no SSDs. Seus anexos de mídia e backups são armazenados no armazenamento de BLOBs do Azure, que geralmente é feito backup por HDDs. Com o lançamento da criptografia em repouso para Cosmos DB, todos os seus bancos de dados, anexos de mídia e backups são criptografados. Seus dados agora são criptografados em trânsito (pela rede) e em repouso (armazenamento não volátil), oferecendo criptografia de ponta a ponta.

Como um serviço de PaaS, Cosmos DB é muito fácil de usar. Como todos os dados de usuário armazenados no Cosmos DB são criptografados em repouso e no transporte, você não precisa realizar nenhuma ação. Outra maneira de colocar isso é que a criptografia em repouso é "ativada" por padrão. Não há controles para desativá-lo. Azure Cosmos DB usa a criptografia AES-256 em todas as regiões em que a conta está em execução. Fornecemos esse recurso enquanto continuamos a atender aos nossos [SLAs de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação de criptografia em repouso para Azure Cosmos DB

A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas de armazenamento de chaves seguras, redes criptografadas e APIs de criptografia. Sistemas que descriptografam e processam dados precisam se comunicar com sistemas que gerenciam chaves. O diagrama mostra como o armazenamento de dados criptografados e o gerenciamento de chaves são separados. 

![Diagrama de design](./media/database-encryption-at-rest/design-diagram.png)

O fluxo básico de uma solicitação de usuário é o seguinte:
- A conta do banco de dados do usuário está pronta e as chaves de armazenamento são recuperadas por meio de uma solicitação para o provedor de recursos do serviço de gerenciamento.
- Um usuário cria uma conexão para Cosmos DB por meio de transporte HTTPS/seguro. (Os SDKs abstraim os detalhes.)
- O usuário envia um documento JSON a ser armazenado pela conexão segura criada anteriormente.
- O documento JSON é indexado, a menos que o usuário tenha desativado a indexação.
- O documento JSON e os dados de índice são gravados no armazenamento seguro.
- Periodicamente, os dados são lidos do armazenamento seguro e armazenados em backup para o repositório de blob criptografado do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: Qual é o custo máximo do armazenamento do Azure se Criptografia do Serviço de Armazenamento estiver habilitado?
R: não há nenhum custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: quem gerencia as chaves de criptografia?
R: as chaves são gerenciadas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: com que frequência as chaves de criptografia são giradas?
R: a Microsoft tem um conjunto de diretrizes internas para a rotação de chaves de criptografia, que Cosmos DB a seguir. As diretrizes específicas não são publicadas. A Microsoft publica o [SDL (ciclo de vida de desenvolvimento da segurança)](https://www.microsoft.com/sdl/default.aspx), que é visto como um subconjunto de diretrizes internas e tem práticas recomendadas úteis para os desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: posso usar minhas próprias chaves de criptografia?
R: o Cosmos DB é um serviço de PaaS e trabalhamos duro para manter o serviço fácil de usar. Percebemos que essa pergunta é muitas vezes solicitada como uma pergunta de proxy para atender a um requisito de conformidade como o PCI-DSS. Como parte da criação desse recurso, trabalhamos com auditores de conformidade para garantir que os clientes que usam Cosmos DB atendam aos seus requisitos sem a necessidade de gerenciar as próprias chaves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: em quais regiões a criptografia está ativada?
R: todas as regiões de Azure Cosmos DB têm a criptografia ativada para todos os dados do usuário.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: a criptografia afeta os SLAs de taxa de transferência e latência de desempenho?
R: não há nenhum impacto ou alteração nos SLAs de desempenho agora que a criptografia em repouso está habilitada para todas as contas existentes e novas. Você pode ler mais sobre o [SLA para Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) página para ver as garantias mais recentes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: o emulador local dá suporte à criptografia em repouso?
R: o emulador é uma ferramenta de desenvolvimento/teste independente e não usa os serviços de gerenciamento de chaves que o serviço gerenciado Cosmos DB usa. Nossa recomendação é habilitar o BitLocker em unidades em que você está armazenando dados confidenciais de teste do emulador. O [emulador dá suporte à alteração do diretório de dados padrão](local-emulator.md) , bem como ao uso de um local bem conhecido.

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral da segurança de Cosmos DB e os aprimoramentos mais recentes, consulte [segurança do banco de dados Cosmos do Azure](database-security.md).
Para obter mais informações sobre certificações da Microsoft, consulte a [central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).
