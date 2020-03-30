---
title: Crie recipientes e bases de dados Azure Cosmos em modo piloto automático.
description: Conheça os benefícios, use casos e como fornecer bases de dados e contentores da Azure Cosmos em modo piloto automático.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246659"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Criar contentores e bases de dados do Azure Cosmos no modo Autopilot (Pré-visualização)

O Azure Cosmos DB permite-lhe aprovisionar o débito nos contentores no modo manual ou Autopilot. Este artigo descreve os benefícios e casos de utilização do modo Autopilot.

> [!NOTE]
> O modo piloto automático está atualmente disponível em pré-visualização pública. Só pode [ativar o piloto automático para novas bases de dados e contentores.](#create-a-database-or-a-container-with-autopilot-mode) Não está disponível para contentores e bases de dados existentes.

Além do fornecimento manual de entrada, pode agora configurar os contentores Azure Cosmos em modo piloto automático. Os contentores e bases de dados configurados no modo piloto automático **irão automaticamente e instantaneamente escalar a produção aprovisionada com base nas necessidades da sua aplicação sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho a nível global.**

Ao configurar recipientes e bases de dados no modo `Tmax` piloto automático, é necessário especificar que a potência máxima não deve ser ultrapassada. Os recipientes podem então escalar `0.1*Tmax < T < Tmax`a sua entrada de modo a que . Por outras palavras, os contentores e bases de dados escalam instantaneamente com base nas necessidades de carga de trabalho, desde 10% do valor máximo de entrada que configuraste até ao valor máximo de entrada configurado. Pode alterar a regulação`Tmax`máxima de entrada numa base de dados ou recipiente de piloto automático a qualquer momento. Com a opção de piloto automático, o 400 RU/s de entrada mínima por recipiente ou base de dados já não é aplicável.

Durante a pré-visualização do piloto automático, para a potência máxima especificada no recipiente ou na base de dados, o sistema permite operar dentro do limite de armazenamento calculado. Se o limite de armazenamento for ultrapassado, a entrada máxima é automaticamente ajustada para um valor mais elevado. Ao utilizar a entrada de nível de base de dados com `0.001*TMax`o modo piloto automático, o número de contentores permitidos dentro de uma base de dados é calculado como: . Por exemplo, se fornecer 20.000 RU/s piloto automático, então a base de dados pode ter 20 contentores.

## <a name="benefits-of-autopilot-mode"></a>Benefícios do modo piloto automático

Os recipientes Azure Cosmos que estão configurados em modo piloto automático têm os seguintes benefícios:

* **Simples:** Os recipientes em modo piloto automático removem manualmente a complexidade de gerir a entrada aprovisionada (RUs) e a capacidade manualmente para vários contentores.

* **Escalável:** Os recipientes em modo piloto automático escalam perfeitamente a capacidade de entrada prevista conforme necessário. Não há nenhuma perturbação nas ligações com clientes, aplicações e não afetam quaisquer SLAs existentes.

* **Rentável:** Quando utiliza recipientes configurados em modo piloto automático, paga apenas os recursos de que as suas cargas de trabalho precisam numa base de hora.

* **Altamente disponível:** Os contentores em modo piloto automático utilizam o mesmo backend distribuído globalmente, tolerante a falhas, altamente disponível para garantir a durabilidade dos dados e a elevada disponibilidade.

## <a name="use-cases-of-autopilot-mode"></a>Utilize caixas de modo piloto automático

Os casos de utilização dos contentores Azure Cosmos configurados em modo piloto automático incluem:

* **Cargas de trabalho variáveis:** Quando estiver a executar uma aplicação levemente utilizada com o uso máximo de 1 hora a várias horas algumas vezes por dia ou várias vezes por ano. Exemplos incluem aplicações de recursos humanos, orçamentação e relatórios operacionais. Para tais cenários, os recipientes configurados no modo piloto automático podem ser utilizados e já não é necessário fornecer manualmente para atingir o pico ou a capacidade média.

* **Cargas de trabalho imprevisíveis:** Quando se está a executar cargas de trabalho onde há uso de base de dados ao longo do dia, mas também picos de atividade que são difíceis de prever. Um exemplo inclui um site de tráfego que vê um aumento de atividade quando a previsão meteorológica muda. Os recipientes configurados no modo piloto automático ajustam a capacidade de atender às necessidades da carga máxima da aplicação e voltam a reduzir a escala quando o aumento de atividade terminar.

* **Novas aplicações:** Se estiver a implementar uma nova aplicação e não tiver a certeza de quanto é que o produto forprovisionado (isto é, quantas RUs) precisa. Com os recipientes configurados no modo piloto automático, pode automaticamente escalar as necessidades e requisitos de capacidade da sua aplicação.

* **Aplicações pouco utilizadas:** Se tiver uma aplicação que só seja utilizada por algumas horas várias vezes por dia ou semana ou mês, como uma aplicação de baixo volume/web/blog site.

* Bases de dados de **desenvolvimento e de ensaio:** Se tiver desenvolvedores que usam contentores durante o horário de trabalho, mas não precisa deles em noites ou fins de semana. Com os recipientes configurados no modo piloto automático, reduzem-se ao mínimo quando não estão a ser utilizados.

* **Cargas de trabalho/consultas de produção programadas:** Quando você tem uma série de pedidos/operações/consultas programadas em um único recipiente, e se houver períodos ociosos em que você quer correr a uma entrada absolutamente baixa, você pode agora fazê-lo facilmente. Quando uma consulta/pedido programado é submetido a um recipiente configurado no modo piloto automático, ele automaticamente escalará o quanto necessário e executará a operação.

As soluções para os problemas anteriores não só requerem uma enorme quantidade de tempo de implementação, como também introduzem complexidade na configuração ou no seu código, e exigem frequentemente uma intervenção manual para os abordar. O modo autopiloto permite que os cenários acima indicados saem da caixa, para que não precise mais de se preocupar com estes problemas.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Comparação – Recipientes configurados no modo manual vs. modo piloto automático

|  | Recipientes configurados em modo manual  | Recipientes configurados em modo piloto automático |
|---------|---------|---------|
| **Entrada aprovisionada** | Provisões manualmente. | Escalado automaticamente e instantaneamente com base nos padrões de utilização da carga de trabalho. |
| **Limitação da taxa dos pedidos/operações (429)**  | Pode acontecer, se o consumo exceder a capacidade prevista. | Não acontecerá se a entrada consumida estiver dentro da potência máxima que escolher com o modo piloto automático.   |
| **Planeamento de capacidade** |  Tem de fazer um planeamento inicial de capacidade e fornecimento da entrada de que necessita. |    Não tens de te preocupar com o planeamento de capacidades. O sistema cuida automaticamente do planeamento de capacidades e da gestão da capacidade. |
| **Preços** | RS/s manualmente aprovisionado por hora. | Para contas de região de escrita única, você paga a entrada usada de hora em hora, utilizando a taxa de piloto automático RU/s por hora. <br/><br/>Para contas com várias regiões de escrita, não há custo extra para o piloto automático. Paga-se a entrada utilizada de hora em hora utilizando a mesma taxa de RU/s multi-master por hora. |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Criar uma base de dados ou um recipiente com modo piloto automático

Pode configurar o piloto automático para novas bases de dados ou contentores ao criá-los através do portal Azure. Utilize os seguintes passos para criar uma nova base de dados ou recipiente, ativar o piloto automático e especificar a potência máxima (RU/s).

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [explorador de DB Azure Cosmos.](https://cosmos.azure.com/)

1. Navegue para a sua conta Azure Cosmos DB e abra o separador **Data Explorer.**

1. Selecione **Novo Recipiente.** Introduza um nome para a sua base de dados, recipiente e uma chave de partição. Em **termos de entrada,** selecione a opção **Autopilot** e escolha a potência máxima (RU/s) que a base de dados ou o recipiente não podem exceder ao utilizar a opção de piloto automático.

   ![Criação de um recipiente e configuração da entrada do Piloto Automático](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecione **OK**.

Pode criar uma base de dados de entrada partilhada com modo piloto automático selecionando a opção de entrada de base de **dados Provision.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limites de entrada e armazenamento para piloto automático

A tabela que se segue mostra os limites máximos de armazenamento e de armazenamento para diferentes opções no modo piloto automático:

|Limite máximo de entrada  |Limite máximo de armazenamento  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Passos seguintes

* Reveja o [faq piloto automático](autopilot-faq.md).
* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
