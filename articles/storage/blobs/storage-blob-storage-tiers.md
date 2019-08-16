---
title: Camadas de acesso quentes, frias e de arquivo para BLOBs-armazenamento do Azure
description: Camadas de acesso quentes, frias e de arquivo para contas de armazenamento do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 48c6d6ed60045d906fcb711bd07ab492b6bbf488
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543679"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo

O armazenamento do Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objeto de BLOB da maneira mais econômica. As camadas de acesso disponíveis incluem:

- Otimizado para o armazenamento de dados que são acessados com frequência.
- Muito otimizado para armazenar dados que são acessados com pouca frequência e armazenados por pelo menos 30 dias.
- **Arquivado** -otimizado para armazenar dados que raramente são acessados e armazenados por pelo menos 180 dias com requisitos de latência flexíveis (na ordem de horas).

As seguintes considerações se aplicam às diferentes camadas de acesso:

- Somente as camadas de acesso quentes e frias podem ser definidas no nível da conta. A camada de acesso de arquivamento não está disponível no nível da conta.
- Camadas quentes, frias e de arquivo podem ser definidas no nível do blob.
- Os dados na camada de acesso frio podem tolerar uma disponibilidade ligeiramente menor, mas ainda exigem alta durabilidade, latência de recuperação e características de taxa de transferência semelhantes aos dados ativos. Para dados legais, um SLA (contrato de nível de serviço) de disponibilidade ligeiramente menor e custos de acesso mais altos em comparação com os dados ativos são compensações aceitáveis para reduzir os custos de armazenamento.
- O armazenamento de arquivos armazena dados offline e oferece os custos de armazenamento mais baixos, mas também os mais altos reidratar de dados e custos de acesso.

Os dados armazenados na nuvem crescem em um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes em termos de como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados.

Cada um desses cenários de acesso a dados se beneficia de uma camada de acesso diferente que é otimizada para um padrão de acesso específico. Com as camadas de acesso frequente, esporádica e de arquivo morto, o armazenamento de BLOBs do Azure atende a essa necessidade de camadas de acesso diferenciado com modelos de preços separados.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

A camada de dados de armazenamento de objetos em alta, esporádica ou arquivo morto só tem suporte em contas de armazenamento de BLOB e Uso Geral v2 (GPv2). As contas de Fins Gerais (GPv1) não suportam as camadas. No entanto, os clientes podem converter facilmente as respetivas contas GPv1 ou de armazenamento de Blobs existentes em contas GPv2 através de um processo de um só clique no portal do Azure. O GPv2 fornece uma nova estrutura de preços para BLOBs, arquivos e filas, além de acesso a uma variedade de novos recursos de armazenamento. Além disso, no futuro, algumas funcionalidades e reduções de preços só estarão disponíveis nas contas GPv2. Portanto, os clientes devem avaliar o uso de contas do GPv2 após a revisão abrangente do novo preço, já que algumas cargas de trabalho podem ser mais caras no GPv2 do que o GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

As contas de armazenamento de BLOBs e GPv2 expõem o atributo **camada de acesso** no nível da conta. Esse atributo permite que você especifique a camada de acesso padrão como frequente ou fria para qualquer blob na conta de armazenamento que não tenha uma camada explícita definida no nível do objeto. Relativamente aos objetos cuja camada está definida ao nível do objeto, a camada da conta não se aplicará. A camada de arquivo pode ser aplicada somente no nível do objeto. Você pode alternar entre essas camadas de acesso a qualquer momento.

## <a name="premium-performance-block-blob-storage"></a>Armazenamento de blobs de blocos de desempenho premium

O armazenamento de blobs de blocos de desempenho premium disponibiliza dados acessados com frequência por meio de hardware de alto desempenho. Os dados nesse nível de desempenho são armazenados em SSDs (unidades de estado sólido), que são otimizados para latência baixa e consistente. O SSDs fornece taxas transacionais e taxa de transferência maiores em comparação com os discos rígidos tradicionais.

O armazenamento de blobs de blocos de desempenho Premium é ideal para cargas de trabalho que exigem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que executam muitas transações pequenas, como a captura de dados de telemetria, mensagens e transformação de dados. Os dados que envolvem usuários finais, como edição interativa de vídeo, conteúdo da Web estático e transações online também são bons candidatos.

