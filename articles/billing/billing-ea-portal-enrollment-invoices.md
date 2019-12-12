---
title: Faturas de inscrição Azure Enterprise
description: Este artigo explica como gerir e interpretar a fatura Azure Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 15c2e84742ff0f552c688ccf1ba795f742e288ca
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849892"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas de inscrição Azure Enterprise

Este artigo explica como gerir e interpretar a fatura Azure Enterprise. A fatura é uma representação da sua conta, pelo que deve analisar a sua precisão. Deve também familiarizar-se com outras tarefas que possam ser necessárias para gerir a fatura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Alterar um número de nota de encomenda de uma fatura de utilização excedida

O Azure EA Portal gera automaticamente um número de nota de encomenda predefinido, a não ser que o administrador EA configure um antes da data da fatura. Um administrador EA pode atualizar o número da nota de encomenda num prazo de sete dias após a receção do e-mail de notificação da fatura.

Um número de Nota de Encomenda também pode ser introduzido para uma conta ou subscrição específica. A predefinição dos relatórios será o número da Nota de Encomenda de nível mais baixo definido na hierarquia, o que significa que se não for introduzido um número da Nota de Encomenda da subscrição, será utilizado o número da Nota de Encomenda da compra e se não for introduzido um número da Nota de Encomenda da compra, será utilizada a Nota de Encomenda do departamento.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Para atualizar o número da nota de encomenda dos serviços do Azure:

1. No Azure EA Portal, clique em **Relatório** e, em seguida, em **Resumo de Utilização**.
1. Selecione **Editar Números de Nota de Encomenda** no canto superior direito.
1. Selecione o botão de opção **Serviços do Azure**.
1. Selecione um **Período de Fatura** no menu pendente de intervalos de datas. Os números das notas de encomenda podem ser editados até sete dias após a notificação da fatura ou até a fatura ser paga, o que ocorrer primeiro.
1. Introduza o novo número da nota de encomenda no campo  **Número da nota de encomenda** .
1. Clique em **Guardar** para submeter a alteração.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Para atualizar o número da nota de encomenda do Marketplace:

1. No Azure EA Portal, clique em **Relatório** e, em seguida, em **Resumo de Utilização**.
1. Selecione **Editar Números de Nota de Encomenda** no canto superior direito
1. Selecione o botão de opção **Marketplace**
1. Selecione um **Período de Fatura** no menu pendente de intervalos de datas. Os números das notas de encomenda podem ser editados até sete dias após a notificação da fatura ou até a fatura ser paga, o que ocorrer primeiro.
1. Introduza o novo número da nota de encomenda no campo  **Número da nota de encomenda** .
1. Clique em **Guardar** para submeter a alteração.

## <a name="cadence-of-azure-ea-billing"></a>Cadência de faturação do Azure EA

### <a name="billing-intervals"></a>Intervalos de Faturação

A faturação por parte da Microsoft será realizada anualmente, na data efetiva da inscrição, altura em que são cobradas todas as compras de alocação dos serviços do Microsoft Azure e os pagamentos em atraso relativos a qualquer utilização que exceda os montantes da alocação. As tarifas de alocação são propostas com base numa taxa mensal e são faturadas anualmente com antecedência. As tarifas de utilização excedida são calculadas mensalmente e faturadas posteriormente no final do período de faturação.

Dependendo de como opta por fazer as suas compras de alocação, a sua fidelização anual coincidirá com a data de aniversário da sua inscrição ou com a data efetiva da sua Subscrição de Emenda de um ano.

Receberá a sua fatura de utilização excedida em função da configuração e da data de início da sua inscrição.

**Inscrições diretas com data de início anterior a 1 de maio de 2018**: Os Clientes Enterprise Azure (EA) diretos são configurados como um período de Faturação Anual para os Serviços do Azure (exceto os serviços do Marketplace). O período de faturação tem por base a data de aniversário. A data de aniversário é a data em que o seu contrato entrou em vigor. Para receber a primeira fatura de Utilização Excedida do Azure, terá de ultrapassar 150% do limiar da sua Alocação Monetária ("MC").  Quando o consumo total de serviços ultrapassar 150% do seu limiar de MC, a sua inscrição será convertida automaticamente para um período de Faturação Trimestral com base na data de aniversário.  Se não ultrapassar 150% do seu limiar de MC, a inscrição permanecerá num período de Faturação Anual e receberá a fatura de Utilização Excedida no fim do ano de Alocação.

**Inscrições diretas com data de início posterior a 1 de maio de 2018**: As Faturas dos Custos Faturados em Separado e do Consumo do Azure do Cliente serão emitidas num período de Faturação Mensal.  Todos os custos não cobertos pela Alocação Monetária do Azure serão devidos como um pagamento de Utilização Excedida.  

**Inscrições indiretas com data de início anterior a 1 de maio de 2018**: Os Clientes Enterprise Azure (EA) indiretos são configurados como um período de Faturação Trimestral.  O Parceiro de Canal (CP) enviará fatura diretamente ao Cliente.  

**Inscrições indiretas com data de início posterior a 1 de maio de 2018**: Qualquer Contrato Indireto com uma data de início posterior ou igual a 1 de maio de 2018 será faturado Mensalmente.  

### <a name="increasing-commitment"></a>Aumento da Alocação

A alocação pode ser aumentada em qualquer altura e será faturada pelo número de meses restantes do período de alocação desse ano. Por exemplo, se se inscrever numa Subscrição de Emenda de um ano e aumentar a alocação durante o mês seis, ser-lhe-ão cobrados os restantes seis meses desse período. As quantidades da alocação serão depois atualizadas durante os últimos seis meses do período de alocação para determinar quaisquer custos de utilização excedida.

### <a name="overage"></a>Utilização Excedente

No caso da utilização excedida, ser-lhe-á cobrada a utilização ou reservas que excederam a sua alocação durante o período de faturação. Para ver uma discriminação do cálculo das quantidades de utilização excedida para itens individuais, consulte o Relatório de Resumo de Utilização Excedida ou contacte o seu parceiro de canal.

Para cada item na fatura, verá o total de custos (Valor Total), o valor de alocação utilizado para cobrir os custos (Alocação Utilizada) e o valor dos custos na alocação em excesso (Valor Líquido).  Os impostos aplicáveis são calculados apenas sobre o valor líquido no excesso da alocação.

A faturação da utilização excedida é automatizada.  O tempo das notificações e das faturas depende da data de fim do período de faturação do cliente.  A notificação de utilização excedida é enviada normalmente sete dias após a data de fim de faturação do cliente, período durante o qual os clientes podem entrar no portal, rever os seus custos e atualizar os números das notas de encomenda geradas pelo sistema (Também podem ser atualizados em qualquer altura antes da emissão da utilização excedida).  As faturas de utilização excedida serão enviadas 7-9 dias depois.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do período de faturação de abril de 2019, os clientes começarão a receber uma única fatura do Azure, uma vez que combinámos todos os custos do Azure e do Azure Marketplace numa única fatura, em vez de duas faturas separadas. (Esta alteração não afeta os clientes na Austrália, Japão ou Singapura.) Durante a transição para uma fatura consolidada, receberá uma fatura parcial do Marketplace. Esta fatura separada final mostrará os custos do Marketplace antes da data de atualização da faturação. Os custos do Marketplace após essa data serão apresentados na fatura do Azure. Após o período de transição, verá todos os custos do Azure e do Marketplace na fatura consolidada.  

