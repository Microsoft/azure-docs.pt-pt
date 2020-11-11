---
title: Faturas de inscrição do Azure Enterprise
description: Este artigo explica como gerir e interpretar a fatura Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 09/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: 57ada2ca760ffff2124582c31fdd095d04d4375f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410973"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas de inscrição do Azure Enterprise

Este artigo explica como gerir e interpretar a fatura do Contrato Enterprise do Azure (Azure EA). A fatura é uma representação da sua conta. Analise-a para ver se está correta. Deve também familiarizar-se com outras tarefas que possam ser necessárias para gerir a fatura.

## <a name="view-usage-summary-and-download-reports"></a>Ver o resumo de utilização e os relatórios de transferências

Os administradores de Enterprise podem ver um resumo dos dados de utilização, dos Pré-pagamentos consumidos do Azure e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada de contas específicas, transfira o relatório de detalhes de utilização:

1. Inicie sessão no portal Azure Enterprise.
1. Selecione **Relatórios**.
1. Selecione o separador **Transferir Utilização**.
1. Na lista de relatórios, selecione **Transferir** para o relatório mensal que quer obter.

   > [!NOTE]
   > O relatório de detalhes de utilização não inclui os impostos aplicáveis.
   >
   > Pode existir uma latência de até oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para ver relatórios e grafos do resumo da utilização:

1. Inicie sessão no portal Azure Enterprise.

