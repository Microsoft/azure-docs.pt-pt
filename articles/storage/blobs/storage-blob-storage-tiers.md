---
title: Níveis de acesso para Azure Blob Storage - quente, fresco e arquivo
description: Leia sobre os níveis de acesso quentes, frescos e de arquivo para o armazenamento Azure Blob. Reveja as contas de armazenamento que suportam o tiering. Compare as opções de armazenamento de blob de bloco.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 51998c159018b614ab519766c54fdddf7437e95b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96923986"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Níveis de acesso para Azure Blob Storage - quente, fresco e arquivo

O armazenamento Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objetos blob da forma mais rentável. As camadas de acesso disponíveis incluem:

- **Hot** - Otimizado para armazenar dados que são acedidos frequentemente.
- **Cool** - Otimizado para armazenar dados que são pouco acessados e armazenados durante pelo menos 30 dias.
- **Arquivo** - Otimizado para armazenar dados que raramente são acedidos e armazenados durante pelo menos 180 dias com requisitos flexíveis de latência (na ordem do dia).

As seguintes considerações aplicam-se às diferentes camadas de acesso:

- Apenas as camadas de acesso esporádico ou frequente podem ser definidas ao nível da conta. A camada de acesso de arquivo não está disponível ao nível da conta.
- As camadas frequentes, esporádicas e de arquivo podem ser definidas ao nível de blobs durante ou após o carregamento.
- Os dados na camada de acesso esporádico podem tolerar uma disponibilidade ligeiramente inferior, mas continuam a exigir uma elevada durabilidade, latência de obtenção e caraterísticas de débito semelhantes aos dados frequentes. Em relação aos dados esporádicos, um SLA (contrato de nível de serviço) de disponibilidade ligeiramente inferior e custos de acesso mais elevados em comparação com os dados frequentes são considerados compensações aceitáveis para custos de armazenamento mais baixos.
- O armazenamento de arquivos armazena dados offline e oferece os custos de armazenamento mais baixos, mas também os custos mais altos de acesso e reidratação de dados.

Os dados armazenados na nuvem crescem a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na cloud podem ser diferentes com base na forma como são gerados, processados e acedidos durante o seu ciclo de vida. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na cloud e raramente ou nunca são acedidos depois de serem armazenados.

Cada um destes cenários de acesso a dados beneficia de um nível de acesso diferente que é otimizado para um determinado padrão de acesso. Com níveis de acesso quentes, frescos e de arquivo, o Azure Blob Storage aborda esta necessidade de camadas de acesso diferenciadas com modelos de preços separados.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

O tiering de dados de armazenamento de objetos entre o calor, o frio e o arquivo só é suportado nas contas Blob Storage e General Purpose v2 (GPv2). As contas de Finalidade Geral v1 (GPv1) não suportam o tiering. Os clientes podem facilmente converter as suas contas de Armazenamento GPv1 ou Blob existentes em contas GPv2 através do portal Azure. O GPv2 fornece novos preços e funcionalidades para bolhas, ficheiros e filas. Algumas funcionalidades e reduções de preços só são oferecidas nas contas do GPv2. Avalie usando as contas de GPv2 após uma revisão abrangente dos preços. Algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

As contas Blob Storage e GPv2 expõem o atributo **Access Tier** ao nível da conta. Este atributo permite especificar o nível de acesso predefinido para qualquer bolha que não o tenha explicitamente definido ao nível do objeto. Para objetos com o nível definido ao nível do objeto, o nível de conta não se aplica. O nível de arquivo só pode ser aplicado ao nível do objeto. Pode alternar entre estes níveis de acesso a qualquer momento.

## <a name="hot-access-tier"></a>Camada de armazenamento frequente

A camada de acesso frequente tem custos de armazenamento mais elevados do que as camadas de acesso esporádico ou de arquivo, mas tem os custos de acesso mais baixos. Exemplos de cenários de utilização da camada de acesso frequente incluem:

- Dados que estão em utilização ativa ou aos quais se espera aceder (ler e escrever) frequentemente.
- Dados preparados para o processamento e eventual migração para a camada de acesso esporádico.

## <a name="cool-access-tier"></a>Camada de armazenamento esporádico

A camada de acesso esporádico tem custos de armazenamento inferiores e custos de acesso superiores em comparação com o armazenamento frequente. Esta camada destina-se de dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Exemplos de cenários de utilização da camada de acesso esporádico incluem:

- Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
- Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
- Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de armazenamento de arquivo

