---
title: Tutorial-criar e gerenciar dados exportados do gerenciamento de custos do Azure
description: Este artigo mostra como você pode criar e gerenciar dados exportados de gerenciamento de custos do Azure para que você possa usá-los em sistemas externos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: cost-management-billing
manager: jasonh
ms.custom: seodec18
ms.openlocfilehash: 76ee5aba0f1d0769e15a5969409dfef2a018e477
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987286"
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
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/) e clientes [do contrato do cliente da Microsoft](get-started-partners.md) . Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). As seguintes permissões do Azure, ou escopos, têm suporte por assinatura para exportação de dados por usuário e grupo. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou eliminar exportações agendadas de uma subscrição.
- Contribuinte – pode criar, modificar ou eliminar as respetivas exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outros utilizadores.
- Leitor – pode agendar exportações para as quais tem permissões.

Para contas de Armazenamento do Azure:
- São necessárias permissões de escrita para alterar a conta de armazenamento configurada, independentemente das permissões na exportação.
- A sua conta de armazenamento do Azure tem de estar configurada para o armazenamento de blobs ou ficheiros.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Para criar ou exibir uma exportação de dados ou agendar uma exportação, abra o escopo desejado no portal do Azure e selecione **análise de custo** no menu. Por exemplo, navegue até **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione **análise de custo** no menu. Na parte superior da página Análise de custos, selecione **Exportar** e, em seguida, escolha uma opção de exportação. Por exemplo, selecione **agendar exportação**.  

> [!NOTE]
> - Além de subscrições, pode criar exportações ao nível de grupos de recursos, contas, departamentos e inscrições. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).
>- Quando estiver conectado como um parceiro no escopo da conta de cobrança ou no locatário de um cliente, você poderá exportar dados para uma conta de armazenamento do Azure que esteja vinculada à sua conta de armazenamento do parceiro. No entanto, você deve ter uma assinatura ativa em seu locatário do CSP.
>


Selecione **Adicionar**, digite um nome para a exportação e, em seguida, selecione a opção **exportação diária de custos do mês até a data** . Selecione **Seguinte**.

![Novo de exportação de exemplo que mostra o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a subscrição para a sua conta de armazenamento do Azure, em seguida, selecione a sua conta de armazenamento.  Especifique o contentor de armazenamento e o caminho do diretório que pretende que o ficheiro de exportação para ir para. Selecione **Seguinte**.

![Novo de exportação de exemplo que mostra os detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Reveja os detalhes da sua exportação e selecione **Criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, são ativadas exportações de novo. Se você quiser desabilitar ou excluir uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **desabilitar** ou **excluir**.

Inicialmente, pode demorar uma ou duas horas para que a exportação seja executada. No entanto, pode demorar até quatro horas antes de os dados serem apresentados nos ficheiros exportados.

### <a name="export-schedule"></a>Agenda de exportações

As exportações agendadas são afetadas pela hora e pelo dia da semana quando cria inicialmente a exportação. Quando você cria uma exportação agendada, a exportação é executada com a mesma frequência para cada ocorrência de exportação subsequente. Por exemplo, para um conjunto de exportação de mês a dia, a exportação é executada diariamente. Da mesma forma, para uma exportação semanal, a exportação é executada todas as semanas no mesmo dia em que é agendada. O tempo de entrega exato da exportação não é garantido e os dados exportados estão disponíveis dentro de quatro horas de tempo de execução. "
Cada exportação cria um novo ficheiro, pelo que as exportações mais antigas não são substituídas.

Há dois tipos de opções de exportação:

**Exportação diária de custos acumulados no mês** – a exportação inicial é executada imediatamente. As exportações subsequentes são executadas no dia seguinte à mesma hora que a exportação inicial. Os dados mais recentes são agregados a partir das exportações diárias anteriores.

**Personalizado** – permite que você agende exportações semanais e mensais com opções da semana para a data e do mês até a data. *A exportação inicial será executada imediatamente.*

Se tiver uma subscrição do Visual Studio, MSDN ou Pay As You Go, o seu período de faturação poderá não estar alinhado com o mês de calendário. No caso desses tipos de subscrição e grupos de recursos, pode criar uma exportação que esteja alinhada com o seu período de faturação ou com os meses de calendário. Para criar uma exportação alinhada ao mês da fatura, navegue até **personalizado**e, em seguida, selecione **cobrança-período-até-data**.  Para criar uma exportação alinhada ao mês do calendário, selecione o **mês até a data**.
>
>

![Nova guia de exportação-noções básicas mostrando uma seleção semanal de semana personalizada para a data](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são recolhidos

Pode verificar facilmente se os seus dados do Cost Management estão a ser recolhidos e ver o ficheiro CSV exportado com o Explorador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página conta de armazenamento, selecione Abrir no Explorer. Se você vir uma caixa de confirmação, selecione **Sim** para abrir o arquivo em Gerenciador de armazenamento do Azure.

![Página de conta de armazenamento que mostra informações de exemplo e a ligação para o abrir no Explorador](./media/tutorial-export-acm-data/storage-account-page.png)

No Explorador de Armazenamento, navegue para o contentor que pretende abrir e selecione a pasta correspondente ao mês atual. É apresentada uma lista de ficheiros CSV. Selecione um e, em seguida, selecione **abrir**.

![Informações de exemplo mostradas no Explorador de armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O ficheiro abre com o programa ou a aplicação que definiu para abrir as extensões de ficheiro CSV. Eis um exemplo do Excel.

![Exemplo exportados dados CSV mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)


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
