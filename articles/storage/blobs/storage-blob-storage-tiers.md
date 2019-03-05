---
title: Premium, o armazenamento frequente, esporádico e arquivo de blobs - armazenamento do Azure
description: Premium, de armazenamento frequente, esporádico e arquivo para contas de armazenamento do Azure.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 03/05/2019
ms.author: kuhussai
ms.subservice: blobs
ms.openlocfilehash: 4660a45014e6afdb091fb40b8fe7f03fdb647aab
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339203"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Armazenamento de Blobs do Azure: Premium (pré-visualização), as camadas de armazenamento frequente, esporádico e arquivo

## <a name="overview"></a>Descrição geral

O armazenamento do Azure oferece as camadas de armazenamento diferentes, que lhe permitem armazenar dados de objeto de Blob da maneira mais econômica. As camadas disponíveis incluem:

- **O armazenamento Premium (pré-visualização)** fornece o hardware de elevado desempenho para dados que são acedidos com frequência.
 
- **Armazenamento de frequente**: está otimizada para armazenar dados que são acedidos com frequência. 

- **Armazenamento de acesso esporádico** está otimizada para armazenar dados que são acedidos com pouca frequência e armazenados durante, pelo menos, 30 dias.
 
- **Armazenamento de arquivo** está otimizada para armazenar dados que são raramente acedidos e armazenados durante, pelo menos, 180 dias com os requisitos de latência flexíveis (na ordem das horas).

As seguintes considerações de acompanham as camadas de armazenamento diferentes:

- A camada de armazenamento de arquivo só está disponível ao nível do blob e não ao nível da conta de armazenamento.
 
- Dados na camada de armazenamento de acesso esporádico podem tolerar disponibilidade ligeiramente inferior, mas ainda requerem uma elevada durabilidade e características de acesso de tempo e de débito semelhantes como dados de acesso frequente. Para dados de acesso esporádico, um SLA de disponibilidade ligeiramente inferior e superior acesso custos comparados comparados frequentes são compromissos aceitáveis em troca de custos do armazenamento.

- O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados.
 
- Apenas as camadas de armazenamento frequente e esporádico podem ser definidas ao nível da conta. Atualmente, não é possível definir a camada de arquivo ao nível da conta.
 
- Acesso frequente, esporádico, e níveis de arquivo podem ser definidos ao nível do objeto.

Dados armazenados na cloud que cresce um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes em termos da forma como são gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e são raramente acedidos após serem armazenados ou não são acedidos de todo.

Cada um destes cenários de acesso a dados beneficia de uma camada de armazenamento diferente, otimizada para um padrão de acesso específico. Com camadas de armazenamento frequente, esporádico e arquivo, endereços de armazenamento de Blobs do Azure a esta necessidade de diferenciados camadas de armazenamento com modelos de preços distintos.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

Pode apenas da camada seus dados de armazenamento de objetos para frequente, acesso esporádico ou arquivo no armazenamento de BLOBs e fins gerais v2 (GPv2) contas. As contas de Fins Gerais (GPv1) não suportam as camadas. No entanto, os clientes podem converter facilmente as respetivas contas GPv1 ou de armazenamento de Blobs existentes em contas GPv2 através de um processo de um só clique no portal do Azure. GPv2 proporciona uma nova estrutura de preço para blobs, ficheiros e filas, bem como acesso a uma variedade de outras funcionalidades de armazenamento novas. Além disso, no futuro, algumas funcionalidades e reduções de preços só estarão disponíveis nas contas GPv2. Por este motivo, os clientes devem considerar a utilização destas contas, mas utilizá-las apenas depois de analisarem os preços de todos os serviços, pois algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

Armazenamento de BLOBs e GPv2 expõem de contas do **camada de acesso** atributo ao nível da conta, que permite que especifique a camada de armazenamento predefinida como frequente ou esporádica para qualquer blob na conta de armazenamento que não tem uma camada explícita definida ao nível de objeto. Relativamente aos objetos cuja camada está definida ao nível do objeto, a camada da conta não se aplicará. A camada de arquivo só pode ser aplicada ao nível do objeto. Pode alternar entre estas camadas de armazenamento em qualquer altura.

## <a name="premium-access-tier"></a>Camada de acesso de Premium

