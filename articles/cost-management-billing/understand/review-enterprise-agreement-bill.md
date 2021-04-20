---
title: Analisar a faturação do Contrato Enterprise do Azure
description: Saiba como ler e compreender a utilização e a faturação dos Contratos Enterprise do Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5d945c421dc0a4abed17b3a355339c131dd922df
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728055"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Compreenda a faturação do Contrato Enterprise do Azure

Os clientes do Azure com um Contrato Enterprise recebem uma fatura quando excedem o crédito da organização ou quando utilizam serviços que não são cobertos pelo crédito.

O crédito da sua organização inclui o seu Pré-pagamento do Azure (que antes se chamava alocação monetária). O Pré-pagamento do Azure é o montante que a sua organização pagou antecipadamente pela utilização dos serviços do Azure. Pode adicionar fundos do Pré-pagamento do Azure ao Contrato Enterprise ao contactar o seu revendedor ou gestor de conta Microsoft.

Este tutorial aplica-se apenas a clientes do Azure com um Contrato Enterprise do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Analisar os custos faturados
> * Analisar os custos de utilização excedida do serviço
> * Analisar a fatura do Marketplace

## <a name="prerequisites"></a>Pré-requisitos

Para analisar e verificar os custos presentes na fatura, tem de ser um Administrador do Enterprise. Para obter mais informações, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](../manage/understand-ea-roles.md). Se não souber quem é o Administrador do Enterprise da sua organização, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Analisar os custos faturados para a maioria dos clientes

Esta secção não se aplica a clientes do Azure na Austrália, Japão ou Singapura.

Receberá uma fatura do Azure quando ocorrer qualquer uma das seguintes situações durante o período de faturação:

- **Utilização excedida do serviço**: os custos de utilização da sua organização excedem o seu saldo de crédito.
- **Custos faturados em separado**: os serviços que sua organização utilizou não são cobertos pelo crédito. É faturado pelos seguintes serviços independentemente do seu saldo de crédito:
    - Canónico
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utilizador Registado
    - OpenLogic
    - Utilizador Registado do Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (Mensal)
    - Visual Studio Enterprise (Anual)
    - Visual Studio Professional (Mensal)
    - Visual Studio Professional (Anual)
- **Custos do Marketplace**: as compras e a utilização do Azure Marketplace não são cobertas pelo crédito da sua organização. Portanto, será faturado pelos custos do Marketplace independentemente do seu saldo de crédito. No Enterprise Portal, um Administrador do Enterprise pode ativar e desativar compras do Marketplace.

A sua fatura apresentará os gastos de utilização do Azure com os custos associados primeiro, seguidos de eventuais gastos do marketplace. Se tiver saldo de crédito, o mesmo é aplicado à utilização do Azure e a sua fatura apresentará esta utilização e a utilização do marketplace sem custos no fim.

Compare o montante total combinado mostrado no Enterprise Portal em **Relatórios** > **Resumo da Utilização** com a sua fatura do Azure. Os montantes no **Resumo da Utilização** não incluem impostos.

