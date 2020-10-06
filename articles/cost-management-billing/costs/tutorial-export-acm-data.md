---
title: Tutorial – Criar e gerir dados exportados do Azure Cost Management
description: Este artigo mostra como pode criar e gerir dados exportados do Azure Cost Management para os utilizar em sistemas externos.
author: bandersmsft
ms.author: banders
ms.date: 08/05/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 6ef5a457bac7b384dc1b4349b1782a752c41ea26
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447611"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerir dados exportados

Se leu o tutorial de Análise de Custos, está familiarizado com a transferência manual dos dados do Cost Management. No entanto, pode criar uma tarefa periódica, que exporta automaticamente os dados do Cost Management para o armazenamento do Azure com base numa frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contêm todas as informações recolhidas pelo Cost Management. Pode utilizar os dados exportados no armazenamento do Azure com sistemas externos e combiná-los com os seus dados personalizados. E pode utilizar os dados exportados num sistema externo, como um dashboard ou outro sistema financeiro.

Veja o vídeo [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Como agendar exportações para o armazenamento com o Azure Cost Management) sobre como criar uma exportação agendada dos seus dados de custos do Azure para o Armazenamento do Azure. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

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

Para criar ou ver uma exportação de dados ou para agendar uma exportação, abra o âmbito desejado no portal do Azure e selecione **Análise de custos** no menu. Por exemplo, navegue para **Subscrições**, selecione uma subscrição na lista e, em seguida, selecione **Análise de custos** no menu. Na parte superior da página Análise de custos, selecione **Definições** e, em seguida, **Exportações**.

> [!NOTE]
> - Além das subscrições, pode criar exportações em grupos de recursos, grupos de gestão, departamentos e inscrições. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).
>- Quando inicia sessão como parceiro no âmbito da conta de faturação ou no inquilino de um cliente, pode exportar os dados para uma conta do Armazenamento do Microsoft Azure que esteja associada à sua conta de armazenamento de parceiro. Contudo, tem de ter uma subscrição ativa no inquilino CSP.

1. Selecione **Adicionar** e escreva um nome para a exportação. 
1. Para **Métrica**, escolha uma opção:
    - **Custo real (Utilização e Compras)** – selecione para exportar a utilização e as compras padrão
    - **Custo amortizado (Utilização e Compras)** – selecione para exportar os custos amortizados das compras, como reservas do Azure
1. Para **Tipo de exportação**, escolha uma opção:
    - **Exportação diária dos custos do mês até à data** – proporciona diariamente um novo ficheiro de exportação dos custos do mês até à data. Os dados mais recentes são agregados a partir das exportações diárias anteriores.
    - **Exportação semanal dos custos nos últimos 7 dias** – cria uma exportação semanal dos custos dos últimos sete dias desde a data de início selecionada da exportação.  
    - **Exportação mensal dos custos do último mês** – proporciona uma exportação dos custos do último mês comparado com o mês atual no qual criou a exportação. Ao avançar, o agendamento executa uma exportação no quinto dia de cada mês com os custos mensais anteriores.  
    - **Exportação única** – permite-lhe escolher um intervalo de datas para o histórico de dados a exportar para o armazenamento de blobs do Azure. Pode exportar um máximo de 90 dias de histórico de custos a partir da data escolhida. Esta exportação é executada imediatamente e está disponível na conta de armazenamento no prazo de duas horas.  
        Dependendo do tipo de exportação, escolha uma data de início ou escolha uma data **De** e **Até**.
1. Especifique a subscrição da conta de armazenamento do Azure e, em seguida, selecione um grupo de recursos ou crie um novo. 
1. Selecione o nome da conta de armazenamento ou crie uma nova. 
1. Selecione a localização (região do Azure).
1. Especifique o contentor de armazenamento e o caminho do diretório para o qual pretende que o ficheiro de exportação vá. 
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Exemplo de nova exportação" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Reveja os detalhes da sua exportação e selecione **Criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, as novas exportações estão ativadas. Se quiser desativar ou eliminar uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **Desativar** ou **Eliminar**.

