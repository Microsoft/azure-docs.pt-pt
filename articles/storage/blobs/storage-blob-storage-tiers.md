---
title: Camadas frequente, esporádica e de arquivo de camadas de acesso para blobs - armazenamento do Azure
description: Camadas frequente, esporádica e de arquivo escalões de acesso para contas de armazenamento do Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 78158f49748a8e9e08f3695860d95a2a6eff9fd4
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402444"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Armazenamento de Blobs do Azure: frequente, esporádico e de arquivo acesso

O armazenamento do Azure disponibiliza camadas de acesso diferentes, que lhe permitem armazenar dados de objeto de blob da maneira mais econômica. As camadas de acesso disponíveis incluem:

- **Frequente** - otimizado para armazenar dados que são acedidos com frequência.
- **Acesso esporádico** - otimizado para armazenar dados que são acedidos com pouca frequência e armazenados durante, pelo menos, 30 dias.
- **Arquivo** - otimizado para armazenar dados que são raramente acedidos e armazenados durante, pelo menos, 180 dias com os requisitos de latência flexíveis (na ordem das horas).

As seguintes considerações aplicam-se para as camadas de acesso diferentes:

- A camada de acesso de arquivo está disponível apenas ao nível do blob e não ao nível da conta de armazenamento.
- Dados na camada de acesso esporádico podem tolerar disponibilidade ligeiramente inferior, mas ainda requerem uma elevada durabilidade e características de acesso de tempo e de débito semelhantes como dados de acesso frequente. Para dados de acesso esporádico, um contrato de nível de serviço disponibilidade ligeiramente inferior (SLA) e acesso mais alto custos comparados comparados frequentes são compromissos aceitáveis em troca de custos do armazenamento.
- O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados.
- Apenas os escalões de acesso frequente e esporádico podem ser definidos ao nível da conta.
- Acesso frequente, esporádico e arquivo pode ser definido ao nível do objeto.

Dados armazenados na cloud que cresce um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Dados armazenados na cloud podem ser diferentes em termos de como ele tem gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem Inativos na cloud e é raramente, acedidos após serem armazenados.

Cada um destes cenários de acesso de dados beneficia de uma camada de acesso diferentes que está otimizada para um padrão de acesso específico. Com acesso frequente, esporádico e de camadas de acesso de arquivo, BLOBs do Azure storage endereços esta necessidade de camadas de acesso diferenciadas com modelos de preços distintos.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

Pode apenas seus dados de armazenamento de objetos para acesso frequente, esporádica camada ou arquivos no armazenamento de BLOBs e fins gerais v2 (contas GPv2). As contas de Fins Gerais (GPv1) não suportam as camadas. No entanto, pode converter facilmente contas de armazenamento de BLOBs ou GPv1 existentes para as contas GPv2 através de um processo num único clique no portal do Azure. GPv2 fornece uma nova estrutura de preços para blobs, ficheiros e filas e acesso a uma variedade de outros novos recursos de armazenamento. Daqui em diante, alguns recortes de recursos e os preços novos serão oferecidos apenas em contas GPv2. Por isso, deve avaliar a utilização de contas GPv2, mas utilizá-las apenas depois de rever os preços para todos os serviços. Algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

Armazenamento de BLOBs e GPv2 expõem de contas do **camada de acesso** atributo ao nível da conta. Esse atributo permite-lhe especificar a camada de acesso predefinida como frequente ou esporádica para qualquer blob na conta de armazenamento que não tem uma camada explícita definida ao nível do objeto. Relativamente aos objetos cuja camada está definida ao nível do objeto, a camada da conta não se aplicará. A camada de arquivo pode ser aplicada apenas ao nível do objeto. Pode alternar entre estas camadas de acesso em qualquer altura.

## <a name="premium-performance-block-blob-storage"></a>Armazenamento de BLOBs de blocos de desempenho Premium

Armazenamento de BLOBs de blocos de desempenho Premium disponibiliza uma dados acedidos com frequência por meio de hardware de elevado desempenho. Dados neste escalão de desempenho são armazenados em unidades de estado sólido (SSDs), que estão otimizadas para latência baixa e consistente. Os SSDs fornecem taxas de transacionais mais elevadas e débito em comparação comparada as unidades de disco rígido tradicionais.

