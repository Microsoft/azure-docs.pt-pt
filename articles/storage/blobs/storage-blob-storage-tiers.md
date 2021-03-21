---
title: Níveis de acesso para Azure Blob Storage - quente, fresco e arquivo
description: Leia sobre os níveis de acesso quentes, frescos e de arquivo para o armazenamento Azure Blob. Reveja as contas de armazenamento que suportam o tiering.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 1a1cb8e1676405cbfbb3f4f61c86d8136b688b88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656843"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Níveis de acesso para Azure Blob Storage - quente, fresco e arquivo

O armazenamento Azure oferece diferentes níveis de acesso, permitindo-lhe armazenar dados de objetos blob da forma mais rentável. Os níveis de acesso disponíveis incluem:

- **Hot** - Otimizado para armazenar dados que são acedidos frequentemente.
- **Cool** - Otimizado para armazenar dados que são pouco acessados e armazenados durante pelo menos 30 dias.
- **Arquivo** - Otimizado para armazenar dados que raramente são acedidos e armazenados durante pelo menos 180 dias com requisitos flexíveis de latência, na ordem do dia.

As seguintes considerações aplicam-se às diferentes camadas de acesso:

- O nível de acesso pode ser definido numa bolha durante ou depois do upload.
- Apenas as camadas de acesso esporádico ou frequente podem ser definidas ao nível da conta. O nível de acesso ao arquivo só pode ser definido ao nível do blob.
- Os dados no nível de acesso fresco têm uma disponibilidade ligeiramente menor, mas ainda têm alta durabilidade, latência de recuperação e características de produção semelhantes aos dados quentes. Para dados frescos, uma disponibilidade ligeiramente mais baixa e custos de acesso mais elevados são compensações aceitáveis para custos globais de armazenamento mais baixos em comparação com os dados quentes. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Os dados no nível de acesso ao arquivo são armazenados offline. O nível de arquivo oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados e latência.
- Os níveis quentes e frescos suportam todas as opções de redundância. O nível de arquivo suporta apenas LRS, GRS e RA-GRS.
- Os limites de armazenamento de dados são definidos ao nível da conta e não por nível de acesso. Pode optar por utilizar todo o seu limite num só nível ou nos três níveis.

Os dados armazenados na nuvem crescem a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na cloud podem ser diferentes com base na forma como são gerados, processados e acedidos durante o seu ciclo de vida. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na cloud e raramente ou nunca são acedidos depois de serem armazenados.

Cada um destes cenários de acesso a dados beneficia de um nível de acesso diferente que é otimizado para um determinado padrão de acesso. Com níveis de acesso quentes, frescos e de arquivo, o Azure Blob Storage aborda esta necessidade de camadas de acesso diferenciadas com modelos de preços separados.

As seguintes ferramentas e bibliotecas de clientes suportam o nível de nível blob e armazenamento de arquivo.

- Portal do Azure
- PowerShell
- Ferramentas Azure CLI
- Biblioteca de cliente .NET
- Biblioteca de cliente Java
- Biblioteca de cliente Python
- biblioteca de clientes Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

O tiering de dados de armazenamento de objetos entre o calor, o frio e o arquivo é suportado nas contas Blob Storage e General Purpose v2 (GPv2). As contas de Finalidade Geral v1 (GPv1) não suportam o tiering. Pode converter facilmente as suas contas de Armazenamento de GPv1 ou Blob existentes em contas GPv2 através do portal Azure. O GPv2 fornece novos preços e funcionalidades para bolhas, ficheiros e filas. Algumas funcionalidades e reduções de preços só são oferecidas nas contas do GPv2. Algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

As contas Blob Storage e GPv2 expõem o atributo **Access Tier** ao nível da conta. Este atributo permite especificar o nível de acesso predefinido para qualquer bolha que não o tenha explicitamente definido ao nível do objeto. Para objetos com o nível explicitamente definido, o nível de conta não se aplica. O nível de arquivo só pode ser aplicado ao nível do objeto. Pode alternar entre os níveis de acesso a qualquer momento.

Utilize GPv2 em vez de blob storage accounts para tiering. O GPv2 suporta todas as funcionalidades que as contas blob storage suportam, além de muito mais. Os preços entre o Blob Storage e o GPv2 são quase idênticos, mas algumas novidades e reduções de preços só estão disponíveis nas contas do GPv2.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de Armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Camada de armazenamento frequente

A camada de acesso frequente tem custos de armazenamento mais elevados do que as camadas de acesso esporádico ou de arquivo, mas tem os custos de acesso mais baixos. Exemplos de cenários de utilização da camada de acesso frequente incluem:

- Dados que estão em uso ativo ou que se espera que sejam lidos e escritos frequentemente
- Dados que são encenados para o processamento e eventual migração para o nível de acesso cool