O nível de acesso ao arquivo tem o menor custo de armazenamento. Mas tem custos de recuperação de dados mais elevados em comparação com os níveis quentes e frescos. Os dados devem permanecer no nível de arquivo durante pelo menos 180 dias ou estar sujeitos a uma taxa de eliminação antecipada. Os dados no nível de arquivo podem demorar várias horas a recuperar dependendo da prioridade da reidratação. Para objetos pequenos, um rehidrato de alta prioridade pode recuperar o objeto do arquivo em menos de 1 hora. Consulte [os dados do blob rehidrata do nível de arquivo](storage-blob-rehydration.md) para saber mais.

Enquanto uma bolha está no armazenamento de arquivo, os dados do blob estão offline e não podem ser lidos, substituídos ou modificados. Para ler ou baixar uma bolha no arquivo, primeiro deve reidratar para um nível online. Não pode tirar fotos de uma bolha no armazenamento de arquivo. No entanto, os metadados blob permanecem on-line e disponíveis, permitindo-lhe listar as etiquetas de índice blob, suas propriedades, metadados e blob index. Não é permitida a definição ou modificação dos metadados blob durante o arquivo; no entanto, pode definir e modificar as etiquetas de índice blob. Para as bolhas no arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier, CopyBlob e DeleteBlob.

Os cenários de utilização de exemplo para o nível de acesso ao arquivo incluem:

- Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
- Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final.
- Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos.

> [!NOTE]
> O nível de arquivo não é atualmente suportado para contas ZRS, GZRS ou RA-GZRS.

## <a name="account-level-tiering"></a>Tiering de nível de conta

As bolhas nos três níveis de acesso podem coexistir na mesma conta. Qualquer bolha que não tenha um nível explicitamente atribuído infere o nível a partir da definição do nível de acesso à conta. Se o nível de acesso vier da conta, vê a propriedade blob **inferred de acesso** definida como "verdadeira", e a propriedade **blob Access Tier** corresponde ao nível da conta. No portal Azure, a propriedade _inferida do nível de acesso_ é exibida com o nível de acesso blob como **Hot (inferido)** ou **Cool (inferido)**.

A alteração do nível de acesso à conta aplica-se a todos os objetos _inferidos_ de nível de acesso armazenados na conta que não têm um conjunto de nível explícito. Se alternar o nível da conta de quente para fresco, será cobrado para operações de escrita (por 10.000) para todas as bolhas sem um nível definido apenas nas contas GPv2. Não há nenhum custo para esta mudança nas contas blob storage. Será cobrado tanto para operações de leitura (por 10.000) como para a recuperação de dados (por GB) se alternar de fresco para quente em Blob Storage ou contas GPv2.

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

