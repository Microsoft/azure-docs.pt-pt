---
title: Restauro pontual para bolhas de bloco
titleSuffix: Azure Storage
description: A restauração pontual das bolhas de blocos proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar uma conta de armazenamento no seu estado anterior num dado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b959038753dd15282de357da746ef9b0e0cf2be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802272"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Restauro pontual para bolhas de bloco

A restauração pontual proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar os dados de blob de bloqueio a um estado anterior. A restauração pontual é útil em cenários em que um utilizador ou aplicação elimina acidentalmente dados ou onde um erro de aplicação corrompe dados. A restauração pontual também permite testar cenários que requerem reverter um conjunto de dados para um estado conhecido antes de realizar mais testes.

A restauração pontual é suportada para contas de armazenamento v2 de uso geral apenas no nível de desempenho padrão. Apenas os dados nos níveis de acesso quente e fresco podem ser restaurados com a restauração pontual.

Para aprender a permitir a restauração pontual para uma conta de armazenamento, consulte [Executar uma restauração pontual em dados de blob de bloco .](point-in-time-restore-manage.md)

## <a name="how-point-in-time-restore-works"></a>Como o ponto-a-tempo restaurar funciona

Para permitir a restauração pontual, cria uma política de gestão para a conta de armazenamento e especifica um período de retenção. Durante o período de retenção, pode restaurar as bolhas de bloqueio do estado atual para um estado num momento anterior.

Para iniciar uma restauração pontual, ligue para a operação [Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) e especifique um ponto de restauro no tempo UTC. Pode especificar gamas lexicográficas de contentores e nomes de bolhas para restaurar, ou omitir o alcance para restaurar todos os recipientes na conta de armazenamento. Até 10 gamas lexicográficas são suportadas por operação de restauro.

O Azure Storage analisa todas as alterações que foram feitas às bolhas especificadas entre o ponto de restauro solicitado, especificado no tempo UTC, e o momento presente. A operação de restauro é atómica, por isso ou consegue restaurar todas as alterações, ou falha. Se houver bolhas que não possam ser restauradas, a operação falha e lê e escreve as operações para os recipientes afetados.

O diagrama que se segue mostra como a restauração pontual funciona. Um ou mais contentores ou gamas de bolhas é restaurado para o seu estado *há* dias, onde *n* é inferior ou igual ao período de retenção definido para restauro pontual. O efeito é reverter a escrita e apagar operações que ocorreram durante o período de retenção.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagrama mostrando como ponto-a-tempo restaura os contentores para um estado anterior":::

Apenas uma operação de restauro pode ser executada numa conta de armazenamento de cada vez. Uma operação de restauro não pode ser cancelada uma vez que está em curso, mas uma segunda operação de restauro pode ser realizada para desfazer a primeira operação.

A operação **Restore Blob Ranges** devolve um ID de restauro que identifica exclusivamente a operação. Para verificar o estado de uma restauração pontual, ligue para a operação **Get Restore Status** com o ID de restauro devolvido da operação Restore **Blob Ranges.**

> [!IMPORTANT]
> Quando executa uma operação de restauro, o Azure Storage bloqueia as operações de dados nas bolhas das gamas que estão a ser restauradas durante a operação. As operações de leitura, escrita e eliminação estão bloqueadas na localização primária. Por esta razão, as operações como a listagem de contentores no portal Azure podem não funcionar como esperado enquanto a operação de restauro estiver em curso.
>
> As operações de leitura a partir do local secundário podem prosseguir durante a operação de restauro se a conta de armazenamento for geo-replicada.

> [!CAUTION]
> Suportes de restauração pontual restauradores contra operações que atuavam apenas em blocos. As operações que funcionaram em contentores não podem ser restauradas. Por exemplo, se eliminar um recipiente da conta de armazenamento chamando a operação [do Recipiente de Eliminação,](/rest/api/storageservices/delete-container) esse recipiente não pode ser restaurado com uma operação de restauro pontual. Em vez de eliminar um recipiente inteiro, elimine as bolhas individuais se desejar restaurá-las mais tarde.

### <a name="prerequisites-for-point-in-time-restore"></a>Pré-requisitos para restauro pontual

A restauração pontual requer que as seguintes funcionalidades de Armazenamento Azure sejam ativadas antes de poder permitir a restauração pontual:

- [Eliminação recuperável](soft-delete-blob-overview.md)
- [Alterar alimentação](storage-blob-change-feed.md)
- [Versão blob](versioning-overview.md)

Ativar estas funcionalidades pode resultar em custos adicionais. Certifique-se de que compreende as implicações da faturação antes de permitir a restauração pontual e as funcionalidades pré-requisitos.

### <a name="retention-period-for-point-in-time-restore"></a>Período de retenção para restauro pontual

Quando ativar a restauração pontual para uma conta de armazenamento, especifique um período de retenção. As bolhas de bloqueio na sua conta de armazenamento podem ser restauradas durante o período de retenção.

O período de retenção começa poucos minutos depois de permitir a restauração pontual. Tenha em mente que não pode restaurar as bolhas num estado antes do início do período de retenção. Por exemplo, se for habilitado a restaurar o ponto a tempo no dia 1 de maio com uma retenção de 30 dias, então no dia 15 de maio pode restaurar até um máximo de 15 dias. No dia 1 de junho, pode restaurar os dados entre 1 e 30 dias.

