---
title: Faturas de inscrição do Azure Enterprise
description: Este artigo explica como gerir e interpretar a fatura Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: d8d189d2ee67e3f98fec5d7d4cd295879e8689c9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743888"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas de inscrição do Azure Enterprise

Este artigo explica como gerir e interpretar a fatura do Contrato Enterprise do Azure (Azure EA). A fatura é uma representação da sua conta. Analise-a para ver se está correta. Deve também familiarizar-se com outras tarefas que possam ser necessárias para gerir a fatura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Alterar um número de nota de encomenda de uma fatura de utilização excedida

O portal Azure Enterprise gera automaticamente um número de nota de encomenda predefinido, a não ser que o administrador do Enterprise defina um antes da data da fatura. Um administrador do Enterprise pode atualizar o número da nota de encomenda num prazo de sete dias após a receção do e-mail de notificação da fatura.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Para atualizar o número da nota de encomenda dos serviços do Azure:

1. No portal Azure Enterprise, selecione **Relatório** > **Resumo da Utilização**.
1. Selecione **Editar Números de Nota de Encomenda** no canto superior direito.
1. Selecione o botão de opção **Serviços do Azure**.
1. Selecione um **Período de Fatura** no menu pendente de intervalos de datas.

   Pode editar um número de nota de encomenda durante um período de sete dias depois de receber uma notificação de fatura, mas antes de pagar a fatura.
1. Introduza um novo número da nota de encomenda no campo **Número da Nota de Encomenda**.
1. Selecione **Guardar** para submeter a alteração.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Para atualizar o número da nota de encomenda do Azure Marketplace:

1. No portal Azure Enterprise, selecione **Relatório** > **Resumo da Utilização**.
1. Selecione **Editar Números de Nota de Encomenda** no canto superior direito.
1. Selecione o botão de opção **Marketplace**.
1. Selecione um **Período de Fatura** no menu pendente de intervalos de datas.

   Pode editar um número de nota de encomenda durante um período de sete dias depois de receber uma notificação de fatura, mas antes de pagar a fatura.
1. Introduza um novo número da nota de encomenda no campo **Número da Nota de Encomenda**.
1. Selecione **Guardar** para submeter a alteração.

## <a name="cadence-of-azure-enterprise-billing"></a>Cadência de faturação do Azure Enterprise

A faturação por parte da Microsoft é realizada anualmente, na data efetiva da inscrição, altura em que são cobradas todas as compras de alocações relativas a serviços do Microsoft Azure. Em relação a qualquer utilização que exceda os montantes das alocações, a Microsoft fatura em atraso.

- As tarifas de alocação são propostas com base numa taxa mensal e são faturadas anualmente com antecedência.
- As tarifas de utilização excedida são calculadas mensalmente e faturadas posteriormente no final do período de faturação.

### <a name="billing-intervals"></a>Intervalos de faturação

O intervalo de faturação depende da forma como escolhe efetuar as compras de alocação. A alocação anual coincide com uma das seguintes:

- A data de aniversário da inscrição
- A data efetiva da Subscrição de Emenda de um ano.

A data em que recebe a fatura de utilização excedida depende da configuração e da data de início da inscrição:

- **Inscrições diretas com data de início anterior a 1 de maio de 2018**:
  - Se estiver num Contrato Enterprise (EA) direto, encontra-se num período de faturação anual para os serviços do Azure, exceto os serviços do Azure Marketplace. O período de faturação baseia-se na data de aniversário: a data em que o contrato entrou em vigor.
  - Se ultrapassar 150% do limiar da alocação monetária (MC) do EA, a inscrição será automaticamente convertida num período de faturação trimestral com base na data de aniversário. Receberá, também, uma fatura de utilização excedida.
  - Se não ultrapassar 150% do limiar da MC, a inscrição permanecerá num período de faturação anual. Receberá a fatura de utilização excedida no fim do ano de alocação.

- **Inscrições diretas com data de início posterior a 1 de maio de 2018**:
  - As faturas dos custos faturados em separado e do consumo do Azure são emitidas num período de faturação mensal.
  - Quaisquer custos não cobertos pela alocação monetária são devidos como um pagamento de utilização excedida.  

- **Inscrições indiretas com data de início anterior a 1 de maio de 2018**:

  Se for um cliente de Contrato Enterprise (EA) indireto com uma data de início anterior a 1 de maio de 2018, estará configurado para um período de faturação trimestral. O parceiro de canal (CP) emite-lhe a fatura diretamente.  

- **Inscrições indiretas com data de início posterior a 1 de maio de 2018**:

  Encontra-se num período de faturação mensal.  

### <a name="increase-your-monetary-commitment"></a>Aumentar a alocação monetária

Pode aumentar a alocação a qualquer momento. É-lhe cobrado o número de meses restantes referentes ao período de alocação desse ano. Por exemplo, se se inscrever numa Subscrição de Emenda de um ano e, em seguida, aumentar a alocação durante o mês seis, ser-lhe-ão faturados os restantes seis meses desse período. As quantidades da alocação são posteriormente atualizadas durante os últimos seis meses do período de alocação. Estas novas quantidades são utilizadas para determinar quaisquer custos de utilização excedida.

