---
title: Armazenamento de blob imutável - Armazenamento Azure
description: O Azure Storage oferece suporte WORM (Write Once, Read Many) para o armazenamento blob (objeto) que permite aos utilizadores armazenar dados num estado não apagamento e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367623"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Armazenar dados de blob críticos de negócios com armazenamento imutável

O armazenamento imutável para armazenamento De Blob Azure permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagamentos e não modificáveis para um intervalo especificado pelo utilizador. Durante a duração do intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou eliminadas. O armazenamento imutável está disponível para contas v1 de uso geral, v2 de uso geral, BlobStorage e BlockBlobStorage em todas as regiões do Azure.

Para obter informações sobre como definir e limpar os deters legais ou criar uma política de retenção baseada no tempo usando o portal Azure, PowerShell ou Azure CLI, consulte set e gere as políticas de [imutabilidade para o armazenamento blob](storage-blob-immutability-policies-manage.md).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Sobre o armazenamento imutável blob

O armazenamento imutável ajuda a organização de cuidados de saúde, instituições financeiras e indústrias relacionadas&mdash;particularmente organizações de corretores&mdash;armazenar dados de forma segura. O armazenamento imutável também pode ser alavancado em qualquer cenário para proteger dados críticos contra modificações ou supressão.

As aplicações típicas incluem:

