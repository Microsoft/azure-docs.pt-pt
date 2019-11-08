---
title: Crie contêineres e bancos de dados do Azure Cosmos no modo de piloto automático.
description: Saiba mais sobre os benefícios, casos de uso e como provisionar bancos de dados e contêineres do Azure Cosmos no modo de piloto automático.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e0c3c88119c3d064326442881854920b411f5ed4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748386"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Criar contêineres e bancos de dados do Azure Cosmos no modo de piloto automático (versão prévia)

Azure Cosmos DB permite provisionar a taxa de transferência em seus contêineres no modo manual ou automático. Este artigo descreve os benefícios e casos de uso do modo AutoPilot.

> [!NOTE]
> O modo AutoPilot está disponível atualmente na visualização pública. Para habilitar o recurso de piloto automático para sua conta do Azure Cosmos, consulte a seção [habilitar AutoPilot](#enable-autopilot) deste artigo. Você pode habilitar o AutoPilot somente para novos bancos de dados e contêineres, ele não está disponível para contêineres e bancos de dados existentes.

Além do provisionamento manual da taxa de transferência, agora você pode configurar contêineres do cosmos do Azure no modo AutoPilot. Os contêineres de Cosmos do Azure e os bancos de dados configurados no modo de piloto automático irão **dimensionar automaticamente e de forma instantânea a taxa de transferência provisionada com base nas necessidades do seu aplicativo sem comprometer os SLAs.**

Você não precisa mais gerenciar manualmente a taxa de transferência provisionada ou manipular problemas de limitação de taxa. Os contêineres de Cosmos do Azure configurados no modo AutoPilot podem ser redimensionados instantaneamente em resposta à carga de trabalho sem afetar a disponibilidade, a latência, a taxa de transferência ou o desempenho da carga de trabalho globalmente. Sob alta utilização, os contêineres de Cosmos do Azure configurados no modo de piloto automático podem ser escalados ou reduzidos verticalmente sem afetar as operações em andamento.

Ao configurar contêineres e bancos de dados no modo de piloto automático, você precisa especificar que a taxa de transferência máxima não `Tmax` ser excedida. Os contêineres podem ser dimensionados instantaneamente com base nas necessidades de carga de trabalho dentro do intervalo de `0.1*Tmax < T < Tmax`. Em outras palavras, os contêineres e os bancos de dados são dimensionados instantaneamente com base nas necessidades de carga de trabalho, de até 10% do valor máximo de taxa de transferência que você configurou e até o valor máximo de taxa de transferência configurado. Você pode alterar a configuração de taxa de transferência máxima (tmax) no banco de dados ou contêiner do AutoPilot em qualquer momento.

Durante a versão prévia do piloto automático, para a taxa de transferência máxima especificada no contêiner ou no banco de dados, o sistema permite operar dentro do limite de armazenamento calculado. Se o limite de armazenamento for excedido, a taxa de transferência máxima será ajustada automaticamente para um valor mais alto. Ao usar a taxa de transferência no nível do banco de dados com o modo AutoPilot, o número de contêineres permitidos em um banco de dados é calculado como: (0, 1 * taxa de transferência máxima). Por exemplo, se você provisionar 20.000 AutoPilot RU/s, o banco de dados poderá ter 20 contêineres.

## <a name="benefits-of-autopilot-mode"></a>Benefícios do modo AutoPilot

Os contêineres do cosmos do Azure que são configurados no modo AutoPilot têm os seguintes benefícios:

* **Simples:** Os contêineres no modo AutoPilot removem a complexidade para gerenciar a taxa de transferência provisionada (RUs) e a capacidade manualmente para vários contêineres.

* **Escalonável:** Os contêineres no modo AutoPilot dimensionam diretamente a capacidade de taxa de transferência provisionada conforme necessário. Não há interrupção nas conexões de cliente, aplicativos e elas não afetam nenhum SLA existente.

* Econômico **:** Quando você usa contêineres de Cosmos do Azure configurados no modo de piloto automático, você paga apenas pelos recursos de que suas cargas de trabalho precisam por hora.

* **Altamente disponível:** Os contêineres de Cosmos do Azure no modo de AutoPilot usam o mesmo back-end distribuído globalmente, tolerante a falhas e altamente disponível para garantir a durabilidade dos dados e a alta disponibilidade sempre.

## <a name="use-cases-of-autopilot-mode"></a>Casos de uso do modo AutoPilot

Os casos de uso dos contêineres Cosmos do Azure configurados no modo AutoPilot incluem:

* **Cargas de trabalho variáveis:** Quando você estiver executando um aplicativo levemente usado com pico de uso de 1 hora a várias horas por dia, ou várias vezes por ano. Os exemplos incluem aplicativos para recursos humanos, orçamento e relatórios operacionais. Para esses cenários, os contêineres configurados no modo de piloto automático podem ser usados, você não precisa mais provisionar manualmente para a capacidade de pico ou média.

* **Cargas de trabalho imprevisíveis:** Quando você estiver executando cargas de trabalho em que há uso de banco de dados ao longo do dia, mas também aos picos de atividade que são difíceis de prever. Um exemplo inclui um site de tráfego que vê um surto de atividade quando a previsão do tempo muda. Os contêineres configurados no modo de piloto automático ajustam a capacidade para atender às necessidades da carga de pico do aplicativo e de reduzir verticalmente quando o surto da atividade terminar.

* **Novos aplicativos:** Se você estiver implantando um novo aplicativo e não tiver certeza quanto à quantidade de produtividade provisionada (ou seja, quantos RUs) você precisa. Com os contêineres configurados no modo AutoPilot, você pode dimensionar automaticamente para as necessidades de capacidade e os requisitos de seu aplicativo.

* **Aplicativos usados com pouca frequência:** Se você tiver um aplicativo que só é usado por algumas horas várias vezes por dia ou semana ou mês, como um aplicativo de baixo volume/Web/site de blog.

* **Bancos de dados de desenvolvimento e teste:** Os desenvolvedores usam as contas do Azure Cosmos durante as horas de trabalho, mas não precisam delas em noites ou em fins de semana. Com contêineres configurados no modo AutoPilot, eles são reduzidos para o mínimo quando não estão em uso.

* **Cargas de trabalho/consultas de produção agendadas:** Quando você tem uma série de solicitações/operações/consultas agendadas em um único contêiner e, se houver períodos ociosos em que você deseja executar com uma taxa de transferência baixa absoluta, agora você pode fazer isso facilmente. Quando uma consulta/solicitação agendada for enviada para um contêiner configurado no modo de piloto automático, ela aumentará automaticamente o máximo possível e executará a operação.

As soluções para os problemas anteriores não só exigem uma enorme quantidade de tempo na implementação, mas também introduzem complexidade na configuração ou no código, e muitas vezes exigem intervenção manual para solucioná-los. O modo Autopilot permite cenários acima prontos para uso, para que você não precise mais se preocupar com esses problemas.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Comparação – contêineres configurados no modo manual vs. modo AutoPilot

|  | Contêineres configurados no modo manual  | Contêineres configurados no modo AutoPilot |
|---------|---------|---------|
| **Taxa de transferência provisionada** | Provisionado manualmente | Escalado de imediato e instantâneo com base nos padrões de uso da carga de trabalho. |
| **Taxa-limitação de solicitações/operações (429)**  | Pode acontecer se o consumo exceder a capacidade provisionada. | Não ocorrerá se a taxa de transferência consumida estiver dentro da taxa de transferência máxima escolhida com o modo AutoPilot.   |
| **Planeamento de capacidade** |  Você precisa fazer um planejamento de capacidade inicial e provisionar a taxa de transferência de que precisa. |    Você não precisa se preocupar com o planejamento de capacidade. O sistema cuida automaticamente do planejamento de capacidade e do gerenciamento de capacidade. |
| **Preços** | O provisionamento manual de RU/s por hora. | Para contas de região de gravação única, você paga pela taxa de transferência usada por hora, usando o AutoPilot RU/s por hora. <br/><br/>Para contas com várias regiões de gravação, não há nenhum custo adicional para o piloto automático. Você paga pela taxa de transferência usada por hora usando a mesma tarifa de RU/s de vários mestres por hora. |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="a-idenable-autopilot-enable-autopilot-from-azure-portal"></a><a id="enable-autopilot"> habilitar o AutoPilot de portal do Azure

Você pode experimentar o AutoPilot em suas contas do Azure Cosmos habilitando o no portal do Azure. Use as etapas a seguir para habilitar a opção AutoPilot:

1. Entre no [portal do Azure.](https://portal.azure.com)

2. Navegue até sua conta do Azure Cosmos e abra a guia **novos recursos** . Selecione **piloto automático** e **registrar** conforme mostrado na seguinte captura de tela:

![Criar um contêiner no modo AutoPilot](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Criar um banco de dados ou um contêiner com o modo de piloto automático

Você pode configurar o AutoPilot para bancos de dados ou contêineres ao criá-los. Use as etapas a seguir para um novo banco de dados ou contêiner, habilite o AutoPilot e especifique a taxa de transferência máxima.

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Azure Cosmos Explorer.](https://cosmos.azure.com/)

1. Navegue até sua conta do Azure Cosmos e abra a guia **Data Explorer** .

1. Selecione **novo contêiner**, insira um nome para o contêiner, uma chave de partição. Selecione a opção **AutoPilot** e escolha a taxa de transferência máxima que o contêiner não pode exceder ao usar a opção AutoPilot.

   ![Criar um contêiner no modo AutoPilot](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecione **OK**

Com etapas semelhantes, você também pode criar um banco de dados com taxa de transferência provisionada no modo AutoPilot.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [provisionar a taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* Saiba como [provisionar a taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).