### <a name="overage"></a>Utilização Excedente

No caso da utilização excedida, é-lhe cobrada a utilização ou as reservas que excederam a alocação durante o período de faturação. Para ver uma discriminação do cálculo das quantidades de utilização excedida para itens individuais, veja o relatório de resumo de utilização ou contacte o seu parceiro de canal.

Para cada item na fatura, verá:

- **Montante Alargado**: o total de custos
- **Utilização da Alocação**: o montante da alocação utilizado para cobrir os custos
- **Montante Líquido**: os custos que excedem a alocação

Os impostos aplicáveis são calculados apenas sobre o valor líquido que excede a alocação.

A faturação da utilização excedida é automatizada. O tempo das notificações e das faturas depende da data de fim do período de faturação.

- A notificação de utilização excedida é normalmente enviada sete dias após a data de fim da faturação.
- As faturas de utilização excedida são enviadas entre sete a nove dias após a notificação.
- Pode rever os custos e atualizar os números de nota de encomenda gerados pelo sistema durante os sete dias entre a notificação de utilização excedida e a emissão da fatura.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do período de faturação de abril de 2019, os clientes começaram a receber uma única fatura do Azure que combina todos os custos do Azure e do Azure Marketplace numa única fatura, em vez de duas faturas separadas. Esta alteração não afeta os clientes na Austrália, no Japão ou em Singapura.

Durante a transição para uma fatura combinada, receberá uma fatura parcial do Azure Marketplace. Esta fatura separada final mostra os custos do Azure Marketplace incorridos antes da data de atualização da faturação. Os custos do Azure Marketplace incorridos após essa data são apresentados na fatura do Azure. Após o período de transição, vê todos os custos do Azure e do Azure Marketplace na fatura combinada.  

## <a name="adjust-billing-frequency"></a>Ajustar a frequência da faturação

A frequência da faturação de um cliente é anual, trimestral ou mensal. O período de faturação é determinado quando um cliente assina o contrato. A faturação mensal é o intervalo de faturação mais pequeno.