Inicialmente, a execução da exportação pode demorar entre 12 e 24 horas. No entanto, a apresentação dos dados nos ficheiros exportados pode demorar mais tempo.

### <a name="export-schedule"></a>Agenda de exportações

As exportações agendadas são afetadas pela hora e pelo dia da semana em que criou inicialmente a exportação. Quando cria uma exportação agendada, a exportação é executada com a mesma frequência para cada ocorrência de exportação subsequente. Por exemplo, numa exportação diária dos custos do mês até à data definida para uma frequência diária, a exportação é executada diariamente. Da mesma forma, numa exportação semanal, a exportação é executada todas as semanas no mesmo dia em que foi agendada. A hora de realização exata da exportação não é garantida e os dados da exportação estão disponíveis dentro de quatro horas após o tempo de execução.

Cada exportação cria um novo ficheiro, pelo que as exportações mais antigas não são substituídas.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Criar uma exportação para várias subscrições

Se tiver um Contrato Enterprise, pode utilizar um grupo de gestão para agregar as informações de custos das subscrições num único contentor. Em seguida, pode exportar os dados de gestão de custos para o grupo de gestão.

As exportações para grupos de gestão de outros tipos de subscrição não são suportadas.

1. Se ainda não tiver criado um grupo de gestão, crie um e atribua subscrições ao mesmo.
1. Na análise de custos, defina o âmbito para o grupo de gestão e selecione **Selecionar este grupo de gestão**.  
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Exemplo de nova exportação" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Crie uma exportação no âmbito para obter os dados de gestão de custos para as subscrições no grupo de gestão.  
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Exemplo de nova exportação":::

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são recolhidos

Pode verificar facilmente se os seus dados do Cost Management estão a ser recolhidos e ver o ficheiro CSV exportado com o Explorador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione Abrir no Explorador. Se vir uma caixa de confirmação, selecione **Sim** para abrir o ficheiro no Explorador de Armazenamento do Microsoft Azure.

![Página da conta de armazenamento a mostrar informações de exemplo e a ligação para Abrir no Explorador](./media/tutorial-export-acm-data/storage-account-page.png)

No Explorador de Armazenamento, navegue para o contentor que pretende abrir e selecione a pasta correspondente ao mês atual. É apresentada uma lista de ficheiros CSV. Selecione um e, em seguida, selecione **Abrir**.

![Informações de exemplo mostradas no Explorador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O ficheiro abre com o programa ou a aplicação que definiu para abrir as extensões de ficheiro CSV. Eis um exemplo do Excel.

![Dados CSV exportados de exemplo mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Transferir um ficheiro de dados CSV exportado

Também pode transferir o ficheiro CSV exportado no portal do Azure. Os passos seguintes explicam como encontrá-lo a partir da análise de custos.

1. Na análise de custos, selecione **Definições**, e, em seguida, selecione **Exportações**.
1. Na lista de exportações, selecione a conta de armazenamento para uma exportação.
1. Na conta de armazenamento, clique em **Contentores**.
1. Na lista de contentores, selecione o contentor.
1. Navegue através dos diretórios e blobs de armazenamento até à data desejada.
1. Selecione o ficheiro CSV e, em seguida, **Transferir**.

[![Transferência de exportação de exemplo](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Ver histórico de execuções de exportação  

Pode ver o histórico de execuções da exportação agendada ao selecionar uma exportação individual na página da lista de exportações. A página da lista de exportações também proporciona acesso rápido à visualização do tempo de execução das exportações anteriores e a próxima hora na qual a execução será executada. Veja a seguir um exemplo a mostrar o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Exemplo de nova exportação":::

Selecione uma exportação para ver o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Exemplo de nova exportação":::

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