O armazenamento de blobs de blocos de desempenho Premium está disponível apenas por meio do tipo de conta de armazenamento de blobs de blocos e atualmente não dá suporte a camadas para camadas de acesso frequente, esporádica ou de arquivamento.

## <a name="hot-access-tier"></a>Escalão de acesso frequente

A camada de acesso quente tem custos de armazenamento maiores do que as camadas frias e de arquivo morto, mas os custos de acesso mais baixos. Os cenários de uso de exemplo para a camada de acesso quente incluem:

- Dados que estão em uso ativo ou que devem ser acessados (lidos e gravados) com frequência.
- Dados que são preparados para processamento e migração eventual para a camada de acesso fria.

## <a name="cool-access-tier"></a>Escalão de acesso esporádico

A camada de acesso frio reduz os custos de armazenamento e os custos de acesso mais altos em comparação com o armazenamento dinâmico. Esta camada destina-se a dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Os cenários de uso de exemplo para a camada de acesso frio incluem:

- Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
- Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
- Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,* , armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de acesso de arquivo

A camada de acesso de arquivamento tem o menor custo de armazenamento e os maiores custos de recuperação de dados em comparação com as camadas quentes e frias. Esta camada destina-se a dados que podem tolerar várias horas de latência de obtenção e que vão permanecer na camada de arquivo, pelo menos, 180 dias.

Enquanto um blob está no armazenamento de arquivo morto, os dados de blob ficam offline e não podem ser lidos, copiados, substituídos ou modificados. Não é possível tirar instantâneos de um blob no armazenamento de arquivos. No entanto, os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e suas propriedades. Para BLOBs no arquivo morto, as únicas operações válidas são getblobproperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob.

Os cenários de uso de exemplo para a camada de acesso de arquivamento incluem:

- Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
- Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,* , ficheiros de multimédia não processados após a transcodificação noutros formatos)
- Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo*, vídeo de câmara de segurança, raios X/MRIs para empresas de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blobs

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]
Consulte [dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md) para saber mais.  

## <a name="account-level-tiering"></a>Camadas em nível de conta

Os BLOBs em todas as três camadas de acesso podem coexistir dentro da mesma conta. Um blob que não tenha uma camada atribuída explicitamente infere a camada da definição da camada de acesso da conta. Se a camada de acesso for inferida da conta, você verá a propriedade de blob **camada de acesso inferida** definida como "true", e a propriedade BLOB da camada de **acesso** ao blob corresponderá à camada da conta. No portal do Azure, a propriedade _camada de acesso deduzida_ é exibida com a camada de acesso de blob como **ativa (inferida)** ou **fria (inferida)** .

A alteração da camada de acesso da conta aplica-se a todos os objetos inferidos da _camada de acesso_ armazenados na conta que não têm uma camada explícita definida. Se mudar a camada da conta de frequente para esporádica, são-lhe cobradas operações de escrita (por 10 000) para todos os blobs que não tenham uma camada definida em contas GPv2 apenas. Não há nenhum encargo para essa alteração nas contas de armazenamento de BLOBs. Você será cobrado por operações de leitura (por 10.000) e por recuperação de dados (por GB) se alternar de fria para quente no armazenamento de BLOBs ou em contas de GPv2.

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

As camadas ao nível do blob permitem-lhe alterar a camada dos seus dados ao nível do objeto através de uma operação individual, chamada [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Pode alterar facilmente a camada de acesso de um blob de entre as camadas frequente, esporádica ou de arquivo à medida que os padrões de utilização se modificam, sem ter de mover dados entre contas. Todas as alterações de camada acontecem imediatamente. No entanto, reidratar um blob do arquivo pode levar várias horas.

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Se um blob estiver na camada de arquivo morto, ele não poderá ser substituído, portanto, carregar o mesmo BLOB não é permitido neste cenário. Você pode substituir um blob em uma camada quente ou fria; nesse caso, o novo BLOB herda a camada do blob que foi substituído.

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. Você também não pode alterar atualmente a camada de um blob de blocos que tenha instantâneos.

### <a name="blob-lifecycle-management"></a>Gerenciamento do ciclo de vida do blob

O gerenciamento do ciclo de vida do armazenamento de BLOBs oferece uma política avançada baseada em regras que você pode usar para fazer a transição de seus dados para a melhor camada de acesso e para expirar os dados no final do ciclo de vida. Consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md) para saber mais.  

