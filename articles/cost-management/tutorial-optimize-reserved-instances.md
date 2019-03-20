---
title: Tutorial – Otimizar os custos das instâncias reservadas com a Cloudyn no Azure | Microsoft Docs
description: Neste tutorial, irá aprender a otimizar os custos de instâncias reservadas do Azure e Amazon Web Services (AWS).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: a9c6e99d3eff14c364d5eba3e6256382f9df6bc4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182715"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Tutorial: Otimizar as instâncias reservadas

Neste tutorial, vai aprender como a Cloudyn pode ajudar a otimizar os custos e a utilização das instâncias reservadas do Azure e do Amazon Web Services (AWS). Uma instância reservada com um dos fornecedores de serviços cloud é um compromisso para um contrato a longo prazo, em que há um compromisso prévio para a utilização futura da VM. Além disso, pode oferecer potencialmente poupanças consideráveis, em comparação com o modelo de preços de VM de pagamento por utilização padrão. As poupanças potenciais só são conseguidas quando utiliza totalmente a capacidade das suas instâncias reservadas.

Este tutorial explica como as Instâncias Reservadas (RIs) do Azure e do AWS são suportadas pela Cloudyn. Também descreve como pode otimizar os custos de instâncias reservadas. Principalmente, ao garantir que as reservas são utilizadas totalmente. Neste tutorial, irá:

> [!div class="checklist"]
> * Compreender os custos de RIs do Azure
> * Obter mais informações sobre as vantagens das RIs
> * Otimizar os custos de RIs do Azure
> * Ver os custos de RIs
> * Avaliar a eficácia dos custos de RI do Azure
> * Otimizar os custos de RIs do AWS
> * Comprar RIs recomendadas
> * Modificar as reservas não utilizadas

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma conta do Azure.
- Tem de ter um registo de avaliação ou uma subscrição paga da Cloudyn.
- Tem de ter comprado RIs do Azure ou AWS.

## <a name="understand-azure-ri-costs"></a>Compreender os custos de RIs do Azure

Quando compra Azure Reserved VM Instances, paga previamente pela utilização futura. O pagamento prévio abrange o custo da utilização futura das VMs:

- de um tipo específico
- numa região específica
- por um período de um ou três anos
- até uma quantidade de VMs compradas.