## <a name="cool-access-tier"></a>Camada de armazenamento esporádico

A camada de acesso esporádico tem custos de armazenamento inferiores e custos de acesso superiores em comparação com o armazenamento frequente. Esta camada destina-se de dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Exemplos de cenários de utilização da camada de acesso esporádico incluem:

- Backup de curto prazo e recuperação de desastres
- Dados mais antigos não utilizados com frequência, mas que se espera que estejam disponíveis imediatamente quando acedidos
- Grandes conjuntos de dados que precisam de ser armazenados de forma eficaz, enquanto mais dados estão a ser recolhidos para o processamento futuro

## <a name="archive-access-tier"></a>Camada de armazenamento de arquivo

O nível de acesso ao arquivo tem o menor custo de armazenamento, mas custos de recuperação de dados mais elevados em comparação com os níveis quentes e frescos. Os dados devem permanecer no nível de arquivo durante pelo menos 180 dias ou estar sujeitos a uma taxa de eliminação antecipada. Os dados no nível de arquivo podem demorar várias horas a recuperar dependendo da prioridade de reidratação especificada. Para objetos pequenos, um rehidrato de alta prioridade pode recuperar o objeto do arquivo em menos de uma hora. Consulte [os dados do blob rehidrata do nível de arquivo](storage-blob-rehydration.md) para saber mais.

Enquanto uma bolha está no armazenamento de arquivo, os dados do blob estão offline e não podem ser lidos ou modificados. Para ler ou baixar uma bolha no arquivo, primeiro deve reidratar para um nível online. Não pode tirar fotos de uma bolha no armazenamento de arquivo. No entanto, os metadados blob permanecem on-line e disponíveis, permitindo-lhe listar as etiquetas de índice blob, suas propriedades, metadados e blob index. Não é permitido definir ou modificar os metadados blob durante o arquivo. No entanto, pode definir e modificar as etiquetas de índice blob. Para as bolhas no arquivo, as únicas operações válidas são [obter propriedades blob,](/rest/api/storageservices/get-blob-properties) [obter metadados blob,](/rest/api/storageservices/get-blob-metadata) [definir tags blob,](/rest/api/storageservices/set-blob-tags) [obter tags blobs,](/rest/api/storageservices/get-blob-tags) [encontrar blobs por tags,](/rest/api/storageservices/find-blobs-by-tags) [List Blobs,](/rest/api/storageservices/list-blobs) [set Blob Tier,](/rest/api/storageservices/set-blob-tier) [Copy Blob](/rest/api/storageservices/copy-blob), e [Delete Blob](/rest/api/storageservices/delete-blob).

Os cenários de utilização de exemplo para o nível de acesso ao arquivo incluem:

- Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
- Dados originais (brutos) que devem ser preservados, mesmo depois de terem sido transformados em forma de forma utilizável final
- Dados de conformidade e arquivo que precisam de ser armazenados por muito tempo e que quase nunca são acedidos

> [!NOTE]
> O nível de arquivo não é suportado para contas ZRS, GZRS ou RA-GZRS. A migração de LRS para GRS não é suportada se a conta de armazenamento contiver bolhas no nível de arquivo.

## <a name="account-level-tiering"></a>Tiering de nível de conta

As bolhas nos três níveis de acesso podem coexistir na mesma conta. Qualquer bolha que não tenha um nível explicitamente atribuído infere o nível a partir da definição do nível de acesso à conta. Se o nível de acesso vier da conta, vê a propriedade blob **inferred de acesso** definida como "verdadeira", e a propriedade **blob Access Tier** corresponde ao nível da conta. No portal Azure, a propriedade _inferida do nível de acesso_ é exibida com o nível de acesso blob como **Hot (inferido)** ou **Cool (inferido)**.

A alteração do nível de acesso à conta aplica-se a todos os objetos _inferidos_ de nível de acesso armazenados na conta que não têm um conjunto de nível explícito. Se alternar o nível da conta de quente para fresco, será cobrado para operações de escrita (por 10.000) para todas as bolhas sem um nível definido apenas nas contas GPv2. Não há nenhum custo para esta mudança nas contas blob storage. Será cobrado tanto para operações de leitura (por 10.000) como para a recuperação de dados (por GB) se alternar de fresco para quente em Blob Storage ou contas GPv2.

Apenas os níveis de acesso quente e fresco podem ser definidos como o nível de acesso à conta predefinido. A camada de arquivo só pode ser definida ao nível do objeto. No upload blob, pode especificar o nível de acesso à sua escolha para ser quente, fresco ou arquivado, independentemente do nível de conta predefinido. Esta funcionalidade permite-lhe escrever dados diretamente no nível de arquivo para perceber economias de custos a partir do momento em que cria dados no armazenamento de bolhas.

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