O nível de nível de bolha permite-lhe enviar dados para o nível de acesso à sua escolha utilizando as operações [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list) e alterar o nível dos seus dados ao nível do objeto utilizando a operação De Gama [Blob Ou](/rest/api/storageservices/set-blob-tier) a funcionalidade de [gestão do ciclo de vida.](#blob-lifecycle-management) Pode enviar dados para o seu nível de acesso necessário e, em seguida, alterar facilmente o nível de acesso blob entre os níveis quentes, frescos ou de arquivo à medida que os padrões de utilização mudam, sem ter que mover dados entre contas. Todos os pedidos de mudança de nível ocorrem imediatamente e as alterações de nível entre quente e fresco são instantâneas. No entanto, reidratar uma bolha do arquivo pode demorar várias horas.

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Ao sobrepor uma bolha no nível quente ou fresco, a bolha recém-criada herda o nível da bolha que foi substituída a menos que o novo nível de acesso blob seja explicitamente definido na criação. Se uma bolha estiver no nível de arquivo, não pode ser substituída, por isso carregar a mesma bolha não é permitido neste cenário. 

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos.

### <a name="blob-lifecycle-management"></a>Gestão do ciclo de vida blob

A gestão do ciclo de vida Blob Storage oferece uma política rica e baseada em regras que pode usar para transitar os seus dados para o melhor nível de acesso e para expirar dados no final do seu ciclo de vida. Consulte gerir o ciclo de [vida de armazenamento Azure Blob](storage-lifecycle-management-concepts.md) para saber mais.  

> [!NOTE]
> Os dados armazenados numa conta de armazenamento de blob de bloco (desempenho premium) não podem atualmente ser hierárquicos para quentes, frescos ou arquivados utilizando [o set Blob Tier](/rest/api/storageservices/set-blob-tier) ou utilizando a gestão do ciclo de vida do armazenamento Azure Blob.
> Para mover dados, tem de copiar sincronizadamente bolhas da conta de armazenamento de blob de bloco para o nível de acesso quente numa conta diferente utilizando o [Put Block From URL API](/rest/api/storageservices/put-block-from-url) ou uma versão da AzCopy que suporta esta API.
> O *Bloco de Put From URL* API copia sincronizadamente os dados no servidor, o que significa que a chamada só completa uma vez que todos os dados são transferidos do local original do servidor para o local de destino.

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando uma bolha é carregada ou movida para o nível quente, frio ou de arquivo, é carregada à taxa correspondente imediatamente após a mudança de nível.

Quando uma bolha é movida para um nível mais frio (hot->arquivo de >, ou arquivo de >frio), a operação é faturada como uma operação de escrita para o nível de destino, onde se aplica a operação de escrita (por 10.000) e os dados escrevem (por GB) taxas do nível de destino.

Quando uma bolha é movida para um nível mais quente (>fresco, >de arquivo quente ou >quente), a operação é faturada como uma leitura do nível de origem, onde se aplica a operação de leitura (por 10.000) e as cargas de recuperação de dados (por GB) do nível de origem. Podem também aplicar-se cobranças com deteções precoces para qualquer blob que seja retirado da camada esporádica ou de arquivo. [A reidratação dos dados do arquivo](storage-blob-rehydration.md) leva tempo e os dados serão cobrados preços de arquivo até que os dados sejam restaurados on-line e as alterações do nível blob para quente ou fresco. O quadro que se segue resume a forma como as alterações de nível são faturadas:

| | **Escrever Encargos (Operação + Acesso)** | **Ler encargos (Operação + Acesso)**
| ---- | ----- | ----- |
| **Direção SetBlobTier** | hot->fresco,<br> arquivo hot->,<br> arquivo cool-> | arquivo->fresco,<br> >de arquivo quente,<br> cool->quente

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Qualquer bolha que seja movida para o nível cool (apenas contas GPv2) está sujeita a um período de eliminação precoce de 30 dias. Qualquer bolha que seja movida para o nível de arquivo está sujeita a um período de eliminação precoce de 180 dias. Estes custos são rateados. Por exemplo, se uma bolha for movida para arquivar e depois apagada ou movida para o nível quente após 45 dias, será cobrada uma taxa de eliminação antecipada equivalente a 135 (180 menos 45) dias de armazenamento dessa bolha no arquivo.

Existem alguns detalhes ao mover-se entre os níveis frescos e de arquivo:

1. Se uma bolha for deduzida como fixe com base no nível de acesso predefinido da conta de armazenamento e a bolha for movida para arquivar, não há nenhuma taxa de eliminação antecipada.
1. Se uma bolha for explicitamente movida para o nível fresco e depois for transferida para o arquivo, aplica-se a taxa de eliminação precoce.

Pode calcular a eliminação precoce utilizando a propriedade blob, **Última Modificada,** se não houver alterações no nível de acesso. Caso contrário, pode utilizar quando o nível de acesso foi modificado pela última vez para arrefecer ou arquivar, visualizando a propriedade blob: **tempo de mudança de nível de acesso**. Para obter mais informações sobre propriedades blob, consulte [Get Blob Properties](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparar opções de armazenamento de blob de bloco

A tabela a seguir mostra uma comparação entre o armazenamento de blocos de desempenho premium e os níveis de acesso quente, fresco e arquivado.

|                                           | **Desempenho premium**   | **Nível quente** | **Nível fresco**       | **Nível de arquivo**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilidade** <br> **(leituras RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Taxas de utilização**                         | Custos de armazenamento mais elevados, acesso mais baixo e custo de transação | Custos de armazenamento mais elevados, menor acesso e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação |
| **Tamanho mínimo do objeto**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Duração mínima do armazenamento**              | N/D                       | N/D          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | Milissegundos de um dígito | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> Os objetos no nível fresco nas contas GPv2 têm uma duração mínima de retenção de 30 dias. As contas blob Storage não têm uma duração mínima de retenção para o nível fresco.

<sup>2</sup> O Armazenamento de Arquivo suporta atualmente 2 prioridades rehidratas, Alta e Standard, que oferece diferentes latências de recuperação. Para obter mais informações, consulte [os dados do blob rehidrata do nível de arquivo.](storage-blob-rehydration.md)

> [!NOTE]
> As contas blob Storage suportam os mesmos objetivos de desempenho e escalabilidade que as contas de armazenamento v2 de uso geral. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para o Blob Storage](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, os seguintes cenários são demonstrados utilizando o portal Azure e o PowerShell:

- Como alterar a camada de acesso predefinida de uma conta de Armazenamento de Blobs ou GPv2.
- Como alterar a camada de um blob numa conta de Armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar o nível de acesso à conta padrão de uma conta de Armazenamento de GPv2 ou Blob

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Em **Definições**, selecione **Configuração** para visualizar e alterar a configuração da conta.

1. Selecione o nível de acesso certo para as suas necessidades: Desa estale o **nível de acesso** para **Cool** ou **Hot**.

1. Clique em **Guardar** na parte superior.

![Alterar o nível de conta padrão no portal Azure](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível de conta. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar o nível de uma bolha numa conta de Armazenamento de GPv2 ou Blob
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Selecione o seu recipiente e, em seguida, selecione a sua bolha.

1. Nas **propriedades Blob**, selecione **Alterar o nível**.

1. Selecione o nível de acesso **Hot,** **Cool** ou **Archive.** Se a sua bolha estiver atualmente arquivada e pretender reidratar para um nível online, também pode selecionar uma Prioridade Rehidrata de **Standard** ou **Alta**.

1. **Selecione Guarde** na parte inferior.

![Alterar nível blob no portal Azure](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte script PowerShell pode ser usado para alterar o nível blob. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do seu recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob. 
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
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento utilizam um modelo de preços para armazenamento de blob block com base no nível de cada bolha. Tenha em mente as seguintes considerações de faturação:

- **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
- **Custos de acesso a dados**: os custos de acesso a dados aumenta conforme a camada se torna mais esporádica. Para obter dados no nível de acesso cool e archive, é-lhe cobrada uma taxa de acesso a dados por gigabyte para leituras.
- **Custos de transação**: Há uma taxa por transação para todos os níveis que aumenta à medida que o nível fica mais frio.
- **Custos de transferência de dados de georreplicação**: este custo aplica-se apenas às contas que têm a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.
- **Alteração do nível de acesso**: A alteração do nível de acesso à conta resultará em taxas de alteração de nível de nível para bolhas _inferidas_ de nível de acesso armazenadas na conta que não têm um conjunto de nível explícito. Para obter informações sobre a alteração do nível de acesso para uma única bolha, consulte a [faturação de nível blob](#blob-level-tiering-billing).

    Alterar o nível de acesso para uma bolha quando a versão está ativada, ou se a bolha tiver instantâneos, pode resultar em custos adicionais. Para obter mais informações sobre como é faturado quando a versão blob está ativada e você muda explicitamente o nível de uma bolha, consulte [preços e faturação](versioning-overview.md#pricing-and-billing) na documentação para a versão blob. Para obter mais informações sobre como você é cobrado quando uma bolha tem instantâneos e você muda explicitamente o nível da bolha, consulte [preços e faturação](snapshots-overview.md#pricing-and-billing) na documentação para instantâneos blob.

> [!NOTE]
> Para obter mais informações sobre preços para blobs de blocos, consulte a página [de preços de armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/) Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>FAQ

**Posso utilizar contas de Armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de Armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de Armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas do GPv1 não suportam o tiering.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de Armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos nos três níveis de acesso (quentes, frescos e de arquivo) na mesma conta?**

Sim. O atributo **Access Tier** definido ao nível da conta é o nível de conta padrão que se aplica a todos os objetos nessa conta sem um nível definido explícito. O nível de nível de bolhas permite-lhe definir o nível de acesso ao nível do objeto, independentemente da definição do nível de acesso na conta. Podem existir bolhas em qualquer uma das três camadas de acesso (quentes, frias ou arquivadas) dentro da mesma conta.

**Posso alterar o nível de acesso predefinido da minha conta de armazenamento Blob ou GPv2?**

Sim, pode alterar o nível de conta predefinido definindo o atributo **de nível De acesso** na conta de armazenamento. A alteração do nível de conta aplica-se a todos os objetos armazenados na conta que não tenham um conjunto de nível explícito (por exemplo, **Hot (inferido)** ou **Cool (inferido)**). O toggling do nível da conta de operações de escrita quentes a frio incorre (por 10.000) para todas as bolhas sem um nível definido apenas nas contas GPv2 e toggling de cool para quente incorre tanto operações de leitura (por 10.000) como taxas de recuperação de dados (por GB) para todos os blobs em Blob Storage e contas GPv2.

**Posso definir a camada de acesso de conta predefinida como arquivo?**

N.º Apenas os níveis de acesso quente e fresco podem ser definidos como o nível de acesso da conta predefinido. A camada de arquivo só pode ser definida ao nível do objeto. No upload do blob, especifica o nível de acesso à sua escolha para ser quente, fresco ou arquivado, independentemente do nível de conta predefinido. Esta funcionalidade permite-lhe escrever dados diretamente no nível de arquivo para perceber economias de custos a partir do momento em que cria dados no armazenamento de bolhas.

**Em que regiões estão disponíveis os níveis de acesso quente, fresco e arquivado?**

Os níveis de acesso quente e fresco, juntamente com o nível de nível de bolhas, estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**Que opções de redundância são suportadas para os níveis de acesso quente, fresco e arquivado?**

Os níveis quentes e frescos suportam todas as opções de redundância. O nível de arquivo suporta apenas LRS, GRS e RA-GRS. ZRS, GZRS e RA-GZRS não são suportados para o nível de arquivo.

**As bolhas no nível de acesso fresco comportam-se de forma diferente das do nível de acesso quente?**

As bolhas no nível de acesso quente têm a mesma latência que as bolhas nas contas de GPv1, GPv2 e Blob Storage. As bolhas no nível de acesso fresco têm uma latência semelhante (em milissegundos) como bolhas nas contas GPv1, GPv2 e Blob Storage. As bolhas no nível de acesso ao arquivo têm várias horas de latência nas contas GPv1, GPv2 e Blob Storage.

As bolhas no nível de acesso fresco têm um nível de serviço de disponibilidade ligeiramente mais baixo (SLA) do que as bolhas armazenadas no nível de acesso quente. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

**As operações entre as camadas frequente, esporádica e de arquivo são iguais?**

Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivo válidas, incluindo GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier e DeleteBlob são 100% consistentes com quente e fresco. Os dados do blob não podem ser lidos ou modificados enquanto estiverem no nível de arquivo até que se rehidratados; apenas as operações de leitura de metadados blob são suportadas durante o arquivo. No entanto, as etiquetas de índice blob podem ser lidas, definidas ou modificadas durante o arquivo.

**Como posso saber que a reidratação de um blob a partir da camada de arquivo para a camada frequente ou esporádica foi concluída?**

Durante a reidratação, pode utilizar a operação de propriedades blob para sondar o atributo **Archive Status** e confirmar quando a mudança de nível estiver completa. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade do estado do arquivo é removida, e a propriedade blob **Access Tier** reflete o novo nível quente ou fresco. Consulte [os dados do blob rehidrata do nível de arquivo](storage-blob-rehydration.md) para saber mais.

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada bolha é sempre faturada de acordo com o nível indicado pela propriedade **Access Tier** da blob. Quando se define um novo nível on-line para uma bolha, a propriedade **Access Tier** reflete imediatamente o novo nível para todas as transições. No entanto, a reidratação de uma bolha do nível de arquivo offline para um nível quente ou fresco pode demorar várias horas. Neste caso, você é cobrado a taxas de arquivo até que a reidratação esteja completa, altura em que a propriedade **Access Tier** reflete o novo nível. Uma vez rehidratado para o nível on-line, você é cobrado para que a bolha a uma taxa quente ou fresca.

**Como posso determinar se incorrerei numa acusação de supressão antecipada ao apagar ou tirar uma bolha do nível fresco ou de arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de acesso esporádico (contas GPv2 apenas) ou de arquivo antes de terem decorrido 30 e 180 dias, respetivamente, irá incorrer numa cobrança por eliminação precoce rateada. Pode determinar quanto tempo uma bolha esteve no nível fresco ou de arquivo, verificando a propriedade do blob **Access Tier Change Time,** que fornece um carimbo da última alteração de nível. Se o nível da bolha nunca tiver sido alterado, pode verificar a propriedade da bolha **modificada.** Para obter mais informações, consulte [Cool e arquive a eliminação precoce.](#cool-and-archive-early-deletion)

**Que ferramentas e SDKs do Azure suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivo?**

O portal, o PowerShell e as ferramentas da CLI do Azure e as bibliotecas de.NET, Java, Python e Node.js suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivos.  

**Quantos dados posso armazenar nas camadas frequente, esporádica e de arquivo?**

O armazenamento de dados juntamente com outros limites são definidos ao nível da conta e não por nível de acesso. Pode optar por utilizar todo o seu limite num só nível ou nos três níveis. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para as contas de armazenamento padrão.](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="next-steps"></a>Passos seguintes

Avalie o calor, o frio e o arquivo nas contas de Armazenamento de GPv2 e Blob

- [Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)
- [Gerir o ciclo de vida do Armazenamento Azure Blob](storage-lifecycle-management-concepts.md)
- [Saiba mais sobre a reidratação de dados blob a partir do nível de arquivo](storage-blob-rehydration.md)
- [Determine se o desempenho premium beneficiaria a sua app](storage-blob-performance-tiers.md)
- [Avaliar a utilização das suas contas do Storage atuais ao ativar as métricas do Storage do Azure](./monitor-blob-storage.md)
- [Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)