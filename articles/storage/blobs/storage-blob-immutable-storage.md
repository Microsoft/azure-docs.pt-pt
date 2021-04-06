---
title: Armazenamento imutável de bolhas - Azure Storage
description: O Azure Storage oferece suporte WORM (Write Once, Read Many) para armazenamento Blob (objeto) que permite aos utilizadores armazenar dados num estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: d1d77e508e627520878dcc27b5a643473d11dd1d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800725"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Armazenar dados de blobs críticos para a empresa com o armazenamento imutável

O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo utilizador. Durante o intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou eliminadas. O armazenamento imutável está disponível para as contas v1, v2, blobstorage e BlockBlobStorage em todas as regiões de Azure.

Para obter informações sobre como definir e limpar os sistemas legais ou criar uma política de retenção baseada no tempo utilizando o portal Azure, PowerShell ou Azure CLI, consulte [set e gerir políticas de imutabilidade para armazenamento blob](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Sobre o armazenamento imutável blob

O armazenamento imutável ajuda a organização de cuidados de saúde, instituições financeiras e indústrias &mdash; relacionadas, particularmente as organizações de &mdash; corretores, a armazenar dados de forma segura. O armazenamento imutável também pode ser alavancado em qualquer cenário para proteger dados críticos contra modificações ou eliminação.

As aplicações típicas incluem:

- **Conformidade regulamentar**: O armazenamento imutável para o armazenamento da Azure Blob ajuda as organizações a abordar a SEC 17a-4(f), CFTC 1.31(d), FINRA, e outros regulamentos. Um livro técnico da Cohasset Associates detalha como o armazenamento imutável aborda estes requisitos regulamentares através do [Portal Microsoft Service Trust](https://aka.ms/AzureWormStorage). O [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre as nossas certificações de conformidade.

- **Retenção segura de documentos**: O armazenamento imutável para o armazenamento Azure Blob garante que os dados não podem ser modificados ou eliminados por qualquer utilizador, incluindo utilizadores com privilégios administrativos de conta.

- **Detenção legal**: O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem informações sensíveis que sejam fundamentais para litígios ou uso de negócios em estado de inviolidade durante a duração desejada até que o porão seja removido. Esta funcionalidade não se limita apenas a casos de uso legal, mas também pode ser considerada como um porão baseado em eventos ou um bloqueio de empresa, onde é necessária a necessidade de proteger dados com base em gatilhos de eventos ou política corporativa.

O armazenamento imutável suporta as seguintes características:

- **[Suporte à política de retenção baseada no tempo](#time-based-retention-policies)**: Os utilizadores podem definir políticas para armazenar dados para um intervalo especificado. Quando uma política de retenção baseada no tempo é definida, as bolhas podem ser criadas e lidas, mas não modificadas ou eliminadas. Após o período de retenção ter expirado, as bolhas podem ser eliminadas, mas não substituídas.

- **[Suporte jurídico da política](#legal-holds)**: Se o intervalo de retenção não for conhecido, os utilizadores podem definir suportes legais para armazenar dados imutáveis até que o porão legal seja apurado.  Quando uma política legal de detenção é definida, as bolhas podem ser criadas e lidas, mas não modificadas ou eliminadas. Cada porão legal está associado a uma etiqueta alfanumérica definida pelo utilizador (como um ID de caso, nome do evento, etc.) que é usada como uma cadeia de identificador. 

- **Suporte para todos os escalões de blobs**: as políticas WORM são independentes da camada de armazenamento de Blobs do Azure e aplicam-se a todas as camadas: frequentes, esporádicas e de arquivo. Os utilizadores podem migrar os dados para a camada com o custo mais otimizado para as cargas de trabalho e manter em simultâneo a imutabilidade dos dados.

- **Configuração ao nível do contentor**: Os utilizadores podem configurar políticas de retenção baseadas no tempo e etiquetas legais de detenção ao nível do contentor. Com definições de nível de contentor simples, os utilizadores podem criar e bloquear as políticas de retenção baseadas no tempo, expandir intervalos de retenção, definir e desmarcar retenções legais e mais. Estas políticas aplicam-se a todos os blobs no contentor, tanto novos como existentes. Para uma conta ativada pelo HNS, estas políticas aplicam-se também a todos os diretórios num contentor.

- **Suporte de registo de auditoria**: Cada recipiente inclui um registo de auditoria de política. Apresenta até sete comandos de retenção baseados no tempo para políticas de retenção baseadas no tempo bloqueado e contém o ID do utilizador, o tipo de comando, os selos de tempo e o intervalo de retenção. Para retenções legais, o registo contém o ID de utilizador, o tipo de comando, os carimbos de data/hora e as etiquetas de retenção legal. Este registo é mantido durante o tempo de vida da apólice, de acordo com as diretrizes regulamentares SEC 17a-4 f. O [Registo de Atividades Azure](../../azure-monitor/essentials/platform-logs-overview.md) mostra um registo mais abrangente de todas as atividades do plano de controlo; ao permitir que os [Registos de Recursos Azure](../../azure-monitor/essentials/platform-logs-overview.md) retenha e mostre operações de plano de dados. É da responsabilidade do utilizador armazenar esses registos de forma persistente, consoante seja preciso por motivos de regulamentação ou outros fins.

## <a name="how-it-works"></a>Como funciona

O armazenamento imutável do Armazenamento de blobs do Azure suporta dois tipos de políticas WORM ou imutáveis: retenção baseada no tempo e retenções legais. Quando uma política de retenção baseada no tempo ou um porão legal é aplicado num recipiente, todas as bolhas existentes movem-se para um estado WORM imutável em menos de 30 segundos. Todas as novas bolhas que são enviadas para esse contentor protegido pela política também irão para um estado imutável. Uma vez que todas as bolhas estejam em estado imutável, a política imutável é confirmada e não são permitidas quaisquer operações de substituição ou eliminação no recipiente imutável. No caso de uma conta ativada pelo HNS, as bolhas não podem ser renomeadas ou transferidas para um diretório diferente.

A supressão da conta de contentores e armazenamento também não é permitida se houver bolhas num recipiente que estejam protegidas por um porão legal ou por uma política baseada no tempo bloqueado. Uma política legal de detenção protegerá contra a eliminação de blob, contentor e armazenamento de contas. Tanto as políticas desbloqueadas como bloqueadas no tempo protegerão contra a eliminação de bolhas durante o tempo especificado. As políticas desbloqueadas e bloqueadas baseadas no tempo só protegerão contra a eliminação do contentor se existir pelo menos uma bolha dentro do recipiente. Apenas um contentor com uma política baseada no tempo *bloqueado* protegerá contra supressões de conta de armazenamento; os contentores com políticas de tempo desbloqueadas não oferecem proteção nem conformidade com a conta de armazenamento.

O diagrama que se segue mostra como as políticas de retenção baseadas no tempo e os detenções legais impedem a escrita e a eliminação das operações enquanto estão em vigor.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Diagrama que mostra como as políticas de retenção e os detém legais impedem a escrita e a eliminação de operações":::

Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas no tempo, consulte Definir e gerir políticas de [imutabilidade para armazenamento de Blob](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Políticas de retenção baseadas no tempo

> [!IMPORTANT]
> Uma política de retenção baseada no tempo deve ser *bloqueada* para que a bolha se encontre num estado imutável (escrever e apagar protegido) para a SEC 17a-4 f e outra conformidade regulamentar. A Microsoft recomenda que bloqueie a apólice num período de tempo razoável, normalmente inferior a 24 horas. O estado inicial de uma política de retenção baseada no tempo aplicado é *desbloqueado*, permitindo-lhe testar a funcionalidade e fazer alterações na política antes de a bloquear. Embora o estado *desbloqueado* preveie uma proteção de imutabilidade, não é recomendado utilizar o estado *desbloqueado* para qualquer outra finalidade que não os ensaios de funcionalidades de curto prazo.
>
> Uma vez bloqueada uma política de retenção baseada no tempo, a política não pode ser eliminada e é permitido um máximo de cinco aumentos para o período de retenção eficaz. O período de retenção não pode ser diminuído.

Quando uma política de retenção baseada no tempo é aplicada num recipiente, todas as bolhas no recipiente permanecerão no estado imutável durante o período de retenção *eficaz.* O período de retenção eficaz para as bolhas é igual à diferença entre o tempo de **criação** do blob e o intervalo de retenção especificado pelo utilizador. Dado que os utilizadores podem prolongar o intervalo de retenção, o armazenamento imutável utiliza o valor mais recente do intervalo de retenção especificado pelo utilizador para calcular o período de retenção efetivo.

Por exemplo, suponha que um utilizador cria uma política de retenção baseada no tempo com um intervalo de retenção de cinco anos. Uma bolha existente naquele recipiente, _testblob1,_ foi criada há um ano; assim, o período de retenção eficaz para _o testblob1_ é de quatro anos. Quando uma nova bolha, _testblob2,_ é carregada para o recipiente, o período de retenção eficaz para o _testblob2_ é de cinco anos a partir do momento da sua criação.

Recomenda-se uma política de retenção baseada no tempo desbloqueada apenas para testes de recursos e uma política deve ser bloqueada para estar em conformidade com a SEC 17a-4(f) e outras conformidades regulamentares.

Aplicam-se os seguintes limites às políticas de retenção:

- Para uma conta de armazenamento, o número máximo de contentores com políticas imutáveis baseadas no tempo é de 10.000.
- O intervalo mínimo de retenção é de 1 dia. O máximo é de 146.000 dias (400 anos).
- Para um recipiente, o número máximo de edições para estender um intervalo de retenção para políticas imutáveis baseadas no tempo bloqueado é 5.
- Para um contentor, são mantidos no máximo sete registos de auditoria de política de retenção baseados no tempo para uma política bloqueada.

### <a name="allow-protected-append-blobs-writes"></a>Permitir que as bolhas de apêndice protegidas escrevam

As bolhas de apêndice são compostas por blocos de dados e otimizadas para operações de apêndice de dados exigidas por cenários de auditoria e registo. Por design, as bolhas de apêndice apenas permitem a adição de novos blocos até ao fim da bolha. Independentemente da imutabilidade, a modificação ou eliminação dos blocos existentes num apêndice não é fundamentalmente permitida. Para saber mais sobre as bolhas de apêndice, consulte [Sobre as bolhas de apêndice.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)

Apenas as políticas de retenção baseadas no tempo têm uma `allowProtectedAppendWrites` configuração que permite escrever novos blocos a uma bolha de apêndice, mantendo a proteção e a conformidade da imutabilidade. Se esta definição estiver ativada, é-lhe permitido criar uma bolha de apêndice diretamente no recipiente protegido pela política e continuar a adicionar novos blocos de dados ao fim das bolhas de apêndice existentes utilizando a API do *Apêndice.* Apenas podem ser adicionados novos blocos e os blocos existentes não podem ser modificados ou eliminados. A proteção da imutabilidade da retenção de tempo ainda se aplica, evitando a supressão da bolha do apêndice até que o período de retenção eficaz tenha decorrido. Ativar esta definição não afeta o comportamento de imutabilidade de bolhas de blocos ou bolhas de página.

Uma vez que esta definição faz parte de uma política de retenção baseada no tempo, as bolhas de apêndice permanecem no estado imutável durante o período de retenção *eficaz.* Uma vez que os novos dados podem ser anexados para além da criação inicial do apêndice blob, há uma ligeira diferença na forma como o período de retenção é determinado. A retenção eficaz é a diferença entre o último tempo de **modificação** do apêndice e o intervalo de retenção especificado pelo utilizador. Da mesma forma, quando o intervalo de retenção é prolongado, o armazenamento imutável utiliza o valor mais recente do intervalo de retenção especificado pelo utilizador para calcular o período de retenção eficaz.

Por exemplo, suponha que um utilizador cria uma política de retenção baseada no tempo com `allowProtectedAppendWrites` um intervalo de retenção de 90 dias. Uma bolha de apêndice, _logblob1,_ é criada no recipiente hoje, novos troncos continuam a ser adicionados à bolha do apêndice durante os próximos 10 dias; assim, o período de retenção efetiva para o _logblob1_ é de 100 dias a partir de hoje (a hora do seu último apêndice + 90 dias).

As políticas de retenção baseadas no tempo desbloqueadas permitem `allowProtectedAppendWrites` ativar e desativar a qualquer momento. Uma vez bloqueada a política de retenção baseada no tempo, a `allowProtectedAppendWrites` definição não pode ser alterada.

As políticas legais de detenção não podem permitir `allowProtectedAppendWrites` e quaisquer detençãos legais anularão a propriedade "allowProtectedAppendWrites". Se for aplicada uma posição legal a uma política de retenção baseada no tempo com `allowProtectedAppendWrites` ativação, a API *do Apêndice* falhará até que o porão legal seja levantado.

## <a name="legal-holds"></a>Retenções legais

Os detenhões legais são de porões temporários que podem ser usados para fins de investigação legal ou políticas gerais de proteção. Cada política de detenção legal tem de ser associada a uma ou mais etiquetas. As etiquetas são usadas como um identificador nomeado, como um ID ou evento de caso, para categorizar e descrever o propósito do porão.

Um contentor pode ter simultaneamente uma política de retenção legal e baseada no tempo. Todos os blobs no contentor permanecem no estado imutável até que todas as retenções legais sejam eliminadas, mesmo que o respetivo período de retenção efetivo tenha expirado. Por outro lado, um blob permanece num estado imutável até que o período de retenção efetivo expire, apesar de todas as retenções legais terem sido eliminadas.

Aplicam-se os seguintes limites aos detém legais:

- Para uma conta de armazenamento, o número máximo de contentores com uma fixação legal é de 10.000.
- Para um contentor, o número máximo de etiquetas de porão legal é de 10.
- O comprimento mínimo de uma etiqueta legal é de três caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contentor, um máximo de 10 registos de auditoria de política legal são mantidos durante a duração da apólice.

## <a name="scenarios"></a>Cenários

A tabela seguinte mostra os tipos de operações de armazenamento Blob que são desativadas para os diferentes cenários imutáveis. Para mais informações, consulte a documentação da [Azure Blob Service REST API.](/rest/api/storageservices/blob-service-rest-api)

| Scenario | Estado blob | Operações de blob negadas | Proteção de contentores e conta |
|--|--|--|--|
| O intervalo de retenção efetivo do blob ainda não expirou e/ou a retenção legal está definida | Imutável: protegido contra eliminação e escrita | Colocar Blob<sup>1</sup>, Colocar Bloco<sup>1</sup>, Colocar Bloco Lista<sup>1</sup>, Eliminar Recipiente, Eliminar Blob, Definir Metadados Blob, Put Page, Definir Propriedades blob, Bolha instantânea, Bolha de cópia incremental, Bloco de Apêndice<sup>2</sup> | Supressão de contentores negada; Supressão da conta de armazenamento negada |
| O intervalo de retenção eficaz na bolha expirou e não está definido qualquer porão legal | Protegido apenas contra escrita (as operações de eliminação são permitidas) | Colocar Blob<sup>1</sup>, Colocar Bloco<sup>1</sup>, Colocar Lista de Bloco<sup>1</sup>, Definir Metadados Blob, Put Page, Definir Propriedades blob, Bolha snapshot, Blob de Cópia Incremental, Bloco de Apêndice<sup>2</sup> | Supressão do contentor negada se existir pelo menos 1 bolha dentro de recipiente protegido; Supressão da conta de armazenamento negada apenas para políticas baseadas no tempo *bloqueado* |
| Nenhuma política worm aplicada (sem retenção baseada no tempo e sem etiqueta de detenção legal) | Mutável | Nenhum | Nenhum |

<sup>1</sup> O serviço blob permite que estas operações criem uma nova bolha uma vez. Não são permitidas todas as operações subsequentes de substituição num caminho de bolha existente num recipiente imutável.

<sup>2</sup> O Bloco de Apêndice só é permitido para políticas de retenção baseadas no tempo com a `allowProtectedAppendWrites` propriedade ativada. Para obter mais informações, consulte a secção ['Escrever blobs' de apêndice protegido.](#allow-protected-append-blobs-writes)

> [!IMPORTANT]
> Algumas cargas de trabalho, como [a SQL Backup para URL,](/sql/relational-databases/backup-restore/sql-server-backup-to-url)criam uma bolha e depois adicionam-na. Se o contentor tiver uma política de retenção ativa baseada no tempo ou se mantiver em vigor, este padrão não será bem sucedido.

## <a name="pricing"></a>Preços

Não existe qualquer custo adicional para a utilização desta funcionalidade. Os dados imutáveis têm o mesmo preço que os dados mutáveis. Para obter detalhes sobre os preços no armazenamento da Azure Blob, consulte a [página de preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>FAQ

**Pode fornecer documentação sobre a conformidade com o WORM?**

Sim. Para documentar o cumprimento, a Microsoft reteve uma empresa líder de avaliação independente especializada em gestão de registos e governação de informação, a Cohasset Associates, para avaliar o armazenamento imutável da Blob e o seu cumprimento de requisitos específicos para a indústria de serviços financeiros. A Cohasset validou esse armazenamento imutável blob, quando utilizado para reter bolhas baseadas no tempo num estado WORM, satisfaz os requisitos de armazenamento relevantes da regra 1.31(c)-(d), finra regra 4511 e regra 17a-4 da SEC. A Microsoft direcionou este conjunto de regras, uma vez que representam a orientação mais prescritiva a nível global para a retenção de registos para as instituições financeiras. O relatório Cohasset está disponível no [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade com a imutabilidade do WORM, contacte o suporte da Azure.

**A funcionalidade aplica-se apenas a blobs de blocos e apetsos de apêndice, ou também a bolhas de página?**

O armazenamento imutável pode ser utilizado com qualquer tipo de bolha, uma vez que está definido ao nível do recipiente, mas recomendamos que utilize o WORM para recipientes que armazenam principalmente bolhas de blocos e bolhas de apêndice. As bolhas existentes num recipiente serão protegidas por uma nova política worm. Mas quaisquer novas bolhas de página precisam de ser criadas fora do recipiente WORM, e depois copiadas. Uma vez copiado para um recipiente WORM, não são permitidas mais alterações a uma bolha de página. A definição de uma política WORM num recipiente que armazena VHDs (bolhas de página) para quaisquer máquinas virtuais ativas é desencorajada, uma vez que bloqueia o disco VM. Recomendamos que reveja minuciosamente a documentação e teste os seus cenários antes de bloquear quaisquer políticas baseadas no tempo.

**Preciso de criar uma nova conta de armazenamento para utilizar esta funcionalidade?**

Não, pode utilizar armazenamento imutável com quaisquer contas v1 de uso geral, v2 de uso geral, BlobStorage ou BlockBlobStorage. As contas de armazenamento V1 para fins gerais são suportadas, mas recomendamos o upgrade para o V2 de uso geral de modo a que possa tirar partido de mais funcionalidades. Para obter informações sobre a atualização de uma conta de armazenamento V1 para fins gerais existentes, consulte [a atualização de uma conta de armazenamento](../common/storage-account-upgrade.md).

**Posso aplicar uma política de retenção baseada no tempo e no tempo?**

Sim, um contentor pode ter simultaneamente uma política de retenção legal e baseada no tempo; no entanto, a definição "permitirprotectedAppendWrites" não será aplicável até que o porão legal seja apurado. Todos os blobs no contentor permanecem no estado imutável até que todas as retenções legais sejam eliminadas, mesmo que o respetivo período de retenção efetivo tenha expirado. Por outro lado, um blob permanece num estado imutável até que o período de retenção efetivo expire, apesar de todas as retenções legais terem sido eliminadas. 

**As políticas legais de detenção são apenas para processos judiciais ou existem outros cenários de utilização?**

Não, o "Hold Legal" é apenas o termo geral usado para uma política de retenção não-baseada no tempo. Não precisa de ser utilizada apenas para processos relacionados com litígios. As políticas de Manutenção Legal são úteis para desativar a sobreposição e elimina para proteger dados importantes do WORM da empresa, onde o período de retenção é desconhecido. Pode usá-lo como uma política de empresa para proteger as cargas de trabalho críticas do WORM da sua missão ou usá-la como uma política de encenação antes que um gatilho de evento personalizado exija o uso de uma política de retenção baseada no tempo. 

**Posso remover uma política de retenção baseada no tempo _ou_ uma posição legal?**

Apenas as políticas de retenção desbloqueadas baseadas no tempo podem ser removidas de um recipiente. Uma vez bloqueada uma política de retenção baseada no tempo, não pode ser removida; apenas são permitidas extensões efetivas do período de retenção. As etiquetas legais podem ser eliminadas. Quando todas as etiquetas legais são eliminadas, o porão legal é removido.

**O que acontece se eu tentar apagar um contentor com uma política de retenção baseada no tempo ou com uma posição legal?**

A operação do Recipiente de Eliminação falhará se existir pelo menos uma bolha dentro do contentor com uma política de retenção bloqueada ou desbloqueada ou se o contentor tiver um porão legal. A operação do Recipiente de Eliminação só será bem sucedida se não existirem bolhas dentro do contentor e não existirem porões legais. 

**O que acontece se eu tentar apagar uma conta de armazenamento com um recipiente que tenha uma política de retenção baseada no tempo ou uma posição legal?**

A supressão da conta de armazenamento falhará se houver pelo menos um contentor com um porão legal ou uma política baseada no tempo **bloqueado.** Um recipiente com uma política de tempo desbloqueada não protege contra a eliminação da conta de armazenamento. Tem de remover todos os depósitos legais e eliminar todos os recipientes **bloqueados** antes de poder apagar a conta de armazenamento. Para obter informações sobre a eliminação do contentor, consulte a pergunta anterior. Também pode aplicar proteções adicionais para a sua conta de armazenamento com [fechaduras Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**Posso mover os dados através de diferentes camadas de bolhas (quente, fresco, arquivo) quando a bolha está no estado imutável?**

Sim, pode utilizar o comando set Blob Tier para mover dados através dos níveis blob, mantendo os dados no estado imutável em conformidade. O armazenamento imutável é suportado em camadas quentes, frias e de arquivo.

**O que acontece se eu não conseguir pagar e o meu intervalo de retenção não tiver expirado?**

No caso de não pagamento, as políticas normais de retenção de dados serão aplicadas conforme estipulado nos termos e condições do seu contrato com a Microsoft. Para obter informações gerais, consulte [a gestão de dados na Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Oferecem um período de avaliação gratuita ou de tolerância para experimentar a funcionalidade?**

Sim. Quando uma política de retenção baseada no tempo é criada pela primeira vez, está num estado *desbloqueado.* Neste estado, pode fazer qualquer alteração desejada para o intervalo de retenção, como aumentar ou diminuir e até mesmo apagar a política. Depois de a apólice estar bloqueada, fica bloqueada até que o intervalo de retenção expire. Esta política bloqueada impede a supressão e a modificação do intervalo de retenção. Recomendamos vivamente que utilize o estado *desbloqueado* apenas para fins experimentais e bloqueie a apólice num período de 24 horas. Estas práticas ajudam-no a cumprir com a SEC 17a-4(f) e outros regulamentos.

**Posso utilizar a eliminação suave ao lado das políticas de bolhas imutáveis?**

Sim, se os seus requisitos de conformidade permitirem a sua eliminação suave. [A eliminação suave para o armazenamento Azure Blob](./soft-delete-blob-overview.md) aplica-se a todos os contentores dentro de uma conta de armazenamento, independentemente de uma política de retenção por precaução legal ou baseada no tempo. Recomendamos que se promova a eliminação suave para proteção adicional antes de serem aplicadas e confirmadas quaisquer políticas imutáveis do WORM.

**Para uma conta ativada pelo HNS, posso mudar o nome ou mover uma bolha quando a bolha estiver no estado imutável?**
Não, tanto o nome como a estrutura do diretório são considerados dados importantes ao nível do contentor que não podem ser modificados uma vez que a política imutável está em vigor. O renome e o movimento só estão disponíveis para contas ativadas pelo HNS em geral.

## <a name="next-steps"></a>Passos seguintes

- [Definir e gerir políticas de imutabilidade para armazenamento blob](storage-blob-immutability-policies-manage.md)
- [Descreva regras para eliminar automaticamente os dados blob com a gestão do ciclo de vida](storage-lifecycle-management-concepts.md)
- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](./soft-delete-blob-overview.md)
- [Proteja subscrições, grupos de recursos e recursos com bloqueios do Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