O período de retenção para a restauração pontual deve ser pelo menos um dia a menos do que o período de retenção especificado para a eliminação suave. Por exemplo, se o período de retenção de eliminação suave for definido para 7 dias, então o período de retenção de restauração pontual pode ser entre 1 e 6 dias.

> [!IMPORTANT]
> O tempo que demora a restaurar um conjunto de dados baseia-se no número de operações de escrita e eliminação efetuadas durante o período de restauração. Por exemplo, uma conta com um milhão de objetos com 3.000 objetos adicionados por dia e 1.000 objetos apagados por dia exigirá aproximadamente duas horas para restaurar um ponto de 30 dias no passado. Um período de retenção e restauro superior a 90 dias no passado não seria recomendado para uma conta com esta taxa de variação.

### <a name="permissions-for-point-in-time-restore"></a>Permissões para restauro pontual

Para iniciar uma operação de restauro, um cliente deve ter permissões de escrita para todos os recipientes na conta de armazenamento. Para autorizar uma operação de restauro com o Azure Ative Directory (Azure AD), atribua a função **de Contribuinte de Conta de Armazenamento** ao principal de segurança ao nível da conta de armazenamento, grupo de recursos ou subscrição.

## <a name="limitations-and-known-issues"></a>Problemas e limitações conhecidos

A restauração pontual para as bolhas de blocos tem as seguintes limitações e questões conhecidas:

- Apenas as bolhas de bloco numa conta de armazenamento v2 de uso geral padrão podem ser restauradas como parte de uma operação de restauro pontual. As bolhas de apêndice, as bolhas de página e as bolhas de bloco premium não são restauradas. 
- Se tiver apagado um recipiente durante o período de retenção, esse recipiente não será restaurado com a operação de restauro pontual. Se tentar restaurar uma gama de bolhas que incluam bolhas num recipiente apagado, a operação de restauro pontual falhará. Para saber se protege os recipientes da eliminação, consulte [a eliminação suave para recipientes (pré-visualização)](soft-delete-container-overview.md).
- Se uma bolha se moveu entre os níveis quentes e frescos no período entre o momento presente e o ponto de restauro, a bolha é restaurada para o seu nível anterior. Restaurar bolhas de bloco no nível de arquivo não é suportado. Por exemplo, se um blob na camada de acesso frequente tiver sido movido para a camada de arquivo há dois dias e uma operação de restauro restaurar para um ponto há três dias, o blob não é restaurado para a camada de acesso frequente. Para restaurar uma bolha arquivada, desloque-a primeiro para fora do nível de arquivo. Para obter mais informações, consulte [os dados do blob rehidrata do nível de arquivo.](storage-blob-rehydration.md)
- Um bloco que foi carregado através de [Put Block](/rest/api/storageservices/put-block) ou Put Block [de URL](/rest/api/storageservices/put-block-from-url), mas não cometido através da Put [Block List](/rest/api/storageservices/put-block-list), não faz parte de uma bolha e, portanto, não é restaurado como parte de uma operação de restauro.
- Uma bolha com um arrendamento ativo não pode ser restaurada. Se uma bolha com um arrendamento ativo for incluída na gama de bolhas para restaurar, a operação de restauro falhará atomicamente. Quebre quaisquer locações ativas antes de iniciar a operação de restauro.
- As imagens instantâneas não são criadas ou eliminadas como parte de uma operação de restauro. Apenas a bolha da base é restaurada ao seu estado anterior.
- Restaurar espaços de nome plano e hierárquico de armazenamento de dados Azure Data Lake Gen2 não é suportado.

> [!IMPORTANT]
> Se restaurar as bolhas de bloco a um ponto que é mais cedo do que 22 de setembro de 2020, as limitações de pré-visualização para a restauração pontual estarão em vigor. A Microsoft recomenda que escolha um ponto de restauro que seja igual ou posterior a 22 de setembro de 2020 para aproveitar a funcionalidade de restauro de ponto a tempo geralmente disponível.

## <a name="pricing-and-billing"></a>Preços e faturação

Não há nenhuma carga para permitir a restauração pontual. No entanto, permitir a restauração pontual também permite a versão blob, a eliminação suave e a alteração do feed, cada um dos quais pode resultar em custos adicionais.

A faturação para a restauração pontual depende da quantidade de dados processados para executar a operação de restauro. A quantidade de dados processados baseia-se no número de alterações que ocorreram entre o ponto de restauro e o momento presente. Por exemplo, assumindo uma taxa de variação relativamente constante para bloquear dados blob numa conta de armazenamento, uma operação de restauro que remonta no tempo de 1 dia custaria 1/10 de uma restauração que remonta no tempo 10 dias.

Para estimar o custo de uma operação de restauro, reveja o registo de alteração para estimar a quantidade de dados que foi modificado durante o período de restauração. Por exemplo, se o período de retenção para a alteração do alimento para animais for de 30 dias, e o tamanho do alimento para alterações for de 10 MB, então o reposição para um ponto 10 dias antes custaria aproximadamente um terço do preço indicado para uma conta LRS nessa região. Restaurar a um ponto que é 27 dias antes custaria aproximadamente nove décimos do preço listado.

Para obter mais informações sobre os preços para a restauração pontual, consulte [os preços do blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Passos seguintes

- [Execute uma restauração pontual em dados de blob de bloco](point-in-time-restore-manage.md)
- [Alterar suporte de alimentação no armazenamento de blob Azure](storage-blob-change-feed.md)
- [Ativar a eliminação recuperável para blobs](./soft-delete-blob-enable.md)
- [Ativar e gerir a versão blob](versioning-enable.md)