### <a name="purchase-order-numbers"></a>Números de Notas de Encomenda

A predefinição da nota de encomenda é um número de Nota de Encomenda gerado pelo sistema. Os utilizadores podem atualizar o número da sua Nota de Encomenda no Enterprise Portal iniciando sessão como Administrador do Enterprise e navegando para a secção Relatórios. Existe uma caixa para o número da nota de encomenda no canto superior direito da janela que permitirá que o Administrador do Enterprise edite o número da nota de encomenda ao clicar no ícone de lápis.

## <a name="adjust-billing-frequency"></a>Ajustar a frequência da faturação

A frequência da faturação de um cliente é anual, trimestral ou mensal. O período de faturação é determinado quando um cliente assina o contrato. A faturação mensal é o intervalo de faturação mais pequeno.

Para inscrições diretas, é necessária a aprovação do administrador do Enterprise para alterar um período de faturação de anual para trimestral. Para inscrições indiretas, é necessária a aprovação de um administrador parceiro. A alteração torna-se efetiva no final do trimestre de faturação a decorrer.

Para alterar um período de faturação anual ou trimestral para mensal, é necessária uma emenda do contrato.  Qualquer alteração ao período de faturação existente requer a aprovação de um administrador do Enterprise ou da pessoa identificada como _Contacto de Faturação_ no contrato. Pode submeter a sua aprovação no site do [Suporte do Azure EA Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) e, em seguida, selecionar a Categoria do Problema **Faturação e Emissão de Faturas**.  A alteração torna-se efetiva no final do trimestre de faturação a decorrer.

Se assinar uma Emenda M503, poderá mover qualquer contrato de qualquer frequência para a faturação mensal. 

## <a name="credits-and-adjustments"></a>Créditos e ajustes

