---
title: Guia de gestão de custos para serviços do Azure Lab
description: Entenda as diferentes formas de ver os custos dos Serviços de Laboratório.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659731"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestão de custos para serviços do Azure Lab

Para a Azure Lab Services, a gestão de custos pode ser dividida em duas áreas distintas: estimativa de custos e análise de custos. A estimativa de custos ocorre quando está a configurar o laboratório para garantir que a estrutura inicial do laboratório se encaixe dentro do orçamento esperado. A análise de custos ocorre geralmente no final do mês para determinar as ações necessárias para o próximo mês.

## <a name="estimate-the-lab-costs"></a>Estimar os custos do laboratório

Cada painel de laboratório tem uma secção **de custos & Billing** que estabelece uma estimativa aproximada do que o laboratório vai custar para o mês. A estimativa de custos resume o uso da hora com o número máximo de utilizadores pelo custo estimado por hora. Para obter a estimativa mais precisa, crie o laboratório, incluindo o [horário.](how-to-create-schedules.md) O painel refletirá o custo estimado. 

Esta estimativa pode não mostrar todos os custos possíveis. Alguns recursos não estão incluídos:

- O custo de preparação do modelo. Pode variar significativamente no tempo necessário para criar o modelo. O custo para executar o modelo é o mesmo que o custo geral do laboratório por hora. 
- Qualquer [galeria de imagens partilhada](how-to-use-shared-image-gallery.md) custa, porque uma galeria pode ser partilhada entre vários laboratórios. 
- Horas incorridas quando o criador do laboratório inicia uma máquina virtual (VM).

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a estimativa de custos do painel.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analise o uso do mês anterior

A análise de custos é para rever o uso do mês anterior para ajudá-lo a determinar quaisquer ajustes para o laboratório. Pode encontrar a repartição dos custos anteriores na análise de custos de [subscrição.](../cost-management-billing/costs/quick-acm-cost-analysis.md) No portal Azure, pode introduzir **Subscrições** na caixa de pesquisa e, em seguida, selecionar a opção **Subscrições.** 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a caixa de pesquisa e a opção Subscrições.](./media/cost-management-guide/subscription-search.png)

Selecione a subscrição específica que pretende rever.

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra a seleção de subscrição.](./media/cost-management-guide/subscription-select.png)

Selecione **Análise de Custos** no painel esquerdo sob **Gestão de Custos**.

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra uma análise de custo de subscrição em um gráfico.](./media/cost-management-guide/subscription-cost-analysis.png)

Este dashboard permite uma análise aprofundada dos custos, incluindo a capacidade de exportar para diferentes tipos de ficheiros num horário. Para mais informações, consulte [a visão geral da Gestão de Custos + Faturação.](../cost-management-billing/cost-management-billing-overview.md)

Pode filtrar por tipo de recurso. A utilização `microsoft.labservices/labaccounts` mostrará apenas o custo associado aos Serviços de Laboratório.

## <a name="understand-the-usage"></a>Compreender o uso

A imagem a seguir é um exemplo de uma análise de custos.

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra uma análise de custos de exemplo para uma subscrição.](./media/cost-management-guide/cost-analysis.png)

Por predefinição, existem seis colunas: **Recurso**, **Tipo de Recurso,** **Localização,** **Nome do grupo de recursos,** **Tags** e **Custo**. A coluna **de recursos** contém a informação sobre a conta do laboratório, nome do laboratório e VM. As filas que mostram a conta do laboratório, o nome do laboratório e o padrão (segunda e terceira filas) são o custo para o laboratório. Os VMs usados têm um custo que você pode ver para as linhas que mostram a conta de laboratório, nome de laboratório, padrão, e nome VM. 

Neste exemplo, adicionar a primeira e segunda linhas (ambas começam com **aaalab/dockerlab)** vai dar-lhe o custo total para o laboratório "dockerlab" na conta de laboratório "aaalab".