Disponível em pré-visualização, é uma camada de acesso de Premium, que torna os dados disponíveis por meio de hardware de elevado desempenho acedidos com frequência. Dados armazenados neste escalão são armazenados em unidades de estado sólidas, que são otimizadas para latência mais baixa e taxas de transacionais mais elevadas em comparação comparadas as unidades de disco rígido tradicionais. A camada de acesso de Premium está disponível via o tipo de conta de armazenamento de Blob de blocos apenas.

Esta camada é ideal para cargas de trabalho que exigem tempos de resposta rápida e consistente. Dados que envolva os utilizadores finais, como edição, estático conteúdo da web, transações online e assim por diante são bons candidatos para a camada de acesso de Premium de vídeo interativo. Esta camada é ideal para cargas de trabalho que realizam muitas pequenas transações, como a captura de dados de telemetria, mensagens e transformação de dados.

## <a name="hot-access-tier"></a>Escalão de acesso frequente

Armazenamento frequente tem custos de armazenamento superiores de acesso esporádico e arquivo de armazenamento, mas os custos de acesso mais baixos. Exemplos de cenários de utilização para a camada de armazenamento frequente incluem:

* Dados que estão a ser utilizados ativamente ou que deverão ser acedidos (para operações de leitura e escrita) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento de acesso esporádico.

## <a name="cool-access-tier"></a>Escalão de acesso esporádico

Camada de armazenamento esporádico tem custos mais baixos de armazenamento e custos de acesso superiores comparados para armazenamento frequente. Esta camada destina-se a dados que permanecem na camada de acesso esporádica durante, pelo menos, 30 dias. Exemplos de cenários de utilização para a camada de armazenamento de acesso esporádico incluem:

* Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
* Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
* Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de acesso de arquivo

Armazenamento de arquivo tem o custo de armazenamento mais baixo e os custos de obtenção de dados superior em comparação com o armazenamento frequente e esporádico. Esta camada destina-se a dados que podem tolerar várias horas de latência de obtenção e irão permanecer na camada de arquivo por, pelo menos, 180 dias.

Embora seja um blob no armazenamento de arquivo, os dados de blob está offline e não podem ser lidos, copiados, substituídos ou modificados. Nem pode tirar instantâneos de BLOBs no armazenamento de arquivo. No entanto, os metadados do blob permanecem online e disponível, permitindo que listar os BLOBs e as respetivas propriedades. Para blobs em Arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob. 


Exemplos de cenários de utilização para a camada de armazenamento de arquivo incluem:

* Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
* Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação noutros formatos)
* Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo,*, filmagens de câmaras de segurança, raios X/RMs antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de chamadas dos clientes para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blobs
Para ler os dados no armazenamento de arquivo, primeiro tem de alterar a camada de blob para frequente ou esporádica. Este processo é conhecido como “reidratação” e pode demorar até 15 horas a ser concluído. Tamanhos de BLOBs grandes são recomendados para um desempenho ideal. “Reidratar” vários pequenos blobs em simultâneo pode provocar mais atrasos.

Durante a “reidratação”, pode verificar a propriedade **Archive Status** do blob para confirmar se a camada foi alterada. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade de estado de arquivo for removida e o **camada de acesso** blob propriedade reflete o escalão acesso esporádico ou frequente de novo.  

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

