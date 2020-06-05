---
title: Guia de gestão de custos para serviços do Azure Lab
description: Entenda as diferentes formas de ver os custos dos Serviços de Laboratório.
services: lab-services
author: rbest
ms.author: rbest
ms.date: 05/09/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: 17d7220887a08854f0df7d9fc8725b815b67fc4c
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84425433"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestão de custos para serviços do Azure Lab

A gestão dos custos pode ser dividida em duas áreas distintas: estimativa de custos e análise de custos.  A estimativa de custos ocorre ao criar o laboratório para garantir que a estrutura inicial do laboratório se encaixará dentro do orçamento esperado.  A análise de custos ocorre geralmente no final do mês para analisar os custos e determinar as ações necessárias para o próximo mês.

## <a name="estimating-the-lab-costs"></a>Estimando os custos do laboratório

Cada painel de laboratório tem uma secção **de custos & Billing** que estabelece uma estimativa aproximada do que o laboratório vai custar para o mês.  A estimativa de custos resume o uso da hora com o número máximo de utilizadores pelo custo estimado por hora.  Para obter a estimativa mais precisa, o laboratório, incluindo o [horário,](how-to-create-schedules.md)e o painel de instrumentos refletirá o custo estimado.  

Esta estimativa pode não ser todos os custos possíveis, há alguns recursos que não estão incluídos.  O custo de preparação do modelo não é contabilizado na estimativa de custos.  Pode variar significativamente no tempo necessário para criar o modelo. O custo para executar o modelo é o mesmo que o custo geral do laboratório por hora. Quaisquer custos [de galeria de imagens partilhadas](how-to-use-shared-image-gallery.md) não estão incluídos no painel de instrumentos, uma vez que uma galeria pode ser partilhada entre vários laboratórios.  Por último, as horas incorridas quando o criador do laboratório inicia uma máquina são excluídas desta estimativa.

