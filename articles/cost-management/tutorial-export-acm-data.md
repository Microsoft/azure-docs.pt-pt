---
title: Tutorial - criar e gerir dados exportados do Azure Cost Management | Microsoft Docs
description: Este artigo mostra-lhe como pode criar e gerir os dados exportados do Azure Cost Management, para que pode usá-lo em sistemas externos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a0b50b86ca164199ca723354e39e194c6cd7423f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014128"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerir os dados exportados

Se leu o tutorial de Análise de Custos, está familiarizado com a transferência manual dos dados do Cost Management. No entanto, pode criar uma tarefa periódica que exporta automaticamente os seus dados de gestão de custos para o armazenamento do Azure numa base diária, semanal ou mensal. Os dados exportados estão no formato CSV e contêm todas as informações recolhidas pelo Cost Management. Pode utilizar os dados exportados no armazenamento do Azure com sistemas externos e combiná-los com os seus dados personalizados. E pode utilizar os dados exportados num sistema externo, como um dashboard ou outro sistema financeiro.

Os exemplos neste tutorial orientam-no na exportação dos dados de gestão de custos e, em seguida, verificam se os dados foram exportados com êxito.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

## <a name="prerequisites"></a>Pré-requisitos
Exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) os clientes. Para ver a lista completa dos tipos de conta suportados, consulte [dados de compreender a gestão de custos](understand-cost-mgt-data.md). As seguintes permissões do Azure, ou âmbitos, são suportados por subscrição para exportação de dados por utilizador e grupo. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou eliminar exportações agendadas de uma subscrição.
- Contribuinte – pode criar, modificar ou eliminar as respetivas exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outros utilizadores.
- Leitor – pode agendar exportações para as quais tem permissões.

Para contas de Armazenamento do Azure:
- São necessárias permissões de escrita para alterar a conta de armazenamento configurada, independentemente das permissões na exportação.
- A sua conta de armazenamento do Azure tem de estar configurada para o armazenamento de blobs ou ficheiros.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou ver uma exportação de dados ou para agendar uma exportação, abra o âmbito pretendido no portal do Azure e selecione **análise de custo** no menu. Por exemplo, navegue até **subscrições**, selecione uma subscrição na lista e, em seguida, selecione **análise de custo** no menu. Na parte superior da página de análise de custo, clique em **exportar** e, em seguida, escolha uma opção de exportação. Por exemplo, clique em **agendar exportação**. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

Clique em **Add**, escreva um nome para a exportação e, em seguida, selecione a **exportação diária dos custos de mês até à data** opção. Clique em **Seguinte**.

![Novo de exportação de exemplo que mostra o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a subscrição para a sua conta de armazenamento do Azure, em seguida, selecione a sua conta de armazenamento.  Especifique o contentor de armazenamento e o caminho do diretório que pretende que o ficheiro de exportação para ir para.  Clique em **Seguinte**.

![Novo de exportação de exemplo que mostra os detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Reveja os detalhes de exportação e clique em **criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, são ativadas exportações de novo. Se pretender desativar ou eliminar uma exportação agendada, clique em qualquer item na lista e, em seguida, clique em **Desativar** ou **Eliminar**.

Inicialmente, pode demorar uma ou duas horas para que a exportação seja executada. No entanto, pode demorar até quatro horas antes de os dados serem apresentados nos ficheiros exportados.

### <a name="export-schedule"></a>Agenda de exportação

Exportações agendadas são afetadas pela hora e dia da semana de quando criar inicialmente a exportação. Quando cria uma exportação agendada, a exportação é executada em simultâneo do dia para cada ocorrência de exportação subsequentes. Por exemplo, vai criar uma exportação diária em 1 21 horas. A seguinte exportação é executada às 1:21 horas do dia seguinte. A hora atual afeta todos os outros tipos de exportação da mesma forma, eles sempre executarem em simultâneo do dia, como quando criou inicialmente a exportação. Num exemplo diferente, é possível criar uma exportação semanal em que, na segunda-feira. O relatório seguinte é executada das 16H00 o seguinte segunda-feira. *Os dados exportados estão disponíveis dentro de quatro horas de tempo de execução.*

Cada exportação cria um novo arquivo, para que as exportações mais antigas não são substituídas.

Existem três tipos de opções de exportação:

**Exportação diária dos custos de mês até à data** – a exportação inicial é executada imediatamente. Exportações subsequentes executam no dia seguinte, ao mesmo tempo em que a exportação inicial. Os dados mais recentes são agregados de exportações diárias anteriores.

**A exportação semanal de custos dos últimos 7 dias** – a exportação inicial é executada imediatamente. Exportações subsequentes executam no dia da semana e ao mesmo tempo em que a exportação inicial. Os custos são para os últimos sete dias.

**Custom** – permite-lhe agendar semanal e mensal exporta com opções de semana até à data e mês até à data. *A exportação inicial será executado imediatamente.*

![Nova exportação - separador de noções básicas que mostra uma seleção de semana até à data semanal personalizada](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são recolhidos

Pode verificar facilmente se os seus dados do Cost Management estão a ser recolhidos e ver o ficheiro CSV exportado com o Explorador de Armazenamento do Azure.

Na lista de exportação, clique no nome da conta de armazenamento. Na página da conta de armazenamento, clique em Abrir no Explorador. Se vir uma caixa de confirmação, clique em **Sim** para abrir o ficheiro no Explorador de Armazenamento do Microsoft Azure.

![Página de conta de armazenamento que mostra informações de exemplo e a ligação para o abrir no Explorador](./media/tutorial-export-acm-data/storage-account-page.png)

No Explorador de Armazenamento, navegue para o contentor que pretende abrir e selecione a pasta correspondente ao mês atual. É apresentada uma lista de ficheiros CSV. Selecione um e, em seguida, clique em **Abrir**.

![Informações de exemplo mostradas no Explorador de armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O ficheiro abre com o programa ou a aplicação que definiu para abrir as extensões de ficheiro CSV. Eis um exemplo do Excel.

![Exemplo exportados dados CSV mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Aceder aos dados exportados a partir de outros sistemas

Um dos objetivos de exportar os dados do Cost Management é aceder aos dados a partir de sistemas externos. Pode utilizar um sistema de dashboard ou outro sistema financeiro. Estes sistemas variam bastante, de modo que mostrar um exemplo seria impraticável.  No entanto, pode começar a perceber como aceder aos seus dados de aplicações em [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

Avance para o tutorial seguinte para otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados.

> [!div class="nextstepaction"]
> [Analisar e tomar medidas relacionadas com recomendações de otimização](tutorial-acm-opt-recommendations.md)