Todos os créditos ou ajustes que se apliquem à sua inscrição estão visíveis em [https://ea.azure.com](https://ea.azure.com) na secção **Relatórios**.

Para ver os créditos, siga estes passos:

1. Selecione a secção **Relatórios**.
1. Clique em **Resumo de Utilização**.
1. No canto superior direito, altere a vista _M_ para _C_.
1. Expanda o campo de ajuste na tabela de alocação de serviços do Azure.
1. Nesta linha, verá os créditos aplicados à sua inscrição, bem como uma breve explicação, por exemplo: Crédito do Contrato de Nível de Serviço.

## <a name="request-an-invoice-copy"></a>Pedir uma cópia da fatura

Para pedir uma cópia da fatura, contacte o seu parceiro.

## <a name="overage-offset-by-customers"></a>Contrapartida de utilização excedida por parte dos clientes

Se o cliente tiver utilizações excedidas que queira utilizar com a alocação monetária, deverão ser cumpridos os seguintes critérios:

- O cliente incorreu em custos de utilização excedida que não foram pagos e que estão incluídos no prazo de um ano a contar da data de fim do serviço faturado.
- O montante da alocação monetária disponível deve cobrir o montante total dos custos incorridos, incluindo todas as faturas do Azure vencidas e por pagar.
- O Período de faturação que está a ser pedido tem de estar totalmente concluído. A faturação ficará concluída por completo após o quinto dia de cada mês.
- O período de faturação que está a ser pedido para compensação deve estar totalmente concluído.
- O desconto ACD baseia-se na nova alocação real menos os fundos planeados para o consumo anterior. Esta condição é válida apenas para os custos de utilização excedida incorridos. Só funciona em serviços que consomem alocação monetária, pelo que não pode cobrir os custos do marketplace. Os custos do marketplace são cobrados separadamente.
- Caso um cliente queira encerrar uma contrapartida de utilização excedida, poderá abrir um pedido de suporte. Em alternativa, a equipa de conta poderá abrir um pedido de suporte. Para concluir o processo, é necessária a aprovação por e-mail de um Administrador EA do Cliente ou do Contacto de Faturação.

## <a name="view-price-sheet-information"></a>Ver as informações da folha de preços

Os administradores do Enterprise podem ver a lista de preços associada à inscrição relativa aos serviços do Azure.

Para Ver a Folha de Preços atual:

1. No Azure EA Portal, clique em **Relatórios** e, em seguida, clique **Folha de Preços**.
2. Veja a folha de preços ou clique em **Transferir**.

![Exemplo que mostra as informações da folha de preços](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Para transferir o histórico de uma lista de preços:

1. No Azure EA Portal, clique em **Relatórios** e, em seguida, em **Transferir utilização**.
2. Transfira a folha de preços.

![Exemplo que mostra onde pode transferir uma folha de preços mais antiga](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Se tiver dúvidas sobre o motivo de existir uma discrepância nos preços, poderá ver a seguir alguns dos motivos:

Os preços podem ter sido alterados entre a inscrição anterior e a nova inscrição. As alterações de preços ocorrem, uma vez que os preços são contratuais para uma inscrição específica, desde a data de início até à data de fim de um contrato. Quando uma inscrição é transferida para uma inscrição nova, segue os preços de um contrato novo. Os preços são definidos pela folha de preços do cliente. Por isso, é possível que os preços sejam mais elevados na nova inscrição.

Se o período de uma inscrição for alargado, os preços também serão alterados. Os preços mudam para tarifas pay as you go.

## <a name="request-detailed-usage-information"></a>Pedir informações de utilização detalhadas

Os administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados a utilização adicional no Azure EA Portal. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada em contas específicas, pode transferir o relatório de Detalhes de Utilização ao navegar para **Relatórios** > **Transferir Utilização**. O relatório não inclui os impostos aplicáveis. Poderá existir uma latência de cerca de oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para as inscrições indiretas, o seu parceiro tem de ativar a função de margem de lucro para que possa ver as informações relacionadas com os custos.

## <a name="reports"></a>Relatórios

Os Administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados a utilização adicional no Enterprise Portal. Os custos serão apresentados ao nível do resumo em todas as contas e subscrições.

**Relatórios do EA**

- Gráficos e resumo de utilização
- Relatório de utilização de serviços
- Relatório de saldo e custo
- Relatório de detalhes de utilização
- Relatórios de custos do Marketplace
- Folha de preços
- Transferência de relatórios avançados
- Formatação de relatórios CSV

**Para ver os gráficos e os relatório de resumo de utilização**

1. No Azure EA Portal, clique em **Relatórios** no painel de navegação à esquerda e veja o separador **Resumo de Utilização**.
1. Selecione o período de alocação pretendido no menu pendente de intervalos de datas no canto superior esquerdo.
1. Selecione o período ou mês pretendido no gráfico para ver detalhes adicionais.
1. Veja o gráfico da utilização de um mês para o outro com uma discriminação da utilização consumida, da sobretaxa de serviços, dos custos cobrados separadamente e dos custos do Marketplace.
1. Para o mês selecionado, filtre por departamentos, contas e subscrições por baixo do grafo.
1. Alterne entre a discriminação de Custo por Serviços e a discriminação de Custo por Hierarquia.
1. Veja os serviços do Azure, os custos cobrados separadamente e os custos do Azure Marketplace em detalhe.

## <a name="service-usage-report"></a>Relatório de utilização de serviços

A página Relatório de Utilização de Serviços permite os administradores do Enterprise vejam um resumo dos dados de utilização dos seus serviços. Embora a utilização seja apresentada ao nível do resumo em todas as contas e subscrições, também pode filtrar o relatório por contas ou subscrições para ver a utilização detalhada.

Note que também poderá existir uma latência de até cinco dias entre a data de utilização incorrida e a data em que essa utilização é refletida neste relatório.

### <a name="to-view-the-report"></a>Para ver o relatório:

1. Inicie sessão no Enterprise Portal.
1. Clique em **Relatórios** no painel de navegação à esquerda.
1. Clique no separador **Resumo de Utilização**.
1. Clique no intervalo de datas pretendido.
1. Escolha as contas ou subscrições que pretende ver.
1. Alterne a vista entre Custo por Serviços e Custo por Hierarquia para apresentar discriminações diferentes.
1. Veja detalhes, incluindo Nome do Serviço, Unidade de Medida, Unidades Consumidas, Taxa Efetiva e Valor Total.

## <a name="download-csv-reports"></a>Transferir relatórios CSV

A página Transferência de Relatório Mensal permite que os administradores do Enterprise transfiram vários relatórios como ficheiros .csv, incluindo o Relatório de Saldo e Custo, o Relatório de Detalhes de Utilização, o Relatório de Custos do Marketplace e a Folha de Preços.

## <a name="to-download-reports"></a>Para transferir os relatórios:

1. No Azure EA portal, clique em **Relatório**.
1. Selecione **Transferir Relatório de Utilização** no friso superior.
1. Selecione **Transferir** ao lado do relatório do mês adequado.



## <a name="csv-report-formatting"></a>Formatação de relatórios CSV

Os clientes que visualizam os relatórios CSV do Azure EA Portal em euros podem deparar-se com problemas de formatação em relação a vírgulas e pontos.

Por exemplo, podem ver:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Deverá ver:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Causa raiz

O Excel importa todos os campos como texto "Geral" e assume que um número é separado no padrão matemático: "1,000.00".  Assim, uma moeda europeia que utilize um ponto (.) para o separador de milhares e uma vírgula para o separador decimal (,) – "1.000,00" será apresentado incorretamente. Isto pode variar dependendo da configuração de idioma regional.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Ao importar o CSV, execute os seguintes passos:

1.    Abra o Excel e vá para Ficheiro > Abrir.
1.    O Assistente de Importação de Texto é apresentado.
1.    Em Tipo de Dados Original, escolha _delimitado_.  A predefinição é _Largura Fixa_.
1.    Clique em **Seguinte**.
1.    Em Delimitadores, selecione a caixa de verificação de Vírgula. A predefinição é "Tabulação" (desmarque).
1.    Clique em **Seguinte**.
1.    Desloque o cursor para as colunas "ResourceRate" e "ExtendedCost".
1.    Selecione a coluna "ResourceRate" (aparecerá realçada a preto).
9.    Na secção Formato dos Dados da Coluna, selecione "Texto" em vez de "Geral".  Verá que o cabeçalho da coluna muda de "Geral" para "Texto".
10. Repita os passos 8 e 9 para a coluna "ExtendedCost". Selecione **Concluir**.

Se tiver o Excel configurado para abrir automaticamente os ficheiros \*.csv, terá de utilizar a função "Abrir" no Excel em alternativa. Abra o Excel e vá para Ficheiro > Abrir.

## <a name="balance-and-charge-report"></a>Relatório de saldo e custo

O Relatório de Saldo e Custo oferece um resumo mensal de informações sobre saldos, novas compras, custos de serviço do Azure Marketplace, ajustes e custos de utilização excedida. Todas as linhas na tabela de resumo da Alocação de Serviços do Azure permanecerão estáticas de um mês para o outro, enquanto os detalhes de todas as compras e ajustes serão apresentados nas secções Detalhes da Nova Compra e Detalhes de Ajuste.

### <a name="download-the-balance-and-charge-report"></a>Transferir o relatório de saldo e custo

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Clique em **Relatórios** no painel de navegação à esquerda.
1. Clique no separador **Transferência de Relatórios**.
1. Selecione o mês apropriado na coluna _Saldo e Custo_ e clique para transferir o relatório.

## <a name="usage-detail-report"></a>Relatório de detalhes de utilização

O Relatório de Detalhes de Utilização oferece um resumo mensal dos serviços e das quantidades que estão a ser consumidas por uma inscrição, incluindo informações sobre nomes de medidores, tipos de medidor e quantidades consumidas.

### <a name="download-the-usage-detail-report"></a>Transferir o relatório de detalhes de utilização

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Clique em **Relatórios** no painel de navegação à esquerda.
1. Clique no separador **Transferir Utilização**.
1. Selecione o mês apropriado na coluna _Detalhes de Utilização_ e clique para transferir o relatório.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Custos do Marketplace nos relatórios do Azure EA Portal

Pode encontrar [aqui](https://azure.microsoft.com/marketplace/faq/) informações adicionais sobre os custos do Marketplace.

Há dois tipos de custos do Marketplace:

- **Com base na utilização:** Produtos medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas à hora, as pesquisas de API do Bing são cobradas por número de pesquisas.
- **Tarifa Mensal:** Cobrada mensalmente com base na utilização/acesso.

Para ver os diferentes custos no Enterprise Portal:

1. **Relatório de Resumo de Utilização –** Mostra **ambos** os custos do Marketplace: com base na utilização e tarifa mensal.
1. **Relatório de Custos do Marketplace –** Mostra **apenas** os custos do Marketplace com base na utilização.  As taxas únicas não são apresentadas.

O Azure Marketplace não está disponível para inscrições MPSA.

## <a name="advanced-report-download"></a>Transferência de relatórios avançados

Para relatórios em intervalos de datas ou contas específicas, pode utilizar a transferência de relatórios avançados. A partir de 30 de agosto de 2016, o formato do ficheiro de saída foi alterado de .xlsx para .csv, de forma a acomodar conjuntos de registos maiores.

1. Selecione **Transferência de Relatórios Avançados**.
1. Selecione o **Intervalo de Datas Adequado**.
1. Selecione as **Contas Adequadas**.
1. Selecione **Pedir Dados de Utilização**.
1. Selecione o botão **Atualizar** até que o estado do relatório atualize para "Transferir".
1. Transfira o relatório.

## <a name="reporting-for-non-enterprise-administrators"></a>Relatórios para administradores não Enterprise

Os Administradores do Enterprise podem ativar o acesso de visualização de custos para os Administradores de Departamento (DA) e os Proprietários de Contas (AO) numa inscrição. Depois de ativado, um proprietário de conta pode transferir os relatórios .csv específicos da sua conta e subscrições, e ver as informações na secção de relatórios do Enterprise Portal.

### <a name="to-enable-access"></a>Para ativar o acesso:

 1. Inicie sessão como administrador do Enterprise.
 1. Clique em **Gerir** no painel de navegação à esquerda.
 1. Clique no separador **Inscrição**.
 1. Na seção Detalhes da Inscrição, selecione o ícone de lápis ao lado de:
    - Os Administradores de Departamento podem ver os custos
    - Os Proprietários de Contas podem ver os custos
 1. Selecione **Ativado**.
 1. Clique em **Guardar**.

### <a name="to-view-reports"></a>Para ver relatórios:

1. Inicie sessão no Azure EA Portal como administrador de departamento ou proprietário de conta.
1. Clique em **Relatórios** no painel de navegação à esquerda.
1. Clique no separador **Resumo de Utilização** para ver informações sobre a conta e subscrição.
1. Clique em **Transferir Relatório de Utilização** para ver os relatórios .csv.

Os administradores de departamento e os proprietários de contas não conseguem ver os custos quando utilizam a função _Transferência de Relatórios Avançados_. Os custos são apresentados como 0 $.

A opção Os Proprietários de Contas podem ver os custos estende-se aos proprietários de contas e a todos os utilizadores que têm permissões em subscrições associadas. Se for um cliente indireto, as funcionalidades dos custos têm de ser ativadas pelo parceiro de canal.

## <a name="move-usage-data-to-another-enrollment"></a>Mover os dados de utilização para outra inscrição

Os dados de utilização só são movidos quando uma transferência tem efeitos retroativos. Existem duas opções para mover os dados de utilização de uma inscrição para outra:

- Transferências de contas de uma inscrição para outra
- Transferências de inscrições de uma inscrição para outra

Para qualquer uma das opções, tem de submeter um [pedido de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipa de Suporte EA para obter ajuda. 

## <a name="azure-ea-pricing-overview"></a>Descrição geral dos preços do Azure EA

Este documento fornece detalhes sobre como a utilização é calculada e responde a muitas das perguntas mais frequentes sobre os preços de vários serviços do Azure em Programas Enterprise onde os cálculos são mais complexos.

### <a name="price-protection"></a>Proteção de preço

Os clientes e parceiros de canal (no caso dos nossos canais indiretos) têm a garantia de receber preços iguais ou inferiores aos indicados na respetiva Folha de Preços do Cliente (CPS) ou o preço em vigor na data efetiva da sua compra do Azure. Este preço é designado por preço de base. Para serviços introduzidos após essa compra, é o preço em vigor no desconto de nível aplicável quando o serviço foi introduzido pela primeira vez. Esta proteção de preço aplica-se durante o período de alocação que pode ser um ou três anos, dependendo do Programa Enterprise.

### <a name="price-changes"></a>Alterações de preço

A Microsoft pode baixar o preço atual do Programa Enterprise para serviços individuais do Azure durante o período de uma inscrição.  Alargaremos estas taxas reduzidas aos clientes existentes, enquanto o preço mais baixo estiver em vigor.  Se estas taxas forem posteriormente aumentadas, o preço da inscrição num serviço não aumentará além do teto de proteção de preço do cliente, conforme definido acima, mas poderá aumentar em relação ao preço reduzido anterior.  Em qualquer dos casos, a Microsoft informará os clientes e os parceiros de canal indiretos de alterações de preço futuras através do envio de um e-mail aos administradores Enterprise e parceiros associados à inscrição.

### <a name="current-effective-pricing"></a>Preços efetivos atuais

Os clientes e os parceiros de canal podem ver os preços atuais de uma inscrição iniciando sessão no [Azure Enterprise Portal](https://ea.azure.com/) e navegando até à página da folha de preços relativa a essa inscrição.  Se comprar o Azure indiretamente através de um dos nossos parceiros de canal, precisará de obter as atualizações de preços junto do seu parceiro de canal, caso este não tenha ativado a apresentação de preços de margem de lucro para a sua inscrição.

### <a name="introduction-of-new-azure-services"></a>Introdução de novos serviços do Azure

Estamos continuamente a melhorar o Azure e adicionamos periodicamente novos serviços cujos preços são calculados separadamente dos serviços existentes.  Alguns serviços em pré-visualização estão disponíveis automaticamente, enquanto outros exigem a ação do cliente através do [Portal do Azure](https://account.windowsazure.com/PreviewFeatures). Note também que, à medida que os serviços passam de Pré-visualização para Disponibilidade Geral, os preços podem aumentar, uma vez que são aplicados SLAs de desempenho e fiabilidade totais. Por último, alguns serviços são disponibilizados com preços Promocionais em vigor quando introduzidos pela primeira vez, que podem ser aumentados numa data futura. Qualquer aumento deste tipo ao passar de preços de Pré-visualização ou Promocionais para Disponibilidade Geral não é limitado pela proteção normal de preço de base e aplicar-se-á à utilização desses serviços daí para a frente. Os clientes podem evitar os custos relacionados com estes serviços, optando por não utilizar o novo serviço.

### <a name="introduction-of-regional-pricing"></a>Introdução de preços regionais

Além da introdução de novos serviços, os serviços também mudam periodicamente de uma base Global única para um modelo mais Regional, uma vez que o suporte regional para esses serviços aumenta. Quando a regionalização de um serviço é introduzida pela primeira vez, a proteção de preço aplica-se a essas novas regiões em comparação com o preço Global anterior em vigor para a inscrição. No entanto, as regiões adicionais introduzidas posteriormente para esse mesmo serviço são consideradas novos serviços e são oferecidas com as suas próprias tarifas individuais, independentemente da proteção de preço de base.

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN Programador/Teste

Os Administradores do Enterprise podem permitir que os Proprietários de Contas criem subscrições com base na oferta de EA MSDN Programador/Teste. Para que isto funcione corretamente, o Proprietário da Conta terá de configurar as subscrições de EA MSDN Programador/Teste necessárias para os subscritores subjacentes do MSDN. Tal permite que os subscritores ativos do MSDN executem cargas de trabalho de desenvolvimento e teste no Azure a tarifas especiais de dev/test. Para obter mais informações, veja [Enterprise Programador/Teste](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Diretrizes de cálculo de utilização do programa Enterprise

Consulte os [Serviços do Azure](https://azure.microsoft.com/services/) e [Preços do Azure](https://azure.microsoft.com/pricing/) para obter informações de preços públicos básicos, unidades de medida, FAQs e diretrizes de relatórios de utilização para cada serviço individual. Além disso, para calcular a utilização do serviço, devem ser utilizadas as seguintes diretrizes do Programa Enterprise.

### <a name="enterprise-program-units-of-measure"></a>Unidades de medida do programa Enterprise

As unidades de medida para os Programas Enterprise são muitas vezes diferentes do que em outros programas nossos, como o programa do Contrato de Subscrição Online da Microsoft (MOSA). Isto significa que, para vários serviços, a unidade de medida é agregada para fornecer os preços normalizados. A unidade de medida apresentada na vista Resumo de Utilização do Enterprise Portal é sempre a medida Enterprise. Uma lista completa das atuais unidades de medida e conversões de cada serviço é disponibilizada em [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Nomes de Serviços Amigáveis).

### <a name="rounding-rules"></a>Regras de arredondamento

O arredondamento feito no Enterprise Portal utiliza a lógica de Arredondamento do Banqueiro padrão do IEEE ou Arredondamento de Gauss. Esta lógica arredonda o número para o dígito par mais próximo para valores de metade de dígito, em que as funções de arredondamento Arredondar Metade por Excesso mais comuns arredondam sempre a metade dos dígitos por excesso para o próximo dígito mais alto. Na verdade, este método oferece uma soma total mais precisa sobre o grupo em comparação com a lógica padrão do Excel.

Quando o primeiro dígito eliminado é 5 e não há dígitos a seguir ou os dígitos a seguir são zeros, torna o dígito anterior par (ou seja, arredonda para o dígito par mais próximo). Por exemplo, 2,315 e 2,325 são ambos 2,32 quando arredondados para o 100º mais próximo.

Para referência, ao utilizar o Excel para modelar as regras de arredondamento e conversão utilizadas no Enterprise Portal, as fórmulas MARRED devem ser utilizadas conforme indicado abaixo.

| Cenário | Fórmula de Lógica do Banqueiro |
| --- | --- |
| Arredondar Utilização | =MARRED({_source_}, 0,0002) |
| Arredondar Preços (2 casas decimais) | =MARRED({_source_}, 0,02) |
| Arredondar Preços (0 casas decimais) | =MARRED({_source_}, 2) |

**Tabela**  **2**  **– Conversão de Horas de Serviços Cloud e Máquinas Virtuais**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Conversão entre o Ficheiro de Utilização de Transferências e a Vista do Portal de Resumo de Utilização

Os dados não processados de utilização de recursos são reportados até um máximo de seis casas decimais, como podem ser vistos no relatório de dados de utilização de transferências. No entanto, o Azure Enterprise Portal arredonda a utilização para quatro casas decimais para unidades de alocação e trunca para zero casas decimais para unidades de utilização excedida. A Utilização Não Processada é primeiro arredondada para quatro dígitos antes da conversão para a unidade de medida Enterprise e as Unidades Enterprise resultantes são arredondadas novamente para quatro dígitos. As horas reais consumidas antes da conversão são apresentadas apenas no relatório de utilização de transferências e não na própria IU.

A título de exemplo, suponha que 694,533404 horas reais de SQL Server são reportadas no Relatório de Utilização de Transferências. Estas unidades são, em seguida, convertidas para 6,94533404 unidades de 100 Horas de Computação que, em seguida, são arredondadas para 6,9453 para serem apresentadas no Enterprise Portal.

Em seguida, estas unidades são simplesmente multiplicadas pelo Preço de Unidade de Alocação e o resultado é truncado para duas casas decimais para determinar o valor total de faturação. No caso do Iene japonês (JPY) e do Won coreano (KRW), o valor total é arredondado para zero casas decimais.

Para este mesmo exemplo em utilização excedida, as unidades faturáveis seriam truncadas para seis e, em seguida, multiplicadas pelo Preço de Unidade de Utilização Excedida para determinar o valor total de faturação. Para a faturação do Fornecedor de Serviços Geridos (MSP), toda a utilização associada a um Departamento marcado como MSP é truncada para zero casas decimais após a conversão para a unidade de medida do EA, tal como acontece para o relatório de utilização excedida. Como resultado, a soma desta utilização pode ser inferior à soma total de toda a utilização reportada na IU, dependendo de o MSP ainda estar dentro do saldo da alocação monetária ou já estar em utilização excedida.

### <a name="graduated-pricing"></a>Preços graduados

Atualmente, os preços do Programa Enterprise não incluem preços graduados (em que os preços por unidade diminuem à medida que a utilização aumenta). Ao mudar de um programa MOSA com preços graduados para um Programa Enterprise, os preços são definidos proporcionalmente ao escalão base para esse serviço, após o ajuste dos descontos do Programa Enterprise, se aplicável.

### <a name="partial-hour-billing"></a>Faturação por hora parcial

Toda a utilização é faturada com base em minutos convertidos para horas parciais em vez de incrementos de hora inteira.  As taxas Enterprise listadas como por hora são simplesmente aplicadas ao total de horas, incluindo quaisquer horas parciais.

### <a name="average-daily-consumption"></a>Consumo diário médio

Quando um serviço é cobrado mensalmente, mas a utilização é reportada diariamente, esta utilização é avaliada uma vez por dia, dividida por 31 e somada pelo número de dias nesse mês de faturação. Isto resulta em taxas que nunca são superiores ao esperado para qualquer mês e são ligeiramente mais baixas para os meses que têm menos de 31 dias.

### <a name="compute-hours-conversion"></a>Conversão de horas de computação

Anteriormente, toda a utilização de Serviços Cloud e de Máquinas Virtuais Básicas e Standard A0, A2, A3 e A4 era emitida em frações (para A0) ou múltiplos (para A2, A3 e A4) de minutos de medidores de Máquinas Virtuais A1. Esta forma causava alguma confusão nos nossos clientes e, por isso, estamos a implementar uma alteração para atribuir a utilização por minuto a medidores de A0, A2, A3 e A4 dedicados.

A nova medição entrará em vigor entre 27 de Janeiro e 28 de janeiro de 2016. Durante este período de transição, na transferência do .csv para a sua implementação, irá reparar em dois itens de linha: Um para a utilização emitida no medidor A1 e outra para a utilização emitida no novo medidor dedicado correspondente ao tamanho da sua implementação.

| **Tamanho de implementação** | **Utilização emitida como múltiplo de A1 até 26 de janeiro de 2016** | **Utilização emitida no medidor dedicado a partir de 27 de janeiro de 2016** |
| --- | --- | --- |
| A0 | 0,25 de hora A1 | 1 de hora A0 |
| A2 | 2 de hora A1 | 1 de hora A2 |
| A3 | 4 de hora A1 | 1 de hora A3 |
| A4 | 8 de hora A1 | 1 de hora A4 |

### <a name="regions"></a>Regiões

Para os serviços em que a zona e a região afetam os preços, a tabela seguinte mostra o mapeamento para geografias e regiões:

| Área Geográfica | Regiões de Transferência de Dados | Regiões de CDN |
| --- | --- | --- |
| Zona 1 | Norte da Europa <br> Europa Ocidental <br> E.U.A. Oeste <br> E.U.A Leste <br> EUA Centro-Norte <br> E.U.A. Centro-Sul <br> Este dos EUA 2 <br> Centro dos EUA | América do Norte <br> Europa |
| Zona 2 | Este da Ásia-Pacífico <br> Sudeste da Ásia-Pacífico <br> Leste do Japão <br> Oeste do Japão <br> Leste da Austrália <br> Sudeste da Austrália | Ásia-Pacífico <br> Japão <br> América Latina <br> Médio Oriente/África <br> Leste da Austrália <br> Sudeste da Austrália |
| Zona 3 | Sul do Brasil |   |

**Tabela**  **4**  **– Regiões de Transferência de Dados**

Não há custos para a saída de dados entre serviços (por exemplo, O365 e Azure) alojados no mesmo datacenter.

### <a name="monetary-commitment-and-unbilled-usage"></a>Alocação monetária e utilização não faturada

A alocação monetária do Azure é um montante pago antecipadamente pelos serviços do Azure. A alocação monetária é consumida à medida que os serviços são utilizados. Os serviços originais do Azure utilizam a alocação monetária. Contudo, existem exceções para os custos faturados separadamente e para os serviços do marketplace.

### <a name="charges-billed-separately"></a>Custos faturados em separado

Alguns produtos e serviços fornecidos por origens de terceiros não consomem a alocação monetária do Azure. Em vez disso, estes itens serão faturados separadamente como parte da fatura de utilização excedida do período de faturação padrão.

Combinámos todos os custos do Azure e do Marketplace numa única fatura que está em linha com o período de faturação da inscrição. (Não se aplica a clientes na Austrália, Japão ou Singapura.)

A fatura consolidada mostrará primeiro a utilização do Azure, seguida dos custos do Marketplace. Os clientes na Austrália, no Japão ou em Singapura continuarão a ver os custos do Marketplace numa fatura separada.

Se não existir utilização excedida no fim do período de faturação padrão, os custos cobrados em separado serão faturados em separado. (Aplicável a clientes na Austrália, no Japão e em Singapura)

**Os serviços cobrados separadamente incluem:**

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

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para os clientes diretos, os custos do Marketplace estão visíveis no Azure EA Portal. As compras e o consumo do Marketplace serão cobrados fora da alocação monetária e serão cobrados trimestralmente/mensalmente após o consumo efetivo.

Os clientes indiretos podem encontrar as suas subscrições do Azure Marketplace na página "Gerir Subscrições" do Azure EA Portal, mas os preços estarão ocultos. Os clientes devem entrar em contacto com o seu LSP para obter informações sobre os custos do Marketplace.

As novas compras mensais ou anuais recorrentes do Marketplace serão cobradas pelo valor total no período em que os itens do Marketplace foram comprados. Estes itens serão renovados automaticamente no período seguinte, no mesmo dia da compra original.

Isto não afetará as cobranças recorrentes existentes do Marketplace. As cobranças mensais recorrentes continuarão a ser renovadas no primeiro dia de cada mês de calendário e as cobranças anuais serão renovadas no aniversário da data de compra.

Alguns serviços de revenda de terceiros disponíveis no Azure Marketplace consomem agora o seu saldo de alocação monetária do Contrato Enterprise (EA). Anteriormente, estes serviços eram cobrados à margem da alocação monetária do EA e faturados separadamente. A alocação monetária do EA para estes serviços no Marketplace ajuda a simplificar a gestão das compras e dos pagamentos dos clientes. Para obter uma lista completa dos serviços que agora consomem a alocação monetária, veja o [site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Parceiros

Os LSPs podem transferir uma lista de preços específica do Marketplace, navegando até à folha de preços no Azure EA Portal e clicando na ligação da **Lista de Preços do Marketplace** no canto superior direito. A lista de preços do Marketplace será um reflexo de todos os serviços disponíveis e respetivos preços.

**Para transferir a lista de preços, siga os passos seguintes:**

1. Navegue até Relatórios > Folha de Preços.
1. No canto superior direito, localize a ligação para a lista de preços do Azure Marketplace debaixo do seu nome de utilizador.
1. Clique com o botão direito do rato na ligação e selecione **Guardar Destino Como**.
1. Na janela Guardar, altere o título do documento para _AzureMarketplacePricelist.zip_, o que irá alterar o ficheiro de um xlsx para um ficheiro zip.
1. Quando a transferência estiver concluída, terá um ficheiro zip com as listas de preços específicas do país.
1. Os LSPs devem consultar o ficheiro do país específico para verem os preços específicos do país. Os LSPs devem utilizar o separador "Notificações" para verem os SKUs que são novos no Marketplace, bem como os SKUs que foram removidos.
1. As alterações de preço não serão frequentes, mas quando ocorrerem, os LSPs serão notificados por e-mail em relação a aumentos de preço e a alterações de FX com 30 dias de antecedência.
1. Os LSPs receberão uma fatura por inscrição, por ISV, por trimestre.

### <a name="enabling-marketplace-purchases"></a>Ativar compras no Marketplace

Os administradores do Enterprise têm a capacidade de "desativar" ou "ativar" compras no Marketplace para todas as subscrições do Azure nessa inscrição. Se o administrador do Enterprise desativar as compras e existirem subscrições do Azure que já têm subscrições do Marketplace, essas subscrições do Marketplace não serão canceladas nem afetadas.

Embora os clientes possam converter as suas subscrições diretas do Azure em EA associando-as à respetiva inscrição no Azure EA Portal, esta ação não converte automaticamente as subscrições subordinadas do Marketplace.

**Para ativar compras no Marketplace:**

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Navegue até _Gerir_.
1. Em _Detalhes da Inscrição_, clique no ícone de lápis ao lado do item de linha _Azure Marketplace_.
1. Alterne entre _Ativado/Desativado_ ou _Apenas SKUs gratuitos/BYOL\*_ , conforme apropriado.
1. Clique em **Guardar**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Custos do Marketplace nos relatórios do Azure EA Portal

Pode encontrar [aqui](https://azure.microsoft.com/marketplace/faq/) informações adicionais sobre os custos do Marketplace.

Há dois tipos de custos do Marketplace:

- **Baseados na utilização:** Os produtos são medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas à hora, as pesquisas de API do Bing são cobradas pelo número de pesquisas.
- **Não baseados na utilização:** Custo único ou tarifa mensal recorrente que é independente da utilização.

Tanto os custos baseados na utilização como os custos não baseados na utilização serão captados no relatório de custos do Marketplace.

Note que o Azure Marketplace não está disponível para inscrições MPSA.

\*BYOL (traga a sua própria licença) e a opção Apenas Gratuitos irá limitar a compra e aquisição de SKUs do Azure Marketplace para BYOL e Apenas SKUs Gratuitos.

### <a name="services-billed-hourly-for-ea"></a>Serviços cobrados à hora para o EA

A Rede de Entrega de Aplicações e a Firewall de Aplicações Web são cobradas à hora para o EA em oposição à tarifa mensal no MOSP.

### <a name="remote-app"></a>Aplicação remota

Os clientes EA pagam pela aplicação remota com base no seu nível de preço EA e não é cobrado qualquer custo adicional. O nível de preço padrão inclui 40 horas iniciais, enquanto o nível de preço ilimitado cobre 80 horas iniciais. A Aplicação Remota deixa de emitir a utilização superior a 80 horas.

## <a name="azure-marketplace-faq"></a>FAQ do Azure Marketplace

Este documento FAQ analisa as atualizações à aplicabilidade da alocação monetária do Azure a alguns serviços publicados por terceiros no Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>O que estamos a alterar em relação aos serviços do Marketplace e à alocação monetária do Azure?

Além das instâncias de VM reservadas (RIs) do Azure, os clientes recebem uma fatura separada para todos os serviços comprados no Azure Marketplace. Estamos a alargar a utilização da alocação monetária do Azure para incluir agora alguns dos serviços do Azure Marketplace publicados por terceiros, que são comprados com mais frequência pelos nossos clientes.

### <a name="why-are-we-making-this-change"></a>Por que razão estamos a fazer esta alteração?

Os clientes procuram continuamente novas formas de tirar partido do pagamento adiantado que fizeram na forma de alocação monetária do Azure.  Estamos a responder a um pedido frequente dos clientes e a afetar uma grande parte dos nossos clientes do Azure Marketplace ao alargarmos a alocação monetária a estes serviços.

### <a name="what-is-the-customer-benefit"></a>Qual é o benefício para o cliente?

Os clientes obtêm uma experiência de faturação mais simples e a possibilidade de garantir que gastam a sua alocação monetária do Azure.  A adição deste benefício à alocação monetária pré-paga e às RIs com a alocação monetária traz ainda mais valor para a alocação monetária do Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Que serviços serão deduzidos da alocação monetária do Azure e como é que o meu cliente saberá?

Durante a experiência de compra no Azure Marketplace, distinguiremos cada serviço que irá utilizar a alocação monetária com um texto de exclusão de responsabilidade. Os editores atualmente suportados incluem determinados serviços publicados pela Red Hat, SUSE, Autodesk e Oracle. Os serviços que têm convenções de nomenclatura semelhantes, mas que são publicados por outras entidades não identificadas acima, não serão deduzidos da alocação monetária. No fim desta FAQ, está disponível uma lista completa.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>E se o meu cliente ficar sem alocação monetária?

No caso dos clientes que consumiram a totalidade da alocação monetária e estão agora em utilização excedida, os custos relacionados com estes serviços aparecerão na próxima fatura de utilização excedida, juntamente com quaisquer outros serviços de consumo.  Isto é uma alteração, já que anteriormente este custos seriam faturados numa fatura própria com outras ofertas do Azure Marketplace.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Por que não ativamos a alocação monetária do Azure para todas as compras no Marketplace?

Trabalhamos frequentemente para proporcionar a melhor experiência para o cliente em relação à alocação monetária do Azure. Esta alteração aplicar-se-á a um grande número de clientes e a uma parte significativa do total de gastos no Azure Marketplace. No futuro, podem ser adicionados outros serviços.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>De que forma é que isto afeta os parceiros e clientes de inscrição indireta?

Não há qualquer impacto para os nossos parceiros ou clientes de inscrição indireta. Estes serviços estão sujeitos às mesmas margens de lucro de parceiros do que outros serviços de consumo. A única alteração será a fatura na qual aparecem e o pagamento dos custos a partir da Alocação Monetária.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Lista de serviços que serão deduzidos da alocação monetária do Azure

As ofertas específicas do Azure Marketplace podem utilizar fundos de alocação monetária do Azure. Veja o artigo sobre a [alocação monetária do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa dos produtos que participam neste programa.

## <a name="additional-information"></a>Informações adicionais

Veja estas fontes adicionais de informações para obter mais detalhes. Estes ficheiros são atualizados duas vezes por mês, no dia 6 e no dia 20 de cada mês. Os detalhes de cada ficheiro são os seguintes:

| Título do Apêndice | Descrição | Convenção de Nomenclatura de URL |
| --- | --- | --- |
| [**Friendly Service Names**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Fornece uma lista de todos os serviços ativos com a categoria de serviço, nome de serviço amigável, nome da alocação e número de peça, nome de consumo e número de peça, unidades de medida e fatores de conversão entre a utilização reportada e a utilização apresentada no Enterprise Portal. | Friendly\_Service\_Names.xlsx |
| [**Service Download Fields**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Esta folha de cálculo fornece uma lista de todas as combinações possíveis dos campos relacionados com o serviço no Relatório de Transferência de Utilização. | Service\_Download\_Fields.xlsx |

**Table**  **5**  **– Fontes de Informação Adicionais**

## <a name="power-bi-reporting"></a>Relatórios do Power BI

### <a name="power-bi-pro"></a>Power BI Pro

O Power BI Pro já está disponível para clientes EA. Com o Power BI Pro, pode gerar e partilhar relatórios para gerir de forma eficaz os seus dados de custos, com funcionalidades adicionais de atualização de dados e colaboração. O Power BI Pro oferece limites superiores de capacidade de dados e transmissão de dados. Em breve, estarão disponíveis novas e empolgantes funcionalidades de gestão de custos para clientes do Azure Enterprise.

Os atuais utilizadores do Power BI Gratuito que utilizam o pacote de conteúdos Microsoft Azure Consumption Insights são elegíveis para uma avaliação gratuita de 60 dias do Power BI Pro. Se quiser continuar a utilizar o Power BI Pro após a avaliação gratuita, pode fazê-lo com a adição de uma licença.

Para se inscrever na avaliação gratuita, vá para o ícone de engrenagem e selecione **Gerir armazenamento pessoal**. Em seguida, selecione **Experimentar o Pro gratuitamente** à direita. Veja [Inscrição self-service do Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) para obter mais informações sobre a avaliação gratuita do Power BI Pro.

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Termos de avaliação do Microsoft Azure EA Power BI Pro

- **Fins gerais**: O Power BI Pro alargado para a oferta de avaliação do pacote de conteúdos "Microsoft Azure Enterprise" (a "Oferta") está disponível para os atuais utilizadores qualificados durante o período de vigência da Oferta, para permitir que acedam a determinadas informações relacionadas com o seu consumo do Microsoft Azure através da utilização de um pacote de conteúdos do Power BI específico.
- **Elegibilidade**: Os utilizadores com um Contrato Enterprise (EA) podem participar na Oferta se tiverem uma função relacionada com a faturação, o serviço, a gestão de serviços e/ou custos do Microsoft Azure da sua organização.
- **Exclusões**:
  - Os utilizadores que já participam na avaliação prolongada do Power BI Pro continuarão a ser elegíveis no âmbito da oferta pré-existente e não podem participar na oferta de avaliação do Azure EA Power BI Pro.
  - Os utilizadores que participam na oferta só podem utilizar o Power BI Pro com o pacote de conteúdos do Microsoft Azure Enterprise. É proibida qualquer outra utilização do Power BI Pro.
  - Período de vigência: A Oferta terá início a 1 de junho de 2017 e termina a 31 de maio de 2018.  A aceitação pode ocorrer em qualquer altura durante o período de 12 meses, embora a oferta termine a 31 de maio de 2018 para todos os utilizadores, independentemente da data em que aceitaram a Oferta.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Para aceder ao pacote de conteúdos do Microsoft Azure Consumption Insights:

1. Navegue até [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Clique em **Obter Agora**.
1. Indique o Número de Inscrição e o Número de Meses. Clique em **Seguinte**.
1. Forneça a sua chave de acesso da API para ligar. Pode encontrar a chave relativa à sua inscrição no [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Clique em **Iniciar sessão**.
1. O processo de importação começará automaticamente. Quando estiver concluído, será apresentado um novo dashboard, relatório e modelo no painel de navegação. Clique no dashboard para ver os dados importados.

Para obter mais informações sobre como gerar a chave de API para a sua inscrição, visite o ficheiro de ajuda API Reports no [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Para obter mais informações sobre o novo pacote de conteúdos, transfira o documento [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Para aceder ao pacote de conteúdos do Power BI EA legado:

 1. Navegue até ao [site do Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Inicie sessão com uma conta escolar ou profissional válida.
    - A conta escolar ou profissional pode ser a mesma ou diferente da que é utilizada para aceder à inscrição através do Azure EA Portal.
 1. No dashboard de serviços, selecione **Microsoft Azure Enterprise** e clique em **Ligar**.
 1. No ecrã “Ligar ao Azure Enterprise”, escolha:
    - URL do Ambiente do Azure: [https://ea.azure.com](https://ea.azure.com/).
    - Número de Meses: escolha entre 1 e 36.
    - Número da Inscrição: introduza o número da inscrição.
    - Clique em **Seguinte**.
 1. Na Caixa da Chave de Autenticação, introduza a Chave de API. Pode obter a chave de API aqui no Azure EA Portal, no separador “Transferir Utilização” acima e clicar em “Chave de Acesso da API”.
    - Copie e cole a chave na caixa “Chave da Conta”.
 1. O carregamento dos dados no Power BI demora cerca de 5 a 30 minutos, dependendo do tamanho dos conjuntos de dados.

Os relatórios do Power BI estão disponíveis para clientes EA diretos, parceiros e indiretos que tenham acesso para ver as informações de faturação.

## <a name="report-faq"></a>FAQ sobre Relatórios

Esta seção do artigo responde a perguntas comuns relacionadas com a interpretação de relatórios.

### <a name="why-is-my-cost-showing-as-0"></a>Por que é que o meu custo é apresentado como 0 $?

**Inscrição direta** Se for proprietário de conta ou administrador de departamento, contacte o seu administrador do EA para ativar a funcionalidade de preços:

1. Clique em **Gerir** no painel de navegação à esquerda.
1. Clique no lápis azul junto a Os administradores de departamento podem ver os custos.
1. Selecione **Ativado** e guarde.
1. Clique no lápis azul junto a Os proprietários de contas podem ver os custos.
1. Selecione **Ativado** e guarde.

Esta ação dará aos proprietários de contas e administradores de departamento acesso a informações sobre custos/preços nos relatórios de utilização.

**Inscrição indireta** Verifique junto do seu parceiro se este ativou a funcionalidade de preços para si. Esta ação só pode ser realizada pelo parceiro e, depois de este ter ativado a funcionalidade, poderá ver o custo e os preços na sua inscrição como administrador do EA.

Se pretender ativar a funcionalidade de visualização de custos para os seus proprietários de contas e administradores de departamento, siga os passos indicados acima em **Inscrição Direta**.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Não há informação do SKU no relatório de detalhes de utilização

O relatório de detalhes de utilização não tem informações sobre o SKU. No entanto, poderá ver as informações sobre serviço utilizadas no relatório. Em seguida, pode transferir o relatório de folha de preços para obter as informações do SKU.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>O valor total no Marketplace não corresponde ao resumo de utilização e ao relatório CSV

O Relatório de Custos do Marketplace mostra apenas os custos do Marketplace com base na utilização. As taxas únicas não são apresentadas. Pode consultar a página de resumo de utilização para ver as utilizações mais atualizadas em termos de custos com base na utilização e taxas únicas.

### <a name="there-is-no-information-on-my-api-report"></a>Não há informações no meu relatório de API

As chaves de API expiram a cada seis meses. Gere uma nova chave de API se estiver a deparar-se com um problema. Também é importante pedir que o administrador do EA gere as novas chaves de API e seguir os passos na FAQ sobre o Relatório de API.

### <a name="my-power-bi-report-isnt-working"></a>O meu relatório do Power BI não está a funcionar

Para problemas relacionados com o Power BI, registe um pedido de suporte técnico junto da equipa do Power BI em [https://support.powerbi.com](https://support.powerbi.com), para que a equipa o possa ajudar.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>A minhas etiquetas de recursos não estão a aparecer nos meus relatórios

As etiquetas de recursos são geridas no portal do Azure. Pode contactar a equipa de Subscrição do Azure em [https://portal.azure.com](https://portal.azure.com). Siga os passos indicados [nesta ligação](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) que explicam como emitir um pedido de suporte.

### <a name="why-does-my-resource-rate-change-every-day"></a>Por que é que a minha taxa de recursos muda todos os dias?

A Taxa de Recursos no relatório de utilização detalhada é um valor calculado e representa a taxa mensal média cobrada por um serviço. Esta taxa é calculada com a média da alocação mensal e dos custos mensais de utilização excedida para uma unidade de serviço. A parte da utilização cobrada na alocação e nas taxas de utilização excedida será alterada para o dia em que o mês termina. Por isso, a taxa de recursos também será alterada durante o mês. A taxa de recursos é bloqueada no quinto dia após o fim do mês.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossário de processos para calcular a taxa de recursos

**Total de Unidades RAW:** Quantidade Consumida no relatório de utilização detalhada.
**Recurso de MOCP Por Unidade:** O sistema de utilização de origem emite as utilizações de cada serviço em unidades diferentes. (Configuração predefinida) **Consumo Por Unidade:** Unidade de Medida EA. (Configuração predefinida) **Preço:** Preço unitário do Azure EA Portal.
**Custo Total:** Custo total do relatório de utilização detalhada ou utilização da alocação + utilização excedida do Azure EA Portal.


### <a name="charges-calculation"></a>Cálculo dos custos

**Conversão em Recurso MOCP por unidade** = ROUND(Total de Unidades RAW * Recurso MOCP Por Unidade,4) **Conversão em unidades** = Unidades após conversão em Recurso MOCP por Unidade / Consumo por Unidade **Custo total** = Unidades * Preço

### <a name="download-usage-calculation-logic"></a>Lógica de Cálculo de Utilização de Transferências

**Taxa de Recursos** = Custo Total /(Total de Unidades RAW / Recurso MOCP Por Unidade)

A taxa de recursos é derivada com base nos custos e geralmente não corresponde ao preço unitário real na folha de preços.

Para os cálculos de custos de utilização excedida, os dados não processados de utilização de recursos são reportados até um máximo de seis casas decimais, como podem ser vistos no relatório de dados de utilização de transferências. No entanto, o Azure EA Portal arredonda a utilização para quatro casas decimais para unidades de alocação e trunca para zero casas decimais para unidades de utilização excedida. Isso significa que, no Azure EA Portal para toda a utilização cobrada como utilização excedida, cobramos apenas as unidades completas. Existirá uma grande diferença observada entre o preço unitário e a taxa de recursos para utilização cobrada como utilização excedida ou em meses mistos.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre como compreender a fatura e os custos, veja [Compreender a fatura do Contrato Enterprise do Azure](billing-understand-your-bill-ea.md).
- Para começar a utilizar o Azure EA Portal, veja [Get started with the Azure EA portal](billing-ea-portal-get-started.md) (Introdução ao Azure EA Portal).
