---
title: Níveis de acesso quentes, frescos e de arquivo para blobs - Armazenamento Azure
description: Níveis de acesso quentes, frescos e de arquivo para contas de armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368714"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Armazenamento Azure Blob: hot, cool e archive access tiers

O armazenamento Azure oferece diferentes níveis de acesso, que lhe permitem armazenar dados de objetos blob da forma mais rentável. Os níveis de acesso disponíveis incluem:

- **Hot** - Otimizado para armazenar dados que são acedidos com frequência.
- **Cool** - Otimizado para armazenar dados que são raramente acedidos e armazenados durante pelo menos 30 dias.
- **Arquivo** - Otimizado para armazenar dados que raramente são acedidos e armazenados durante pelo menos 180 dias com requisitos flexíveis de latência (por ordem de horas).

As seguintes considerações aplicam-se aos diferentes níveis de acesso:

- Apenas os níveis de acesso quentes e frescos podem ser definidos ao nível da conta. O nível de acesso ao arquivo não está disponível ao nível da conta.
- Os níveis de calor, arrefecimento e arquivo podem ser definidos ao nível da bolha durante o upload ou após o upload.
- Os dados no nível de acesso fresco podem tolerar uma disponibilidade ligeiramente menor, mas ainda requerem alta durabilidade, latência de recuperação e características de entrada semelhantes a dados quentes. Para dados frescos, um acordo de nível de serviço de disponibilidade ligeiramente mais baixo (SLA) e custos de acesso mais elevados em comparação com os dados quentes são compensações aceitáveis para custos de armazenamento mais baixos.
- O arquivo armazena os dados offline e oferece os custos de armazenamento mais baixos, mas também os custos de rehidratação e acesso mais elevados.

Os dados armazenados na nuvem crescem a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes com base na forma como são gerados, processados e acedidos ao longo da sua vida útil. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e raramente são, se é que alguma vez, acedidos depois de armazenados.

Cada um destes cenários de acesso a dados beneficia de um nível de acesso diferente que é otimizado para um determinado padrão de acesso. Com níveis de acesso quentes, frescos e de arquivo, o armazenamento azure Blob aborda esta necessidade de níveis de acesso diferenciados com modelos de preços separados.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

O diferencial de dados de armazenamento de objetos entre o hot, o cool e o arquivo é suportado apenas nas contas de armazenamento Blob e General Purpose v2 (GPv2). As contas De Propósito Geral v1 (GPv1) não suportam o tiering. Os clientes podem facilmente converter as suas contas de armazenamento GPv1 ou Blob existentes para contas GPv2 através do portal Azure. O GPv2 fornece novos preços e funcionalidades para bolhas, ficheiros e filas. Algumas funcionalidades e cortes de preços só são oferecidos nas contas GPv2. Avalie a utilização de contas GPv2 após uma revisão exaustiva dos preços. Algumas cargas de trabalho podem ser mais caras em GPv2 do que GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

O armazenamento blob e as contas GPv2 expõem o atributo do **Access Tier** ao nível da conta. Este atributo permite especificar o nível de acesso padrão para qualquer bolha que não tenha o conjunto explícito ao nível do objeto. Para objetos com o nível definido ao nível do objeto, o nível de conta não se aplica. O nível de arquivo só pode ser aplicado ao nível do objeto. Pode alternar entre estes níveis de acesso a qualquer momento.

## <a name="hot-access-tier"></a>Escalão de acesso frequente

O nível de acesso quente tem custos de armazenamento mais elevados do que os níveis de refrigeração e arquivo, mas os custos de acesso mais baixos. Exemplos de utilização para o nível de acesso quente incluem:

- Dados que estejam em uso ativo ou que se espera que sejam acedidos (lidos e escritos) com frequência.
- Dados que são encenados para processamento e eventual migração para o nível de acesso fresco.

## <a name="cool-access-tier"></a>Escalão de acesso esporádico

O nível de acesso fresco tem custos de armazenamento mais baixos e custos de acesso mais elevados em comparação com o armazenamento quente. Esta camada destina-se de dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Os cenários de utilização de exemplo para o nível de acesso cool incluem:

- Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
- Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
- Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,* , armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de acesso de arquivo