O nível de nível de bolha permite-lhe enviar dados para o nível de acesso à sua escolha utilizando as operações [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list) e alterar o nível dos seus dados ao nível do objeto utilizando a operação De Gama [Blob Ou](/rest/api/storageservices/set-blob-tier) a [função de gestão do ciclo de vida.](#blob-lifecycle-management) Pode enviar dados para o seu nível de acesso necessário e, em seguida, alterar facilmente o nível de acesso blob entre os níveis quentes, frescos ou de arquivo à medida que os padrões de utilização mudam, sem ter que mover dados entre contas. Todos os pedidos de mudança de nível ocorrem imediatamente e as alterações de nível entre quente e fresco são instantâneas. Reidratar uma bolha do nível do arquivo pode demorar várias horas.

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. **Access Tier Change Time** é uma propriedade de nível blob e não é atualizado quando o nível de conta padrão é alterado. As propriedades da conta e as propriedades blob são separadas. Seria proibitivamente caro atualizar o Tempo de **Alteração de Nível** de Acesso em cada bolha de uma conta de armazenamento sempre que o nível de acesso padrão da conta for alterado.

Ao sobrepor uma bolha no nível quente ou fresco, a bolha recém-criada herda o nível da bolha que foi substituída a menos que o novo nível de acesso blob seja explicitamente definido na criação. Se uma bolha estiver no nível de arquivo, não pode ser substituída, por isso carregar a mesma bolha não é permitido neste cenário.

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos.

### <a name="blob-lifecycle-management"></a>Gestão do ciclo de vida blob

A gestão do ciclo de vida do armazenamento blob oferece uma política rica e baseada em regras que pode usar para transitar os seus dados para o melhor nível de acesso e para expirar dados no final do seu ciclo de vida. Consulte [os custos da Otimização automatizando os níveis de acesso ao Armazenamento Azure Blob](storage-lifecycle-management-concepts.md) para saber mais.

> [!NOTE]
> Os dados armazenados numa conta de armazenamento de blob de bloco (desempenho premium) não podem atualmente ser hierárquicos para quentes, frescos ou arquivados utilizando [o set Blob Tier](/rest/api/storageservices/set-blob-tier) ou utilizando a gestão do ciclo de vida do armazenamento Azure Blob.
> Para mover dados, tem de copiar sincronizadamente bolhas da conta de armazenamento de blob de bloco para o nível de acesso quente numa conta diferente utilizando o [Put Block From URL API](/rest/api/storageservices/put-block-from-url) ou uma versão da AzCopy que suporta esta API.
> O **Bloco de Put From URL** API copia sincronizadamente os dados no servidor, o que significa que a chamada só completa uma vez que todos os dados são transferidos do local original do servidor para o local de destino.

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando uma bolha é carregada ou movida entre os níveis, é carregada à taxa correspondente imediatamente após o upload ou alteração de nível.

Quando uma bolha é movida para um nível mais frio (hot->arquivo de >, ou arquivo de >frio), a operação é faturada como uma operação de escrita para o nível de destino, onde se aplica a operação de escrita (por 10.000) e os dados escrevem (por GB) taxas do nível de destino.

Quando uma bolha é movida para um nível mais quente (>fresco, >de arquivo quente ou >quente), a operação é faturada como uma leitura do nível de origem, onde se aplica a operação de leitura (por 10.000) e as cargas de recuperação de dados (por GB) do nível de origem. As taxas [de eliminação antecipada](#cool-and-archive-early-deletion) de qualquer bolha retirada do nível fresco ou de arquivo também podem ser aplicadas. [A reidratação dos dados do nível de arquivo](storage-blob-rehydration.md) leva tempo e os dados serão cobrados preços de arquivo até que os dados sejam restaurados on-line e o nível de bolhas mude para quente ou fresco.

A tabela seguinte resume a forma como as alterações de nível são faturadas.

| | **Escreva encargos (operação + acesso)** | **Ler encargos (operação + acesso)** |
| ---- | ----- | ----- |
| **Set Blob Tier** (Definir Camada de Blob) | quente -> fresco<br> arquivo hot -><br> arquivo cool -> | arquivo -> fresco<br> arquivo -> quente<br> fresco -> quente

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Qualquer bolha que seja movida para o nível cool (apenas contas GPv2) está sujeita a um período de eliminação precoce de 30 dias. Qualquer bolha que seja movida para o nível de arquivo está sujeita a um período de eliminação precoce de 180 dias. Estes custos são rateados. Por exemplo, se uma bolha for movida para arquivar e depois apagada ou movida para o nível quente após 45 dias, será cobrada uma taxa de eliminação antecipada equivalente a 135 (180 menos 45) dias de armazenamento dessa bolha no arquivo.

Existem alguns detalhes ao mover-se entre os níveis frescos e de arquivo:

1. Se uma bolha for deduzida como fixe com base no nível de acesso predefinido da conta de armazenamento e a bolha for movida para arquivar, não há nenhuma taxa de eliminação antecipada.
1. Se uma bolha for explicitamente movida para o nível fresco e depois for transferida para o arquivo, aplica-se a taxa de eliminação precoce.

Calcular o tempo de eliminação precoce utilizando a propriedade blob **última modificada,** se não houver alterações no nível de acesso. Caso contrário, utilize quando o nível de acesso foi modificado pela última vez para arrefecer ou arquivar, visualizando a propriedade blob: **tempo de mudança de nível de acesso**. Para obter mais informações sobre propriedades blob, consulte [Get Blob Properties](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparar opções de armazenamento de blob de bloco

A tabela a seguir mostra uma comparação entre o armazenamento de blocos de desempenho premium e os níveis de acesso quente, fresco e arquivado.

|                                           | **Desempenho premium**   | **Nível quente** | **Nível fresco**       | **Nível de arquivo**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilidade** <br> **(leituras RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Taxas de utilização**                         | Custos de armazenamento mais elevados, acesso mais baixo e custo de transação | Custos de armazenamento mais elevados, menor acesso e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação | Custos de armazenamento mais baixos, acesso mais elevado e custos de transação |
| **Duração mínima do armazenamento**              | N/D                       | N/D          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | Milissegundos de um dígito | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> Os objetos no nível fresco nas contas GPv2 têm uma duração mínima de retenção de 30 dias. As contas blob Storage não têm uma duração mínima de retenção para o nível fresco.

<sup>2</sup> O Armazenamento de Arquivo suporta atualmente duas prioridades de reidratação, altas e padrão, oferecendo diferentes atrasos e custos de recuperação. Para obter mais informações, consulte [os dados do blob rehidrata do nível de arquivo.](storage-blob-rehydration.md)

> [!NOTE]
> As contas blob Storage suportam os mesmos objetivos de desempenho e escalabilidade que as contas de armazenamento v2 de uso geral. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para o Blob Storage](scalability-targets.md).

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento utilizam um modelo de preços para armazenamento de blob de blocos com base no nível de cada bolha. Tenha em mente as seguintes considerações de faturação:

- **Custos de armazenamento**: para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
- **Custos de acesso a dados**: os custos de acesso a dados aumenta conforme a camada se torna mais esporádica. Para obter dados no nível de acesso cool e archive, é-lhe cobrada uma taxa de acesso a dados por gigabyte para leituras.
- **Custos de transação**: Há uma taxa por transação para todos os níveis que aumenta à medida que o nível fica mais frio.
- **Custos de transferência de dados de geo-replicação**: Este encargo aplica-se apenas a contas com geo-replicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: as transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) estão sujeitas a uma cobrança pela utilização de largura de banda por gigabyte, tal como as contas do Storage para fins gerais.
- **Alteração do nível de acesso**: A alteração do nível de acesso à conta resultará em taxas de alteração de nível para todas as bolhas que não tenham um conjunto de nível explícito. Para obter informações sobre a alteração do nível de acesso para uma única bolha, consulte [a faturação de nível blob](#blob-level-tiering-billing).

    Alterar o nível de acesso para uma bolha quando a versão está ativada, ou se a bolha tiver instantâneos, pode resultar em custos adicionais. Para obter informações sobre bolhas com versão ativada, consulte [preços e faturação](versioning-overview.md#pricing-and-billing) na documentação de versão blob. Para obter informações sobre bolhas com instantâneos, consulte [preços e faturação](snapshots-overview.md#pricing-and-billing) na documentação de instantâneos blob.

> [!NOTE]
> Para obter mais informações sobre os preços das bolhas do Bloco, consulte [os preços do blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre os custos de transferência de dados de saída, consulte a página [Detalhes de Preços da Largura de Banda.](https://azure.microsoft.com/pricing/details/bandwidth/)

## <a name="availability"></a>Disponibilidade

Diferentes níveis de acesso, juntamente com o nível de nível de bolhas, estão disponíveis em regiões selecionadas. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir bolhas e contas através dos níveis de acesso.

- [Como gerir o nível de uma bolha numa conta de Armazenamento Azure](manage-access-tier.md)
- [Como gerir o nível de acesso à conta padrão de uma conta de Armazenamento Azure](../common/manage-account-default-access-tier.md)
- [Otimizar os custos automatizando os níveis de acesso ao armazenamento Azure Blob](storage-lifecycle-management-concepts.md)