Para obter o custo global da galeria de imagens, altere o tipo de recurso para `Microsoft.Compute/Galleries` . Uma galeria de imagens partilhada pode não aparecer nos custos, dependendo do local onde a galeria está armazenada.

> [!NOTE]
> Uma galeria de imagens partilhada está ligada à conta do laboratório. Significa que vários laboratórios podem usar a mesma imagem.

## <a name="separate-the-costs"></a>Separar os custos

Algumas universidades usaram a conta de laboratório e o grupo de recursos como formas de separar as aulas. Cada turma tem a sua própria conta de laboratório e grupo de recursos. 

No painel de análise de custos, adicione um filtro baseado no nome do grupo de recursos com o nome de grupo de recursos apropriado para a classe. Então, só os custos para esta classe serão visíveis. Isto permite uma delimitação mais clara entre as classes quando você está vendo os custos. Pode utilizar a funcionalidade [de exportação programada](../cost-management-billing/costs/tutorial-export-acm-data.md) da análise de custos para baixar os custos de cada classe em ficheiros separados.

## <a name="manage-costs"></a>Gerir os custos

Dependendo do tipo de aula, existem formas de gerir os custos para reduzir os casos de VMs que estão a funcionar sem que um aluno os utilize.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Definições automáticas de paragem para controlo de custos

As funções de paragem automática permitem evitar horas de utilização em VM desperdiçadas nos laboratórios. As seguintes definições capturam a maioria dos casos em que os utilizadores deixam acidentalmente as suas máquinas virtuais em funcionamento:

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra as três definições automáticas de paragem.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Pode configurar estas definições tanto ao nível da conta do laboratório como ao nível do laboratório. Se os ativares ao nível da conta do laboratório, são aplicados a todos os laboratórios dentro da conta do laboratório. Para todas as novas contas de laboratório, estas definições são ligadas por defeito. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Desconectar automaticamente os utilizadores de máquinas virtuais que o SISTEMA considera inativos

> [!NOTE]
> Esta definição está disponível apenas para máquinas virtuais do Windows.

Quando os **utilizadores desligarem quando as máquinas virtuais estão inativas,** o utilizador é desligado de quaisquer máquinas do laboratório quando o Windows OS considera que a sessão está inativa (incluindo as máquinas virtuais do modelo). A [definição de ocioso do Windows OS](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) utiliza dois critérios: 

* Ausência do utilizador: sem entrada de teclado ou rato.
* Falta de consumo de recursos: Todos os processadores e todos os discos ficaram inativos durante uma certa percentagem de tempo.

Os utilizadores verão uma mensagem como esta no VM antes de serem desligados: 

> [!div class="mx-imgBorder"]
> ![Screenshot que mostra uma mensagem de aviso de que uma sessão foi inativa sobre o seu tempo limite e será desligada.](./media/cost-management-guide/idle-timer-expired.png)
 
A máquina virtual ainda está em funcionamento quando o utilizador está desligado. Se o utilizador voltar a ligar-se à máquina virtual iniciando sessão, janelas ou ficheiros que estavam abertos ou funcionando que não foram salvos antes da desconexão ainda lá estiver. Neste estado, como a máquina virtual está em funcionamento, ainda conta como ativo e acumula custo. 
 
Para desligar automaticamente as máquinas virtuais do Windows desligadas, utilize a combinação de **utilizadores desligados quando as máquinas virtuais estão inativas** e **desligue as máquinas virtuais quando os utilizadores desligam** as definições.

Por exemplo, se configurar as definições da seguinte forma:
 
* **Desligue os utilizadores quando as máquinas virtuais estiverem inativas:** 15 minutos após a deteção do estado de marcha lenta.
* **Desligue as máquinas virtuais quando os utilizadores desligarem:** 5 minutos após o utilizador desligar.
 
As máquinas virtuais do Windows desligam-se automaticamente 20 minutos após o utilizador deixar de as utilizar. 
 