As camadas ao nível do blob permitem-lhe alterar a camada dos seus dados ao nível do objeto através de uma operação individual, chamada [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Pode facilmente alterar o escalão de acesso de um blob de entre os escalões acesso frequente, esporádico ou arquivo como alteração de padrões de utilização, sem ter de mover dados entre contas. Todas as alterações de camada acontecem imediatamente. No entanto, se um blob de arquivo, pode demorar várias horas. 

A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Se um blob estiver na camada de arquivo, ele não é possível substituir, para que carregar o mesmo blob não é permitido neste cenário. Pode substituir um blob num acesso frequente ou escalão acesso esporádico, nesse caso, o blob novo herda a camada do blob que foi substituído.

Podem coexistir na mesma conta blobs nas três camadas de armazenamento. Um blob que não tenha uma camada atribuída explicitamente infere a camada da definição da camada de acesso da conta. Se a camada de acesso for inferida a partir da conta, consulte a **camada de acesso inferida** propriedade definida como "true" e o blob de BLOBs **camada de acesso** propriedade de blob corresponde à camada da conta. No portal do Azure, a propriedade da camada de acesso inferido é apresentada com a camada de acesso do blob (por exemplo, acesso frequente (inferido) ou esporádico (inferido)).

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. Também não é possível alterar a camada de um blob de bloco que tem instantâneos.

> [!NOTE]
> Dados armazenados na camada de acesso Premium atualmente não podem ser camadas frequente, esporádico ou arquivo usando [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ou utilizar a gestão de ciclo de vida de armazenamento de Blobs do Azure. Para mover dados, tem forma síncrona de copiar blobs contra o acesso de Premium para frequente utilizando o [colocar o bloco de API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que suporte esta API. O *colocar o bloco de URL* API de forma síncrona copia os dados no servidor, que significa que a chamada é concluída apenas uma vez todos os dados são movidos da localização original do servidor para a localização de destino.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob
Gestão de ciclo de vida de armazenamento de BLOBs (pré-visualização) oferece uma política de avançado e baseado em regras que pode utilizar para fazer a transição de seus dados para a camada de acesso melhor e expirar os dados no final do seu ciclo de vida. Ver [gerir o ciclo de vida de armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md) para saber mais.  

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádica, frequente -> arquivos ou esporádica -> arquivos), a operação é faturada como uma operação de escrita para a camada de destino, onde a operação de escrita (por 10 000) e os encargos de escrita (por GB) de dados da camada de destino são aplicáveis. Quando um blob é movido para uma camada mais frequente (arquivo -> esporádica, arquivo -> frequente ou esporádico -> frequente), a operação é faturada como uma leitura da camada de origem, em que a operação de leitura (por 10 000) e os encargos de obtenção (por GB) de dados de camada de origem são aplicáveis. A tabela seguinte resume como são cobradas as alterações às camadas.

| | **Escrever custos (operação + acesso)** | **Custos de leitura (operação + acesso)** 
| ---- | ----- | ----- |
| **Direção de SetBlobTier** | Frequente -> esporádica, frequente -> arquivos, esporádica -> arquivos | Arquivo -> esporádica, arquivo -> frequente, esporádica -> frequente

Se mudar a camada da conta de acesso frequente para esporádico, será cobrado para operações de escrita (por 10 000) para todos os blobs sem uma camada definida apenas em contas GPv2. Não existe nenhum custo associado para que esta alteração nas contas de armazenamento de Blobs. Será cobrado para operações de leitura (por 10 000) e obtenção de dados (por GB) se mudar seu armazenamento de BLOBs ou GPv2 conta de acesso esporádico para frequente. Podem aplicar-se também Cobranças com deteções precoces para qualquer blob retirado da camada de acesso esporádico ou arquivo.

### <a name="cool-and-archive-early-deletion"></a>Eliminação antecipada de Acesso Esporádico e Arquivo

Para além por GB, por mês qualquer blob que seja movido na camada esporádica (contas GPv2 apenas) está sujeito a um período de eliminação antecipada de 30 dias, e qualquer blob que seja movido para a camada de arquivo está sujeito a um período de eliminação antecipada do arquivo de 180 dias. Estes custos são rateados. Por exemplo, se um blob é movido para arquivo e, em seguida, eliminado ou movido para a camada frequente após 45 dias, será cobrada uma taxa de eliminação antecipada equivalente a 135 (180 menos 45) dias de armazenamento desse blob no arquivo.

Pode calcular a eliminação antecipada, utilizando a propriedade de blob **hora de criação**, não se tiver ocorrido nenhum acesso alterações às camadas. Caso contrário, pode utilizar quando a camada de acesso foi modificado pela última vez para acesso esporádico ou arquivo visualizando a propriedade de blob: **alteração-tempo de acesso-escalão**. Para obter mais informações sobre as propriedades de blob, veja [obter propriedades de Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparison-of-the-storage-tiers"></a>Comparação das camadas de armazenamento

A tabela seguinte mostra uma comparação entre o acesso frequente, esporádica e de camadas de armazenamento de arquivo.

| | **Camada de armazenamento frequente** | **Camada de armazenamento esporádico** | **Camada de armazenamento de arquivo**
| ---- | ----- | ----- | ----- |
| **Disponibilidade** | 99,9% | 99% | N/D |
| **Disponibilidade** <br> **(leituras RA-GRS)**| 99,99% | 99,9% | N/D |
| **Custos de utilização** | Custos de armazenamento superiores, menor de acesso e os custos de transação | Custos de armazenamento mais baixos, acesso superiores e os custos de transação | Custos de armazenamento mais baixos, acesso a mais alto e os custos de transação |
| **Tamanho mínimo do objeto** | N/D | N/D | N/D |
| **Duração mínima do armazenamento** | N/D | 30 dias (GPv2 apenas) | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | milissegundos | milissegundos | < 15 hrs
| **Metas de escalabilidade e desempenho** | Igual às contas do Storage para fins gerais | Igual às contas do Storage para fins gerais | Igual às contas do Storage para fins gerais |

> [!NOTE]
> As contas do Blob Storage suportam as mesmas metas de desempenho e escalabilidade que as contas do Storage para fins gerais. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, são demonstrados os seguintes cenários através do portal do Azure:

* Como alterar a camada de acesso predefinida de uma conta de armazenamento de Blobs ou GPv2.
* Como alterar a camada de um blob numa conta de armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso predefinida de uma conta GPv2 ou de Armazenamento de Blobs

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.

3. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.

4. Selecione a camada de armazenamento correta para as suas necessidades: Definir o **camada de acesso** a qualquer uma **esporádico** ou **frequente**.

5. Clique em Guardar na parte superior do painel.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Altere a camada de um blob numa conta GPv2 ou de armazenamento de Blobs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Para navegar para o blob na conta de armazenamento, selecione Todos os Recursos, selecione a conta de armazenamento, selecione o contentor e, em seguida, selecione o blob.

3. No painel de propriedades do Blob, clique no menu pendente **Camada de acesso** para selecionar a camada de armazenamento **Frequente**, **Esporádica** ou **Arquivo**.

5. Clique em Guardar na parte superior do painel.

## <a name="pricing-and-billing"></a>Preços e faturação

Todas as contas de armazenamento utilizam um modelo de preços para o armazenamento de Blobs com base na camada de cada blob. Tenha em atenção as seguintes considerações de faturas:

* **Os custos de armazenamento**: Para além da quantidade de dados armazenados, o custo do armazenamento de dados varia consoante a camada de armazenamento. O custo por gigabyte diminui conforme a camada se torna mais esporádica.
* **Os custos de acesso a dados**: Aumentar os custos de acesso de dados conforme a camada se torna mais esporádica. Para dados de acesso esporádico e de camada de armazenamento de arquivo, é cobrada uma taxa de acesso de dados por gigabyte para leituras.
* **Os custos de transação**: Existe um custo por transação para todas as camadas que aumenta conforme a camada se torna mais esporádica.
* **Os custos de transferência de dados de Georreplicação**: Este custo aplica-se apenas a contas com a georreplicação configurada, incluindo GRS e RA-GRS. A transferência de dados de georreplicação está sujeita a uma taxa por gigabyte.
* **Custos de transferência de dados de saída**: Transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) incorrem a faturação de utilização de largura de banda numa base por gigabyte, consistente com as contas de armazenamento para fins gerais.
* **A alteração da camada de armazenamento**: Alteração da camada de armazenamento de conta de acesso esporádico para frequente incorre um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de armazenamento de conta de acesso frequente para esporádico incorre um encargo igual à escrita de todos os dados na camada esporádica (contas GPv2 apenas).

> [!NOTE]
> Para obter mais informações sobre os preços para as contas de armazenamento de BLOBs, veja [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) página. Para obter mais informações sobre as taxas aplicáveis às transferências de dados de saída, veja a página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>FAQ

**Posso utilizar contas de armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas GPv1 não suportam as camadas.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todos os três (acesso frequente, esporádico e arquivo) camadas de armazenamento na mesma conta?**

Sim. O atributo **Access Tier** definido ao nível de uma conta é a camada predefinida que se aplica a todos os objetos nessa conta sem uma camada de conjunto explícita. No entanto, a criação de camadas ao nível do blob permite-lhe definir a camada de acesso ao nível do objeto, independentemente de qual seja a definição de camada de acesso da conta. Os BLOBs em qualquer uma das três camadas de armazenamento (frequente, esporádico ou arquivo) podem existir na mesma conta.

**Posso alterar a camada de armazenamento da minha conta de Armazenamento de Blobs ou GPv2?**

Sim, pode definir o atributo **Access Tier** na conta de armazenamento para alterar a camada de armazenamento predefinida. A alteração da camada de armazenamento aplica-se a todos os objetos armazenados na conta que não têm uma camada explícita definida. A alteração da camada de armazenamento de acesso frequente para esporádico incorre em operações de escrita (por 10 000) para todos os blobs sem uma camada definida apenas em contas GPv2 e alteração de esporádica para frequente incorre em ambas as operações de leitura (por 10 000) e os custos de obtenção de dados (por GB) para todos os blobs no armazenamento de BLOBs e as contas GPv2.

**Pode definir minha camada de acesso de conta predefinida para o arquivo?**

Não. Apenas as camadas de armazenamento frequente e esporádico podem ser definidas como a camada de acesso de conta predefinida. A camada de arquivo só pode ser definida ao nível do objeto.

**Em que regiões estão as camadas de armazenamento frequente, esporádico e arquivo disponíveis no?**

As camadas de armazenamento frequente e esporádico, juntamente com a camada ao nível de BLOBs estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**Os blobs na camada de armazenamento esporádico terão um comportamento diferente na camada de armazenamento frequente?**

Os BLOBs na camada de armazenamento frequente têm a mesma latência dos blobs nas contas de armazenamento de BLOBs, GPv1 e GPv2. Os BLOBs na camada de armazenamento esporádico têm uma latência semelhante (em milissegundos) aos blobs nas contas de armazenamento de BLOBs, GPv1 e GPv2. Os BLOBs na camada de armazenamento de arquivo têm várias horas latência nas contas de armazenamento de BLOBs, GPv1 e GPv2.

Os BLOBs na camada de armazenamento esporádico têm um nível de serviço disponibilidade de ligeiramente inferior (SLA) dos blobs armazenados na camada de armazenamento frequente. Para obter mais informações, veja [SLA para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádico e arquivo são iguais?**

Sim. Todas as operações entre frequente e esporádico são 100% consistentes. Todos os válido operações de arquivo incluindo a exclusão, lista, obter propriedades/metadados do blob e definir camada de blob são 100% consistentes com acesso frequente e esporádico. Um blob não pode ser lidos ou modificado enquanto estiverem na camada de arquivo.

**Quando um blob da camada de arquivo para o escalão acesso esporádico ou frequente a ser reidratado, como posso saber que a "reidratação" esteja concluída?**

Durante a reidratação, pode utilizar a operação “get blob properties” para consultar o atributo **Archive Status** e verificar se a alteração de camada já está concluída. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade de estado de arquivo for removida e o **camada de acesso** blob propriedade reflete o escalão acesso esporádico ou frequente de novo.  

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada blob é sempre faturado de acordo com a camada indicada do blob **camada de acesso** propriedade. Ao definir uma nova camada para um blob, o **camada de acesso** propriedade reflete imediatamente o novo escalão para todas as transições. No entanto, se um blob da camada de arquivo para um acesso frequente ou escalão acesso esporádico pode demorar várias horas. Neste caso, é-lhe cobrada às tarifas de arquivo até que a "reidratação" esteja concluída, altura em que o **camada de acesso** propriedade reflete o novo escalão. Nesse momento é faturado por desse blob no acesso frequente ou esporádica taxa.

**Como posso saber se me vai ser faturada uma cobrança de eliminação precoce quando eliminar ou retirar um blob de acesso esporádico ou de uma camada de arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de arquivo antes de 30 dias e 180 dias esporádica (contas GPv2 apenas) ou respectivamente incorre uma cobrança de eliminação precoce rateada. Pode determinar o intervalo de tempo que um blob está na camada de acesso esporádico ou arquivo, verificando a **tempo de alteração da camada de acesso** propriedade, que fornece um carimbo da última alteração da camada de Blobs. Para obter mais informações, consulte [eliminação antecipada do arquivo e esporádico](#cool-and-archive-early-deletion).

**Quais ferramentas do Azure e SDKs suportam a criação de camadas ao nível do blob e o armazenamento de arquivo?**

Portal do Azure, PowerShell e CLI ferramentas e bibliotecas de cliente .NET, Java, Python e node. js todas as suportam a criação de camadas ao nível do blob e o armazenamento de arquivo.  

**A quantidade de dados posso armazenar nas camadas frequente, esporádico e arquivo?**

O armazenamento de dados, juntamente com outros limites, é definido ao nível da conta e não de camada de armazenamento. Por conseguinte, pode optar por utilizar todas seu limite numa só camada ou nas três. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Avaliar frequente, esporádico e arquivo nas contas de armazenamento GPv2

[Verificar a disponibilidade de acesso frequente, esporádico e arquivo por região](https://azure.microsoft.com/regions/#services)

[Gerir o ciclo de vida de armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)

[Verificação de acesso frequente, esporádico e arquivo preço no armazenamento de BLOBs e GPv2 por região, as contas](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