1. Selecione um termo de Pré-pagamento.

   Para alterar o intervalo de datas do **Resumo de Utilização** , pode alternar entre **M** (Mensal) e **P** (Personalizado) no canto superior direito da página e, em seguida, introduzir datas de início e de fim personalizadas.

   ![Criar e ver o resumo da utilização e transferir os relatórios na vista personalizada](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Para ver detalhes adicionais, pode selecionar um período ou mês no grafo.

   - O grafo mostra a utilização por mês com uma discriminação da utilização consumida, da sobretaxa de serviços, dos custos cobrados separadamente e dos custos do Azure Marketplace.
   - Para o mês selecionado, pode utilizar os campos abaixo do grafo para filtrar por departamentos, contas e subscrições.
   - Pode alternar entre **Custo por Serviços** e **Custo por Hierarquia**.
   - Veja detalhes do **Serviço Azure** , dos **Custos Cobrados Separadamente** e do **Azure Marketplace** ao expandir as secções relevantes.

Assista a este vídeo para perceber como ver a utilização:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Transferir relatórios CSV

Os administradores do Enterprise utilizam a página Transferência de Relatórios Mensais para transferir os seguintes relatórios como ficheiros CSV:

- Saldo e custo
- Detalhes de utilização
- Custos do Azure Marketplace
- Folha de preços

Para transferir os relatórios:

1. No portal Azure Enterprise, selecione **Relatórios**.
2. Selecione **Transferir Utilização** na parte superior da página.
3. Selecione **Transferir** ao lado do relatório do mês desejado.

   > [!NOTE]
   > Poderá existir uma latência de cerca de cinco dias entre a data de utilização incorrida e a data em que a utilização é mostrada nos relatórios.
   >
   > Os utilizadores que transferirem os ficheiros CSV para o Excel com o Safari poderão deparar-se com erros de formatação. Para evitar esses erros, abra o ficheiro com um editor de texto.

![Exemplo que mostra página Transferir Utilização](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Assista a este vídeo para ver como transferir as informações de utilização:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Transferência de relatórios avançados

Pode utilizar a transferência de relatórios avançados para obter relatórios que cubram contas ou intervalos de datas específicos. O ficheiro de saída tem o formato CSV de forma a suportar grandes conjunto de registos.

1. No portal Azure Enterprise, selecione **Transferência de Relatório Avançado**.
1. Selecione um intervalo de data adequado e as contas adequadas.
1. Selecione **Pedir Dados de Utilização**.
1. Selecione o botão **Atualizar** até que o estado do relatório atualize para **Transferir**.
1. Transfira o relatório.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Transferir relatórios de utilização e informações de faturação de uma inscrição anterior

Pode transferir relatórios de utilização e informações de faturação de uma inscrição anterior após ter ocorrido uma transferência de inscrição. Os relatórios históricos ficam disponíveis no portal Azure Enterprise e na gestão de custos.

O portal Azure Enterprise filtra as inscrições inativas de forma a não serem apresentadas na vista. Precisa de desmarcar a caixa **Ativa** para ver as inscrições transferidas inativas.  

![A desmarcação da caixa Ativa permite ao utilizador ver as inscrições inativas](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Alterar um número de nota de encomenda de uma fatura de utilização excedida futura

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

## <a name="azure-enterprise-billing-frequency"></a>Frequência de faturação do Azure Enterprise

A faturação por parte da Microsoft é realizada anualmente, na data efetiva da inscrição, altura em que são cobradas todas as compras de Pré-pagamento relativas a serviços do Microsoft Azure. Em relação a qualquer utilização que exceda os montantes do Pré-pagamento, a Microsoft fatura em atraso.

- As tarifas de Pré-pagamento são propostas com base numa taxa mensal e são faturadas anualmente com antecedência.
- As tarifas de utilização excedida são calculadas mensalmente e faturadas posteriormente no final do período de faturação.

### <a name="billing-intervals"></a>Intervalos de faturação

O intervalo de faturação depende da forma como escolhe fazer as compras de Pré-pagamento. O Pré-pagamento anual coincide com uma das seguintes:

- A data de aniversário da inscrição
- A data efetiva da Subscrição de Emenda de um ano.

A data em que recebe a fatura de utilização excedida depende da configuração e da data de início da inscrição:

- **Inscrições diretas com data de início anterior a 1 de maio de 2018** :
  - Se estiver num Contrato Enterprise (EA) direto, encontra-se num período de faturação anual para os serviços do Azure, exceto os serviços do Azure Marketplace. O período de faturação baseia-se na data de aniversário: a data em que o contrato entrou em vigor.
  - Se ultrapassar 150% do limiar do Pré-pagamento do Azure do EA, a inscrição será automaticamente convertida num período de faturação trimestral com base na data de aniversário. Receberá, também, uma fatura de utilização excedida.
  - Se não ultrapassar 150% do limiar do Pré-pagamento do Azure, a inscrição permanecerá num período de faturação anual. Receberá a fatura de utilização excedida no fim do ano de Pré-pagamento.

- **Inscrições diretas com data de início posterior a 1 de maio de 2018** :
  - As faturas dos custos faturados em separado e do consumo do Azure são emitidas num período de faturação mensal.
  - Quaisquer custos que não estejam cobertos pelo Pré-pagamento do Azure são devidos como pagamento de utilização excedida.  

- **Inscrições indiretas com data de início anterior a 1 de maio de 2018** :

  Se for um cliente de Contrato Enterprise (EA) indireto com uma data de início anterior a 1 de maio de 2018, estará configurado para um período de faturação trimestral. O parceiro de canal (CP) emite-lhe a fatura diretamente.  

- **Inscrições indiretas com data de início posterior a 1 de maio de 2018** :

  Encontra-se num período de faturação mensal.  

### <a name="increase-your-azure-prepayment"></a>Aumentar o Pré-pagamento do Azure

Pode aumentar o Pré-pagamento a qualquer momento. É-lhe cobrado o número de meses restantes referentes ao período de Pré-pagamento desse ano. Por exemplo, se se inscrever numa Subscrição de Emenda de um ano e, em seguida, aumentar o Pré-pagamento durante o mês seis, ser-lhe-ão faturados os restantes seis meses desse período. As quantidades do Pré-pagamento são posteriormente atualizadas durante os últimos seis meses do período de Pré-pagamento. Estas novas quantidades são utilizadas para determinar quaisquer custos de utilização excedida.

### <a name="overage"></a>Utilização Excedente

No caso da utilização excedida, é-lhe cobrada a utilização ou as reservas que excederam o Pré-pagamento durante o período de faturação. Para ver uma discriminação do cálculo das quantidades de utilização excedida para itens individuais, veja o relatório de resumo de utilização ou contacte o seu parceiro de canal.

Para cada item na fatura, verá:

- **Montante Alargado** : o total de custos
- **Utilização do Pré-pagamento** : o montante do Pré-pagamento utilizado para cobrir os custos
- **Montante Líquido** : os custos que excedem o Pré-pagamento

Os impostos aplicáveis são calculados apenas sobre o montante líquido que excede o Pré-pagamento.

A faturação da utilização excedida é automatizada. O tempo das notificações e das faturas depende da data de fim do período de faturação.

- A notificação de utilização excedida é normalmente enviada sete dias após a data de fim da faturação.
- As faturas de utilização excedida são enviadas entre sete a nove dias após a notificação.
- Pode rever os custos e atualizar os números de nota de encomenda gerados pelo sistema durante os sete dias entre a notificação de utilização excedida e a emissão da fatura.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do período de faturação de abril de 2019, os clientes começaram a receber uma única fatura do Azure que combina todos os custos do Azure e do Azure Marketplace numa única fatura, em vez de duas faturas separadas. Esta alteração não afeta os clientes na Austrália, no Japão ou em Singapura.

Durante a transição para uma fatura combinada, receberá uma fatura parcial do Azure Marketplace. Esta fatura separada final mostra os custos do Azure Marketplace incorridos antes da data de atualização da faturação. Os custos do Azure Marketplace incorridos após essa data são apresentados na fatura do Azure. Após o período de transição, vê todos os custos do Azure e do Azure Marketplace na fatura combinada.  

### <a name="adjust-billing-frequency"></a>Ajustar a frequência da faturação

A frequência da faturação de um cliente é anual, trimestral ou mensal. O período de faturação é determinado quando um cliente assina o contrato. A faturação mensal é o intervalo de faturação mais pequeno.

- Para inscrições diretas, é necessária a **aprovação** do administrador do Enterprise para alterar um período de faturação de anual para trimestral. Para inscrições indiretas, é necessária a aprovação de um administrador parceiro. A alteração torna-se efetiva no final do trimestre de faturação a decorrer.
- Para alterar um período de faturação anual ou trimestral para mensal, é necessária uma **emenda** do contrato.  Qualquer alteração ao período de faturação existente requer a aprovação de um administrador do Enterprise ou do “Contacto de Faturação”.
- **Submeta** a aprovação ao [suporte do portal Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selecione a categoria do problema: **Faturação e Emissão de Faturas**.

A alteração torna-se efetiva no final do trimestre de faturação a decorrer.

Se assinar uma Emenda M503, poderá mover qualquer contrato de qualquer frequência para a faturação mensal. 

### <a name="request-an-invoice-copy"></a>Pedir uma cópia da fatura

Para pedir uma cópia da fatura, contacte o seu parceiro.

## <a name="credits-and-adjustments"></a>Créditos e ajustes

Pode ver todos os créditos ou ajustes aplicados à inscrição na secção **Relatórios** do [portal Azure Enterprise](https://ea.azure.com).

Para ver os créditos:

1. No [portal Azure Enterprise](https://ea.azure.com), selecione a secção **Relatórios**.
1. Selecione **Resumo da Utilização**.
1. No canto superior direito, altere a vista **M** para **C**.
1. Expanda o campo de ajuste na tabela de Pré-pagamento de serviços do Azure.
1. Pode ver os créditos aplicados à inscrição e uma breve explicação. Por exemplo: Crédito do Contrato de Nível de Serviço.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Pagar a utilização excedida com o Pré-pagamento do Azure

Para aplicar o Pré-pagamento do Azure a utilizações excedidas, tem de cumprir os seguintes critérios:

- Incorreu em custos de utilização excedida que não foram pagos e que estão incluídos no prazo de um ano a contar da data de fim do serviço faturado.
- O montante do Pré-pagamento do Azure disponível cobre o montante total dos custos incorridos, incluindo todas as faturas do Azure vencidas e por pagar.
- O período de faturação que quer completar tem de estar totalmente encerrado. A faturação ficará concluída por completo após o quinto dia de cada mês.
- O período de faturação que quer compensar tem de estar totalmente encerrado.
- O Desconto de Pré-pagamento do Azure (ACD) baseia-se no novo Pré-pagamento menos os fundos planeados para o consumo anterior. Esta condição é válida apenas para os custos de utilização excedida incorridos. Só é válida para serviços que consomem o Pré-pagamento do Azure, pelo que não se aplica aos custos do Azure Marketplace. Os custos do Azure Marketplace são cobrados separadamente.

Para encerrar uma compensação de utilização excedida, o utilizador ou a equipa de conta podem abrir um pedido de suporte. É necessária uma aprovação por e-mail do administrador do Enterprise ou do Contacto de Faturação.

## <a name="move-charges-to-another-enrollment"></a>Mover os custos para outra inscrição

Os dados de utilização só são movidos quando uma transferência tem efeitos retroativos. Existem duas opções para mover os dados de utilização de uma inscrição para outra:

- Transferências de contas de uma inscrição para outra
- Transferências de inscrições de uma inscrição para outra

Para qualquer uma das opções, tem de submeter um [pedido de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipa de Suporte EA para obter ajuda. 

## <a name="enterprise-agreement-usage-calculations"></a>Cálculos de utilização do Contrato Enterprise

Veja os [serviços do Azure](https://azure.microsoft.com/services/) e os [preços do Azure](https://azure.microsoft.com/pricing/) para obter mais informações de preços públicos básicos, unidades de medida, FAQs e diretrizes de relatórios de utilização para cada serviço individual. Encontra mais informações acerca dos cálculos do EA nas seguintes secções.

### <a name="enterprise-agreement-units-of-measure"></a>Unidades de medida do Contrato Enterprise

As unidades de medida para os Contratos Enterprise são muitas vezes diferentes das unidades de medida observadas noutros programas nossos, como o programa do Contrato de Subscrição Online da Microsoft (MOSA). Esta disparidade significa que, para vários serviços, a unidade de medida é agregada para apresentar os preços normalizados. A unidade de medida apresentada na vista Resumo de Utilização do portal Azure Enterprise é sempre a medida Enterprise. É disponibilizada uma lista completa das atuais unidades de medida e conversões de cada serviço no ficheiro do Excel [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Nomes de Serviços Amigáveis).

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversão entre o relatório de detalhes de utilização e a página de resumo de utilização

No relatório de dados de utilização de transferências, pode ver a utilização de recursos não processada até seis casas decimais. No entanto, os dados de utilização apresentados no portal Azure Enterprise são arredondados em quatro casas decimais para unidades de Pré-pagamento e truncados para zero casas decimais para unidades de utilização excedida. Os dados de utilização não processada são primeiramente arredondados para quatro dígitos antes da conversão para as unidades utilizadas no portal Azure Enterprise. Em seguida, as unidades do Enterprise convertidas são arredondadas novamente para quatro dígitos. Só pode ver as horas reais consumidas antes da conversão no relatório de utilização de transferências e não no portal Azure Enterprise.

Por exemplo: Se forem reportadas 694,533404 horas reais de SQL Server no relatório de detalhes de utilização. Estas unidades são convertidas para 6,94533404 unidades de 100 horas de computação que, em seguida, são arredondadas para 6,9453 e são apresentadas no portal Azure Enterprise.

- Para determinar o montante total da faturação, as unidades apresentadas são multiplicadas pelo Preço de Unidade de Pré-pagamento e o resultado é truncado para duas casas decimais. No caso do Iene japonês (JPY) e do Won coreano (KRW), o valor total é arredondado para zero casas decimais.
- No caso da utilização excedida, as unidades de faturação são truncadas para seis dígitos e, em seguida, multiplicadas pelo Preço de Unidade de Utilização Excedida para determinar o valor total de faturação.
- Para a faturação do Fornecedor de Serviços Geridos (MSP), toda a utilização associada a um departamento marcado como MSP é truncada para zero casas decimais após a conversão para a unidade de medida do EA. Como resultado, a soma desta utilização pode ser inferior à soma total de toda a utilização reportada no portal Azure Enterprise. Depende se o MSP está dentro do saldo do Pré-pagamento do Azure ou se está em utilização excedida.

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

Não há custos para a saída de dados entre serviços alojados no mesmo datacenter. Por exemplo, Microsoft 365 e Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Pré-pagamento do Azure e utilização não faturada

O Pré-pagamento do Azure é um montante pago antecipadamente pelos serviços do Azure. O Pré-pagamento do Azure é consumido à medida que os serviços são utilizados. Os serviços originais do Azure são faturados relativamente ao Pré-pagamento do Azure. No entanto, alguns custos são faturados separadamente e os serviços do Azure Marketplace não consomem o Pré-pagamento do Azure.

### <a name="charges-billed-separately"></a>Custos faturados em separado

Alguns produtos e serviços fornecidos por terceiros não consomem o Pré-pagamento do Azure. Em vez disso, estes itens são faturados separadamente como parte da fatura de utilização excedida do período de faturação padrão.

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

## <a name="next-steps"></a>Passos seguintes

- Os seguintes ficheiros de Excel apresentam detalhes acerca dos serviços do Azure e são atualizados no dia 6 e no dia 20 de cada mês:

   | Título | Descrição | Nome de ficheiro |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Nomes de Serviços Amigáveis) | Lista todos os serviços ativos e inclui: <br>  <ul><li>categoria do serviço</li>   <li>nome de serviço amigável</li>   <li>nome do Pré-pagamento e número de peça</li> <li>nome de consumo e número do mosaico</li>   <li>unidades de medida</li>   <li>fatores de conversão entre a utilização reportada e a utilização apresentada no portal Enterprise</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) (Campos de Transferências de Serviços) | Esta folha de cálculo fornece uma lista de todas as combinações possíveis dos campos relacionados com o serviço no Relatório de Transferência de Utilização. | Service\_Download\_Fields.xlsx |

- Para obter informações sobre como compreender a fatura e os custos, veja [Compreender a fatura do Contrato Enterprise do Azure](../understand/review-enterprise-agreement-bill.md).
- Para começar a utilizar o portal Azure Enterprise, veja [Introdução ao portal Azure EA](ea-portal-get-started.md).
