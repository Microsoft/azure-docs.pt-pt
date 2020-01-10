---
title: Armazenamento de blob imutável-armazenamento do Azure
description: O armazenamento do Azure oferece suporte de WORM (Write Once, Read Many) para armazenamento de BLOB (objeto) que permite aos usuários armazenar dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 9d0919651842a6f6f935c9f1e338c9d335b80f47
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749154"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Armazene dados de blob críticos para os negócios com armazenamento imutável

O armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem objetos de dados críticos para os negócios em um estado de WORM (gravar uma vez, ler muitos). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante a duração do intervalo de retenção, os BLOBs podem ser criados e lidos, mas não podem ser modificados ou excluídos. O armazenamento imutável está disponível para contas de armazenamento de BLOBs V2 e de uso geral em todas as regiões do Azure.

Para obter informações sobre como definir e limpar as isenções legais ou criar uma política de retenção baseada em tempo usando o portal do Azure, o PowerShell ou o CLI do Azure, consulte [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md).

## <a name="about-immutable-blob-storage"></a>Sobre o armazenamento de BLOBs imutável

O armazenamento imutável ajuda a organização de saúde, instituições financeiras e setores relacionados&mdash;particularmente as organizações do revendedor&mdash;para armazenar dados com segurança. O armazenamento imutável também pode ser aproveitado em qualquer cenário para proteger dados críticos contra modificação ou exclusão.

As aplicações típicas incluem:

- **Conformidade regulatória**: o armazenamento imutável para o armazenamento de BLOBs do Azure ajuda as organizações a lidar com SEC 17a-4 (f), CFTC 1.31 (d), FINRA e outras regulamentações. Um White paper técnico da Cohasset Associates fornece detalhes sobre como o armazenamento imutável resolve esses requisitos regulatórios é baixável por meio do [portal de confiança do serviço da Microsoft](https://aka.ms/AzureWormStorage). O [central de confiabilidade do Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre nossas certificações de conformidade.

- **Retenção segura de documentos**: o armazenamento imutável para o armazenamento de BLOBs do Azure garante que os dados não possam ser modificados ou excluídos por qualquer usuário, incluindo usuários com privilégios administrativos de conta.

- **Retenção legal**: armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem informações confidenciais que são críticas para uso de litígio ou comercial em um estado de prova de adulteração até a duração desejada até que a suspensão seja removida. Esse recurso não é limitado apenas a casos de uso jurídico, mas também pode ser considerado como uma suspensão baseada em evento ou um bloqueio corporativo, em que a necessidade de proteger dados com base em gatilhos de evento ou política corporativa é necessária.

O armazenamento imutável oferece suporte aos seguintes recursos:

- **[Suporte à política de retenção baseada em tempo](#time-based-retention-policies)** : os usuários podem definir políticas para armazenar dados para um intervalo especificado. Quando uma política de retenção baseada em tempo é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Depois que o período de retenção tiver expirado, os BLOBs poderão ser excluídos, mas não substituídos.

- **[Suporte à política de manutenção legal](#legal-holds)** : se o intervalo de retenção não for conhecido, os usuários poderão definir as isenções legais para armazenar dados imutáveis até que a retenção legal seja apagada.  Quando uma política de manutenção legal é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Cada retenção legal é associado a uma marca alfanumérica definida pelo usuário (como uma ID de caso, um nome de evento, etc.) que é usada como uma cadeia de caracteres de identificador. 

- **Suporte para todas as camadas de blob**: as políticas de worm são independentes da camada de armazenamento de BLOBs do Azure e se aplicam a todas as camadas: quente, fria e arquivo morto. Os utilizadores podem migrar os dados para a camada com o custo mais otimizado para as cargas de trabalho e manter em simultâneo a imutabilidade dos dados.

- **Configuração em nível de contêiner**: os usuários podem configurar políticas de retenção baseadas em tempo e marcas de espera legal no nível de contêiner. Com definições de nível de contentor simples, os utilizadores podem criar e bloquear as políticas de retenção baseadas no tempo, expandir intervalos de retenção, definir e desmarcar retenções legais e mais. Estas políticas aplicam-se a todos os blobs no contentor, tanto novos como existentes.

- **Suporte ao log de auditoria**: cada contêiner inclui um log de auditoria de política. Ele mostra até sete comandos de retenção baseados em tempo para políticas de retenção baseadas em tempo bloqueadas e contém a ID de usuário, o tipo de comando, os carimbos de data/hora e o intervalo de retenção. Para retenções legais, o registo contém o ID de utilizador, o tipo de comando, os carimbos de data/hora e as etiquetas de retenção legal. Esse log é retido durante o tempo de vida da política, de acordo com as diretrizes regulatórias da SEC 17a-4 (f). O [log de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) mostra um log mais abrangente de todas as atividades do plano de controle; ao habilitar [os logs de diagnóstico do Azure](../../azure-monitor/platform/platform-logs-overview.md) , o mantém e mostra as operações do plano de dados. É da responsabilidade do utilizador armazenar esses registos de forma persistente, consoante seja preciso por motivos de regulamentação ou outros fins.

## <a name="how-it-works"></a>Como funciona

O armazenamento imutável do Armazenamento de blobs do Azure suporta dois tipos de políticas WORM ou imutáveis: retenção baseada no tempo e retenções legais. Quando uma política de retenção baseada em tempo ou uma retenção legal é aplicada em um contêiner, todos os BLOBs existentes são movidos para um estado de WORM imutável em menos de 30 segundos. Todos os novos BLOBs que são carregados nesse contêiner também serão movidos para o estado imutável. Depois que todos os BLOBs forem movidos para o estado imutável, a política imutável será confirmada e todas as operações de substituição ou exclusão para objetos novos e existentes no contêiner imutável não serão permitidas.

A exclusão da conta de armazenamento e de contêiner não será permitida se houver BLOBs no contêiner ou na conta de armazenamento que são protegidos por uma política imutável. A operação de exclusão do contêiner falhará se pelo menos um blob existir com uma política de retenção baseada em tempo bloqueada ou uma retenção legal. A operação de exclusão da conta de armazenamento falhará se houver pelo menos um contêiner de WORM com uma retenção legal ou um blob com um intervalo de retenção ativo.

### <a name="time-based-retention-policies"></a>Políticas de retenção baseadas em tempo

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve estar *bloqueada* para que o blob esteja em um estado compatível imutável (gravação e exclusão protegida) para SEC 17a-4 (f) e outra conformidade regulatória. É recomendável que você bloqueie a política em um período de tempo razoável, normalmente menos de 24 horas. O estado inicial de uma política de retenção baseada em tempo é *desbloqueado*, permitindo que você teste o recurso e faça alterações na política antes de bloqueá-lo. Embora o estado *desbloqueado* forneça proteção contra imutabilidade, não é recomendável usar o estado *desbloqueado* para qualquer outra finalidade que não seja a avaliação de recursos de curto prazo. 

Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os BLOBs no contêiner permanecerão no estado imutável durante o período de retenção *efetivo* . O período de retenção efetivo para os blobs existentes é igual à diferença entre a hora de criação dos blobs e o intervalo de retenção especificado pelo utilizador.

Para novos blobs, o período de retenção efetivo é igual ao intervalo de retenção especificado pelo utilizador. Dado que os utilizadores podem prolongar o intervalo de retenção, o armazenamento imutável utiliza o valor mais recente do intervalo de retenção especificado pelo utilizador para calcular o período de retenção efetivo.

Por exemplo, suponha que um usuário crie uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos. Um blob existente nesse contêiner, _testblob1_, foi criado há um ano atrás. O período de retenção efetivo para _testblob1_ é de quatro anos. Quando um novo BLOB, _testblob2_, é carregado no contêiner, o período de retenção efetivo para o novo BLOB é de cinco anos.

Uma política de retenção baseada em tempo desbloqueada é recomendada apenas para teste de recursos e uma política deve ser bloqueada para ser compatível com a SEC 17a-4 (f) e outra conformidade regulatória. Quando uma política de retenção baseada em tempo é bloqueada, a política não pode ser removida e um máximo de cinco aumentos no período de retenção efetivo é permitido. Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas em tempo, consulte [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md).

Os seguintes limites se aplicam às políticas de retenção:

- Para uma conta de armazenamento, o número máximo de contêineres com políticas imutáveis baseadas em tempo bloqueado é de 1.000.
- O intervalo de retenção mínimo é de um dia. O máximo é de 146.000 dias (400 anos).
- Para um contêiner, o número máximo de edições para estender um intervalo de retenção para políticas imutáveis baseadas em tempo bloqueado é 5.
- Para um contêiner, um máximo de sete logs de auditoria de política de retenção baseadas em tempo são mantidos para uma política bloqueada.

### <a name="legal-holds"></a>Retenções legais

Quando você define uma retenção legal, todos os BLOBs existentes e novos permanecem no estado imutável até que a retenção legal seja apagada. Para obter mais informações sobre como definir e limpar as isenções legais, consulte [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md).

Um contêiner pode ter um controle legal e uma política de retenção baseada em tempo ao mesmo tempo. Todos os blobs no contentor permanecem no estado imutável até que todas as retenções legais sejam eliminadas, mesmo que o respetivo período de retenção efetivo tenha expirado. Por outro lado, um blob permanece num estado imutável até que o período de retenção efetivo expire, apesar de todas as retenções legais terem sido eliminadas.

A tabela a seguir mostra os tipos de operações de armazenamento de BLOBs que estão desabilitadas para os diferentes cenários imutáveis. Para obter mais informações, consulte a documentação da [API REST do serviço blob do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Cenário  |Estado do blob  |Operações de BLOB não permitidas  |
|---------|---------|---------|
|O intervalo de retenção efetivo do blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra eliminação e escrita         | Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, excluir contêiner, excluir BLOB, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo         |
|O intervalo de retenção efetivo no blob expirou     |Protegido apenas contra escrita (as operações de eliminação são permitidas)         |Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo         |
|Todas as contenções legais foram limpas e nenhuma política de retenção baseada em tempo está definida no contêiner     |Mutável         |Nenhuma         |
|Nenhuma política de WORM é criada (retenção baseada em tempo ou espera legal)     |Mutável         |Nenhuma         |

<sup>1</sup> o aplicativo permite que essas operações criem um novo BLOB uma vez. Todas as operações de substituição subsequentes em um caminho de blob existente em um contêiner imutável não são permitidas.

Os seguintes limites se aplicam a isenções legais:

- Para uma conta de armazenamento, o número máximo de contêineres com uma configuração de retenção legal é 1.000.
- Para um contêiner, o número máximo de marcas de retenção legal é 10.
- O comprimento mínimo de uma marca de retenção legal é de três caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contêiner, um máximo de 10 logs de auditoria de política de retenção legal são mantidos durante a política.

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso. Os dados imutáveis são cobrados da mesma maneira que os dados mutáveis. Para obter detalhes de preços no armazenamento de BLOBs do Azure, consulte a [página de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>FAQ

**Você pode fornecer documentação sobre a conformidade do WORM?**

Sim. Para documentar a conformidade, a Microsoft manteve uma importante empresa de avaliação independente especializada em gerenciamento de registros e governança de informações, Cohasset Associates, para avaliar o armazenamento de BLOBs imutável e sua conformidade com os requisitos específicos para o setor de serviços financeiros. Cohasset validou que o armazenamento de blob imutável, quando usado para manter BLOBs baseados em tempo em um estado de WORM, atende aos requisitos de armazenamento relevantes do CFTC Rule 1.31 (c)-(d), à regra de FINRA 4511 e à norma SEC 17a-4. A Microsoft direcionou esse conjunto de regras, pois representam as diretrizes mais prescritivas globalmente para retenção de registros para instituições financeiras. O relatório Cohasset está disponível na [central de confiabilidade do serviço da Microsoft](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade do WORM, entre em contato com o suporte do Azure.

**O recurso se aplica apenas a blobs de blocos ou a páginas e a blobs de acréscimo também?**

O armazenamento imutável pode ser usado com qualquer tipo de blob como definido no nível de contêiner, mas recomendamos que você use WORM para contêineres que armazenam principalmente blobs de bloco. Ao contrário de blobs de bloco, todos os blobs de página e blobs de acréscimo precisam ser criados fora de um contêiner de WORM e, em seguida, copiados no. Depois de copiar esses BLOBs em um contêiner de WORM, não são permitidos *acréscimos* adicionais a um blob de acréscimo ou alterações em um blob de páginas. Definir uma política de WORM em um contêiner que armazena VHDs (BLOBs de páginas) para qualquer máquina virtual ativa é desencorajado, pois ele bloqueará o disco da VM.

**É necessário criar uma nova conta de armazenamento para usar esse recurso?**

Não, você pode usar o armazenamento imutável com todas as contas de armazenamento de BLOB ou de uso geral existentes ou recém-criadas. Esse recurso destina-se ao uso com blobs de blocos em contas de armazenamento de BLOBs e GPv2. Contas de armazenamento v1 de uso geral não têm suporte, mas podem ser facilmente atualizadas para uso geral v2. Para obter informações sobre como atualizar uma conta de armazenamento v1 de uso geral existente, consulte [atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

**Posso aplicar uma política de retenção baseada em tempo e em retenção legal?**

Sim, um contêiner pode ter um controle legal e uma política de retenção baseada em tempo ao mesmo tempo. Todos os blobs no contentor permanecem no estado imutável até que todas as retenções legais sejam eliminadas, mesmo que o respetivo período de retenção efetivo tenha expirado. Por outro lado, um blob permanece num estado imutável até que o período de retenção efetivo expire, apesar de todas as retenções legais terem sido eliminadas.

**As políticas de retenção legal são apenas para procedimentos legais ou existem outros cenários de uso?**

Não, a retenção legal é apenas o termo geral usado para uma política de retenção não baseada em tempo. Ele não precisa ser usado apenas para procedimentos relacionados a litígios. As políticas de retenção legal são úteis para desabilitar a substituição e as exclusões para proteger dados importantes do WORM corporativo, em que o período de retenção é desconhecido. Você pode usá-lo como uma política corporativa para proteger suas cargas de trabalho de WORMs de missão crítica ou usá-las como uma política de preparo antes que um gatilho de evento personalizado exija o uso de uma política de retenção baseada em tempo. 

**Posso remover uma política de retenção baseada em tempo _bloqueada_ ou uma retenção legal?**

Somente políticas de retenção baseadas em tempo desbloqueadas podem ser removidas de um contêiner. Quando uma política de retenção baseada em tempo é bloqueada, ela não pode ser removida; somente as extensões de período de retenção efetivas são permitidas. As marcas de retenção legal podem ser excluídas. Quando todas as marcas legais são excluídas, a retenção legal é removida.

**O que acontece se tentar eliminar um contentor com uma política de retenção baseada no tempo ou de retenção legal *bloqueada*?**

A operação de exclusão de contêiner falhará se pelo menos um blob existir com uma política de retenção baseada em tempo bloqueada ou uma retenção legal. A operação excluir contêiner só terá sucesso se nenhum blob com um intervalo de retenção ativo existir e se não houver nenhuma isenção legal. Você deve excluir os BLOBs antes de poder excluir o contêiner.

**O que acontece se tentar eliminar uma conta de armazenamento com um contentor WORM que tenha uma política de retenção baseada no tempo ou de retenção legal *bloqueada*?**

A eliminação da conta de armazenamento falhará se existir, pelo menos, um contentor WORM com uma retenção legal ou um blob com um intervalo de retenção ativo. Você deve excluir todos os contêineres de WORM antes de excluir a conta de armazenamento. Para obter informações sobre a exclusão de contêiner, consulte a pergunta anterior.

**Posso mover os dados entre camadas de blob diferentes (frequente, esporádico, progressivo) quando o blob está no estado imutável?**

Sim, você pode usar o comando definir camada de BLOB para mover dados entre as camadas de BLOB e, ao mesmo tempo, manter os dados no estado imutável em conformidade. O armazenamento imutável tem suporte em camadas de blob quentes, frias e de arquivo morto.

**O que acontece se eu não conseguir pagar e o meu intervalo de retenção não tiver expirado?**

No caso de não pagamento, as políticas de retenção de dados normais serão aplicadas conforme estipulado nos termos e condições de seu contrato com a Microsoft.

**Oferecem um período de avaliação gratuita ou de tolerância para experimentar a funcionalidade?**

Sim. Quando uma política de retenção baseada em tempo é criada pela primeira vez, ela está em um estado *desbloqueado* . Nesse estado, você pode fazer qualquer alteração desejada no intervalo de retenção, como aumentar ou diminuir e até mesmo excluir a política. Depois que a política estiver bloqueada, ela permanecerá bloqueada até que o intervalo de retenção expire. Essa política bloqueada impede a exclusão e a modificação no intervalo de retenção. É altamente recomendável que você use o estado *desbloqueado* somente para fins de avaliação e bloqueie a política em um período de 24 horas. Essas práticas ajudam você a cumprir com a SEC 17a-4 (f) e outras regulamentações.

**Posso usar a exclusão reversível juntamente com políticas de blob imutáveis?**

Sim. A [exclusão reversível para o armazenamento de BLOBs do Azure](storage-blob-soft-delete.md) aplica-se a todos os contêineres em uma conta de armazenamento, independentemente de uma política de retenção baseada em tempo ou em retenção É recomendável habilitar a exclusão reversível para proteção adicional antes de qualquer política de WORM imutável ser aplicada e confirmada.

**Onde o recurso está disponível?**

O armazenamento imutável está disponível nas regiões pública, China e governamental do Azure. Se o armazenamento imutável não estiver disponível em sua região, entre em contato com o suporte e azurestoragefeedback@microsoft.comde email.

## <a name="next-steps"></a>Passos seguintes

[Definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md)