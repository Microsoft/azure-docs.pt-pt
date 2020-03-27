---
title: Tutorial – Criar e gerir dados exportados do Azure Cost Management
description: Este artigo mostra como pode criar e gerir dados exportados do Azure Cost Management para os utilizar em sistemas externos.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: b950235eccbd41b1dfa46c23985e804212d9e8f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80155907"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerir dados exportados

Se leu o tutorial de Análise de Custos, está familiarizado com a transferência manual dos dados do Cost Management. No entanto, pode criar uma tarefa periódica, que exporta automaticamente os dados do Cost Management para o armazenamento do Azure com base numa frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contêm todas as informações recolhidas pelo Cost Management. Pode utilizar os dados exportados no armazenamento do Azure com sistemas externos e combiná-los com os seus dados personalizados. E pode utilizar os dados exportados num sistema externo, como um dashboard ou outro sistema financeiro.

Veja o vídeo [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Como agendar exportações para o armazenamento com o Azure Cost Management) sobre como criar uma exportação agendada dos seus dados de custos do Azure para o Armazenamento do Azure.

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Os exemplos neste tutorial orientam-no na exportação dos dados de gestão de custos e, em seguida, verificam se os dados foram exportados com êxito.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de contas do Azure, incluindo os clientes [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Contrato de Cliente Microsoft](get-started-partners.md). Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md). As seguintes permissões, ou âmbitos, do Azure são suportadas por subscrição para a exportação de dados por utilizador e grupo. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou eliminar exportações agendadas de uma subscrição.
- Contribuinte – pode criar, modificar ou eliminar as respetivas exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outros utilizadores.
- Leitor – pode agendar exportações para as quais tem permissões.

Para contas de Armazenamento do Azure:
- São necessárias permissões de escrita para alterar a conta de armazenamento configurada, independentemente das permissões na exportação.
- A sua conta de armazenamento do Azure tem de estar configurada para o armazenamento de blobs ou ficheiros.

Se tiver uma subscrição nova, não pode utilizar as funcionalidades do Cost Management imediatamente. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou ver uma exportação de dados ou para agendar uma exportação, abra o âmbito desejado no portal do Azure e selecione **Análise de custos** no menu. Por exemplo, navegue para **Subscrições**, selecione uma subscrição na lista e, em seguida, selecione **Análise de custos** no menu. No topo da página Análise de custos, selecione **Exportar** e, em seguida, escolha uma opção de exportação. Por exemplo, selecione **Agendar exportação**.  

> [!NOTE]
> - Além das subscrições, pode criar exportações em grupos de recursos, contas, departamentos e inscrições. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).
>- Quando inicia sessão como parceiro no âmbito da conta de faturação ou no inquilino de um cliente, pode exportar os dados para uma conta do Armazenamento do Microsoft Azure que esteja associada à sua conta de armazenamento de parceiro. Contudo, tem de ter uma subscrição ativa no inquilino CSP.
>


Selecione **Adicionar**, escreva um nome para a exportação e, em seguida, selecione a opção **Exportação diária dos custos do mês até à data**. Selecione **Seguinte**.

![Exemplo de nova exportação a mostrar o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a subscrição da sua conta de armazenamento do Azure e, em seguida, selecione a conta de armazenamento.  Especifique o contentor de armazenamento e o caminho do diretório para o qual quer que o ficheiro seja exportado. Selecione **Seguinte**.

![Exemplo de nova exportação a mostrar os detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Reveja os detalhes da exportação e selecione **Criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, as novas exportações estão ativadas. Se quiser desativar ou eliminar uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **Desativar** ou **Eliminar**.

Inicialmente, pode demorar uma ou duas horas para que a exportação seja executada. No entanto, pode demorar até quatro horas antes de os dados serem apresentados nos ficheiros exportados.

### <a name="export-schedule"></a>Agendamento de exportações

As exportações agendadas são afetadas pela hora e pelo dia da semana em que criou inicialmente a exportação. Quando cria uma exportação agendada, a exportação é executada com a mesma frequência para cada ocorrência de exportação subsequente. Por exemplo, numa exportação de custos do mês até à data definida para uma frequência diária, a exportação é executada diariamente. Da mesma forma, numa exportação semanal, a exportação é executada todas as semanas no mesmo dia em que foi agendada. A hora de realização exata da exportação não é garantida e os dados da exportação estão disponíveis dentro de quatro horas após o tempo de execução.
Cada exportação cria um novo ficheiro para que as exportações anteriores não sejam substituídas.

Existem dois tipos de opções de exportação:

**Exportação diária de custos do mês até à data** – a exportação inicial é executada imediatamente. As exportações subsequentes são executadas no dia seguinte à mesma hora que a exportação inicial. Os dados mais recentes são agregados a partir de exportações diárias anteriores.

**Personalizada** – permite-lhe agendar exportações semanais e mensais com opções de custos da semana até à data e do mês até à data. *A exportação inicial será executada imediatamente.*

Se tiver uma subscrição Pay As You Go, MSDN ou do Visual Studio, o período de faturação poderá não estar alinhado com o mês do calendário. Para esses tipos de subscrições e grupos de recursos, pode criar uma exportação que esteja alinhada com o seu período de faturação ou com os meses do calendário. Para criar uma exportação alinhada com o mês de faturação, navegue para **Personalizada** e, em seguida, selecione **Período de faturação até à data**.  Para criar uma exportação alinhada com o mês do calendário, selecione **Do mês até à data**.
>
>

![Nova exportação – separador Noções básicas a mostrar a seleção Personalizado > Semana té à data > Semanal](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são recolhidos

Pode verificar facilmente se os seus dados do Cost Management estão a ser recolhidos e ver o ficheiro CSV exportado com o Explorador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione Abrir no Explorador. Se vir uma caixa de confirmação, selecione **Sim** para abrir o ficheiro no Explorador de Armazenamento do Microsoft Azure.

![Página da conta de armazenamento a mostrar informações de exemplo e a ligação para Abrir no Explorador](./media/tutorial-export-acm-data/storage-account-page.png)

No Explorador de Armazenamento, navegue para o contentor que pretende abrir e selecione a pasta correspondente ao mês atual. É apresentada uma lista de ficheiros CSV. Selecione um e, em seguida, selecione **Abrir**.

![Informações de exemplo mostradas no Explorador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O ficheiro abre com o programa ou a aplicação que definiu para abrir as extensões de ficheiro CSV. Eis um exemplo do Excel.

![Dados CSV exportados de exemplo mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Aceder aos dados exportados a partir de outros sistemas

Um dos objetivos de exportar os dados do Cost Management é aceder aos dados a partir de sistemas externos. Pode utilizar um sistema de dashboard ou outro sistema financeiro. Estes sistemas variam bastante, de modo que mostrar um exemplo seria impraticável.  No entanto, pode começar a perceber como aceder aos seus dados de aplicações em [Introdução ao Armazenamento do Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

Avance para o tutorial seguinte para otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados.

> [!div class="nextstepaction"]
> [Analisar e tomar medidas relacionadas com recomendações de otimização](tutorial-acm-opt-recommendations.md)