- **Conformidade regulamentar**: Armazenamento imutável para armazenamento De Blob Azure ajuda as organizações a abordar sec 17a-4(f), CFTC 1.31(d), FINRA, e outros regulamentos. Um livro branco técnico da Cohasset Associates detalha como o armazenamento imutável aborda estes requisitos regulamentares é descarregado através do [Portal microsoft Service Trust](https://aka.ms/AzureWormStorage). O [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre as nossas certificações de conformidade.

- **Conservação segura de documentos**: O armazenamento immutável para o armazenamento do Blob Azure garante que os dados não podem ser modificados ou eliminados por qualquer utilizador, incluindo utilizadores com privilégios administrativos de conta.

- **Detenção legal**: O armazenamento imutável para armazenamento de Blob Azure permite que os utilizadores armazenem informações sensíveis que sejam fundamentais para o contencioso ou uso de negócios num estado à prova de adulteração durante a duração pretendida até que o porão seja removido. Esta funcionalidade não se limita apenas a casos de uso legal, mas também pode ser considerada como um porão baseado em eventos ou um bloqueio de empresa, onde é necessária a necessidade de proteger dados baseados em gatilhos de eventos ou política corporativa.

O armazenamento imutável suporta as seguintes funcionalidades:

- Suporte à **[política de retenção baseado no tempo:](#time-based-retention-policies)** Os utilizadores podem definir políticas para armazenar dados para um intervalo determinado. Quando uma política de retenção baseada no tempo é definida, as bolhas podem ser criadas e lidas, mas não modificadas ou eliminadas. Após o período de retenção ter expirado, as bolhas podem ser eliminadas, mas não sobreescritas.

- Apoio à política de **[detenção legal](#legal-holds)** : Se o intervalo de retenção não for conhecido, os utilizadores podem definir os deveres legais para armazenar dados imutáveis até que o detenção legal seja apurado.  Quando uma política de detenção legal é definida, as bolhas podem ser criadas e lidas, mas não modificadas ou eliminadas. Cada porão legal está associado a uma etiqueta alfanumérica definida pelo utilizador (como um caso ID, nome de evento, etc.) que é usada como uma cadeia de identificador. 

- **Suporte para todos os níveis de blob**: As políticas WORM são independentes do nível de armazenamento Azure Blob e aplicam-se a todos os níveis: quente, fresco e arquivo. Os utilizadores podem transitar dados para o nível mais otimizado para os seus volumes de trabalho, mantendo simultaneamente a imutabilidade dos dados.

- **Configuração ao nível do contentor:** Os utilizadores podem configurar políticas de retenção baseadas no tempo e etiquetas legais de retenção ao nível do contentor. Utilizando configurações simples ao nível do contentor, os utilizadores podem criar e bloquear políticas de retenção baseadas no tempo, estender intervalos de retenção, definir e limpar os deters legais, e muito mais. Estas políticas aplicam-se a todas as bolhas no contentor, tanto existentes como novas.

- **Suporte de registo de auditoria**: Cada recipiente inclui um registo de auditoria de política. Ele mostra até sete comandos de retenção baseados no tempo para políticas de retenção baseadas no tempo bloqueados e contém o ID do utilizador, tipo de comando, selos de tempo e intervalo de retenção. Para os porões legais, o registo contém o ID do utilizador, o tipo de comando, os selos temporais e as etiquetas legais de retenção. Este registo é conservado durante toda a vida da apólice, de acordo com as diretrizes regulamentares sec 17a-4(f). O [Registo de Atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) mostra um registo mais abrangente de todas as atividades de planos de controlo; ao permitir que os [Registos de Diagnóstico Azure](../../azure-monitor/platform/platform-logs-overview.md) retenham e mostre operações de avião de dados. É da responsabilidade do utilizador armazenar esses registos de forma persistente, como pode ser exigido para fins regulamentares ou outros.

## <a name="how-it-works"></a>Como funciona

O armazenamento imutável para armazenamento De Blob Azure suporta dois tipos de políticas worm ou imutáveis: retenção baseada no tempo e detenção legal. Quando uma política de retenção baseada no tempo ou por detenção legal é aplicada num recipiente, todas as bolhas existentes se movem para um estado WORM imutável em menos de 30 segundos. Todas as novas bolhas que são enviadas para esse contentor protegido pela política também irão para um estado imutável. Uma vez que todas as bolhas estejam num estado imutável, a política imutável é confirmada e quaisquer operações de sobressaem ou apagar em recipienteico imutável não são permitidas.

A eliminação da conta de contentores e de armazenamento também não é permitida se houver bolhas num recipiente protegidopor um porão legal ou por uma política de tempo bloqueada. Uma política de detenção legal protegerá contra a eliminação de contas de blob, contentores e depósitos. As políticas desbloqueadas e bloqueadas baseadas no tempo protegerão contra a eliminação de bolhas durante o tempo especificado. As políticas desbloqueadas e bloqueadas baseadas no tempo protegerão apenas contra a eliminação do contentor se existir em mínimos contentores uma bolha. Apenas um contentor com uma política de tempo *bloqueada* protegerá contra a eliminação de contas de armazenamento; os recipientes com políticas desbloqueadas baseadas no tempo não oferecem proteção de eliminação de contas de armazenamento nem conformidade.

Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas no tempo, consulte definir e gerir políticas de [imutabilidade para armazenamento blob](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Políticas de retenção baseadas no tempo

> [!IMPORTANT]
> Uma política de retenção baseada no tempo deve ser *bloqueada* para que a bolha esteja num estado imutável conforme (escrever e eliminar protegido) para a SEC 17a-4(f) e outra conformidade regulamentar. Recomendamos que bloqueie a apólice num período de tempo razoável, normalmente menos de 24 horas. O estado inicial de uma política de retenção baseada no tempo aplicado está *desbloqueado,* permitindo-lhe testar a funcionalidade e fazer alterações na política antes de a bloquear. Embora o estado *desbloqueado* forneça proteção de imutabilidade, não recomendamos a utilização do estado *desbloqueado* para qualquer outro fim que não os ensaios de funcionalidades de curto prazo. 

Quando uma política de retenção baseada no tempo for aplicada num recipiente, todas as bolhas do recipiente permanecerão no estado imutável durante o período de retenção *efetiva.* O período de retenção efetiva das bolhas é igual à diferença entre o tempo de **criação** da bolha e o intervalo de retenção especificado pelo utilizador. Uma vez que os utilizadores podem estender o intervalo de retenção, o armazenamento imutável utiliza o valor mais recente do intervalo de retenção especificado pelo utilizador para calcular o período de retenção efetiva.

Por exemplo, suponha que um utilizador cria uma política de retenção baseada no tempo com um intervalo de retenção de cinco anos. Uma bolha existente naquele contentor, _testblob1,_ foi criada há um ano; assim, o período de retenção efetiva para _o testblob1_ é de quatro anos. Quando uma nova bolha, _testblob2_, é enviada para o recipiente, o período de retenção efetiva para o _testblob2_ é de cinco anos a partir do momento da sua criação.

Recomenda-se apenas uma política de retenção baseada no tempo desbloqueada para os testes de recurso e uma política deve ser bloqueada para poder estar em conformidade com a SEC 17a-4(f) e outras conformidades regulamentares. Uma vez bloqueada uma política de retenção baseada no tempo, a política não pode ser eliminada e é permitido um máximo de cinco aumentos para o período de retenção efetivo.

Os seguintes limites aplicam-se às políticas de retenção:

- Para uma conta de armazenamento, o número máximo de contentores com políticas imutáveis com tempo fechado é de 10.000.
- O intervalo mínimo de retenção é de 1 dia. O máximo é de 146.000 dias (400 anos).
- Para um recipiente, o número máximo de edimas para estender um intervalo de retenção para políticas imutáveis com base no tempo bloqueada é 5.
- Para um contentor, um máximo de sete registos de auditoria da política de retenção baseados no tempo são mantidos para uma política bloqueada.

### <a name="allow-protected-append-blobs-writes"></a>Permitir que as bolhas de apêndice protegidos escrevam

As bolhas de apêndice são compostas por blocos de dados e otimizadas para operações de apêndice de dados exigidas por cenários de auditoria e registo. Por design, as bolhas de apêndice apenas permitem a adição de novos blocos até ao fim da bolha. Independentemente da imutabilidade, a modificação ou eliminação dos blocos existentes numa bolha de apêndice não é fundamentalmente permitida. Para saber mais sobre as bolhas de apêndice, consulte [sobre as bolhas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)de apêndice .

Apenas as políticas de retenção baseadas no tempo têm uma definição `allowProtectedAppendWrites` que permite escrever novos blocos para uma bolha de apêndice, mantendo simultaneamente a proteção e conformidade da imutabilidade. Se ativado, é permitido criar uma bolha de apêndice diretamente no recipiente protegido pela política e continuar a adicionar novos blocos de dados ao fim das bolhas de apêndice existentes utilizando a API do *Apêndice.* Só podem ser adicionados novos blocos e quaisquer blocos existentes não podem ser modificados ou eliminados. A proteção contra a imutabilidade da retenção de tempo continua a aplicar-se, evitando a eliminação da bolha do apêndice até ao decorrido o período de retenção efetivo. Permitir esta definição não afeta o comportamento de imutabilidade de bolhas de blocoou bolhas de página.

Uma vez que esta definição faz parte de uma política de retenção baseada no tempo, as bolhas de apêndice permanecem no estado imutável durante o período de retenção *efetiva.* Uma vez que novos dados podem ser anexados para além da criação inicial da bolha do apêndice, há uma ligeira diferença na forma como o período de retenção é determinado. A retenção efetiva é a diferença entre o último tempo de **modificação** do apêndice blob e o intervalo de retenção especificado pelo utilizador. Da mesma forma, quando o intervalo de retenção é prolongado, o armazenamento imutável utiliza o valor mais recente do intervalo de retenção especificado pelo utilizador para calcular o período de retenção efetivo.

Por exemplo, suponha que um utilizador cria uma política de retenção baseada no tempo com `allowProtectedAppendWrites` habilitado e um intervalo de retenção de 90 dias. Uma bolha de apêndice, _logblob1,_ é criada no recipiente hoje, novos troncos continuam a ser adicionados à bolha do apêndice para os próximos 10 dias; assim, o período de retenção efetiva para o _logblob1_ é de 100 dias a partir de hoje (a hora do seu último apêndice + 90 dias).

As políticas de retenção baseadas no tempo desbloqueadas permitem que a definição de `allowProtectedAppendWrites` seja ativada e desativada a qualquer momento. Uma vez bloqueada a política de retenção baseada no tempo, a definição de `allowProtectedAppendWrites` não pode ser alterada.

As políticas legais de detenção não podem permitir `allowProtectedAppendWrites` e quaisquer deveres legais anularão a propriedade "allowProtectedAppendWrites". Se um porão legal for aplicado a uma política de retenção baseada no tempo com `allowProtectedAppendWrites` ativado, a API do *Bloco de Aplicação* falhará até que a detenção legal seja levantada.

## <a name="legal-holds"></a>Retenções legais

Os detém legais são porões temporários que podem ser usados para fins de investigação legal ou políticas gerais de proteção. Cada política de detenção legal tem de ser associada a uma ou mais etiquetas. As etiquetas são usadas como identificador nomeado, como um caso de identificação ou evento, para categorizar e descrever o propósito do porão.

Um contentor pode ter um porão legal e uma política de retenção baseada no tempo ao mesmo tempo. Todas as bolhas nesse contentor permanecem no estado imutável até que todos os deteres legais sejam limpos, mesmo que o seu período de retenção efetiva tenha expirado. Inversamente, uma bolha permanece num estado imutável até que o prazo de retenção efetiva expire, apesar de todos os deters legais terem sido ilibados.

Os seguintes limites aplicam-se aos deters legais:

- Para uma conta de armazenamento, o número máximo de contentores com uma fixação legal é de 10.000.
- Para um contentor, o número máximo de etiquetas legais é de 10.
- O comprimento mínimo de uma etiqueta legal é de três caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contentor, um máximo de 10 registos de auditoria de política legal são mantidos durante a duração da apólice.

## <a name="scenarios"></a>Cenários
A tabela que se segue mostra os tipos de operações de armazenamento blob que são desativadas para os diferentes cenários imutáveis. Para mais informações, consulte a documentação da API do [Serviço Blob Azure.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

|Cenário  |Estado blob  |Operações blob negadas  |Proteção de contentores e contas
|---------|---------|---------|---------|
|O intervalo de retenção efetivo do blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra eliminação e escrita         | Coloque blob<sup>1</sup>, Put Block<sup>1,</sup>Put Block List<sup>1,</sup>Delete Container, Delete Blob, set Blob Metadata, Put Page, set Blob Properties, Snapshot Blob, Incremental Copy Blob, Bloco de Apêndice<sup>2</sup>         |Eliminação do contentor negada; Eliminação da Conta de Armazenamento negada         |
|Intervalo de retenção eficaz na bolha expirou e não é definido qualquer porão legal    |Protegido apenas contra escrita (as operações de eliminação são permitidas)         |Coloque blob<sup>1</sup>, Put Block<sup>1,</sup>Put Block List<sup>1,</sup>set Blob Metadata, Put Page, set Blob Properties, Snapshot Blob, Incremental Copy Blob, Bloco de Apêndice<sup>2</sup>         |Eliminação do contentor negada se existir pelo menos 1 bolha dentro de recipiente protegido; Eliminação da Conta de Armazenamento negada apenas por políticas *bloqueadas* baseadas no tempo         |
|Nenhuma política WORM aplicada (sem retenção baseada no tempo e sem etiqueta legal)     |Mutável         |Nenhum         |Nenhum         |

<sup>1</sup> O serviço de blob permite que estas operações criem uma nova bolha uma vez. Não são permitidas todas as operações de sobrecarga subsequentes num caminho de bolha existente num contentor imutável.

<sup>2</sup> O Bloco de Apêndice só é permitido para políticas de retenção baseadas no tempo com a propriedade `allowProtectedAppendWrites` habilitada. Para mais informações, consulte a secção Permitir a aplicação de [abatimentos de apor](#allow-protected-append-blobs-writes) as bolhas.

## <a name="pricing"></a>Preços

Não existe nenhuma taxa adicional para a utilização desta funcionalidade. Os dados imutáveis têm um preço da mesma forma que os dados mutáveis. Para obter detalhes sobre o armazenamento do Blob Azure, consulte a página de preços do [Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>FAQ

**Pode fornecer documentação sobre a conformidade com o WORM?**

Sim. Para documentar a conformidade, a Microsoft manteve uma empresa de avaliação independente líder especializada em gestão de registos e governação de informação, Cohasset Associates, para avaliar o armazenamento imutável blob e o seu cumprimento com requisitos específicos do indústria de serviços financeiros. Cohasset validou que o armazenamento de Blob imutável, quando utilizado para reter bolhas baseadas no tempo num estado WORM, satisfaz os requisitos de armazenamento relevantes da regra 1.31(c) c,d, da regra FINRA 4511 e da regra 17a-4 da SEC. A Microsoft direcionou este conjunto de regras, uma vez que representam a orientação mais prescritiva a nível global para a retenção de registos para instituições financeiras. O relatório Cohasset está disponível no [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade com a imutabilidade do WORM, contacte o suporte do Azure.

**A funcionalidade aplica-se apenas a blocos de bolhas e aanexar bolhas, ou a bolhas de página também?**

O armazenamento imutável pode ser utilizado com qualquer tipo de bolha tal como está definido ao nível do recipiente, mas recomendamos que utilize WORM para recipientes que armazenem principalmente bolhas de blocos e blocos de apêndice. As bolhas existentes num recipiente serão protegidas por uma política de WORM recém-definida. Mas quaisquer novas bolhas de página precisam de ser criadas fora do recipiente WORM, e depois copiadas. Uma vez copiado para um recipiente WORM, não são permitidas mais alterações numa bolha de página. A definição de uma política WORM num recipiente que armazena VHDs (bolhas de página) para quaisquer máquinas virtuais ativas é desencorajada, uma vez que bloqueia o disco VM. Recomendamos que reveja cuidadosamente a documentação e teste os seus cenários antes de bloquear quaisquer políticas baseadas no tempo.

**Preciso de criar uma nova conta de armazenamento para usar esta funcionalidade?**

Não, pode utilizar armazenamento imutável com quaisquer contas v1 de uso geral existentes ou recém-criadas, v2 de propósito geral, BlobStorage ou BlockBlobStorage. As contas de armazenamento v1 de uso geral são suportadas, mas recomendamos a atualização para v2 de propósito geral, de modo a que possa tirar partido de mais funcionalidades. Para obter informações sobre a atualização de uma conta de armazenamento v1 de uso geral existente, consulte [atualizar uma conta](../common/storage-account-upgrade.md)de armazenamento .

**Posso aplicar uma política de retenção legal e baseada no tempo?**

Sim, um contentor pode ter uma posição legal e uma política de retenção baseada no tempo ao mesmo tempo; no entanto, a definição de "permitirAprotecção de AppendWrites" não se aplicará até que a detenção legal seja desmarcada. Todas as bolhas nesse contentor permanecem no estado imutável até que todos os deteres legais sejam limpos, mesmo que o seu período de retenção efetiva tenha expirado. Inversamente, uma bolha permanece num estado imutável até que o prazo de retenção efetiva expire, apesar de todos os deters legais terem sido ilibados. 

**As políticas legais são apenas para processos judiciais ou existem outros cenários de utilização?**

Não, a Legal Hold é apenas o termo geral usado para uma política de retenção sem tempo. Não precisa de ser utilizado apenas para processos relacionados com litígios. As políticas legais de detenção são úteis para desativar a sobreposição e eliminações para proteger dados importantes do WORM da empresa, onde o período de retenção é desconhecido. Pode usá-lo como uma política de empresa para proteger as cargas de trabalho críticas do WORM da sua missão ou usá-la como uma política de encenação antes de um gatilho de evento personalizado exigir o uso de uma política de retenção baseada no tempo. 

**Posso remover uma política de retenção baseada no tempo _ou_ uma espera legal?**

Apenas as políticas de retenção baseadas no tempo desbloqueadas podem ser removidas de um recipiente. Uma vez bloqueada uma política de retenção baseada no tempo, não pode ser suprimida; só são permitidas extensões eficazes do período de retenção. As etiquetas legais de espera podem ser eliminadas. Quando todas as etiquetas legais são eliminadas, o porão legal é removido.

**O que acontece se eu tentar apagar um contentor com uma política de retenção baseada no tempo ou por um domínio legal?**

A operação 'Eliminar contentores' falhará se existir pelo menos uma bolha dentro do contentor com uma política de retenção bloqueada ou desbloqueada baseada no tempo ou se o contentor tiver um porão legal. A operação "Eliminar contentores" só terá sucesso se não existirem bolhas dentro do contentor e não existirem deveres legais. 

**O que acontece se eu tentar apagar uma conta de armazenamento com um contentor que tenha uma política de retenção baseada no tempo ou um porão legal?**

A eliminação da conta de armazenamento falhará se houver pelo menos um contentor com um conjunto legal de espera ou uma política de tempo **bloqueada.** Um contentor com uma política desbloqueada baseada no tempo não protege contra a eliminação da conta de armazenamento. Deve remover todos os porões legais e eliminar todos os recipientes **bloqueados** antes de poder eliminar a conta de armazenamento. Para obter informações sobre a eliminação do contentor, consulte a pergunta anterior. Também pode aplicar mais proteções de eliminação para a sua conta de armazenamento com [fechaduras do Gestor de Recursos Azure](../../azure-resource-manager/management/lock-resources.md).

**Posso mover os dados através de diferentes níveis de blob (quente, fresco, arquivo) quando a bolha está em estado imutável?**

Sim, pode utilizar o comando set Blob Tier para mover dados através dos níveis de bolha, mantendo os dados no estado imutável conforme. O armazenamento imutável é suportado em camadas de bolhas quentes, frescas e de arquivo.

**O que acontece se eu não conseguir pagar e o meu intervalo de retenção não tiver expirado?**

Em caso de não pagamento, as políticas normais de retenção de dados serão aplicadas conforme estipulado nos termos e condições do seu contrato com a Microsoft. Para obter informações gerais, consulte a [gestão de dados na Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Oferecem um período de avaliação gratuita ou de tolerância para experimentar a funcionalidade?**

Sim. Quando uma política de retenção baseada no tempo é criada pela primeira vez, está num estado *desbloqueado.* Neste estado, pode fazer qualquer alteração desejada no intervalo de retenção, como aumentar ou diminuir e até mesmo apagar a política. Depois de a apólice estar bloqueada, mantém-se bloqueada até que o intervalo de retenção expire. Esta política bloqueada impede a eliminação e modificação do intervalo de retenção. Recomendamos vivamente que utilize o estado *desbloqueado* apenas para efeitos de julgamento e bloqueie a apólice num período de 24 horas. Estas práticas ajudam-no a cumprir com a SEC 17a-4(f) e outros regulamentos.

**Posso usar soft delete ao lado de políticas de blob immutáveis?**

Sim, se os seus requisitos de conformidade permitirem ativar a eliminação suave. [A eliminação suave para o armazenamento de Blob Azure](storage-blob-soft-delete.md) aplica-se a todos os contentores dentro de uma conta de armazenamento, independentemente de uma política legal de retenção ou baseada no tempo. Recomendamos que permita eliminar suavemente para proteção adicional antes que quaisquer políticas imutáveis do WORM sejam aplicadas e confirmadas.

## <a name="next-steps"></a>Passos seguintes

- [Definir e gerir políticas de imutabilidade para armazenamento blob](storage-blob-immutability-policies-manage.md)
- [Definir regras para tierificar automaticamente e eliminar dados blob com gestão de ciclo de vida](storage-lifecycle-management-concepts.md)
- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/storage-blob-soft-delete.md)
- [Proteja subscrições, grupos de recursos e recursos com fechaduras do Gestor de Recursos Azure.](../../azure-resource-manager/management/lock-resources.md)