Armazenamento de BLOBs de blocos de desempenho Premium é ideal para cargas de trabalho que exigem tempos de resposta rápida e consistente. É melhor para cargas de trabalho que realizam muitas pequenas transações, como a captura de dados de telemetria, mensagens e transformação de dados. Dados que envolva os utilizadores finais, como edição de vídeo interativo, o conteúdo web estático e as transações online também são bons candidatos.

Armazenamento de BLOBs de blocos de desempenho Premium está disponível apenas via o tipo de conta de armazenamento de BLOBs de bloco e não suporta atualmente a disposição em camadas para frequente, esporádico ou arquivo de acesso.

## <a name="hot-access-tier"></a>Escalão de acesso frequente

O escalão de acesso frequente tem custos de armazenamento superiores de escalões de acesso esporádico e arquivo, mas os custos de acesso mais baixos. Exemplos de cenários de utilização para o escalão de acesso frequente incluem:

- Dados que está a ser utilizado de Active Directory ou devem ser acedidos (de leitura e escrita) com frequência.
- Dados preparados para processamento e eventual migração para o escalão de acesso esporádico.

## <a name="cool-access-tier"></a>Escalão de acesso esporádico

O escalão de acesso esporádico tem custos de armazenamento inferior e custos de acesso superiores comparados para armazenamento frequente. Esta camada destina-se a dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Exemplos de cenários de utilização para o escalão de acesso esporádico incluem:

- Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
- Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
- Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de acesso de arquivo

A camada de acesso de arquivo tem o custo de armazenamento mais baixo e os custos de obtenção de dados superior em comparação com os escalões acesso frequentes e esporádico. Esta camada destina-se a dados que podem tolerar várias horas de latência de obtenção e que vão permanecer na camada de arquivo, pelo menos, 180 dias.

Embora seja um blob no armazenamento de arquivo, os dados de blob está offline e não podem ser lidos, copiados, substituídos ou modificados. Não é possível tirar instantâneos de BLOBs no armazenamento de arquivo. No entanto, os metadados do blob permanecem online e disponível, permitindo que listar os BLOBs e as respetivas propriedades. Para blobs em arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob.

Exemplos de cenários de utilização para a camada de acesso de arquivo incluem:

- Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
- Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação noutros formatos)
- Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo*, filmagens de câmaras de segurança, X-raios/RMS antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de cliente chamadas para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blobs

Para ler os dados no armazenamento de arquivo, tem de, primeiro, alterar a camada do blob para frequente ou esporádica. Este processo é conhecido como “reidratação” e pode demorar até 15 horas a ser concluído. Tamanhos de BLOBs grandes são recomendados para um desempenho ideal. “Reidratar” vários pequenos blobs em simultâneo pode provocar mais atrasos.

Durante a “reidratação”, pode verificar a propriedade **Archive Status** do blob para confirmar se a camada foi alterada. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova.  

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