> [!div class="mx-imgBorder"]
> ![Diagrama que ilustra a combinação de configurações que resulta em paragem automática de VM.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Desligue automaticamente as máquinas virtuais quando os utilizadores desligam
 
As **máquinas virtuais desligadas quando os utilizadores desligam** a definição suportam máquinas virtuais Windows e Linux. Quando esta regulação estiver acesa, ocorrerá uma paragem automática quando:
 
* Para o Windows, uma ligação de ambiente de trabalho remoto (RDP) está desligada.
* Para o Linux, uma ligação SSH está desligada.
 
> [!NOTE]
> Apenas são suportadas [distribuições e versões específicas do Linux.](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)
 
Pode especificar quanto tempo as máquinas virtuais devem esperar que o utilizador se reconecte antes de desligar automaticamente. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Desligue automaticamente as máquinas virtuais que são iniciadas mas os utilizadores não se conectam
 
Num laboratório, um utilizador pode iniciar uma máquina virtual, mas nunca ligar-se a ela. Por exemplo:
 
* Um horário no laboratório começa todas as máquinas virtuais para uma sessão de aula, mas alguns alunos não aparecem e não se ligam às suas máquinas. 
* Um utilizador inicia uma máquina virtual mas esquece-se de se ligar. 
 
As **máquinas virtuais desligadas quando os utilizadores não ligam** a definição irão capturar estas caixas e desligar automaticamente as máquinas virtuais. 
 
Para obter informações sobre como configurar e permitir o encerramento automático de VMs na desconexão, consulte estes artigos:

* [Configure o encerramento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md)
* [Configure o encerramento automático de VMs para um laboratório](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Hora programada vs. tempo de quota

Compreender [o tempo programado](classroom-labs-concepts.md#schedules) e o tempo de [quota](classroom-labs-concepts.md#quota) irá ajudá-lo a configurar um laboratório para melhor se adaptar às necessidades do professor e dos alunos. 

A hora programada é uma hora definida onde todos os VMs do aluno foram iniciados e estão disponíveis para ligação. A hora programada é comumente usada quando todos os alunos têm os seus próprios VMs e estão seguindo as instruções do professor a uma hora definida durante o dia (como horas de aula). A desvantagem é que todos os VMs do aluno são iniciados e estão a acumular custos, mesmo que um aluno não faça login num VM. 

O tempo de quota é atribuído a cada aluno para uso a seu critério e é frequentemente utilizado para o estudo independente. Os VMs só começam quando o aluno começar o VM. 

Um laboratório pode usar o tempo da quota ou a hora programada, ou uma combinação de ambos. Se uma classe não precisa de tempo programado, use apenas o tempo de quota para o uso mais eficaz dos VMs.

### <a name="scheduled-event-stop-only"></a>Evento agendado: apenas parar

Na programação, pode adicionar um tipo de evento stop-only que irá parar todas as máquinas num momento específico. Alguns donos de laboratórios marcaram um evento só para parar todos os dias à meia-noite para reduzir o custo e o uso de quotas quando um aluno se esquece de desligar o VM que está a usar. A desvantagem deste tipo de evento é que todos os VMs serão encerrados, mesmo que um aluno esteja a usar um VM.

### <a name="other-costs-related-to-labs"></a>Outros custos relacionados com laboratórios 

Alguns custos não são colocados nos Serviços de Laboratório, mas podem ser ligados a um serviço de laboratório. Pode ligar uma galeria de imagens partilhada a um laboratório, mas não vai aparecer sob os custos dos Serviços de Laboratório e tem custos. Para ajudar a reduzir os custos globais, deve remover quaisquer imagens não usudas da galeria porque as imagens têm um custo de armazenamento inerente. 

Os laboratórios podem ter ligações com outros recursos Azure através de uma rede virtual. Quando um laboratório é removido, deve remover a rede virtual e os outros recursos.

## <a name="conclusion"></a>Conclusão

Esperemos que a informação neste artigo lhe tenha dado uma melhor compreensão das ferramentas que podem ajudá-lo a reduzir os custos de utilização.