Pode ver as Azure Reserved VM Instances compradas no portal do Azure, em [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

O termo _Instância de Máquina Virtual Reservada do Azure_ só se aplica a um modelo de preços. Não altera de todo as suas VMs em execução. O termo é específico do Azure e é, geralmente, mais referido como _instância reservada_ ou _reserva_. As instâncias reservadas que comprou não se aplicam a VMs específicas - estas são aplicadas a qualquer VM correspondente. Por exemplo, uma reserva de um tipo de VM que é executada numa região que escolheu para a sua reserva comprada.

As instâncias reservadas compradas aplicam-se apenas ao hardware básico. Não abrangem as licenças de software de uma VM. Por exemplo, poderá reservar uma instância e ter uma VM correspondente a executar o Windows. A instância reservada abrange apenas o custo base da VM. Neste exemplo, paga o preço integral de quaisquer licenças do Windows necessárias. Para obter um desconto do sistema operativo ou outro software em execução nas suas VMs, deve considerar a utilização dos [Benefícios Híbridos do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Os Benefícios Híbridos oferecem um tipo semelhante de desconto para as licenças de software, como as instâncias reservadas o fazem para as VMs de base.

A utilização de Instâncias Reservadas não afeta diretamente o custo. Por outras palavras, executar uma VM a 100% de utilização da CPU ou a 0% utilização da CPU tem o mesmo efeito: está a pagar previamente pela alocação da VM — não é a utilização real.

Vamos ver como a utilização da VM a pedido está relacionada com os custos em relação a instâncias reservadas, na imagem seguinte:

![Custos a pedido em comparação com os custos de instâncias reservadas](./media/tutorial-optimize-reserved-instances/azure01.png)



As barras vermelhas mostram o custo acumulado da compra da instância reservada. Paga apenas a tarifa única. A utilização da VM é gratuita. As barras azuis mostram o custo acumulado da mesma VM em execução com o modelo de preços pay as you go ou a pedido. Algures entre os sétimo e oitavo meses da utilização da VM, há um *ponto de rentabilidade*. A partir do oitavo mês começa a poupar dinheiro, neste exemplo.

## <a name="benefits-of-ris"></a>Benefícios das RIs

Cada compra de instância reservada aplica-se a uma VM de um tamanho e localização específicos. Por exemplo, D2s\_v3 em execução na localização EUA Oeste, conforme mostrado na imagem seguinte:

![Detalhes de instância reservada do Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

A compra da instância reservada torna-se vantajosa quando uma VM é executada um número suficiente de horas para alcançar o ponto de rentabilidade da reserva. A VM tem de corresponder ao tamanho e à localização da sua instância reservada. Por exemplo, o ponto de rentabilidade é, aproximadamente, no sétimo mês e meio no gráfico anterior. Por isso, a compra é vantajosa quando a VM que corresponde à reserva é executada, pelo menos, 7.5 meses \* 30 dias \* 24 horas = 5400 horas. Se a VM correspondente estiver em execução menos de 5400 horas, a reserva é mais dispendiosa do que no modelo pay as you go.

O ponto de rentabilidade poderá ser diferente para cada tamanho e localização da VM. Também depende do preço do modelo pay as you go de VM negociado. Antes de efetuar uma compra, deve verificar o ponto de rentabilidade aplicável ao seu caso.

Outro ponto a ter em consideração ao comprar a reserva é o âmbito da instância reservada. O âmbito determina se o benefício da reserva é partilhado ou se é aplicável a uma subscrição específica. As instâncias reservadas partilhadas são aplicadas aleatoriamente a todas as subscrições, a todas as primeiras VMs correspondentes encontradas.

O âmbito partilhado de compra é mais flexível e recomenda-se sempre que possível. As possibilidades de utilização de todas as instâncias reservadas são significativamente mais elevadas com o âmbito partilhado. No entanto, quando o proprietário de uma subscrição paga pela instância reservada, pode não ter outra opção a não ser comprá-la com o âmbito de Subscrição Individual.

## <a name="optimize-azure-ri-costs"></a>Otimizar os custos de RIs do Azure

A Cloudyn suporta instâncias reservadas e Benefícios Híbridos, ao:

- Mostrar os custos associados a modelos de preços
- Controlar a utilização de RIs
- Avaliar o impacto de RIs
- Alocar custos de RIs, de acordo com as políticas

A primeira ação que deve tomar antes de comprar uma instância reservada consiste em avaliar o impacto da compra da RI:

- Quanto irá custar?
- Quanto irá economizar?
- Qual é o ponto de rentabilidade?

O relatório de Impacto de Compra de Instância Reservada pode ajudar a responder a estas questões.

## <a name="assess-azure-ri-cost-effectiveness"></a>Avaliar a eficácia dos custos de RI do Azure

No portal do Cloudyn, navegue para **Otimizador** > **Comparação de RIs** e, em seguida, selecione **Impacto de Compra de Instância Reservada**.

No relatório de Impacto de Compra de Instância Reservada, selecione um tamanho de VM (Tipo de Instância), Localização (Região), o período de reserva, a quantidade e o tempo de execução esperado. Em seguida, pode avaliar se irá poupar dinheiro com a compra.

Por exemplo, se comprar uma reserva de uma VM do tipo DS1\_v2 nos EUA Leste e a mesma for executada ininterruptamente durante um ano inteiro, então poderia poupar $369,48 anualmente. O ponto de rentabilidade situa-se nos cinco meses. Veja a imagem seguinte:

![Ponto de rentabilidade de instância reservada do Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

No entanto, se for executada apenas 50% do tempo, o ponto de rentabilidade situar-se-á nos 10 meses e a poupança será apenas de $49,74 anualmente. Poderá não beneficiar ao comprar a reserva desse tipo de instância neste exemplo. Veja a imagem seguinte:

![Exemplo do ponto de rentabilidade para VMs do Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Ver os custos de RIs

Quando compra uma reserva, efetua um pagamento único. Existem duas formas de ver o pagamento na Cloudyn:

- Custo Real
- Custo Amortizado

### <a name="actual-reserved-instance-cost"></a>Custo real de instância reservada

Os relatórios de Análise de Custo Real e Análise ao Longo do Tempo mostram o montante total pago pela reserva, a partir do mês de compra. Ajudam a ver os gastos reais durante um período.

Navegue para **Custos** > **Análise de Custo** > no portal do Cloudyn e, em seguida, selecione **Análise de Custo Real** ou **Custo Efetivo ao Longo do Tempo**. A seguir, defina os filtros. Por exemplo, pode filtrar apenas o serviço e o grupo de VMs do Azure por Tipo de Recurso e Modelo de Preço. Veja a imagem seguinte:

![Exemplo do custo real de instâncias reservadas](./media/tutorial-optimize-reserved-instances/azure05.png)

Pode filtrar por um serviço, **VM do Azure** neste exemplo e agrupar por **Modelo de Preço** e **Tipo de Recurso**, conforme mostrado na imagem seguinte:

![Exemplo de grupos de relatório de custo real e filtros, agrupados por tipo de modelo e o recurso de preço](./media/tutorial-optimize-reserved-instances/azure06.png)

Também pode analisar o tipo de pagamentos que fez, como tarifas únicas, tarifas de utilização e tarifas de licença.

### <a name="amortized-reserved-instance-cost"></a>Custo amortizado de instância reservada

Paga uma tarifa prévia que é visível no mês de compra ao adquirir uma RI. Não é visível nas faturas subsequentes. Por isso, observar a sua utilização mensal poderá ser enganador. Na realidade, o mês custa-lhe a utilização mensal mais a parte proporcional (amortizada) de quaisquer tarifas únicas anteriores. O relatório de Custo Amortizado pode ajudá-lo a obter a perceção real.

O custo amortizado de instância reservada é calculado ao utilizar a tarifa única de reserva e ao amortizá-la ao longo do período de reserva. Nos relatórios de Custo Real, as tarifas únicas são visíveis no mês de compra da reserva. Os gastos diários e mensais não são apresentados no custo real da implementação. Os relatórios de Custo Amortizado mostram o custo real da implementação ao longo do tempo.  O relatório de custo amortizado é a única forma de ver as tendências de custo verdadeiras. Também é a única forma de projetar as despesas futuras.

No relatório de Custo Real, viu um pico da compra de uma RI em 16 de novembro, de $747. No relatório de Custo Amortizado (ver a imagem seguinte), há um custo de dia parcial em 16 de novembro. A partir de 17 de novembro, vê o custo amortizado da RI, de $747/365 = $2,05. Poderá também constatar que a reserva adquirida é utilizada, pelo que pode otimizá-la, mudando-a para um tamanho de VM diferente.

Para vê-la, navegue até **Custos** > **Análise de Custo** > e, em seguida, selecione **Análise de Custo Amortizado** ou **Custo Amortizado ao Longo do Tempo**.

![Relatório de exemplo que mostra amortizado reservado custo de instância](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Otimizar os custos de RIs do AWS

As instâncias reservadas são um compromisso aberto. São úteis quando existe uma utilização constante das VMs, uma vez que as instâncias reservadas são menos dispendiosas do que as instâncias a pedido. No entanto, têm de ser suficientemente utilizadas. O compromisso é a utilização de recursos, normalmente VMs, durante um período de tempo definido — um ou três anos. Ao assumir o compromisso de comprar, paga previamente pelos recursos com uma reserva. No entanto, poderá não utilizar sempre totalmente o que assumiu na reserva.

Por exemplo, pode avaliar o seu ambiente e determinar que teve 20 instâncias D2 padrão em execução constante durante o último ano. Pode comprar uma reserva para as mesmas e, potencialmente, poupar bastante dinheiro. Num exemplo diferente, poderá ter-se comprometido a utilizar dez instâncias MA4 durante o ano. Mas poderá só ter utilizado cinco até agora. Os dois exemplos ilustram uma utilização de RI ineficiente. Existem duas formas de otimizar os custos das instâncias reservadas, com os relatórios de otimização do Cloudyn:

- Rever as recomendações de compra para o que pode comprar com base no histórico de utilização
- Modificar as reservas não utilizadas

Utilizes os relatórios de _Recomendações de Compra de RI de EC2_ e _Reservas Atualmente Não Utilizadas de EC2_ para melhorar a utilização e os custos de instâncias reservadas.

## <a name="buy-recommended-ris"></a>Comprar RIs recomendadas

O Cloudyn compara a utilização de instâncias a pedido e compara-as com instâncias reservadas potenciais. Quando encontra poupanças possíveis, as respetivas recomendações são mostradas no relatório de Recomendações de Compra de EC2.

No menu de relatórios na parte superior do portal, clique em **Otimizador** > **Otimização de Preços** > **Recomendações de Compra de RI de EC2**.

A imagem seguinte mostra as recomendações de compra do relatório.

![Mostrar de exemplo, as recomendações do relatório de recomendações de compra de EC2 de compra](./media/tutorial-optimize-reserved-instances/aws01.png)

Neste exemplo, a conta Cloudyn\_A tem 32 recomendações de compra de instâncias reservadas. Se seguir as recomendações de compra, pode poupar potencialmente até $137.770 anualmente. Tenha em atenção que as recomendações de compra fornecidas pelo Cloudyn partem do princípio de que a utilização de cargas de trabalho em execução irá permanecer consistente.

Para ver detalhes que explicam por que motivo cada compra é recomendada, clique o símbolo de adição ( **+** ) em **Justificações** . Eis um exemplo para a primeira recomendação na lista.

![Detalhes de justificação de compra do exemplo que mostra](./media/tutorial-optimize-reserved-instances/aws02.png)

O exemplo anterior mostra que executar a carga de trabalho a pedido iria custar $90.456 anualmente. No entanto, se comprasse a reserva seguinte com antecedência, a mesma carga de trabalho custaria $56.592 de custos e pouparia $33.864 anualmente.

Clique no símbolo de adição junto a **Impacto da Compra de RI de EC2** para ver o seu ponto de rentabilidade ao longo de um ano, aproximadamente, quando o seu investimento de compra é realizado. Cerca de oito meses após efetuar a compra, o custo acumulado a pedido começa a exceder o custo acumulado de RI no exemplo seguinte:

![Detalhes de impacto de compra do exemplo que mostra](./media/tutorial-optimize-reserved-instances/aws03.png)

Começa a poupar dinheiro nesse momento.

Pode rever as **Instâncias ao Longo do Tempo** para verificar a exatidão da recomendação de compra sugerida. Neste exemplo, pode ver que seis instâncias eram utilizadas em média para a carga de trabalho durante o último período de 30 dias.

![Exemplo que mostra o histórico de utilização de instâncias ao longo do tempo](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Modificar as reservas não utilizadas

As reservas não utilizadas são comuns em muitos ambientes informáticos de consumidores de recursos da cloud. Garantir que as reservas não utilizadas são totalmente utilizadas pode significar uma poupança de dinheiro quando modificar as reservas para irem de encontro às suas necessidades atuais. Por exemplo, poderá ter uma subscrição que contém instâncias D3 padrão em execução no Linux. Se não utilizar totalmente a reserva, pode alterar o tipo de instância. Em alternativa, pode mover os recursos não utilizados para uma reserva diferente ou para uma conta diferente.

O AWS vende instâncias reservadas para zonas de disponibilidade e regiões específicas. Se tiver comprado instâncias reservadas para uma zona de disponibilidade específica, não é possível mover as reservas entre zonas. No entanto, é possível mover facilmente instâncias reservadas regionais entre zonas, utilizando o relatório de **Reservas Atualmente Não Utilizadas de EC2**. Ou, em alternativa, pode modificá-las para que tenham um âmbito regional e, então, elas irão aplicar instâncias correspondentes em todas as zonas de disponibilidade.

No menu de relatórios na parte superior do portal, clique em **Otimizador** > **Ineficiências** > **Reservas Atualmente Não Utilizadas de EC2**.

As imagens seguintes mostram o relatório com as instâncias reservadas não utilizadas.

![Informações sobre as reservas não utilizadas de resumido que mostra de exemplo](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Clique no símbolo de adição em **Detalhes** para ver os detalhes de uma reserva específica.

![Exemplo que mostra os detalhes de reservas não utilizadas](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

No exemplo anterior, existe um total de 77 reservas não utilizadas em várias zonas de disponibilidade. A primeira reserva tem 51 instâncias não utilizadas. Ao procurar mais abaixo na lista, existem modificações potenciais de instâncias de reserva que pode fazer, utilizando o tipo de instância **m3.2xlarge** na zona de disponibilidade **us-east-1c**.

Clique em **Modificar** para que a primeira reserva na lista abra a página **Modificar RI**, que mostra os dados da reserva.

![Exemplo que mostra as reservas que pode ser modificado](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

São apresentadas as instâncias de reserva que pode modificar. Na imagem de exemplo seguinte, existem 51 reservas não utilizadas que pode modificar, mas são necessárias 54 entre as duas reservas. Se modificar as reservas não utilizadas para utilizá-las todas, quatro instâncias irão continuar a ser executadas a pedido. Neste exemplo, divida as reservas não utilizadas, em que a primeira reserva irá utilizar 30 e a segunda reserva irá utilizar 21.

Clique no símbolo de adição da primeira entrada de reserva e defina a **Quantidade de reserva** para **30**. Para a segunda entrada, defina a quantidade de reserva para **21** e, em seguida, clique em **Aplicar**.

![Exemplo que mostra as alterações para a quantidade de reserva](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Todas as instâncias não utilizadas para a reserva são utilizadas totalmente, e 51 instâncias deixam de estar em execução a pedido. Neste exemplo, pode poupar bastante dinheiro à sua organização, ao reduzir a utilização a pedido e ao utilizar as reservas que já foram pagas.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> * Compreendeu os custos de RIs do Azure
> * Obteve mais informações sobre as vantagens das RIs
> * Otimizou os custos de RIs do Azure
> * Viu os custos de RIs
> * Avaliou a eficácia de custos de RIs do Azure
> * Custos de RIs do AWS otimizados
> * Comprou RIs recomendadas
> * Modificou as reservas não utilizadas


Avance para o próximo tutorial para saber mais sobre como controlar o acesso a dados.

> [!div class="nextstepaction"]
> [Control access to data](tutorial-user-access.md) (Controlar o acesso a dados)