> [!div class="mx-imgBorder"]
> ![Estimativa de custos do painel](../media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Analisar o uso de meses anteriores

A análise de custos é para rever o uso de meses anteriores para ajudar a determinar quaisquer ajustes para o laboratório.  A repartição dos custos no passado pode ser encontrada na Análise de Custos de [Subscrição.](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)  No portal Azure, pode escrever "Subscrições" no campo de pesquisa superior e selecionar a opção Subscrições.  

> [!div class="mx-imgBorder"]
> ![Pesquisa de assinaturas](../media/cost-management-guide/subscription-search.png)

Selecione a subscrição específica que deve ser revista.

> [!div class="mx-imgBorder"]
> ![Seleção de assinaturas](../media/cost-management-guide/subscription-select.png)

 Selecione "Análise de Custos" no painel esquerdo sob **Gestão de Custos**.

 > [!div class="mx-imgBorder"]
> ![Análise de custos de subscrição](../media/cost-management-guide/subscription-cost-analysis.png)

Este dashboard permitirá uma análise aprofundada dos custos, incluindo a capacidade de exportar para diferentes tipos de ficheiros num horário.  A Gestão de Custos tem inúmeras capacidades para mais informações, consulte [a Visão Geral da Faturação de Gestão de Custos](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtragem pelo tipo de recurso: `microsoft.labservices/labaccounts` mostrará apenas o custo associado aos Serviços de Laboratório.

## <a name="understanding-the-usage"></a>Compreender o uso

Abaixo está uma amostra da análise de custos.

> [!div class="mx-imgBorder"]
> ![Análise de custos de subscrição](../media/cost-management-guide/cost-analysis.png)

Por predefinição existem seis colunas: Recurso, Tipo de Recurso, Localização, Nome do grupo de recursos, Tags, Custo.  A coluna de recursos contém a informação sobre a conta do Laboratório, o Nome do Laboratório e o VM.  As linhas com conta Lab / Nome de Laboratório /padrão é o custo para o laboratório, que pode ser visto na segunda e terceira linhas.  Os VMs usados terão um custo sob a conta Lab / Nome do Laboratório / nome padrão / VM.  Neste exemplo, adicionando a primeira linha com a segunda linha, ambos começando com "aaalab/ dockerlab" vai dar-lhe o custo total para o laboratório "dockerlab" na Conta de Laboratório "aaalab".

Para obter informações partilhadas na galeria de imagens, altere o tipo de recurso para `Microsoft.Compute/Galleries` , o que lhe dará o custo global para a galeria de imagens.  As galerias Share Image podem não aparecer nos custos dependendo do local onde a galeria está armazenada.

> [!NOTE]
> A Galeria de Imagens Partilhada está ligada à conta do laboratório.  Significa que vários laboratórios podem usar a mesma imagem.

## <a name="separating-costs"></a>Custos de separação

Algumas universidades usaram a conta de laboratório e o grupo de recursos como formas de separar as diferentes classes.  Cada turma terá a sua própria conta de laboratório e grupo de recursos. No painel de análise de custos, adicione um filtro baseado no nome do grupo de recursos com o nome de grupo de recursos apropriado para a classe e apenas os custos para essa classe serão visíveis.  Isto permite uma delimitação mais clara entre as diferentes classes ao ver os custos.  A funcionalidade [de exportação programada](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) da análise de Custos permite que os custos de cada classe sejam descarregados em ficheiros separados.

## <a name="managing-costs"></a>Gestão de custos

Dependendo do tipo de aula, existem formas de gerir os custos para reduzir que os VMs estão a funcionar sem que um aluno utilize a máquina.

### <a name="auto-shutdown-on-disconnect"></a>Desparagem automática na desconexão

Na criação do laboratório, o proprietário do laboratório pode definir os VMs no laboratório para [desligar quando a ligação RDP ao VM estiver desligada.](how-to-enable-shutdown-disconnect.md)  Esta definição reduz o cenário em que o aluno desliga mas esquece-se de parar o VM.

### <a name="quota-vs-scheduled-time"></a>Quota vs hora marcada

Compreender o [tempo de quota](classroom-labs-concepts.md#quota) vs hora [programada](classroom-labs-concepts.md#schedules) permitirá ao proprietário do laboratório configurar o laboratório para melhor se adaptar às necessidades do professor e dos alunos.  A hora programada é uma hora definida onde todos os VMs do aluno foram iniciados e estão disponíveis para se conectar.  Normalmente programado é usado na situação em que todos os alunos terão o seu próprio VM, e estão seguindo as instruções do professor em uma hora definida durante o dia como horas de aula.  A desvantagem é que todos os VMs do aluno são iniciados e estão a acumular custos, mesmo que um aluno não faça login no VM.  O tempo de quota é atribuído a cada aluno que pode usar a seu critério e é frequentemente utilizado para estudar independentemente. Os VMs só começam quando o aluno iniciar o VM.  

Um laboratório pode usar o tempo da quota, hora marcada, ou uma combinação de ambos. Se uma classe não precisa do tempo programado, use apenas o tempo de quota para o uso mais eficaz dos VMs.

### <a name="scheduled-event---stop-only"></a>Evento agendado - pare apenas

Na Agenda pode adicionar um tipo de evento apenas stop, que irá parar todas as máquinas num momento específico.  Alguns proprietários de laboratórios marcaram um evento único para todos os dias à meia-noite para reduzir o custo e o uso de quotas quando um aluno se esquece de desligar o VM que está a usar.  A desvantagem deste tipo de evento é que todos os VMs serão encerrados mesmo que o aluno esteja a usar o VM.

### <a name="other-costs-related-to-labs"></a>Outros custos relacionados com laboratórios 

Há custos que não são colocados nos serviços de laboratório, mas que podem ser ligados a um serviço de laboratório.  Uma galeria de imagens partilhada pode ser conectada a laboratórios, mas não mostra sob os custos dos serviços do Laboratório e tem custos.  Para ajudar a manter os custos globais baixos, deve remover quaisquer imagens não uusadas da galeria, uma vez que as imagens têm um custo de armazenamento herdado.  Os laboratórios podem ter ligações com outro recurso Azure por uma rede virtual (VNet) quando um laboratório é removido, deve remover o VNet e os outros recursos.

## <a name="conclusion"></a>Conclusão

Esperemos que a informação acima lhe dará uma melhor compreensão dos custos de utilização e como usar as ferramentas fornecem para reduzir os custos excedentários.