O nível de acesso ao arquivo tem o menor custo de armazenamento. Mas tem custos de recuperação de dados mais elevados em comparação com os níveis quentes e frescos. Os dados no nível de arquivo podem demorar várias horas a recuperar. Os dados devem permanecer no nível de arquivo durante pelo menos 180 dias ou estar sujeitos a uma taxa de eliminação antecipada.

Enquanto uma bolha está no armazenamento de arquivo, os dados blob estão offline e não podem ser lidos, substituídos ou modificados. Para ler ou descarregar uma bolha no arquivo, deve primeiro reidratá-la para um nível online. Não se pode tirar fotos de uma bolha no armazém de arquivos. No entanto, os metadados blob permanecem online e disponíveis, permitindo-lhe listar a bolha e as suas propriedades. Para blobs em arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob e DeleteBlob. Consulte [os dados da bolha Rehydrate do nível de arquivo](storage-blob-rehydration.md) para saber mais.

Os cenários de utilização de exemplo para o nível de acesso ao arquivo incluem:

- Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
- Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final.
- Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos.

## <a name="account-level-tiering"></a>Tiering ao nível da conta

As bolhas nos três níveis de acesso podem coexistir dentro da mesma conta. Qualquer bolha que não tenha um nível explicitamente atribuído infere o nível a partir da definição do nível de acesso à conta. Se o nível de acesso vier da conta, vê a propriedade de **indiferimento** de Nível de Acesso definida como "verdadeira", e a propriedade blob Access **Tier** corresponde ao nível da conta. No portal Azure, a propriedade _inferida do nível_ de acesso é exibida com o nível de acesso blob como **Hot (inferred)** ou **Cool (inferido)** .

A alteração do nível de acesso à conta aplica-se a todos os objetos _inferidos de nível de acesso_ armazenados na conta que não tenham um conjunto de nível explícito. Se alternar o nível de conta de quente para fresco, será cobrado por operações de escrita (por 10.000) para todas as bolhas sem um nível definido apenas nas contas GPv2. Não há nenhum custo para esta mudança nas contas de armazenamento blob. Será cobrado tanto pelas operações de leitura (por 10.000) como pela recuperação de dados (por GB) se alternar de fresco para quente no armazenamento blob ou nas contas GPv2.

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