- Para inscrições diretas, é necessária a **aprovação** do administrador do Enterprise para alterar um período de faturação de anual para trimestral. Para inscrições indiretas, é necessária a aprovação de um administrador parceiro. A alteração torna-se efetiva no final do trimestre de faturação a decorrer.
- Para alterar um período de faturação anual ou trimestral para mensal, é necessária uma **emenda** do contrato.  Qualquer alteração ao período de faturação existente requer a aprovação de um administrador do Enterprise ou do “Contacto de Faturação”.
- **Submeta** a aprovação ao [suporte do portal Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selecione a categoria do problema: **Faturação e Emissão de Faturas**.

A alteração torna-se efetiva no final do trimestre de faturação a decorrer.

Se assinar uma Emenda M503, poderá mover qualquer contrato de qualquer frequência para a faturação mensal. 

## <a name="credits-and-adjustments"></a>Créditos e ajustes

Pode ver todos os créditos ou ajustes aplicados à inscrição na secção **Relatórios** do [portal Azure Enterprise](https://ea.azure.com).

Para ver os créditos:

1. No [portal Azure Enterprise](https://ea.azure.com), selecione a secção **Relatórios**.
1. Selecione **Resumo da Utilização**.
1. No canto superior direito, altere a vista **M** para **C**.
1. Expanda o campo de ajuste na tabela de alocação de serviços do Azure.
1. Pode ver os créditos aplicados à inscrição e uma breve explicação. Por exemplo: Crédito do Contrato de Nível de Serviço.

## <a name="request-an-invoice-copy"></a>Pedir uma cópia da fatura

Para pedir uma cópia da fatura, contacte o seu parceiro.

## <a name="overage-offset"></a>Compensação de utilização excedida

Para aplicar a alocação monetária a utilizações excedidas, tem de cumprir os seguintes critérios:

- Incorreu em custos de utilização excedida que não foram pagos e que estão incluídos no prazo de um ano a contar da data de fim do serviço faturado.
- O montante da alocação monetária disponível cobre o montante total dos custos incorridos, incluindo todas as faturas do Azure vencidas e por pagar.
- O período de faturação que quer completar tem de estar totalmente encerrado. A faturação ficará concluída por completo após o quinto dia de cada mês.
- O período de faturação que quer compensar tem de estar totalmente encerrado.
- O Desconto de Alocação do Azure (ACD) baseia-se na nova alocação real menos os fundos planeados para o consumo anterior. Esta condição é válida apenas para os custos de utilização excedida incorridos. Só é válida para serviços que consomem alocação monetária, pelo que não se aplica aos custos do Azure Marketplace. Os custos do Azure Marketplace são cobrados separadamente.

Para encerrar uma compensação de utilização excedida, o utilizador ou a equipa de conta podem abrir um pedido de suporte. É necessária uma aprovação por e-mail do administrador do Enterprise ou do Contacto de Faturação.

## <a name="view-price-sheet-information"></a>Ver as informações da folha de preços

Os administradores do Enterprise podem ver a lista de preços associada à inscrição relativa aos serviços do Azure.

Para ver a folha de preços atual:

1. No portal Azure Enterprise, selecione **Relatórios** e, em seguida, **Folha de Preços**.
2. Veja a folha de preços ou selecione **Transferir**.

![Exemplo que mostra as informações da folha de preços](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Para transferir o histórico de uma lista de preços:

1. No portal Azure Enterprise, selecione **Relatórios** e, em seguida, **Transferir Utilização**.
2. Transfira a folha de preços.

![Exemplo que mostra onde pode transferir uma folha de preços mais antiga](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Alguns motivos para diferenças nos preços:

- Os preços podem ter sido alterados entre a inscrição anterior e a nova inscrição. As alterações de preços podem ocorrer, uma vez que os preços são contratuais para uma inscrição específica, desde a data de início até à data de fim de um contrato.
- Quando transfere para uma nova inscrição, os preços são alterados para o novo contrato. Os preços são definidos pela folha de preços, que pode ser mais elevada na nova inscrição.
- Se o período de uma inscrição for alargado, os preços também serão alterados. Os preços mudam para tarifas pay as you go.

## <a name="request-detailed-usage-information"></a>Pedir informações de utilização detalhadas

Os administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada em contas específicas, transfira o relatório de detalhes de utilização ao aceder a **Relatórios** > **Transferir Utilização**.

> [!NOTE]
> O relatório de detalhes de utilização não inclui os impostos aplicáveis. Pode existir uma latência de cerca de oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para as inscrições indiretas, o seu parceiro tem de ativar a função de margem de lucro para que possa ver as informações relacionadas com os custos.

## <a name="reports"></a>Relatórios

Os administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

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
1. Em **Tipo de Dados Original**, escolha**delimitados**.  A predefinição é **Largura Fixa**.
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

## <a name="move-usage-data-to-another-enrollment"></a>Mover os dados de utilização para outra inscrição

Os dados de utilização só são movidos quando uma transferência tem efeitos retroativos. Existem duas opções para mover os dados de utilização de uma inscrição para outra:

- Transferências de contas de uma inscrição para outra
- Transferências de inscrições de uma inscrição para outra

Para qualquer uma das opções, tem de submeter um [pedido de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipa de Suporte EA para obter ajuda. 

## <a name="azure-ea-pricing-overview"></a>Descrição geral dos preços do Azure EA

Esta secção apresenta os detalhes acerca da forma como é calculada a utilização. Responde a várias perguntas frequentes relativas aos custos de vários serviços do Azure num Contrato Enterprise, onde os cálculos são mais complexos.

### <a name="price-protection"></a>Proteção de preço

Como cliente ou parceiro de canal, tem a garantia de que recebe preços iguais ou inferiores aos indicados na respetiva Folha de Preços do Cliente (CPS) ou o preço em vigor na data efetiva da compra do Azure. Este preço é designado por preço de base. Para serviços introduzidos após a compra do Azure, é-lhe cobrado o preço que estiver em vigor no desconto de nível aplicável quando o serviço é introduzido pela primeira vez. Esta proteção de preço aplica-se durante o período de alocação – um ou três anos, consoante o Contrato Enterprise.

### <a name="price-changes"></a>Alterações de preço

A Microsoft pode baixar o preço atual do Contrato Enterprise para serviços individuais do Azure durante o período de uma inscrição. Vamos alargar estas taxas reduzidas aos clientes existentes, enquanto o preço mais baixo estiver em vigor. Se estas tarifas forem posteriormente aumentadas, o preço da inscrição de um serviço não aumentará para além do teto de proteção de preços, conforme definido acima. Contudo, a tarifa pode aumentar em relação ao preço reduzido anterior. Em qualquer dos casos, vamos informar os clientes e os parceiros de canal indiretos de alterações de preço futuras através do envio de um e-mail aos administradores Enterprise e Parceiros associados à inscrição.

### <a name="current-effective-pricing"></a>Preços efetivos atuais

Os clientes e os parceiros de canal podem ver os preços atuais de uma inscrição ao iniciar sessão no [portal Azure Enterprise](https://ea.azure.com/) e ao ver a página da folha de preços relativa a essa inscrição. Se comprar o Azure indiretamente através de um dos nossos parceiros de canal, precisará de obter as atualizações de preços junto do parceiro de canal, a menos que o mesmo tenha ativado a apresentação de preços de margem de lucro para a inscrição.

### <a name="introduction-of-new-azure-services"></a>Introdução de novos serviços do Azure

Estamos continuamente a melhorar o Azure e adicionamos periodicamente novos serviços cujos preços são calculados separadamente dos serviços existentes. Alguns serviços em pré-visualização estão disponíveis automaticamente, enquanto outros exigem a ação do cliente no [portal de Contas do Azure](https://account.windowsazure.com/PreviewFeatures).

Alguns serviços começam com preços promocionais em vigor quando introduzidos pela primeira vez, que podem ser aumentados numa data futura.

À medida que os serviços passam de pré-visualização para disponibilidade geral (GA), os preços podem aumentar, uma vez que são aplicados SLAs de desempenho e fiabilidade totais. Tal aumento não é limitado pela proteção normal de preço de base. A utilização desses serviços é cobrada à tarifa aumentada. Para evitar custos relacionados com estes novos serviços, tem de optar por não os utilizar.

### <a name="introduction-of-regional-pricing"></a>Introdução de preços regionais

Além da introdução de novos serviços, os serviços também mudam periodicamente de uma base global única para um modelo mais regional, uma vez que o suporte regional para esses serviços aumenta.

Quando a regionalização de um serviço é introduzida pela primeira vez, a proteção de preço de base aplica-se a essas novas regiões. No entanto, se as regiões adicionais para esse mesmo serviço forem introduzidas posteriormente, serão considerados novos serviços e serão oferecidos com as próprias tarifas individuais, independentemente da proteção de preço de base.

### <a name="enterprise-devtest"></a>Enterprise Dev/Test

Os administradores do Enterprise podem permitir que os proprietários de contas criem subscrições com base na oferta Enterprise Dev/Test. O proprietário da conta tem de configurar as subscrições Enterprise Dev/Test que são necessárias para os subscritores subjacentes. Esta configuração permite que os subscritores ativos do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure a tarifas especiais Enterprise Dev/Test. Para obter mais informações, veja [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Cálculos de utilização do Contrato Enterprise

Veja os [serviços do Azure](https://azure.microsoft.com/services/) e os [preços do Azure](https://azure.microsoft.com/pricing/) para obter mais informações de preços públicos básicos, unidades de medida, FAQs e diretrizes de relatórios de utilização para cada serviço individual. Encontra mais informações acerca dos cálculos do EA nas seguintes secções.

### <a name="enterprise-agreement-units-of-measure"></a>Unidades de medida do Contrato Enterprise

As unidades de medida para os Contratos Enterprise são muitas vezes diferentes das unidades de medida observadas noutros programas nossos, como o programa do Contrato de Subscrição Online da Microsoft (MOSA). Esta disparidade significa que, para vários serviços, a unidade de medida é agregada para apresentar os preços normalizados. A unidade de medida apresentada na vista Resumo de Utilização do portal Azure Enterprise é sempre a medida Enterprise. É disponibilizada uma lista completa das atuais unidades de medida e conversões de cada serviço no ficheiro do Excel [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Nomes de Serviços Amigáveis).

### <a name="rounding-rules"></a>Regras de arredondamento

O portal Azure Enterprise segue a lógica de Arredondamento do Banqueiro padrão do IEEE ou Arredondamento de Gauss. Esta lógica arredonda os números para o dígito par mais próximo para valores de metade de dígito. A lógica de arredondamento Arredondar Metade por Excesso mais típica arredonda sempre a metade dos dígitos por excesso para o próximo dígito mais alto. Na verdade, este método do portal Azure Enterprise oferece uma soma total mais precisa sobre o grupo em comparação com a lógica padrão do Excel.

Para ilustrar: quando o primeiro dígito ignorado é um 5 e não existem dígitos seguintes ou os dígitos seguintes são zeros, arredonda-se para o dígito par mais próximo. Por exemplo: tanto 2,315 como 2,325, quando arredondados para o 100º mais próximo, ficam 2,32.

Para referência, a tabela que se segue mostra as fórmulas do Excel que pode utilizar para modelar as regras do portal Azure Enterprise para arredondamento e conversão:

| Cenário | Fórmula de Lógica do Banqueiro |
| --- | --- |
| Arredondar Utilização | =MARRED({_source_}, 0,0002) |
| Arredondar Preços (2 casas decimais) | =MARRED({_source_}, 0,02) |
| Arredondar Preços (0 casas decimais) | =MARRED({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversão entre o relatório de detalhes de utilização e a página de resumo de utilização

No relatório de dados de utilização de transferências, pode ver a utilização de recursos não processada até seis casas decimais. No entanto, os dados de utilização apresentados no portal Azure Enterprise são arredondados quatro casas decimais para unidades de alocação e truncados para zero casas decimais para unidades de utilização excedida. Os dados de utilização não processada são primeiramente arredondados para quatro dígitos antes da conversão para as unidades utilizadas no portal Azure Enterprise. Em seguida, as unidades do Enterprise convertidas são arredondadas novamente para quatro dígitos. Só pode ver as horas reais consumidas antes da conversão no relatório de utilização de transferências e não no portal Azure Enterprise.

Por exemplo: Se forem reportadas 694,533404 horas reais de SQL Server no relatório de detalhes de utilização. Estas unidades são convertidas para 6,94533404 unidades de 100 horas de computação que, em seguida, são arredondadas para 6,9453 e são apresentadas no portal Azure Enterprise.

- Para determinar o valor total da faturação, as unidades apresentadas são multiplicadas pelo Preço de Unidade de Alocação e o resultado é truncado para duas casas decimais. No caso do Iene japonês (JPY) e do Won coreano (KRW), o valor total é arredondado para zero casas decimais.
- No caso da utilização excedida, as unidades de faturação são truncadas para seis dígitos e, em seguida, multiplicadas pelo Preço de Unidade de Utilização Excedida para determinar o valor total de faturação.
- Para a faturação do Fornecedor de Serviços Geridos (MSP), toda a utilização associada a um departamento marcado como MSP é truncada para zero casas decimais após a conversão para a unidade de medida do EA. Como resultado, a soma desta utilização pode ser inferior à soma total de toda a utilização reportada no portal Azure Enterprise. Depende se o MSP está dentro do saldo de alocação monetária ou se está em utilização excedida.

### <a name="graduated-pricing"></a>Preços graduados

Atualmente, os preços do Contrato Enterprise não incluem preços graduados, onde os custos por unidade diminuem à medida que a utilização aumenta. Ao mudar de um programa MOSA com preços graduados para um Contrato Enterprise, os preços são definidos proporcionalmente ao escalão base do serviço menos quaisquer ajustes aplicáveis dos descontos do Contrato Enterprise.

### <a name="partial-hour-billing"></a>Faturação por hora parcial

Toda a utilização faturada baseia-se em minutos convertidos para horas parciais em vez de incrementos de hora inteira. As tarifas Enterprise listadas por hora aplicam-se ao total de horas em adição com as horas parciais.

### <a name="average-daily-consumption"></a>Consumo diário médio

Alguns serviços têm um preço definido mensalmente, mas a utilização é reportada diariamente. Nestes casos, a utilização é avaliada uma vez por dia, dividida por 31 e somada pelo número de dias nesse mês de faturação. Assim, as tarifas nunca são superiores ao esperado para qualquer mês e são ligeiramente mais baixas para os meses que têm menos de 31 dias.

### <a name="compute-hours-conversion"></a>Conversão de horas de computação

Antes de 28 de janeiro de 2016, a utilização de Serviços Cloud e de Máquinas Virtuais Básicas e Standard A0, A2, A3 e A4 era emitida como minutos de medidores de Máquinas Virtuais A1. As VMs A0 eram contabilizadas como frações de minutos de VM A1, enquanto as A2s, A3s e A4s eram convertidas em múltiplos. Como esta política causava alguma confusão nos nossos clientes, implementámos uma alteração para atribuir a utilização por minuto a medidores de A0, A2, A3 e A4 dedicados.

A nova medição entrou em vigor entre 27 de janeiro e 28 de janeiro de 2016. Na transferência do CSV que mostra a utilização durante o período desta transição, pode ver:

- A utilização emitida no medidor A1
- A utilização emitida no novo medidor dedicado correspondente ao tamanho da sua implementação.

| **Tamanho de implementação** | **Utilização emitida como múltiplo de A1 até 26 de janeiro de 2016** | **Utilização emitida no medidor dedicado a partir de 27 de janeiro de 2016** |
| --- | --- | --- |
| A0 | 0,25 de hora A1 | 1 de hora A0 |
| A2 | 2 de hora A1 | 1 de hora A2 |
| A3 | 4 de hora A1 | 1 de hora A3 |
| A4 | 8 de hora A1 | 1 de hora A4 |

### <a name="regions"></a>Regiões

Para os serviços em que a zona e a região afetam os preços, veja a tabela seguinte que apresenta um mapa de geografias e regiões:

| Área Geográfica | Regiões de Transferência de Dados | Regiões de CDN |
| --- | --- | --- |
| Zona 1 | Norte da Europa <br> Europa Ocidental <br> E.U.A. Oeste <br> E.U.A Leste <br> E.U.A. Centro-Norte <br> E.U.A. Centro-Sul <br> E.U.A. Leste 2 <br> E.U.A. Central | América do Norte <br> Europa |
| Zona 2 | Este da Ásia-Pacífico <br> Sudeste da Ásia-Pacífico <br> Leste do Japão <br> Oeste do Japão <br> Leste da Austrália <br> Austrália Sudeste | Ásia-Pacífico <br> Japão <br> América Latina <br> Médio Oriente/África <br> Leste da Austrália <br> Austrália Sudeste |
| Zona 3 | Sul do Brasil |   |

Não há custos para a saída de dados entre serviços alojados no mesmo datacenter. Por exemplo, o Office 365 e o Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Alocação monetária e utilização não faturada

A alocação monetária do Azure é um montante pago antecipadamente pelos serviços do Azure. A alocação monetária é consumida à medida que os serviços são utilizados. Os serviços originais do Azure são faturados relativamente à alocação monetária. No entanto, alguns custos são faturados separadamente e os serviços do Azure Marketplace não consomem alocação monetária.

### <a name="charges-billed-separately"></a>Custos faturados em separado

Alguns produtos e serviços fornecidos por origens de terceiros não consomem a alocação monetária do Azure. Em vez disso, estes itens são faturados separadamente como parte da fatura de utilização excedida do período de faturação padrão.

Combinámos todos os custos do Azure e do Azure Marketplace numa única fatura que está em linha com o período de faturação da inscrição. A fatura combinada não se aplica a clientes do Azure na Austrália, no Japão ou em Singapura.

A fatura combinada mostra, primeiramente, a utilização do Azure, seguida de quaisquer custos do Azure Marketplace. Os clientes na Austrália, no Japão ou em Singapura vêm os respetivos custos do Azure Marketplace numa fatura separada.

Se não existir utilização excedida no fim do período de faturação padrão, os custos faturados em separado terão uma faturação à parte. Este processo aplica-se a clientes na Austrália, no Japão e em Singapura.

Os seguintes serviços são faturados separadamente:

- Canónico
- Citrix XenApp Essentials
- Utilizador Registado do Citrix XenDesktop
- OpenLogic
- Utilizador Registado do Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (Mensal)
- Visual Studio Enterprise (Anual)
- Visual Studio Professional (Mensal)
- Visual Studio Professional (Anual)

## <a name="what-to-expect-after-change-of-channel-partner"></a>O que esperar após a mudança de parceiro de canal

Se a mudança de parceiro de canal (COCP) ocorrer a meio do mês, um cliente receberá uma fatura de utilização do parceiro associado anterior e outra fatura para a utilização referente ao novo parceiro.

As faturas serão disponibilizadas no mês a seguir ao final do período de faturação. Se a cadência de faturação for mensal, a fatura de setembro será disponibilizada em outubro para ambos os parceiros. Se o ciclo de faturação for trimestral ou anual, o cliente pode esperar uma fatura do parceiro associado anterior para a utilização durante o respetivo período e a restante para o novo parceiro com base na cadência de faturação.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para os clientes diretos, os custos do Azure Marketplace estão visíveis no portal Azure Enterprise. As compras e o consumo do Azure Marketplace são faturados fora da alocação monetária numa cadência trimestral ou mensal após o consumo efetivo.

Os clientes indiretos podem encontrar as respetivas subscrições do Azure Marketplace na página **Gerir Subscrições** do portal Azure Enterprise, mas os preços ficam ocultos. Os clientes devem entrar em contacto com o respetivo Fornecedor de Soluções de Licenciamento (LSP) para obter mais informações acerca dos custos do Azure Marketplace.

As novas compras mensais ou anuais recorrentes do Azure Marketplace são faturadas pelo valor total durante o período em que os itens do Azure Marketplace foram comprados. Estes itens serão renovados automaticamente no período seguinte, no mesmo dia da compra original.

Os custos mensais recorrentes que existam continuam a ser renovados no primeiro dia de cada mês de calendário. Os custos anuais continuam a ser renovados no aniversário da data de compra.

Alguns serviços de revenda de terceiros disponíveis no Azure Marketplace consomem agora o saldo de alocação monetária do Contrato Enterprise (EA). Anteriormente, estes serviços eram cobrados à margem da alocação monetária do EA e faturados separadamente. A alocação monetária do EA para estes serviços no Azure Marketplace ajuda a simplificar a gestão das compras e dos pagamentos dos clientes. Para obter uma lista completa dos serviços que agora consomem a alocação monetária, veja a [atualização de 6 de março de 2018 no site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Parceiros

Os LSPs podem transferir uma lista de preços do Azure Marketplace a partir da página da folha de preços no portal Azure Enterprise. Selecione a ligação da **Lista de preço do Marketplace** no canto superior direito. A lista de preços do Azure Marketplace mostra todos os serviços disponíveis e os respetivos preços.

Para transfira a lista de preços:

1. No portal Azure Enterprise, aceda a **Relatórios** > **Folha de Preços**.
1. No canto superior direito, localize a ligação para a lista de preços do Azure Marketplace por baixo do nome de utilizador.
1. Clique com o botão direito do rato na ligação e selecione **Guardar Destino Como**.
1. Na janela **Guardar**, altere o título do documento para`AzureMarketplacePricelist.zip`, o que vai alterar o ficheiro de um .xlsx para um ficheiro .zip.
1. Após a transferência estar concluída, terá um ficheiro zip com as listas de preços específicas do país.
1. Os LSPs devem consultar o ficheiro do país específico para verem os preços específicos do país. Os LSPs podem utilizar o separador **Notificações** para estarem a par dos SKUs que são novos na rede ou que foram descontinuados.
1. As alterações de preços ocorrem com pouca frequência. Os LSPs recebem notificações por e-mail dos aumentos de preços e das alterações de câmbio internacional (FX) com 30 dias de antecedência.
1. Os LSPs recebem uma fatura por inscrição, por ISV, por trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Ativar as compras no Azure Marketplace

Os administradores do Enterprise podem desativar ou ativar as compras do Azure Marketplace para todas as subscrições do Azure na respetiva inscrição. Se o administrador do Enterprise desativar as compras e existirem subscrições do Azure que já têm subscrições do Azure Marketplace, essas subscrições do Azure Marketplace não serão canceladas nem afetadas.

Embora os clientes possam converter as suas subscrições diretas do Azure para o Azure EA ao associá-las à inscrição no portal Azure Enterprise, esta ação não converte automaticamente as subscrições subordinadas.

Para ativar compras do Azure Marketplace:

1. Inicie sessão no portal Azure Enterprise como administrador do Enterprise.
1. Aceda a **Gerir**.
1. Em **Detalhes da Inscrição**, selecione o ícone do lápis ao lado do item de linha **Azure Marketplace**.
1. Alterne entre **Ativado/Desativado** ou **Apenas SKUs Gratuitos/BYOL**, conforme apropriado.
1. Selecione **Guardar**.

> [!NOTE]
> A opção Apenas Gratuitos e BYOL (traga a sua própria licença) limita a compra e aquisição de SKUs do Azure Marketplace para apenas SKUs Gratuitos e BYOL.

Obtenha mais informações acerca dos [Custos do Azure Marketplace nos relatórios do portal Azure Enterprise](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Serviços faturados à hora para o Azure EA

Os seguintes serviços são faturados à hora ao abrigo de um Contrato Enterprise em vez da tarifa mensal do MOSP:

- Rede de Entrega de Aplicações
- Firewall de Aplicação Web

### <a name="azure-remoteapp"></a>Azure RemoteApp

Se tiver um Contrato Enterprise, pagará o Azure RemoteApp com base no nível de preço do respetivo Contrato Enterprise. Não existem custos adicionais. O preço padrão inclui 40 horas iniciais. O preço ilimitado cobre 80 horas iniciais. O RemoteApp deixa de emitir a utilização superior a 80 horas.

## <a name="azure-marketplace-faq"></a>FAQ do Azure Marketplace

Esta secção explica como se pode aplicar a alocação monetária do Azure a alguns serviços de revenda de terceiros no Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>O que mudou nos serviços do Azure Marketplace e no alocação monetária do EA?

Desde 1 de março de 2018 que alguns serviços de revenda de terceiros consomem a alocação monetária (MC) do EA. À exceção das instâncias de VM reservadas (RIs) do Azure, os serviços eram previamente faturados fora da alocação monetária do EA e possuíam uma faturação à parte.

Alargámos a utilização da alocação monetária para incluir alguns dos serviços do Azure Marketplace publicados por terceiros, que são comprados com mais frequência. A alocação monetária do EA para estes serviços no Azure Marketplace ajuda a simplificar a gestão das compras e dos pagamentos.

### <a name="why-did-we-make-this-change"></a>Por que motivo fizemos esta alteração?

Os clientes estão continuamente à procura de outras formas de tirar partido do pagamento antecipado da alocação monetária. Esta alteração foi frequentemente pedida pelos clientes e teve impacto numa grande parte dos clientes do Azure Marketplace.

### <a name="how-do-you-benefit"></a>Como beneficia?

Obtém uma experiência de faturação mais simples e é pode gastar a alocação monetária do EA de forma mais adequada. Como estes serviços estão incluídos na MC pré-paga, a alocação monetária do EA torna-se mais útil.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Que serviços do Azure Marketplace utilizam a alocação monetária do EA e como posso saber?

Ao comprar um serviço que utiliza a MC, o Azure Marketplace apresenta uma exclusão de responsabilidade. São suportados alguns serviços publicados pelo Red Hat, SUSE, Autodesk e Oracle. Atualmente, os serviços com nomes semelhantes publicados por outras entidades não são deduzidos na MC. No fim desta FAQ, está disponível uma lista completa.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>E se a minha alocação monetária esgotar?

Se consumir a totalidade da alocação monetária e entrar em utilização excedida, os custos relacionados com estes serviços serão apresentados na próxima fatura de utilização excedida, juntamente com quaisquer outros serviços de consumo. Antes da alteração de 1 de março de 2018, estes custos eram faturados com outros serviços do Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Porque é que nem todos os Azure Marketplaces consomem alocação monetária do EA?

Trabalhamos frequentemente para proporcionar a melhor experiência para o cliente em relação à alocação monetária do EA. Esta alteração aplicar-se a um grande número de clientes e a uma parte significativa do total de gastos no Azure Marketplace. No futuro, podem ser adicionados outros serviços.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>De que forma é que isto afeta os parceiros e clientes de inscrição indireta?

Não há qualquer impacto para os nossos parceiros ou clientes de inscrição indireta. Estes serviços estão sujeitos às mesmas margens de lucro de parceiros do que outros serviços de consumo. A única alteração é que os custos são apresentamos numa fatura diferente e o pagamento dos custos é debitado na alocação monetária do cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Existe uma lista dos serviços do Azure Marketplace que consomem a alocação monetária do EA?

As ofertas específicas do Azure Marketplace podem utilizar fundos de alocação monetária do Azure. Veja os [serviços de terceiros que utilizam alocação monetária](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa dos produtos que participam neste programa.

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

## <a name="reports-faq"></a>FAQ sobre Relatórios

Esta secção aborda questões comuns acerca dos relatórios.

### <a name="why-is-my-cost-showing-as-0"></a>Por que é que o meu custo é apresentado como 0 $?

Para clientes de **inscrição direta**, os administradores do Enterprise podem conceder aos proprietários de conta e aos administradores de departamento o acesso a informações sobre custos/preços nos relatórios de utilização. Siga estes passos.

1. No portal Azure Enterprise, selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o lápis azul junto a Os administradores de departamento podem ver os custos.
1. Selecione **Ativado** e guarde.
1. Selecione o lápis azul junto a Os proprietários de contas podem ver os custos.
1. Selecione **Ativado** e guarde.

> [!NOTE]
> Se for proprietário de conta ou administrador de departamento, contacte o administrador do Enterprise para ativar a funcionalidade de preços.

Para clientes de **inscrição indireta**, contacte o parceiro para verificar se este ativou a funcionalidade de preços para si. Esta ação pode ser executada pelo parceiro. Depois de ativado, pode ver o custo e os preços da inscrição como administrador Enterprise.

Para os parceiros, se quiser ativar a funcionalidade de visualização de custos para um proprietário de conta ou administrador de departamento, siga os passos em **inscrição direta**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Por que motivo não existem informação de SKU no meu relatório de detalhes de utilização?

O relatório de detalhes de utilização não contem informações de SKU. Contudo, o relatório inclui informações de utilização para que possa transferir o relatório da folha de preços para obter as informações de SKU.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Porque é que o montante total no Azure Marketplace não corresponde aos relatórios de detalhes e resumo de utilização?

O relatório de custos do Azure Marketplace mostra apenas os custos com base na utilização. As tarifas únicas não são apresentadas. Veja a página de resumo de utilização dos custos com base na utilização mais atualizados e taxas únicas.

### <a name="why-is-there-no-information-on-my-api-report"></a>Porque é que não há informações no meu relatório de API?

As chaves de API expiram a cada seis meses. Se estiver com um problema, um administrador do Enterprise deverá gerar uma nova chave API. Lembre-se de seguir os passos na FAQ sobre o Relatório de API FAQ.

### <a name="why-isnt-my-power-bi-report-working"></a>Porque é que o meu relatório do Power BI não está a funcionar?

Para problemas relacionados com o Power BI, registe um pedido de suporte junto da [equipa de suporte técnico do Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Porque é que as minhas etiquetas de recursos não são apresentadas nos meus relatórios

As etiquetas de recursos são geridas no portal do Azure. Pode contactar a equipa de subscrição do Azure no [portal do Azure](https://portal.azure.com). Siga os passos no artigo [Como criar um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Por que é que a minha taxa de recursos muda todos os dias?

A taxa de recursos apresentada no relatório de utilização detalhada é um valor calculado. Representa a tarifa média mensal cobrada pelo serviço. A taxa do recurso é calculada a partir da média da alocação mensal e dos custos mensais de utilização excedida para uma unidade de serviço. A parte da utilização cobrada na alocação e nas taxas de utilização excedida é alterada para o dia em que o mês termina. Assim sendo, a taxa de recursos listada também muda durante o mês. A taxa de recursos é bloqueada no quinto dia após o fim do mês.

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

- Os seguintes ficheiros de Excel apresentam detalhes acerca dos serviços do Azure e são atualizados no dia 6 e no dia 20 de cada mês:

   | Título | Descrição | Nome de ficheiro |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Nomes de Serviços Amigáveis) | Lista todos os serviços ativos e inclui: <br>  <ul><li>categoria do serviço</li>   <li>nome de serviço amigável</li>   <li>nome da alocação e número do mosaico</li> <li>nome de consumo e número do mosaico</li>   <li>unidades de medida</li>   <li>fatores de conversão entre a utilização reportada e a utilização apresentada no portal Enterprise</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) (Campos de Transferências de Serviços) | Esta folha de cálculo fornece uma lista de todas as combinações possíveis dos campos relacionados com o serviço no Relatório de Transferência de Utilização. | Service\_Download\_Fields.xlsx |

- Para obter informações sobre como compreender a fatura e os custos, veja [Compreender a fatura do Contrato Enterprise do Azure](../understand/review-enterprise-agreement-bill.md).
- Para começar a utilizar o portal Azure Enterprise, veja [Introdução ao portal Azure EA](ea-portal-get-started.md).
