---
title: Armazenamento imutável para BLOBs de armazenamento do Azure | Microsoft Docs
description: O armazenamento do Azure oferece suporte de WORM (Write Once, Read Many) para armazenamento de BLOB (objeto) que permite aos usuários armazenar dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a3f9768043c9acef9640b8e286611de94e44a7ff
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910495"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Armazene dados críticos para os negócios no armazenamento de BLOBs do Azure

O armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem objetos de dados críticos para os negócios em um estado de WORM (gravar uma vez, ler muitos). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Os objetos de blob podem ser criados e lidos, mas não modificados ou excluídos, durante a duração do intervalo de retenção. O armazenamento imutável é habilitado para Uso Geral V2 e contas de armazenamento de BLOBs em todas as regiões do Azure.

## <a name="overview"></a>Descrição geral

O armazenamento imutável ajuda a organização de saúde, instituições financeiras e setores relacionados, especialmente as organizações de distribuidores de agente, para armazenar dados com segurança. Ele também pode ser aproveitado em qualquer cenário para proteger dados críticos contra modificação ou exclusão. 

As aplicações típicas incluem:

- **Conformidade regulatória**: O armazenamento imutável para o armazenamento de BLOBs do Azure ajuda as organizações a lidar com SEC 17a-4 (f), CFTC 1.31 (d), FINRA e outras regulamentações. Um White paper técnico da Cohasset Associates fornece detalhes sobre como o armazenamento imutável resolve esses requisitos regulatórios é baixável por meio do [portal de confiança do serviço da Microsoft](https://aka.ms/AzureWormStorage). O [central de confiabilidade do Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre nossas certificações de conformidade.

- **Retenção segura de documentos**: O armazenamento imutável para o armazenamento de BLOBs do Azure garante que os dados não possam ser modificados ou excluídos por qualquer usuário, incluindo os usuários com privilégios administrativos de conta.

- **Retenção legal**: O armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem informações confidenciais que são críticas para o uso de litígios ou negócios em um estado de prova de adulteração até a duração desejada até que a suspensão seja removida. Esse recurso não é limitado apenas a casos de uso jurídico, mas também pode ser considerado como uma suspensão baseada em evento ou um bloqueio corporativo, em que a necessidade de proteger dados com base em gatilhos de evento ou política corporativa é necessária.

O armazenamento imutável dá suporte ao seguinte:

- **[Suporte à política de retenção baseada em tempo](#time-based-retention)** : Os usuários podem definir políticas para armazenar dados para um intervalo especificado. Quando uma política de retenção baseada em tempo é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Depois que o período de retenção tiver expirado, os BLOBs poderão ser excluídos, mas não substituídos.

- **[Suporte de política de manutenção legal](#legal-holds)** : Se o intervalo de retenção não for conhecido, os usuários poderão definir as isenções legais para armazenar dados immutably até que a retenção legal seja apagada.  Quando uma política de manutenção legal é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Cada retenção legal é associado a uma marca alfanumérica definida pelo usuário (como uma ID de caso, um nome de evento, etc.) que é usada como uma cadeia de caracteres de identificador. 

- **Suporte para todas as camadas de blob**: As políticas de WORM são independentes da camada de armazenamento de BLOBs do Azure e se aplicam a todas as camadas: frequente, fria e arquivo morto. Os usuários podem fazer a transição de dados para a camada mais otimizada para custos para suas cargas de trabalho, mantendo a imutabilidade dos dados.

- **Configuração em nível de contêiner**: Os usuários podem configurar políticas de retenção baseadas em tempo e marcas de espera legal no nível de contêiner. Usando configurações de nível de contêiner simples, os usuários podem criar e bloquear políticas de retenção baseadas em tempo, estender intervalos de retenção, definir e limpar as isenções legais e muito mais. Essas políticas se aplicam a todos os BLOBs no contêiner, existentes e novas.

- **Suporte ao log de auditoria**: Cada contêiner inclui um log de auditoria de política. Ele mostra até sete comandos de retenção baseados em tempo para políticas de retenção baseadas em tempo bloqueadas e contém a ID de usuário, o tipo de comando, os carimbos de data/hora e o intervalo de retenção. Para as isenções legais, o log contém a ID de usuário, o tipo de comando, os carimbos de data/hora e as marcas de retenção legais. Esse log é retido durante o tempo de vida da política, de acordo com as diretrizes regulatórias da SEC 17a-4 (f). O [log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) mostra um log mais abrangente de todas as atividades do plano de controle; ao habilitar [os logs de diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) , o mantém e mostra as operações do plano de dados. É responsabilidade do usuário armazenar esses logs de forma persistente, como pode ser necessário para fins regulatórios ou outras finalidades.

## <a name="how-it-works"></a>Como funciona

O armazenamento imutável para o armazenamento de BLOBs do Azure dá suporte a dois tipos de políticas de WORM ou imutáveis: retenção baseada em tempo e retenções legais. Quando uma política de retenção baseada em tempo ou uma retenção legal é aplicada em um contêiner, todos os BLOBs existentes são movidos para um estado de WORM imutável em menos de 30 segundos. Todos os novos BLOBs que são carregados nesse contêiner também serão movidos para o estado imutável. Depois que todos os BLOBs forem movidos para o estado imutável, a política imutável será confirmada e todas as operações de substituição ou exclusão para objetos novos e existentes no contêiner imutável não serão permitidas.

A exclusão de contêiner e conta também não será permitida se houver BLOBs protegidos por uma política imutável. A operação de exclusão de contêiner falhará se pelo menos um blob existir com uma política de retenção baseada em tempo bloqueada ou uma retenção legal. A eliminação da conta de armazenamento falhará se existir, pelo menos, um contentor WORM com uma retenção legal ou um blob com um intervalo de retenção ativo. 

### <a name="time-based-retention"></a>Retenção baseada em tempo

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve estar *bloqueada* para que o blob esteja em um estado compatível imutável (gravação e exclusão protegida) para SEC 17a-4 (f) e outra conformidade regulatória. É recomendável que você bloqueie a política em um período de tempo razoável, normalmente menos de 24 horas. O estado inicial de uma política de retenção baseada em tempo é *desbloqueado*, permitindo que você teste o recurso e faça alterações na política antes de bloqueá-lo. Embora o estado *desbloqueado* forneça proteção contra imutabilidade, não é recomendável usar o estado *desbloqueado* para qualquer outra finalidade que não seja a avaliação de recursos de curto prazo. 

Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os BLOBs no contêiner permanecerão no estado imutável durante o período de retenção *efetivo* . O período de retenção efetivo para BLOBs existentes é igual à diferença entre a hora da modificação do blob e o intervalo de retenção especificado pelo usuário.

Para novos blobs, o período de retenção efetivo é igual ao intervalo de retenção especificado pelo utilizador. Como os usuários podem estender o intervalo de retenção, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

> [!TIP]
> **Example:** Um usuário cria uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos.
>
> O blob existente nesse contêiner, _testblob1_, foi criado há um ano atrás. O período de retenção efetivo para _testblob1_ é de quatro anos.
>
> Um novo BLOB, _testblob2_, agora é carregado no contêiner. O período de retenção efetivo para esse novo BLOB é de cinco anos.

Uma política de retenção baseada em tempo desbloqueada é recomendada apenas para teste de recursos e uma política deve ser bloqueada para ser compatível com a SEC 17a-4 (f) e outra conformidade regulatória. Quando uma política de retenção baseada em tempo é bloqueada, a política não pode ser removida e um máximo de 5 aumentos no período de retenção efetivo é permitido. Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas em tempo, consulte a seção [introdução](#getting-started) .

### <a name="legal-holds"></a>Retenções legais

Quando você define uma retenção legal, todos os BLOBs existentes e novos permanecem no estado imutável até que a retenção legal seja apagada. Para obter mais informações sobre como definir e limpar as isenções legais, consulte a seção [introdução](#getting-started) .

Um contêiner pode ter um controle legal e uma política de retenção baseada em tempo ao mesmo tempo. Todos os BLOBs nesse contêiner permanecem no estado imutável até que todas as contenções legais sejam limpas, mesmo que seu período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, embora todas as isenções legais tenham sido limpas.

A tabela a seguir mostra os tipos de operações de BLOB que estão desabilitadas para os diferentes cenários imutáveis. Para obter mais informações, consulte a documentação da [API do serviço blob do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Cenário  |Estado do blob  |Operações de BLOB não permitidas  |
|---------|---------|---------|
|O intervalo de retenção efetivo do blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra eliminação e escrita         | Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, excluir contêiner, excluir BLOB, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo         |
|O intervalo de retenção efetivo no blob expirou     |Protegido apenas contra escrita (as operações de eliminação são permitidas)         |Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo         |
|Todas as contenções legais foram limpas e nenhuma política de retenção baseada em tempo está definida no contêiner     |Mutável         |Nenhum         |
|Nenhuma política de WORM é criada (retenção baseada em tempo ou espera legal)     |Mutável         |Nenhum         |

<sup>1</sup> o aplicativo permite que essas operações criem um novo BLOB uma vez. Todas as operações de substituição subsequentes em um caminho de blob existente em um contêiner imutável não são permitidas.

## <a name="supported-values"></a>Valores suportados

### <a name="time-based-retention"></a>Retenção baseada em tempo
- Para uma conta de armazenamento, o número máximo de contêineres com políticas imutáveis baseadas em tempo bloqueado é de 1.000.
- O intervalo de retenção mínimo é 1 dia. O máximo é de 146.000 dias (400 anos).
- Para um contêiner, o número máximo de edições para estender um intervalo de retenção para políticas imutáveis baseadas em tempo bloqueado é 5.
- Para um contêiner, um máximo de sete logs de auditoria de política de retenção com base em tempo são mantidos para uma política bloqueada.

### <a name="legal-hold"></a>Retenção legal
- Para uma conta de armazenamento, o número máximo de contêineres com uma configuração de retenção legal é 1.000.
- Para um contêiner, o número máximo de marcas de retenção legal é 10.
- O comprimento mínimo de uma marca de retenção legal é de 3 caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contêiner, um máximo de 10 logs de auditoria de política de retenção legal são mantidos durante a política.

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso. Os dados imutáveis são cobrados da mesma maneira que os dados regulares e mutáveis. Para obter detalhes de preços no armazenamento de BLOBs do Azure, consulte a [página de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Introdução
O armazenamento imutável está disponível somente para contas de armazenamento de BLOBs e Uso Geral v2. Essas contas devem ser gerenciadas por meio de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Para obter informações sobre como atualizar uma conta de armazenamento Uso Geral v1 existente, consulte [atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

As versões mais recentes do [portal do Azure](https://portal.azure.com), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)e [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) dão suporte ao armazenamento imutável para o armazenamento de BLOBs do Azure. O [suporte à biblioteca de cliente](#client-libraries) também é fornecido.

### <a name="azure-portal"></a>Portal do Azure

1. Crie um novo contentor ou selecione um contentor existente para armazenar os blobs que precisam de ser mantidos no estado imutável.
 O contêiner deve estar em uma conta de armazenamento de BLOBs ou de GPv2.
2. Selecione **política de acesso** nas configurações do contêiner. Em seguida, selecione **+ Adicionar política** em **armazenamento de BLOBs imutável**.

    ![Configurações de contêiner no portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, selecione **retenção baseada em tempo** no menu suspenso.

    !["Retenção baseada em tempo" selecionada em "tipo de política"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Insira o intervalo de retenção em dias (valores aceitáveis de 1 a 146000 dias).

    ![Caixa "atualizar o período de retenção para"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    O estado inicial da política é desbloqueado, permitindo que você teste o recurso e faça alterações na política antes de bloqueá-lo. O bloqueio da política é essencial para conformidade com regulamentos, como SEC 17a-4.

5. Bloquear a política. Clique com o botão direito do mouse nas reticências ( **...** ) e o menu a seguir aparecerá com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Selecione **Bloquear política** e confirmar o bloqueio. A política agora está bloqueada e não pode ser excluída, somente as extensões do intervalo de retenção serão permitidas. Não são permitidas exclusões e substituições de BLOB. 

    ![Confirmar "política de bloqueio" no menu](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Para habilitar as isenções legais, selecione **+ Adicionar política**. Selecione **reter legal** no menu suspenso.

    !["Retenção legal" no menu em "tipo de política"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Crie uma retenção legal com uma ou mais marcas.

    ![Caixa "nome da marca" sob o tipo de política](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Para limpar uma retenção legal, basta remover a marca de identificador de retenção legal aplicada.

### <a name="azure-cli"></a>CLI do Azure

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy` e `az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>PowerShell

O módulo AZ. Storage dá suporte ao armazenamento imutável.  Para habilitar o recurso, siga estas etapas:

1. Verifique se você tem a versão mais recente do PowerShellGet instalada `Install-Module PowerShellGet –Repository PSGallery –Force`:.
2. Remova qualquer instalação anterior do Azure PowerShell.
3. Instalar Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

A seção de [código do PowerShell de exemplo](#sample-powershell-code) mais adiante neste artigo ilustra o uso do recurso.

## <a name="client-libraries"></a>Bibliotecas de cliente

As bibliotecas de cliente a seguir dão suporte ao armazenamento imutável para o armazenamento de BLOBs do Azure:

- [Biblioteca de cliente .NET versão 7.2.0-Preview e posterior](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteca de cliente Node. js versão 4.0.0 e posterior](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteca de cliente Python versão 2.0.0 Release Candidate 2 e posterior](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Biblioteca de cliente Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>FAQ

**Você pode fornecer documentação sobre a conformidade do WORM?**

Sim. Para documentar a conformidade, a Microsoft manteve uma importante empresa de avaliação independente especializada em gerenciamento de registros e governança de informações, Cohasset Associates, para avaliar o armazenamento de BLOBs imutável do Azure e sua conformidade com os requisitos específicos para o setor de serviços financeiros. Cohasset validou que o armazenamento de blob imutável do Azure, quando usado para manter BLOBs baseados em tempo em um estado de WORM, atende aos requisitos de armazenamento relevantes do CFTC Rule 1.31 (c)-(d), à regra de FINRA 4511 e à norma SEC 17a-4. A Microsoft direcionou esse conjunto de regras, pois representam as diretrizes mais prescritivas globalmente para retenção de registros para instituições financeiras. O relatório Cohasset está disponível na [central de confiabilidade do serviço da Microsoft](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade do WORM, entre em contato com o suporte do Azure.

**O recurso se aplica apenas a blobs de blocos ou a páginas e a blobs de acréscimo também?**

O armazenamento imutável pode ser usado com qualquer tipo de blob como definido no nível de contêiner, mas recomendamos que você use WORM para contêineres que armazenam principalmente blobs de bloco. Ao contrário de blobs de bloco, todos os blobs de página e blobs de acréscimo precisam ser criados fora de um contêiner de WORM e, em seguida, copiados no. Depois de copiar esses BLOBs em um contêiner de WORM, não são permitidos *acréscimos* adicionais a um blob de acréscimo ou alterações em um blob de páginas. Portanto, a definição de uma política de WORM em um contêiner que armazena VHDs (BLOBs de páginas) para qualquer máquina virtual ativa é fortemente desencorajada, pois bloqueará o disco da VM.

**É necessário criar uma nova conta de armazenamento para usar esse recurso?**

Não, você pode usar o armazenamento imutável com todas as contas de armazenamento de BLOB ou Uso Geral v2 existentes ou recém-criadas. Esse recurso destina-se ao uso com blobs de blocos em contas de armazenamento de BLOBs e GPv2. Não há suporte para contas de armazenamento Uso Geral v1, mas elas podem ser facilmente atualizadas para Uso Geral v2. Para obter informações sobre como atualizar uma conta de armazenamento Uso Geral v1 existente, consulte [atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

**Posso aplicar uma política de retenção baseada em tempo e em retenção legal?**

Sim, um contêiner pode ter um controle legal e uma política de retenção baseada em tempo ao mesmo tempo. Todos os BLOBs nesse contêiner permanecem no estado imutável até que todas as contenções legais sejam limpas, mesmo que seu período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, embora todas as isenções legais tenham sido limpas.

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

O armazenamento imutável está disponível nas regiões pública, China e governamental do Azure. Se o armazenamento imutável não estiver disponível em sua região, entre em contato com o azurestoragefeedback@microsoft.comsuporte e o email.

## <a name="sample-powershell-code"></a>Código do PowerShell de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo de script do PowerShell a seguir é para referência. Esse script cria uma nova conta de armazenamento e um contêiner. Em seguida, ele mostra como definir e limpar as isenções legais, criar e bloquear uma política de retenção baseada em tempo (também conhecida como uma política de imutabilidade) e estender o intervalo de retenção.

Configurar e testar a conta de armazenamento do Azure:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Definir e limpar as isenções legais:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Criar ou atualizar políticas de imutabilidade:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Recuperar políticas de imutabilidade:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Bloquear políticas de imutabilidade (Add-Force para ignorar o prompt):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Estender políticas de imutabilidade:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Remover uma política de imutabilidade desbloqueada (Add-Force para ignorar o prompt):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
