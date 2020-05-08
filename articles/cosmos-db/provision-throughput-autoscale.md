---
title: Crie recipientes e bases de dados Azure Cosmos em escala automática.
description: Conheça os benefícios, use casos e como fornecer bases de dados e contentores Azure Cosmos em produção de escala automática.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791719"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Crie contentores e bases de dados Azure Cosmos com produção de escala automática

O Azure Cosmos DB permite-lhe configurar os seus recipientes com a produção de entrada ou de escala automática aprovisionada padrão (manual). Este artigo descreve os benefícios e utiliza casos de escala automática.

> [!NOTE]
> Só é possível ativar a [escala automática para novas bases de dados e contentores.](#create-db-container-autoscale) Não está disponível para contentores e bases de dados existentes.

Além do fornecimento padrão de produção, pode agora configurar os contentores Azure Cosmos com a produção aprovisionada em escala automática. Os contentores e bases de dados configurados em escala automática de produção aprovisionada **escalarão automaticamente e instantaneamente a produção aprovisionada com base nas necessidades da sua aplicação sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho a nível global.**

Ao configurar recipientes e bases de dados em escala `Tmax` automática, é necessário especificar que a entrada máxima não deve ser ultrapassada. Os recipientes podem então escalar `0.1*Tmax < T < Tmax`a sua entrada de modo a que . Por outras palavras, os contentores e bases de dados escalam instantaneamente com base nas necessidades de carga de trabalho, desde 10% do valor máximo de entrada que configuraste até ao valor máximo de entrada configurado. Depois de configurar a escala automática,`Tmax`pode alterar a regulação máxima de entrada ( ) numa base de dados ou recipiente em qualquer momento. Com a opção de escala automática, o rendimento mínimo de 400 RU/s por recipiente ou base de dados já não é aplicável.

Para a potência máxima especificada no recipiente ou na base de dados, o sistema permite operar dentro do limite de armazenamento calculado. Se o limite de armazenamento for ultrapassado, a entrada máxima é automaticamente ajustada para um valor mais elevado. Ao utilizar a entrada de nível de base de dados com `0.001*TMax`escala automática, o número de contentores permitidos dentro de uma base de dados é calculado como: . Por exemplo, se fornecer 20.000 RU/s de escala automática, então a base de dados pode ter 20 contentores.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Benefícios da produção de escala automática provisionada

Os recipientes Azure Cosmos que são configurados com escala automática têm os seguintes benefícios:

* **Simples:** Os recipientes com escala automática removem manualmente a complexidade da gestão do transporte (UI) e a capacidade manualmente para vários contentores.

* **Escalável:** Os recipientes com escala automática escalam perfeitamente a capacidade de produção prevista, se necessário. Não há nenhuma perturbação nas ligações com clientes, aplicações e não afetam quaisquer SLAs existentes.

* **Rentável:** Quando utiliza recipientes configurados com escala automática, só paga os recursos de que as suas cargas de trabalho precisam numa base de hora.

* **Altamente disponível:** Os recipientes com escala automática utilizam o mesmo backend distribuído globalmente, tolerante a falhas, altamente disponível para garantir a durabilidade dos dados e a elevada disponibilidade.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Utilizar casos de produção de escala automática aprovisionado

As caixas de utilização para contentores Azure Cosmos configuradas com escala automática incluem:

* **Cargas de trabalho variáveis:** Quando estiver a executar uma aplicação levemente utilizada com o uso máximo de 1 hora a várias horas algumas vezes por dia ou várias vezes por ano. Exemplos incluem aplicações de recursos humanos, orçamentação e relatórios operacionais. Para tais cenários, os recipientes configurados com escala automática podem ser utilizados, e já não é necessário fornecer manualmente para atingir o pico ou a capacidade média.

* **Cargas de trabalho imprevisíveis:** Quando se está a executar cargas de trabalho onde há uso de base de dados ao longo do dia, mas também picos de atividade que são difíceis de prever. Um exemplo inclui um site de tráfego que vê um aumento de atividade quando a previsão meteorológica muda. Os recipientes configurados com escala automática ajustam a capacidade de atender às necessidades da carga máxima da aplicação e voltam a reduzir a escala quando o aumento de atividade terminar.

* **Novas aplicações:** Se estiver a implementar uma nova aplicação e não tiver a certeza de quanto é que o produto forprovisionado (isto é, quantas RUs) precisa. Com recipientes configurados com escala automática, pode automaticamente escalar as necessidades e requisitos de capacidade da sua aplicação.

* **Aplicações pouco utilizadas:** Se tiver uma aplicação que só seja utilizada por algumas horas várias vezes por dia ou semana ou mês, como uma aplicação de baixo volume/web/blog site.

* Bases de dados de **desenvolvimento e de ensaio:** Se tiver desenvolvedores que usam contentores durante o horário de trabalho, mas não precisa deles em noites ou fins de semana. Com os recipientes configurados com escala automática, reduzem-se ao mínimo quando não estão a ser utilizados.

* **Cargas de trabalho/consultas de produção programadas:** Quando você tem uma série de pedidos/operações/consultas programadas em um único recipiente, e se houver períodos ociosos em que você quer correr a uma entrada absolutamente baixa, você pode agora fazê-lo facilmente. Quando uma consulta/pedido programado é submetido a um recipiente configurado com escala automática, ele automaticamente escalará o quanto necessário e executará a operação.

As soluções para os problemas anteriores não só requerem uma enorme quantidade de tempo de implementação, como também introduzem complexidade na configuração ou no seu código, e exigem frequentemente uma intervenção manual para os abordar. A escala automática permite que os cenários acima indicados saem da caixa, para que não precise mais de se preocupar com estes problemas.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Comparação – standard (manual) Vs. autoescala provisionada

|  | Contentores configurados com entrada normalizada  | Recipientes configurados com produção de autoescala provisionada |
|---------|---------|---------|
| **Débito aprovisionado** | Provisões manualmente. | Escalado automaticamente e instantaneamente com base nos padrões de utilização da carga de trabalho. |
| **Limitação da taxa dos pedidos/operações (429)**  | Pode acontecer, se o consumo exceder a capacidade prevista. | Não acontecerá se a entrada consumida estiver dentro da potência máxima que escolher com escala automática.   |
| **Planeamento de capacidade** |  Tem de fazer um planeamento inicial de capacidade e fornecimento da entrada de que necessita. |    Não tens de te preocupar com o planeamento de capacidades. O sistema cuida automaticamente do planeamento de capacidades e da gestão da capacidade. |
| **Preços** | RS/s manualmente aprovisionado por hora. | Para contas de região de escrita única, você paga a entrada usada de hora em hora, utilizando a taxa de RU/s por hora de escala automática. <br/><br/>Para contas com várias regiões de escrita, não há custo extra para a escala automática. Paga-se a entrada utilizada de hora em hora utilizando a mesma taxa de RU/s multi-master por hora. |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Criar uma base de dados ou um recipiente com escala automática

Pode configurar automaticamente para novas bases de dados ou contentores ao criá-los através do portal Azure. Utilize os seguintes passos para criar uma nova base de dados ou recipiente, ativar automaticamente e especificar a potência máxima (RU/s).

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [explorador de DB Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue para a sua conta Azure Cosmos DB e abra o separador **Data Explorer.**

1. Selecione **Novo Recipiente.** Introduza um nome para a sua base de dados, recipiente e uma chave de partição. Em **versão de produção,** selecione a opção **De escala Automática** e escolha a entrada máxima (RU/s) que a base de dados ou o recipiente não podem exceder ao utilizar a opção de escala automática.

   ![Criação de um recipiente e configuração de entrada em escala automática](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Selecione **OK**.

Pode criar uma base de dados de entrada partilhada com escala automática selecionando a opção de entrada de base de **dados Provision.**

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limites de produção e armazenamento para a escala automática

O quadro seguinte mostra os limites máximos de armazenamento e de armazenamento para diferentes opções em escala automática:

|Limite máximo de entrada  |Limite máximo de armazenamento  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Passos seguintes

* Reveja o [FAQ](autoscale-faq.md)de escala automática .
* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