As camadas ao nível do blob permitem-lhe alterar a camada dos seus dados ao nível do objeto através de uma operação individual, chamada [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Pode alterar facilmente a camada de acesso de um blob de entre as camadas frequente, esporádica ou de arquivo à medida que os padrões de utilização se modificam, sem ter de mover dados entre contas. Todas as alterações de camada acontecem imediatamente. No entanto, se um blob de arquivo, pode demorar várias horas.

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Se um blob estiver na camada de arquivo, ele não é possível substituir, para que carregar o mesmo blob não é permitido neste cenário. Pode substituir um blob numa camada de acesso frequente ou esporádico, nesse caso, o blob novo herda a camada do blob que foi substituído.

BLOBs em todas as camadas de acesso de três podem coexistir na mesma conta. Um blob que não tenha uma camada atribuída explicitamente infere a camada da definição da camada de acesso da conta. Se a camada de acesso for inferida a partir da conta, consulte a **camada de acesso inferida** propriedade definida como "true" e o blob de BLOBs **camada de acesso** propriedade de blob corresponde à camada da conta. No portal do Azure, a camada de acesso inferida a propriedade é apresentada com a camada de acesso do blob (por exemplo, **acesso frequente (inferido)** ou **esporádico (inferido)**).

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. Também não é possível alterar a camada de um blob de bloco que tem instantâneos.

> [!NOTE]
> Dados armazenados numa conta de armazenamento de BLOBs de bloco atualmente não podem ser camadas frequente, esporádico ou arquivo usando [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ou utilizar a gestão de ciclo de vida de armazenamento de Blobs do Azure.
> Para mover dados, deve copiar sincronicamente blobs da conta de armazenamento de BLOBs de blocos para o escalão de acesso frequente numa conta diferente com o [colocar o bloco de API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que suporte esta API.
> O *colocar o bloco de URL* API de forma síncrona copia os dados no servidor, que significa que a chamada é concluída apenas uma vez todos os dados são movidos da localização original do servidor para a localização de destino.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

Gestão de ciclo de vida de armazenamento de BLOBs (pré-visualização) oferece uma política de avançado e baseado em regras que pode utilizar para fazer a transição de seus dados para a camada de acesso melhor e expirar os dados no final do seu ciclo de vida. Ver [gerir o ciclo de vida de armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md) para saber mais.  

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádica, frequente -> arquivos ou esporádica -> arquivos), a operação é faturada como uma operação de escrita para a camada de destino, onde a operação de escrita (por 10 000) e os encargos de escrita (por GB) de dados da camada de destino são aplicáveis.

Quando um blob é movido para uma camada mais frequente (arquivo -> esporádica, arquivo -> frequente ou esporádica -> frequente), a operação é faturada como uma leitura da camada de origem, em que a operação de leitura (por 10 000) e os encargos de obtenção (por GB) de dados de camada de origem são aplicáveis. A tabela seguinte resume como são cobradas as alterações às camadas.

| | **Escrever custos (operação + acesso)** | **Custos de leitura (operação + acesso)**
| ---- | ----- | ----- |
| **Direção de SetBlobTier** | frequente -> esporádica, frequente -> arquivos, acesso esporádico -> arquivos | arquivo -> esporádica, arquivo -> frequente, esporádica -> frequente

Se mudar a camada da conta de frequente para esporádica, são-lhe cobradas operações de escrita (por 10 000) para todos os blobs que não tenham uma camada definida em contas GPv2 apenas. Não existe nenhum custo associado para que esta alteração nas contas de armazenamento de Blobs. Se mudar a conta GPv2 ou de armazenamento de Blobs de esporádica para frequente, são-lhe cobradas as operações de escrita (por 10 000) e obtenção de dados (por GB). Podem também aplicar-se cobranças com deteções precoces para qualquer blob que seja retirado da camada esporádica ou de arquivo.

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Para além dos custos por mês por GB, qualquer blob que seja movido para a camada esporádica (apenas contas GPv2) está sujeito a um período de 30 dias de eliminação precoce esporádica e qualquer blob que seja incluído na camada de arquivos está sujeito a um período de 180 dias de eliminação precoce de arquivos. Estes custos são rateados. Por exemplo, se um blob for movido para arquivo e, em seguida, for eliminado ou movido para a camada frequente após 45 dias, é-lhe cobrada uma tarifa de eliminação precoce equivalente a 135 (180 menos 45) dias de armazenamento desse blob no arquivo.

Pode calcular a eliminação antecipada, utilizando a propriedade de blob **hora de criação**, não se tiver ocorrido nenhum acesso alterações às camadas. Caso contrário, pode utilizar quando a camada de acesso foi modificado pela última vez para acesso esporádico ou arquivo visualizando a propriedade de blob: **alteração-tempo de acesso-escalão**. Para obter mais informações sobre as propriedades de blob, veja [obter propriedades de Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparar opções de armazenamento de BLOBs de bloco

A seguinte tabela mostra uma comparação de armazenamento de BLOBs de blocos de desempenho premium e o acesso frequente, esporádico e arquivo escalões de acesso.

|                                           | **Desempenho Premium** | **Escalão de acesso frequente** | **Escalão de acesso esporádico** | **Camada de arquivo**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Disponibilidade**                          | 99,9%            | 99,9%        | 99% | N/D |
| **Disponibilidade** <br> **(leituras RA-GRS)**  | N/A              | 99,99%       | 99,9% | N/D |
| **Custos de utilização**                         | Custos de armazenamento superiores, menor de acesso e os custos de transação | Custos de armazenamento superiores, menor de acesso e os custos de transação | Custos de armazenamento mais baixos, acesso superiores e os custos de transação | Custos de armazenamento mais baixos, acesso a mais alto e os custos de transação |
| **Tamanho mínimo do objeto**                   | N/D | N/D | N/D | N/D |
| **Duração mínima do armazenamento**              | N/D | N/A | 30 dias (GPv2 apenas) | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | Milissegundos de dígito | milissegundos | milissegundos | < 15 hrs

> [!NOTE]
> Para destinos de escalabilidade e desempenho, consulte [metas de escalabilidade e desempenho de armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, são demonstrados os seguintes cenários através do portal do Azure:

- Como alterar a camada de acesso predefinida de uma conta de armazenamento de Blobs ou GPv2.
- Como alterar a camada de um blob numa conta de armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso predefinida de uma conta GPv2 ou de Armazenamento de Blobs

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.

1. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.

1. Selecione a camada de acesso corretas para as suas necessidades: Definir o **camada de acesso** a qualquer uma **esporádico** ou **frequente**.

1. Clique em **Guardar**, na parte superior do painel.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob numa conta de armazenamento de BLOBs ou GPv2

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Para navegar para o blob na sua conta de armazenamento, selecione **todos os recursos**, selecione a sua conta de armazenamento, selecione o contentor e, em seguida, selecione o blob.

1. Na **propriedades do Blob** painel, selecione a **camada de acesso** menu pendente para selecionar o **frequente**, **esporádico**, ou **arquivo**  camada de acesso.

1. Clique em **Guardar**, na parte superior do painel.

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento utilizam um modelo de preços para o armazenamento de Blobs com base na camada de cada blob. Tenha em atenção as seguintes considerações de faturas:

- **Os custos de armazenamento**: Para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de acesso. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
- **Os custos de acesso a dados**: Aumentar os custos de acesso de dados conforme a camada se torna mais esporádica. Para dados na camada de acesso esporádico e de arquivo, é cobrada uma taxa de acesso de dados por gigabyte para leituras.
- **Os custos de transação**: Existe um custo por transação para todas as camadas que aumenta conforme a camada se torna mais esporádica.
- **Os custos de transferência de dados de Georreplicação**: Este custo aplica-se apenas a contas com a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
- **Custos de transferência de dados de saída**: Transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) incorrem a faturação de utilização de largura de banda numa base por gigabyte, consistente com as contas de armazenamento para fins gerais.
- **A alteração da camada de acesso**: Alteração da camada de acesso de conta de acesso esporádico para frequente incorre um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de acesso da conta de frequente para esporádica incorre um encargo igual à escrita de todos os dados na camada esporádica (contas GPv2 apenas).

> [!NOTE]
> Para obter mais informações sobre os preços para as contas de armazenamento de BLOBs, veja [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) página. Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>FAQ

**Posso utilizar contas de armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas GPv1 não suportam as camadas.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todas as três (frequente, esporádico e de arquivo) escalões na mesma conta de acesso?**

Sim. O atributo **Access Tier** definido ao nível de uma conta é a camada predefinida que se aplica a todos os objetos nessa conta sem uma camada de conjunto explícita. No entanto, a criação de camadas ao nível do blob permite-lhe definir a camada de acesso ao nível do objeto, independentemente de qual seja a definição de camada de acesso da conta. Os BLOBs em qualquer uma das camadas de acesso de três (frequente, esporádico ou arquivo) podem existir na mesma conta.

**Pode alterar a camada de acesso predefinida da minha conta de armazenamento de BLOBs ou GPv2?**

Sim, pode alterar a camada de acesso predefinida, definindo a **camada de acesso** atributo na conta de armazenamento. A alteração da camada de acesso aplica-se a todos os objetos armazenados na conta que não têm uma camada explícita definida. Alteração da camada de acesso de frequente para esporádica implica operações de escrita (por 10 000) para todos os blobs sem uma camada definida apenas em contas GPv2 e a alteração de esporádica para frequente implica operações de leitura (por 10 000) e os custos de obtenção de dados (por GB) para todos os blobs no armazenamento de BLOBs e as contas GPv2.

**Posso definir a camada de acesso de conta predefinida como arquivo?**

Não. Apenas os escalões de acesso frequente e esporádico podem ser definidas como a camada de acesso de conta predefinida. A camada de arquivo só pode ser definida ao nível do objeto.

**Em que regiões são frequente, esporádica e de arquivo acesso disponíveis no?**

Os escalões de acesso frequente e esporádico, juntamente com a camada ao nível de BLOBs estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**Os blobs na camada de acesso esporádico terão um comportamento diferente na camada de acesso frequente?**

Os BLOBs na camada de acesso frequente têm a mesma latência dos blobs nas contas de armazenamento de BLOBs, GPv1 e GPv2. Os BLOBs na camada de acesso esporádico têm uma latência semelhante (em milissegundos) aos blobs nas contas de armazenamento de BLOBs, GPv1 e GPv2. Os BLOBs na camada de acesso de arquivo têm várias horas latência nas contas de armazenamento de BLOBs, GPv1 e GPv2.

Os BLOBs na camada de acesso esporádico têm um nível de serviço disponibilidade de ligeiramente inferior (SLA) dos blobs armazenados na camada de acesso frequente. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são iguais?**

Sim. Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivos válidas, incluindo “delete”, “list”, “get blob properties/metadata” e “set blob tier”, são 100% consistentes com as camadas frequente e esporádica. Os blobs não podem ser lidos nem modificados enquanto estiverem na camada de arquivo.

**Como posso saber que a reidratação de um blob a partir da camada de arquivo para a camada frequente ou esporádica foi concluída?**

Durante a reidratação, pode utilizar a operação “get blob properties” para consultar o atributo **Archive Status** e verificar se a alteração de camada já está concluída. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova.  

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada blob é sempre faturado de acordo com a camada indicada do blob **camada de acesso** propriedade. Ao definir uma nova camada para um blob, o **camada de acesso** propriedade reflete imediatamente o novo escalão para todas as transições. No entanto, a reativação de um blob a partir de uma camada de arquivo para um escalão de acesso frequente ou esporádico pode demorar várias horas. Neste caso, é-lhe cobrada às tarifas de arquivo até que a "reidratação" esteja concluída, altura em que o **camada de acesso** propriedade reflete o novo escalão. Nesse momento é faturado por desse blob na taxa de acesso frequente ou esporádico.

**Como posso saber se me vai ser faturada uma cobrança de eliminação precoce quando eliminar ou retirar um blob da camada esporádica ou de arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de acesso esporádico (contas GPv2 apenas) ou de arquivo antes de terem decorrido 30 e 180 dias, respetivamente, irá incorrer numa cobrança por eliminação precoce rateada. Pode determinar o intervalo de tempo que um blob está na camada de acesso esporádico ou arquivo, verificando a **tempo de alteração da camada de acesso** propriedade, que fornece um carimbo da última alteração da camada de Blobs. Para obter mais informações, consulte [eliminação precoce de acesso esporádico e arquivo](#cool-and-archive-early-deletion).

**Que ferramentas e SDKs do Azure suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivo?**

O portal, o PowerShell e as ferramentas da CLI do Azure e as bibliotecas de.NET, Java, Python e Node.js suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivos.  

**Quantos dados posso armazenar nas camadas frequente, esporádica e de arquivo?**

Armazenamento de dados, juntamente com outros limites são definidos ao nível da conta e não por camada de acesso. Por conseguinte, pode optar por utilizar todas seu limite numa só camada ou nas três. Para obter mais informações, consulte [metas de escalabilidade e desempenho do armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Avaliar as camadas frequente, esporádica e de arquivo nas contas GPv2 e de armazenamento de Blobs

[Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)

[Gerir o ciclo de vida de armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)

[Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)