> [!NOTE]
> Os dados armazenados em uma conta de armazenamento de blob de blocos (desempenho Premium) atualmente não podem ser colocados em camadas para quente, frio ou arquivo morto usando [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ou usando o gerenciamento de ciclo de vida do armazenamento de BLOBs do Azure.
> Para mover dados, você deve copiar blobs de forma síncrona da conta de armazenamento de blobs de blocos para a camada de acesso quente em uma conta diferente usando o [bloco Put da API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que dê suporte a essa API.
> O *bloco Put da API de URL* copia dados de forma síncrona no servidor, o que significa que a chamada é concluída apenas uma vez que todos os dados são movidos do local do servidor original para o local de destino.

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando um blob é movido para uma camada mais fria (Hot-> frio, arquivo > quente ou arquivo de > frio), a operação é cobrada como uma operação de gravação na camada de destino, em que a operação de gravação (por 10.000) e encargos de gravação de dados (por GB) da camada de destino se aplicam.

Quando um blob é movido para uma camada mais quente (arquivo morto-> frio, > quente ou frio > quente), a operação é cobrada como uma leitura da camada de origem, em que a operação de leitura (por 10.000) e os encargos de recuperação de dados (por GB) da camada de origem se aplicam. Podem também aplicar-se cobranças com deteções precoces para qualquer blob que seja retirado da camada esporádica ou de arquivo. A tabela a seguir resume como as alterações de camada são cobradas.

| | **Encargos de gravação (operação + acesso)** | **Ler encargos (operação + acesso)**
| ---- | ----- | ----- |
| **Direção do SetBlobTier** | frio de > quente,<br> arquivo de > quente,<br> arquivo de > frio | arquivo morto-> frio,<br> arquivo morto-> quente,<br> frio-> quente

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Para além dos custos por mês por GB, qualquer blob que seja movido para a camada esporádica (apenas contas GPv2) está sujeito a um período de 30 dias de eliminação precoce esporádica e qualquer blob que seja incluído na camada de arquivos está sujeito a um período de 180 dias de eliminação precoce de arquivos. Estes custos são rateados. Por exemplo, se um blob for movido para arquivo e, em seguida, for eliminado ou movido para a camada frequente após 45 dias, é-lhe cobrada uma tarifa de eliminação precoce equivalente a 135 (180 menos 45) dias de armazenamento desse blob no arquivo.

Você pode calcular a exclusão antecipada usando a propriedade BLOB, **Last-Modified**, se não houver nenhuma alteração na camada de acesso. Caso contrário, você pode usar quando a camada de acesso foi modificada pela última vez para fria ou arquivo morto exibindo a propriedade blob: **acesso-camada-alteração-tempo**. Para obter mais informações sobre propriedades de BLOB, consulte [obter propriedades de blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparando opções de armazenamento de blobs de blocos

A tabela a seguir mostra uma comparação do armazenamento de blobs de blocos de desempenho premium e as camadas de acesso frequente, fria e de arquivo.

|                                           | **Desempenho premium**   | **Camada quente** | **Camada fria**       | **Camada de arquivo morto**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilidade**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilidade** <br> **(leituras RA-GRS)**  | N/A                       | 99,99%       | 99,9%               | Offline           |
| **Custos de utilização**                         | Custos de armazenamento mais altos, menor acesso e custo da transação | Custos de armazenamento mais altos, acesso menor e custos de transações | Custos de armazenamento mais baixos, acesso mais alto e custos de transações | Custos de armazenamento mais baixos, acesso mais alto e custos de transações |
| **Tamanho mínimo do objeto**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Duração mínima do armazenamento**              | N/D                       | N/A          | 30 dias<sup>1</sup> | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | Milissegundos de dígito único | milissegundos | milissegundos        | horas<sup>2</sup> |

<sup>1</sup> os objetos na camada fria em contas GPv2 têm uma duração de retenção mínima de 30 dias. As contas de armazenamento de BLOBs não têm uma duração de retenção mínima para a camada fria.

<sup>2</sup> armazenamento de arquivos atualmente dá suporte a duas reidratar prioridades, alta e padrão, que oferecem latências de recuperação diferentes. Para obter mais informações, consulte [blob reidratação](#blob-rehydration).

> [!NOTE]
> As contas de armazenamento de BLOBs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de uso geral v2. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, são demonstrados os seguintes cenários através do portal do Azure:

- Como alterar a camada de acesso predefinida de uma conta de armazenamento de Blobs ou GPv2.
- Como alterar a camada de um blob numa conta de armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso predefinida de uma conta GPv2 ou de Armazenamento de Blobs

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.

1. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.

1. Selecione a camada de acesso certa para suas necessidades: Defina a **camada de acesso** como **fria** ou **quente**.

1. Clique em **Guardar**, na parte superior do painel.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob em uma conta de armazenamento de BLOBs ou de GPv2

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Para navegar para o blob na conta de armazenamento, selecione Todos os Recursos, selecione a conta de armazenamento, selecione o contentor e, em seguida, selecione o blob.

1. Na folha **Propriedades do blob** , selecione o botão **alterar camada** para abrir a folha da camada.

1. Selecione a camada de acesso **quente**, **fria**ou de **arquivamento** . Se o blob estiver atualmente no arquivo morto e você quiser reidratar-lo em uma camada online, você também poderá selecionar uma prioridade reidratar de **padrão** ou **alta**.

1. Clique em **OK** na parte inferior do painel.

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento usam um modelo de preços para armazenamento de blobs de blocos com base na camada de cada blob. Tenha em mente as seguintes considerações de cobrança:

- **Custos de armazenamento**: Além da quantidade de dados armazenados, o custo de armazenamento de dados varia dependendo da camada de acesso. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
- **Custos de acesso a dados**: As cobranças de acesso a dados aumentam à medida que a camada fica mais fria. Para dados na camada de acesso fria e de arquivo morto, você será cobrado por um encargo de acesso a dados por gigabyte para leituras.
- **Custos de transações**: Há uma cobrança por transação para todas as camadas que aumentam à medida que a camada fica mais fria.
- **Custos de transferência de dados de replicação geográfica**: Essa cobrança só se aplica a contas com a replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: As transferências de dados de saída (dados transferidos para fora de uma região do Azure) incorrem na cobrança do uso de largura de banda por gigabyte, consistentes com contas de armazenamento de uso geral.
- **Alterando a camada de acesso**: Alterar a camada de acesso da conta resultará em encargos de alteração de camada para BLOBs de _camada de acesso_ inferidos armazenados na conta que não têm uma camada explícita definida. Para obter informações sobre como alterar a camada de acesso de um único BLOB, consulte [cobrança em camadas no nível do blob](#blob-level-tiering-billing).

> [!NOTE]
> Para obter mais informações sobre os preços para BLOBs de blocos, consulte a página de [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) . Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>FAQ

**Posso utilizar contas de armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas GPv1 não suportam as camadas.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todas as três camadas de acesso (quentes, frias e de arquivamento) na mesma conta?**

Sim. O atributo de **camada de acesso** definido no nível da conta é a camada de conta padrão que se aplica a todos os objetos nessa conta sem uma camada de conjunto explícita. No entanto, a criação de camadas ao nível do blob permite-lhe definir a camada de acesso ao nível do objeto, independentemente de qual seja a definição de camada de acesso da conta. Os BLOBs em qualquer uma das três camadas de acesso (quente, fria ou arquivo) podem existir na mesma conta.

**Posso alterar a camada de acesso padrão do meu BLOB ou da conta de armazenamento do GPv2?**

Sim, você pode alterar a camada de conta padrão definindo o atributo de **camada de acesso** na conta de armazenamento. A alteração da camada de conta aplica-se a todos os objetos armazenados na conta que não têm um conjunto de camadas explícita (por exemplo, **quente (inferido)** ou **frio (inferido)** ). Alternar a camada de conta de operações de gravação quentes para frias (por 10.000) para todos os BLOBs sem uma camada de conjunto somente nas contas GPv2 e alternar de fria para quente incorre em operações de leitura (por 10.000) e de recuperação de dados (por GB) para todos os BLOBs no armazenamento de BLOBs e contas de GPv2.

**Posso definir a camada de acesso de conta predefinida como arquivo?**

Não. Somente as camadas de acesso quente e fria podem ser definidas como a camada de acesso de conta padrão. A camada de arquivo só pode ser definida ao nível do objeto.

**Em quais regiões os níveis de acesso quente, frio e arquivo estão disponíveis?**

As camadas de acesso quente e fria juntamente com camadas no nível do blob estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**Os BLOBs na camada de acesso frio se comportam de forma diferente daqueles na camada de acesso quente?**

Os BLOBs na camada de acesso quente têm a mesma latência que os BLOBs nas contas de armazenamento GPv1, GPv2 e BLOB. Os BLOBs na camada de acesso frio têm uma latência semelhante (em milissegundos) como BLOBs nas contas de armazenamento GPv1, GPv2 e BLOB. Os BLOBs na camada de acesso de arquivamento têm várias horas de latência em contas de armazenamento de GPv1, GPv2 e blobs.

Os BLOBs na camada de acesso frio têm um SLA (nível de serviço) de disponibilidade ligeiramente menor do que os BLOBs armazenados na camada de acesso quente. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são iguais?**

Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivamento válidas, incluindo getblobproperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob, são de 100% consistentes com a quente e a fria. Os dados de BLOB não podem ser lidos ou modificados enquanto estiverem na camada de arquivo até a operação; somente as operações de leitura de metadados de blob têm suporte durante o arquivamento.

**Como posso saber que a reidratação de um blob a partir da camada de arquivo para a camada frequente ou esporádica foi concluída?**

Durante a reidratação, pode utilizar a operação “get blob properties” para consultar o atributo **Archive Status** e verificar se a alteração de camada já está concluída. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova.  

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada blob é sempre cobrado de acordo com a camada indicada pela propriedade **camada de acesso** do blob. Quando você define uma nova camada para um blob, a propriedade de **camada de acesso** reflete imediatamente a nova camada para todas as transições. No entanto, a reativação de um blob a partir de uma camada de arquivo para um escalão de acesso frequente ou esporádico pode demorar várias horas. Nesse caso, você será cobrado em taxas de arquivamento até que reidratação seja concluído, ponto em que a propriedade da **camada de acesso** reflete a nova camada. Nesse ponto, você será cobrado pelo blob à taxa quente ou fria.

**Como posso saber se me vai ser faturada uma cobrança de eliminação precoce quando eliminar ou retirar um blob da camada esporádica ou de arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de acesso esporádico (contas GPv2 apenas) ou de arquivo antes de terem decorrido 30 e 180 dias, respetivamente, irá incorrer numa cobrança por eliminação precoce rateada. Você pode determinar por quanto tempo um blob esteve na camada fria ou de arquivo verificando a propriedade blob de **alteração da camada de acesso** , que fornece um carimbo da última alteração de camada. Se a camada do blob nunca foi alterada, você poderá verificar a propriedade de blob **modificada pela última vez** . Para obter mais informações, consulte [exclusão antecipada de frio e arquivo](#cool-and-archive-early-deletion).

**Que ferramentas e SDKs do Azure suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivo?**

O portal, o PowerShell e as ferramentas da CLI do Azure e as bibliotecas de.NET, Java, Python e Node.js suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivos.  

**Quantos dados posso armazenar nas camadas frequente, esporádica e de arquivo?**

O armazenamento de dados junto com outros limites é definido no nível da conta e não por camada de acesso. Portanto, você pode optar por usar todo o limite em uma camada ou em todas as três camadas. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>Avaliar a quente, a frio e o arquivo em contas de armazenamento de BLOBs e GPv2

[Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)

[Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)

[Saiba mais sobre os dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)

[Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
