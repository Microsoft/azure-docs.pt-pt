---
title: Tutorial-criar e gerenciar dados exportados do gerenciamento de custos do Azure
description: Este artigo mostra como você pode criar e gerenciar dados exportados de gerenciamento de custos do Azure para que você possa usá-los em sistemas externos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229819"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: criar e gerir dados exportados

Se leu o tutorial de Análise de Custos, está familiarizado com a transferência manual dos dados do Cost Management. No entanto, pode criar uma tarefa periódica que exporta automaticamente os seus dados de gestão de custos para o armazenamento do Azure numa base diária, semanal ou mensal. Os dados exportados estão no formato CSV e contêm todas as informações recolhidas pelo Cost Management. Pode utilizar os dados exportados no armazenamento do Azure com sistemas externos e combiná-los com os seus dados personalizados. E pode utilizar os dados exportados num sistema externo, como um dashboard ou outro sistema financeiro.

Assista ao vídeo [como agendar exportações para armazenamento com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sobre como criar uma exportação agendada de seus dados de custo do Azure para o armazenamento do Azure.

Os exemplos neste tutorial orientam-no na exportação dos dados de gestão de custos e, em seguida, verificam se os dados foram exportados com êxito.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo clientes [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/) . Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). As seguintes permissões do Azure, ou escopos, têm suporte por assinatura para exportação de dados por usuário e grupo. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou eliminar exportações agendadas de uma subscrição.
- Contribuinte – pode criar, modificar ou eliminar as respetivas exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outros utilizadores.
- Leitor – pode agendar exportações para as quais tem permissões.

Para contas de Armazenamento do Azure:
- São necessárias permissões de escrita para alterar a conta de armazenamento configurada, independentemente das permissões na exportação.
- A sua conta de armazenamento do Azure tem de estar configurada para o armazenamento de blobs ou ficheiros.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou exibir uma exportação de dados ou agendar uma exportação, abra o escopo desejado no portal do Azure e selecione **análise de custo** no menu. Por exemplo, navegue até **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione **análise de custo** no menu. Na parte superior da página análise de custo, clique em **Exportar** e escolha uma opção de exportação. Por exemplo, clique em **agendar exportação**.  

> [!NOTE]
> - Além das assinaturas, você pode criar exportações em grupos de recursos, contas, departamentos e registros. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).
>- Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta de armazenamento do Azure que esteja vinculada à sua conta de armazenamento do parceiro. No entanto, você deve ter uma assinatura ativa em seu locatário do CSP.
>


Clique em **Adicionar**, digite um nome para a exportação e, em seguida, selecione a opção **exportação diária de custos do mês até a data** . Clique em **Seguinte**.

![Novo de exportação de exemplo que mostra o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a subscrição para a sua conta de armazenamento do Azure, em seguida, selecione a sua conta de armazenamento.  Especifique o contentor de armazenamento e o caminho do diretório que pretende que o ficheiro de exportação para ir para.  Clique em **Seguinte**.

![Novo de exportação de exemplo que mostra os detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Examine os detalhes de exportação e clique em **criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, são ativadas exportações de novo. Se pretender desativar ou eliminar uma exportação agendada, clique em qualquer item na lista e, em seguida, clique em **Desativar** ou **Eliminar**.

Inicialmente, pode demorar uma ou duas horas para que a exportação seja executada. No entanto, pode demorar até quatro horas antes de os dados serem apresentados nos ficheiros exportados.

### <a name="export-schedule"></a>Exportar agenda

As exportações agendadas são afetadas pela hora e dia da semana de quando você cria inicialmente a exportação. Quando você cria uma exportação agendada, a exportação é executada na mesma hora do dia para cada ocorrência de exportação subsequente. Por exemplo, você cria uma exportação diária às 1:00 PM. A próxima exportação é executada às 1:00 no dia seguinte. A hora atual afeta todos os outros tipos de exportação da mesma maneira — eles sempre são executados ao mesmo tempo do dia em que você criou inicialmente a exportação. Em um exemplo diferente, você cria uma exportação semanal às 4:00 PM na segunda-feira. O próximo relatório é executado às 4:00 PM na segunda-feira seguinte. *Os dados exportados estão disponíveis dentro de quatro horas de tempo de execução.*

Cada exportação cria um novo arquivo, portanto, as exportações mais antigas não são substituídas.

Há três tipos de opções de exportação:

**Exportação diária de custos acumulados no mês** – a exportação inicial é executada imediatamente. As exportações subsequentes são executadas no dia seguinte ao mesmo tempo que a exportação inicial. Os dados mais recentes são agregados das exportações diárias anteriores.

**Exportação semanal de custos para os últimos 7 dias** – a exportação inicial é executada imediatamente. Exportações subsequentes são executadas no dia da semana e ao mesmo tempo que a exportação inicial. Os custos são dos últimos sete dias.

**Personalizado** – permite que você agende exportações semanais e mensais com opções da semana para a data e do mês até a data. *A exportação inicial será executada imediatamente.*

Se você tiver uma assinatura paga conforme o uso, MSDN ou Visual Studio, o período de cobrança da fatura poderá não ser alinhado ao mês do calendário. Para esses tipos de assinaturas e grupos de recursos, você pode criar uma exportação alinhada ao seu período de fatura ou a meses de calendário. Para criar uma exportação alinhada ao mês da fatura, navegue até **personalizado**e, em seguida, selecione **cobrança-período-até-data**.  Para criar uma exportação alinhada ao mês do calendário, selecione o **mês até a data**.
>
>

![Nova guia de exportação-noções básicas mostrando uma seleção semanal de semana personalizada para a data](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

Avance para o tutorial seguinte para otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados.

> [!div class="nextstepaction"]
> [Analisar e tomar medidas relacionadas com recomendações de otimização](tutorial-acm-opt-recommendations.md)