O tiering de nível blob permite-lhe fazer o upload de dados para o nível de acesso da sua escolha utilizando as operações [de Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list) e alterar o nível dos seus dados ao nível do objeto utilizando a função de funcionamento do set [Blob Tier](/rest/api/storageservices/set-blob-tier) ou da gestão do Ciclo de [Vida.](#blob-lifecycle-management) Pode fazer o upload de dados para o seu nível de acesso necessário e, em seguida, alterar facilmente o nível de acesso blob entre os níveis quentes, frescos ou de arquivo à medida que os padrões de utilização mudam, sem ter que mover dados entre contas. Todos os pedidos de mudança de nível acontecem imediatamente e as alterações de nível entre quente e fresco são instantâneas. No entanto, reidratar uma bolha do arquivo pode demorar várias horas.

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Ao sobrepor uma bolha no nível quente ou fresco, a bolha recém-criada herda o nível da bolha que foi substituída a menos que o novo nível de acesso blob esteja explicitamente definido na criação. Se uma bolha está no nível de arquivo, não pode ser substituída, por isso carregar a mesma bolha não é permitida neste cenário. 

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. Também não pode alterar atualmente o nível de uma bolha de bloco que tem instantâneos.

### <a name="blob-lifecycle-management"></a>Gestão de ciclo de vida blob

A gestão do ciclo de vida blob Storage oferece uma política rica e baseada em regras que pode usar para transitar os seus dados para o melhor nível de acesso e para expirar dados no final do seu ciclo de vida. Consulte Gerir o ciclo de [vida de armazenamento de blob Azure](storage-lifecycle-management-concepts.md) para saber mais.  

> [!NOTE]
> Os dados armazenados numa conta de armazenamento de blocos blob (desempenho premium) não podem atualmente ser nivelado para quente, fresco ou arquivo utilizando [o set Blob Tier](/rest/api/storageservices/set-blob-tier) ou utilizando a gestão do ciclo de vida do Armazenamento De Blob Azure.
> Para mover dados, deve copiar sincronizadamente bolhas da conta de armazenamento de blocos blob para o nível de acesso quente numa conta diferente utilizando o [Put Block From URL API](/rest/api/storageservices/put-block-from-url) ou uma versão da AzCopy que suporta esta API.
> O *Bloco De Colocação de URL* API copia sincronizadamente os dados no servidor, o que significa que a chamada só completa uma vez que todos os dados são transferidos da localização original do servidor para a localização do destino.

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando uma bolha é carregada ou movida para o nível quente, fresco ou de arquivo, é carregada à taxa correspondente imediatamente após a alteração de nível.

Quando uma bolha é movida para um nível mais frio (hot->cool, hot>archive, ou cool>archive), a operação é faturada como uma operação de escrita para o nível de destino, onde se aplicam as taxas de escrita (por 10.000) e os dados escrevem (por GB) encargos do nível de destino.

Quando uma bolha é movida para um nível mais quente (archive->cool, archive->hot, ou cool>hot), a operação é faturada como uma leitura do nível de origem, onde se aplicam as taxas de leitura (por 10.000) e a recolha de dados (por GB) do nível de origem. Podem também aplicar-se cobranças com deteções precoces para qualquer blob que seja retirado da camada esporádica ou de arquivo. [Os dados rehidratantes do arquivo](storage-blob-rehydration.md) demoram tempo e os dados serão cobrados preços de arquivo até que os dados sejam restaurados on-line e o nível blob mude para quente ou fresco. O quadro seguinte resume como as alterações de nível são faturadas:

| | **Taxas de escrita (Operação + Acesso)** | **Ler Encargos (Operação + Acesso)**
| ---- | ----- | ----- |
| **Direção SetBlobTier** | hot&>cool,<br> hot&>arquivo,<br> cool&>arquivo | arquivo->cool,<br> arquivo->quente,<br> cool->quente

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Qualquer bolha que seja movida para o nível cool (apenas contas GPv2) está sujeita a um período de eliminação precoce de 30 dias. Qualquer bolha que seja transferida para o nível de arquivo está sujeita a um período de eliminação antecipada de 180 dias. Estes custos são rateados. Por exemplo, se uma bolha for movida para arquivar e depois ser eliminada ou transferida para o nível quente após 45 dias, será-lhe cobrada uma taxa de eliminação antecipada equivalente a 135 (180 menos 45) dias de armazenamento dessa bolha no arquivo.

Pode calcular a eliminação antecipada utilizando a propriedade blob, **Last-Modificad,** se não houver alterações no nível de acesso. Caso contrário, pode utilizar quando o nível de acesso foi modificado pela última vez para arrefecer ou arquivar visualizando a propriedade blob: **access-tier-change-time**. Para obter mais informações sobre propriedades blob, consulte [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparar opções de armazenamento de blocos blob

A tabela seguinte mostra uma comparação entre o armazenamento de blocos de desempenho premium, e os níveis de acesso quentes, frescos e de arquivo.

|                                           | **Desempenho premium**   | **Nível quente** | **Nível legal**       | **Nível de arquivo**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Banda           |
| **Disponibilidade** <br> **(leituras RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Banda           |
| **Custos de utilização**                         | Custos de armazenamento mais elevados, menor acesso e custo de transação | Custos de armazenamento mais elevados, acessomais baixos e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Os custos de armazenamento mais baixos, os custos de acesso mais elevados e os custos de transação |
| **Tamanho mínimo do objeto**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Duração mínima do armazenamento**              | N/D                       | N/D          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | Milissegundos de um dígito | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> Os objetos do nível de arrefecimento nas contas GPv2 têm uma duração mínima de retenção de 30 dias. As contas de armazenamento blob não têm uma duração mínima de retenção para o nível de arrefecimento.

<sup>2</sup> O Armazenamento de Arquivo suporta atualmente 2 prioridades de reidratação, High and Standard, que oferece diferentes tardios de recuperação. Para mais informações, consulte [os dados blob rehydrate do nível de arquivo](storage-blob-rehydration.md).

> [!NOTE]
> As contas de armazenamento blob suportam os mesmos objetivos de desempenho e escalabilidade que as contas de armazenamento v2 de uso geral. Para obter mais informações, consulte [a escalabilidade e os objetivos](scalability-targets.md)de desempenho para o armazenamento blob .

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, são demonstrados os seguintes cenários utilizando o portal Azure e a powershell:

- Como alterar a camada de acesso predefinida de uma conta de armazenamento de Blobs ou GPv2.
- Como alterar a camada de um blob numa conta de armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso predefinida de uma conta GPv2 ou de Armazenamento de Blobs

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Em **Definições,** **selecione Configuração** para visualizar e alterar a configuração da conta.

1. Selecione o nível de acesso certo para as suas necessidades: Desloque o **nível** de acesso para **Cool** ou **Hot**.

1. Clique em **Guardar** no topo.

![Alterar o nível da conta de armazenamento](media/storage-tiers/account-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível de conta. A variável `$rgName` deve ser inicializada com o nome do seu grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da sua conta de armazenamento. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar o nível de uma bolha numa conta de armazenamento GPv2 ou Blob
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Selecione o seu recipiente e, em seguida, selecione a sua bolha.

1. Nas **propriedades Blob,** selecione **Nível De alteração**.

1. Selecione o nível de acesso **Hot,** **Cool**ou **Archive.** Se a sua bolha estiver atualmente arquivada e pretender reidratar-se a um nível online, também poderá selecionar uma Prioridade de Rehidratação de **Padrão** ou **Alta**.

1. Selecione **Guardar** na parte inferior.

![Alterar o nível da conta de armazenamento](media/storage-tiers/blob-access-tier.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível de blob. A variável `$rgName` deve ser inicializada com o nome do seu grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da sua conta de armazenamento. A variável `$containerName` deve ser inicializada com o nome do recipiente. A variável `$blobName` deve ser inicializada com o seu nome blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento utilizam um modelo de preços para armazenamento de blocos blob com base no nível de cada bolha. Tenha em mente as seguintes considerações de faturação:

- **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
- **Custos de acesso a dados**: os custos de acesso a dados aumenta conforme a camada se torna mais esporádica. Para obter dados no nível de acesso fresco e de arquivo, é-lhe cobrada uma taxa de acesso de dados por gigabyte para leituras.
- **Custos de transação**: Há uma taxa por transação para todos os níveis que aumenta à medida que o nível fica mais frio.
- **Custos de transferência de dados de georreplicação**: este custo aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.
- **Alteração do nível**de acesso : A alteração do nível de acesso à conta resultará em taxas de alteração de nível de nível de acesso para as bolhas _inferidas_ de nível de acesso armazenadas na conta que não tenham um conjunto de nível explícito. Para obter informações sobre a alteração do nível de acesso para uma única bolha, consulte a [faturação de nivelamento de nível Blob](#blob-level-tiering-billing).

> [!NOTE]
> Para obter mais informações sobre preços para blocos blobs, consulte a página de [Preços de Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/) Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>FAQ

**Posso utilizar contas de armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas GPv1 não suportam o tiering.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos nos três níveis de acesso (quentes, frescos e arquivados) na mesma conta?**

Sim. O atributo **do Access Tier** definido ao nível da conta é o nível de conta por defeito que se aplica a todos os objetos nessa conta sem um nível definido explícito. O nível de nível de blob permite-lhe definir o nível de acesso ao nível do objeto, independentemente da definição do nível de acesso na conta. As bolhas em qualquer um dos três níveis de acesso (quentes, frescos ou arquivados) podem existir dentro da mesma conta.

**Posso alterar o nível de acesso padrão da minha conta de armazenamento Blob ou GPv2?**

Sim, pode alterar o nível de conta predefinido definindo o atributo **do nível** de Acesso na conta de armazenamento. A alteração do nível de conta aplica-se a todos os objetos armazenados na conta que não tenham um conjunto de nível explícito (por exemplo, **Hot (inferido)** ou **Cool (inferido).** Toggling o nível de conta de operações de escrita quente a cool incurs (por 10.000) para todas as bolhas sem um nível definido nas contas GPv2 apenas e toggling de cool to hot incurs ambas as operações de leitura (por 10.000) e taxas de recuperação de dados (por GB) para todas as bolhas no armazenamento Blob e contas GPv2.

**Posso definir a camada de acesso de conta predefinida como arquivo?**

Não. Apenas podem ser definidos níveis de acesso quentes e frescos como o nível de acesso à conta predefinida. A camada de arquivo só pode ser definida ao nível do objeto. No upload de blob, especifice o nível de acesso da sua escolha para ser quente, fresco ou arquivar independentemente do nível de conta predefinido. Esta funcionalidade permite-lhe escrever dados diretamente no nível de arquivo para realizar economias de custos a partir do momento em que cria dados no armazenamento de blob.

**Em que regiões estão disponíveis os níveis de acesso quentes, frescos e de arquivo?**

Os níveis de acesso quentes e frescos, juntamente com o nível de nível de bolhas, estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**As bolhas no nível de acesso fresco comportam-se de forma diferente das do nível de acesso quente?**

As bolhas no nível de acesso quente têm a mesma latência que as bolhas nas contas de armazenamento GPv1, GPv2 e Blob. As bolhas no nível de acesso fresco têm uma latência semelhante (em milissegundos) como bolhas nas contas de armazenamento GPv1, GPv2 e Blob. As bolhas no nível de acesso ao arquivo têm várias horas de latência nas contas de armazenamento GPv1, GPv2 e Blob.

As bolhas no nível de acesso fresco têm um nível de serviço de disponibilidade ligeiramente mais baixo (SLA) do que as bolhas armazenadas no nível de acesso quente. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são iguais?**

Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivo válidas, incluindo GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob são 100% consistentes com quente e fresco. Os dados blob não podem ser lidos ou modificados enquanto estiverem no nível de arquivo até serem rehidratados; apenas as operações de leitura de metadados blob são suportadas enquanto estão no arquivo.

**Como posso saber que a reidratação de um blob a partir da camada de arquivo para a camada frequente ou esporádica foi concluída?**

Durante a reidratação, pode utilizar a operação de propriedades de get blob para sondar o atributo **do Estado** do Arquivo e confirmar quando a mudança de nível estiver completa. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova. Consulte [os dados da bolha Rehydrate do nível de arquivo](storage-blob-rehydration.md) para saber mais.

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada bolha é sempre faturada de acordo com o nível indicado pela propriedade do **Nível** de Acesso da Blob. Quando você estabelece um novo nível on-line para uma bolha, a propriedade **Access Tier** reflete imediatamente o novo nível para todas as transições. No entanto, reidratar uma bolha do nível de arquivo offline para um nível quente ou fresco pode demorar várias horas. Neste caso, você é cobrado a taxas de arquivo até que a reidratação esteja completa, altura em que a propriedade **Access Tier** reflete o novo nível. Uma vez rehidratado ao nível on-line, você é cobrado para essa bolha a uma taxa quente ou fria.

**Como determino se incorreria numa acusação de eliminação precoce ao apagar ou mover uma bolha para fora do nível de fresco ou arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de acesso esporádico (contas GPv2 apenas) ou de arquivo antes de terem decorrido 30 e 180 dias, respetivamente, irá incorrer numa cobrança por eliminação precoce rateada. Pode determinar quanto tempo uma bolha esteve no nível de arrefecimento ou arquivo, verificando a propriedade blob **Access Tier Change Time,** que fornece um carimbo da alteração do último nível. Se o nível da bolha nunca foi alterado, pode verificar a última propriedade blob **modificada.** Para mais informações, consulte [Cool e archive early deletion](#cool-and-archive-early-deletion).

**Que ferramentas e SDKs do Azure suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivo?**

O portal, o PowerShell e as ferramentas da CLI do Azure e as bibliotecas de.NET, Java, Python e Node.js suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivos.  

**Quantos dados posso armazenar nas camadas frequente, esporádica e de arquivo?**

O armazenamento de dados juntamente com outros limites são definidos ao nível da conta e não por nível de acesso. Pode optar por utilizar todo o seu limite num só nível ou nos três níveis. Para obter mais informações, consulte [a escalabilidade e os objetivos](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)de desempenho para as contas de armazenamento padrão .

## <a name="next-steps"></a>Passos seguintes

Avalie contas de armazenamento quentes, legais e legais nas contas de armazenamento GPv2 e Blob

- [Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)
- [Gerir o ciclo de vida do Armazenamento de blobs do Azure](storage-lifecycle-management-concepts.md)
- [Saiba mais sobre a reidratação de dados blob do nível de arquivo](storage-blob-rehydration.md)
- [Determine se o desempenho premium beneficiaria a sua app](storage-blob-performance-tiers.md)
- [Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)
- [Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