[Inicie sessão no Azure EA Portal](https://ea.azure.com). Em seguida, selecione **Relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de ecrã que mostra a opção M + C no Resumo de utilização.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

O valor combinado da **Utilização Total** e do **Azure Marketplace** deve corresponder ao **Montante Total Alargado** na fatura. Para obter mais detalhes sobre os custos, aceda a **Transferir Utilização**.  

![Captura de ecrã que mostra o separador Transferir Utilização](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Analisar os custos faturados para outros clientes

Esta seção aplica-se apenas a clientes do Azure na Austrália, Japão ou Singapura.

Receberá uma ou mais faturas do Azure quando ocorrer qualquer uma das seguintes situações:

- **Utilização excedida do serviço**: os custos de utilização da sua organização excedem o seu saldo de crédito.
- **Custos faturados em separado**: os serviços que sua organização utilizou não são cobertos pelo crédito. Será faturado pelos seguintes serviços:
    - Canónico
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Utilizador Registado
    - OpenLogic
    - Utilizador Registado do Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (Mensal)
    - Visual Studio Enterprise (Anual)
    - Visual Studio Professional (Mensal)
    - Visual Studio Professional (Anual)
- **Custos do Marketplace**: as compras e a utilização do Azure Marketplace não são cobertas pelo crédito da sua organização e são faturadas separadamente. No Enterprise Portal, um Administrador do Enterprise pode ativar e desativar compras do Marketplace.

Quando tem custos referentes a utilização excedida de serviço e custos que são faturados separadamente durante o período de faturação, receberá uma fatura. A fatura inclui os dois tipos de custos. Os custos dos Marketplaces são sempre faturados separadamente.

## <a name="review-service-overage-charges-for-other-customers"></a>Analisar os custos de utilização excedida do serviço para outros clientes

Esta secção aplica-se apenas se estiver na Austrália, no Japão ou em Singapura.

Compare o montante de utilização total no Enterprise Portal em **Relatórios** > **Resumo da Utilização** com a sua fatura de utilização excedida do serviço. A fatura de utilização excedida do serviço inclui a utilização que excede o crédito da sua organização e/ou os serviços que não são cobertos pelo crédito. Os montantes no **Resumo da Utilização** não incluem impostos.

Inicie sessão no [Azure EA Portal](https://ea.azure.com) e, em seguida, selecione **Relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de ecrã que mostra a opção M + C no Resumo de utilização.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

O montante de **Utilização Total** deve corresponder ao **Montante Total Alargado** na fatura de utilização excedida do serviço. Para obter mais informações sobre os custos, aceda a **Transferir Utilização** > **Transferir Relatório Avançado**. Este relatório não inclui impostos nem custos relativos a custos de reservas ou do marketplace.  

![Captura de ecrã que mostra a opção Transferir Relatório Avançado no separador Transferir utilização.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

A tabela a seguir lista os termos e as descrições apresentados na fatura e no **Resumo de Utilização** no Enterprise Portal:

|Termo da fatura|Termo do Resumo da Utilização|Descrição|
|---|---|---|
|Montante Alargado Total|Utilização Total|O custo de utilização total antes dos impostos relativo ao período especificado antes do crédito ser aplicado.|
|Utilização de Alocação|Utilização de Alocação|O crédito aplicado durante esse período específico.|
|Venda Total|Utilização Excedida Total|O custo total de utilização que excede o montante do seu crédito. Este montante não inclui impostos.|
|Montante dos Impostos|Não aplicável|Imposto que se aplica ao montante total de venda para o período especificado.|
|Montante Total|Não aplicável|O montante devido da fatura depois do crédito ser aplicado e do imposto ser adicionado.|

### <a name="review-marketplace-invoice"></a>Analisar a fatura do Marketplace

Esta secção aplica-se apenas se estiver na Austrália, no Japão ou em Singapura.

Compare o total do Azure Marketplace em **Relatórios** > **Resumo de Utilização** no Enterprise Portal com a sua fatura do marketplace. A fatura do Marketplace diz respeito apenas a compras e utilização do Azure Marketplace. Os montantes no **Resumo de Utilização** já incluem um imposto determinado pelo publicador.

Inicie sessão no [portal Enterprise](https://ea.azure.com) e, em seguida, selecione **Relatórios**. No canto superior direito do separador, mude a vista de **M** para **C** e escolha o período da fatura.  

![Captura de ecrã que mostra a opção M + C no Resumo de utilização.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

O total do **Azure Marketplace** deve corresponder à **Venda Total** na fatura do marketplace. Para obter mais informações sobre os custos com base na utilização, aceda a **Transferir Utilização**. Em **Custos do Marketplace**, selecione **Transferir**. O preço do marketplace inclui um imposto conforme determinado pelo editor. Os clientes não recebem uma fatura separada do editor referente à cobrança de impostos sobre a transação.

![Captura de ecrã que mostra a opção de transferência nos custos do Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Ver as informações da folha de preços

Os administradores do Enterprise podem ver a lista de preços associada à inscrição relativa aos serviços do Azure.

Para ver a folha de preços atual:

1. No portal Azure Enterprise, selecione **Relatórios** e, em seguida, **Folha de Preços**.
2. Veja a folha de preços ou selecione **Transferir**.

![Exemplo que mostra as informações da folha de preços](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Para transferir o histórico de uma lista de preços:

1. No portal Azure Enterprise, selecione **Relatórios** e, em seguida, **Transferir Utilização**.
2. Transfira a folha de preços.

![Exemplo que mostra onde pode transferir uma folha de preços mais antiga](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Alguns motivos para diferenças nos preços:

- Os preços podem ter sido alterados entre a inscrição anterior e a nova inscrição. As alterações de preços podem ocorrer, uma vez que os preços são contratuais para uma inscrição específica, desde a data de início até à data de fim de um contrato.
- Quando transfere para uma nova inscrição, os preços são alterados para o novo contrato. Os preços são definidos pela folha de preços, que pode ser mais elevada na nova inscrição.
- Se o período de uma inscrição for alargado, os preços também serão alterados. Os preços mudam para tarifas pay as you go.

## <a name="request-detailed-usage-information"></a>Pedir informações de utilização detalhadas

Os administradores de Enterprise podem ver um resumo dos dados de utilização, dos Pré-pagamentos consumidos do Azure e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada em contas específicas, transfira o relatório de detalhes de utilização ao aceder a **Relatórios** > **Transferir Utilização**.

> [!NOTE]
> O relatório de detalhes de utilização não inclui os impostos aplicáveis. Pode existir uma latência de cerca de oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para as inscrições indiretas, o seu parceiro tem de ativar a função de margem de lucro para que possa ver as informações relacionadas com os custos.

## <a name="reports"></a>Relatórios

Os administradores de Enterprise podem ver um resumo dos dados de utilização, dos Pré-pagamentos consumidos do Azure e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

### <a name="azure-enterprise-reports"></a>Relatórios do Azure Enterprise

- Gráficos e resumo de utilização
- Relatório de utilização de serviços
- Relatório de saldo e custo
- Relatório de detalhes de utilização
- Relatório de custos do Azure Marketplace
- Folha de preços
- Transferência de relatórios avançados
- Formatação de relatórios CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Para ver relatórios e grafos do resumo da utilização:

1. Aceda ao portal Azure Enterprise.
1. Selecione **Relatórios** no painel esquerdo.
1. Selecione o separador **Resumo de Utilização**.
1. Selecione o período de alocação no menu de intervalos de datas no canto superior esquerdo.
1. Selecione o período ou mês no grafo para ver detalhes adicionais.
1. Neste separador, pode:
   - Ver um grafo da utilização ao longo dos meses com uma discriminação da utilização, da sobretaxa de serviços, dos custos cobrados separadamente e dos custos do Azure Marketplace.
   - Filtrar por departamentos, contas e subscrições por baixo do grafo.
   - Alternar entre a discriminação de **Custo por Serviços** e a discriminação de **Custo por Hierarquia**.
   - Ver os detalhes dos serviços do Azure, dos custos cobrados separadamente e dos custos do Azure Marketplace.

## <a name="service-usage-report"></a>Relatório de utilização de serviços

A página do relatório de utilização de serviços permite que os administradores do Enterprise vejam um resumo dos dados de utilização dos serviços. A utilização é apresentada ao nível do resumo em todas as contas e subscrições. Para ver uma utilização detalhada, pode filtrar o relatório por contas ou subscrições.

> [!NOTE]
> Poe existir uma latência de até cinco dias entre a data de utilização incorrida e a data em que essa utilização é refletida neste relatório.

### <a name="to-view-the-report"></a>Para ver o relatório:

1. Inicie sessão no portal Azure Enterprise.
1. Selecione **Relatórios** no painel de navegação esquerdo.
1. Selecione o separador **Resumo de Utilização**.
1. Selecione o intervalo de datas.
1. Escolha as contas ou subscrições que pretende ver.
1. Opcionalmente, pode:
   - Alternar a vista entre **Custo por Serviços** e **Custo por Hierarquia** para apresentar discriminações diferentes.
   - Ver os detalhes de Nome do Serviço, Unidade de Medida, Unidades Consumidas, Taxa Efetiva e Custo Total.

## <a name="download-csv-reports"></a>Transferir relatórios CSV

A página de transferência de relatórios mensais permite que os administradores do Enterprise transferiram vários relatórios como ficheiros CSV. Os relatórios que podem ser transferidos incluem:

- Relatório de saldo e custo
- Relatório de detalhes de utilização
- Relatório de custos do Azure Marketplace
- Folha de preços

### <a name="to-download-reports"></a>Para transferir os relatórios:

1. No portal Azure Enterprise, selecione **Relatórios**.
1. Selecione **Transferir Relatório de Utilização** no friso superior.
1. Selecione **Transferir** ao lado do relatório do mês adequado.

### <a name="csv-report-formatting-issues"></a>Problemas de formatação de relatórios CSV

Os clientes que vêm os relatórios CSV do portal Azure Enterprise em euros podem deparar-se com problemas de formatação em relação a vírgulas e pontos.

Por exemplo, podem ver:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Deverá ver:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Este problema de formatação ocorre devido às predefinições da funcionalidade de importação do Excel. O Excel importa todos os campos como texto “Geral” e assume que um número é separado de acordo com a norma matemática. Por exemplo: "1,000.00".

Se uma moeda europeia utilizar um ponto (.) para o separador de milhares e uma vírgula para o separador decimal (,), será apresentada incorretamente. Por exemplo: “1.000,00”. Os resultados da importação podem variar conforme a definição do idioma regional.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Para importar o ficheiro CSV sem problemas de formatação:

1. No Microsoft Excel, aceda a **Ficheiro** > **Abrir**.
   O Assistente de Importação de Texto é apresentado.
1. Em **Tipo de Dados Original**, escolha **delimitados**.  A predefinição é **Largura Fixa**.
1. Selecione **Seguinte**.
1. Em Delimitadores, selecione a caixa de verificação de **Vírgula**. Desmarque **Separador** se estiver selecionado.
1. Selecione **Seguinte**.
1. Desloque o cursor para as colunas **ResourceRate** e **ExtendedCost**.
1. Selecione a coluna **ResourceRate**. É apresentada realçada a preto.
1. Na secção **Formato dos Dados da Coluna**, selecione **Texto** em vez de **Geral**. O cabeçalho da coluna mudará de **Geral** para **Texto.**
1. Repita os passos 8 e 9 para a coluna **ExtendedCost** e, em seguida, selecione **Concluir**.

> [!TIP]
> Se tiver definido os ficheiros CSV para abrirem automaticamente no Excel, terá de utilizar a função **Abrir** no Excel em alternativa. No Excel, aceda a **Ficheiro** > **Abrir**.

## <a name="balance-and-charge-report"></a>Relatório de saldo e custo

O relatório de saldo e custo oferece um resumo mensal de informações sobre saldos, novas compras, custos de serviço do Azure Marketplace, ajustes e custos de utilização excedida.

Todas as linhas na tabela de resumo da Alocação de Serviços do Azure permanecem estáticas de um mês para outro. Poderá localizar os detalhes de todas as compras e ajustes nas secções **Detalhes de Novas Compras** e **Detalhes de Ajuste**.

### <a name="download-the-balance-and-charge-report"></a>Transferir o relatório de saldo e custo

1. Inicie sessão no portal Azure Enterprise como administrador do Enterprise.
1. Selecione **Relatórios** no painel esquerdo.
1. Selecione o separador **Transferência de Relatórios**.
1. Selecione o mês apropriado na coluna **Saldo e Custo** e selecione para transferir o relatório.

## <a name="usage-detail-report"></a>Relatório de detalhes de utilização

O relatório de detalhes de utilização apresenta um resumo mensal do consumo de serviços e quantidades das inscrições. Inclui informações sobre nomes de medidores, tipos de medidores e quantidades consumidas.

### <a name="download-the-usage-detail-report"></a>Transferir o relatório de detalhes de utilização

1. Inicie sessão no portal Azure Enterprise como administrador do Enterprise.
1. Selecione **Relatórios** no painel de navegação esquerdo.
1. Selecione o separador **Transferir Utilização**.
1. Selecione o mês apropriado na coluna **Detalhes de Utilização** e selecione para transferir o relatório.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Custos do Azure Marketplace nos relatórios do portal Azure Enterprise

Há dois tipos de custos do Azure Marketplace:

- **Com base na utilização:** Produtos medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas à hora, as pesquisas de API do Bing são cobradas por número de pesquisas.
- **Tarifa mensal:** cobrada mensalmente com base na utilização ou no acesso.

Para obter mais informações acerca dos custos do Azure Marketplace, veja as [FAQS do Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Para ver os diferentes custos no portal Azure Enterprise:

- **Relatório de resumo de utilização**: mostra **ambos** os custos do Azure Marketplace – com base na utilização e tarifa mensal.
- **Relatório de custos do Marketplace**: mostra **apenas** os custos do Azure Marketplace com base na utilização.  As tarifas únicas não são apresentadas.

> [!NOTE]
> O Azure Marketplace não está disponível para inscrições MPSA.

## <a name="advanced-report-download"></a>Transferência de relatórios avançados

Para relatórios em intervalos de datas ou contas específicas, pode utilizar a transferência de relatórios avançados. Desde 30 de agosto de 2016, o formato do ficheiro de saída é CSV, de forma a acomodar conjuntos de registos maiores.

1. No portal Azure Enterprise, selecione **Transferência de Relatório Avançado**.
1. Selecione o **Intervalo de Datas Adequado**.
1. Selecione as **Contas Adequadas**.
1. Selecione **Pedir Dados de Utilização**.
1. Selecione o botão **Atualizar** até que o estado do relatório atualize para **Transferir**.
1. Transfira o relatório.

## <a name="reporting-for-non-enterprise-administrators"></a>Relatórios para administradores não Enterprise

Os administradores do Enterprise podem dar permissões aos administradores de departamento (DA) e aos proprietários de contas (AO) para verem custos numa inscrição. Os proprietários de contas com acesso podem transferir relatórios CSV específicos da respetiva conta e subscrições. Além disso, podem ver as informações na secção de relatórios do portal Azure Enterprise.

### <a name="to-enable-access"></a>Para ativar o acesso:

 1. Inicie sessão no portal Azure Enterprise como administrador do Enterprise.
 1. Selecione **Gerir** no painel de navegação esquerdo.
 1. Selecione o separador **Inscrição**.
 1. Na seção **Detalhes da Inscrição**, selecione o ícone de lápis ao lado de **Os Administradores de Departamento podem ver os custos** ou **Os Proprietários de Contas podem ver os custos**.
 1. Selecione **Ativado**.
 1. Selecione **Guardar**.

### <a name="to-view-reports"></a>Para ver relatórios:

1. Inicie sessão no portal Azure Enterprise como administrador de departamento ou proprietário de conta.
1. Selecione **Relatórios** no painel de navegação esquerdo.
1. Selecione o separador **Resumo de Utilização** para ver informações sobre a conta e subscrição.
1. Selecione **Transferir Relatório de Utilização** para ver os relatórios CSV.

Os administradores de departamento e os proprietários de contas não conseguem ver os custos quando utilizam a função **Transferência de Relatórios Avançados**. Os custos são apresentados como 0 $.

As permissões do proprietário de conta para ver os custos estendem-se aos proprietários de contas e a todos os utilizadores que têm permissões em subscrições associadas. Se for um cliente indireto, as funcionalidades dos custos têm de ser ativadas pelo parceiro de canal.

## <a name="power-bi-reporting"></a>Relatórios do Power BI

Os relatórios do Power BI estão disponíveis para clientes EA diretos, parceiros e indiretos que tenham acesso para ver as informações de faturação.

### <a name="power-bi-pro"></a>Power BI Pro

O Power BI Pro está disponível para clientes EA. Com o Power BI Pro, pode gerar e partilhar relatórios para gerir de forma eficaz os seus dados de custos. Além disso, também possui funcionalidades adicionais de atualização de dados e colaboração. O Power BI Pro oferece limites superiores de capacidade de dados e transmissão de dados.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Para aceder ao Microsoft Azure Consumption Insights:

1. Aceda a [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selecione **Obter Agora**.
1. Indique um número de inscrição e o número de meses e selecione **Seguinte**.
1. Forneça a sua chave de acesso da API para ligar. Pode encontrar a chave relativa à sua inscrição no [portal Enterprise](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selecione **Iniciar sessão** para iniciar automaticamente o processo de importação.
1. Quando estiver concluído, é apresentado um novo dashboard, relatório e modelo no painel de navegação. Selecione o dashboard para ver os dados importados.

> [!TIP]
>
> - Para saber mais sobre como gerar a chave de API para a inscrição, veja o ficheiro de ajuda Relatórios da API no [portal Enterprise](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Para obter mais informações sobre a ligação do Power BI ao consumo Azure, veja [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Para aceder ao pacote de conteúdos do Power BI EA legado:

1. Aceda ao [site do Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Inicie sessão com uma conta escolar ou profissional válida.

   A conta escolar ou profissional pode ser a mesma ou diferente da que utiliza para aceder à inscrição através do portal Azure Enterprise.
1. No dashboard de serviços, selecione **Microsoft Azure Enterprise** e selecione **Ligar**.
1. No ecrã **Ligar ao Azure Enterprise**, preencha os campos:
    - URL do Ambiente do Azure: [https://ea.azure.com](https://ea.azure.com/)
    - Número de Meses: de 1 a 36
    - Número da Inscrição: o seu número de inscrição
1. Selecione **Seguinte**.
1. Na **Caixa da Chave de Autenticação**, introduza a chave de API.

    Pode obter a chave de API no portal Azure Enterprise no separador **Transferir Utilização**. Selecione a **Chave de Acesso da API** e, em seguida, cole a chave na caixa da **Chave da Conta**.
1. O carregamento dos dados no Power BI demora cerca de 5 a 30 minutos, consoante o tamanho dos conjuntos de dados.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossário de processos para calcular a taxa de recursos

- **Total de Unidades RAW:** quantidade consumida no relatório de utilização detalhada.
- **Recurso de MOCP Por Unidade:** o sistema de utilização de origem emite as utilizações de cada serviço em unidades diferentes. (Configuração predefinida)
- **Consumo Por Unidade:** unidade de medida do Azure Enterprise. (Configuração predefinida)
- **Preço:** preço unitário do portal Azure Enterprise.
- **Custo Total:** custo total do relatório de utilização detalhada ou utilização da alocação mais a utilização excedida do portal Azure Enterprise.

### <a name="charges-calculations"></a>Cálculos dos custos

- **Converter em recursos MOCP consumidos** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Converter em unidades consumidas** = `Consumed MOCP Resources / Consumption per Unit`
- **Calcular o custo total** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Lógica presente na Lógica de Cálculo de Utilização

**Taxa de Recurso** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

A taxa de recursos é derivada com base nos custos. Pode não corresponder ao preço unitário real na folha de preços.

No relatório de dados de utilização de transferências, pode ver a utilização de recursos não processada até seis casas decimais. Estes dados são utilizados para os cálculos dos custos de utilização excedida. No entanto, os dados de utilização apresentados no portal Azure Enterprise são arredondados quatro casas decimais para unidades de alocação e truncados para zero casas decimais para unidades de utilização excedida. No portal Azure Enterprise, toda a utilização excedida é cobrada apenas para unidades completas. Pode observar uma grande diferença observada entre o preço unitário e a taxa de recursos para a utilização cobrada como utilização excedida ou em meses mistos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Analisar os custos faturados
> * Analisar os custos de utilização excedida do serviço
> * Analisar a fatura do Marketplace

Avance para o próximo artigo para saber mais acerca da utilização do Azure EA Portal.

> [!div class="nextstepaction"]
> [Introdução ao Azure EA Portal](../manage/ea-portal-get-started.md)