---
title: Gerir feeds de dados em Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Saiba como gerir os feeds de dados que adicionou ao Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184727"
---
# <a name="how-to-manage-your-data-feeds"></a>Como: Gerir os feeds de dados

Saiba como gerir os seus feeds de dados a bordo no Metrics Advisor. Este artigo guia-o através da gestão de feeds de dados no Metrics Advisor.

## <a name="edit-a-data-feed"></a>Editar um feed de dados

> [!NOTE]
> Os seguintes detalhes não podem ser alterados após a criação de um feed de dados. 
> * ID de feed de dados
> * Hora de Criação
> * Dimensão
> * Tipo de Fonte
> * Granularidade

Apenas o administrador de um feed de dados é autorizado a eseuar alterações no mesmo. 

Para parar ou reativar um feed de dados:

1. Na página da lista de feed de dados, clique na operação que pretende realizar no feed de dados.

2. Na página de dados, clique no botão **''' ''' ''' ''' ''' ''' '''**

Para eliminar um feed de dados: 

1. Na página da lista de feed de dados, clique em **Eliminar** no feed de dados.

2. Na página de dados de feed details, clique em **Eliminar**.

Ao alterar a hora de início, tem de verificar novamente o esquema. Pode alterá-lo utilizando **parâmetros de edição.**

##  <a name="backfill-your-data-feed"></a>Reencha o feed de dados

Selecione o botão  **Backfill** para ativar uma ingestão imediata num carimbo de tempo, para corrigir uma ingestão falhada ou sobrepor os dados existentes.
- A hora de início é inclusiva.
- O fim de hora é exclusivo.
- A deteção de anomalias é re-activada apenas no intervalo selecionado.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Feed de dados de enchimento de reenchimento":::

## <a name="manage-permission-of-a-data-feed"></a>Gerir a permissão de um feed de dados

O acesso ao espaço de trabalho é controlado pelo recurso Metrics Advisor, que utiliza o Azure Ative Directory para autenticação. Outra camada de controlo de permissão é aplicada a dados métricos.

O Metrics Advisor permite-lhe conceder permissões a diferentes grupos de pessoas em diferentes feeds de dados. Existem dois tipos de funções: 

- Administrador: Quem tem permissões completas para gerir um feed de dados, incluindo modificar e eliminar.
- Espectador: Quem tem acesso a uma visão apenas de leitura do feed de dados.
 

## <a name="advanced-settings"></a>Definições avançadas

Existem várias configurações avançadas opcionais ao criar um novo feed de dados, que podem ser modificados na página de detalhes do feed de dados.

### <a name="ingestion-options"></a>Opções de ingestão

* **Compensação do tempo de ingestão**: Por padrão, os dados são ingeridos de acordo com a granularidade especificada. Por exemplo, uma métrica com um tempotando *diário* será ingerida um dia após a sua hora de tempo. Pode utilizar o offset para atrasar o tempo de ingestão com um número *positivo,* ou adianta-lo com um número *negativo.*

* **Conurreência máxima**: Desconfiem este parâmetro se a sua fonte de dados suportar uma concordância limitada. Caso contrário, deixe na definição predefinida.

* **Pare de voltar a tentar depois:** Se a ingestão de dados tiver falhado, voltará a tentar automaticamente dentro de um período de um período. O início do período é o momento em que a primeira ingestão de dados aconteceu. A duração do período é definida de acordo com a granularidade. Se deixar o valor padrão (-1), o valor será determinado de acordo com a granularidade como abaixo.
    
    | Granularidade       | Pare de tentar depois           |
    | :------------ | :--------------- |
    | Diariamente, Personalizado (>= 1 dia), Semanal, Mensal, Anual     | 7 dias          |
    | Hora, Costume (< 1 dia)       | 72 horas |

* **Intervalo de retenção min**: Pode especificar o intervalo mínimo ao tentar novamente retirar dados da fonte. Se deixar o valor predefinido (-1), o intervalo de repetição será determinado de acordo com a granularidade como abaixo.
    
    | Granularidade       | Intervalo mínimo de retíria           |
    | :------------ | :--------------- |
    | Diariamente, Personalizado (>= 1 dia), semanal, mensal     | 30 minutos          |
    | Hora, Costume (< 1 dia)      | 10 minutos |
    | Anual | 1 dia          |
 
### <a name="fill-gap-when-detecting"></a>Preencher lacuna ao detetar: 

> [!NOTE]
> Esta definição não afetará a sua fonte de dados e não afetará os gráficos de dados apresentados no portal. O preenchimento automático só ocorre durante a deteção de anomalias.

Algumas séries de tempo não são contínuas. Quando faltam pontos de dados, o Metrics Advisor utilizará o valor especificado para os preencher antes da deteção de anomalias para obter uma maior precisão.
As opções são: 

* Utilizando o valor do ponto de dados anterior. Isto é usado por defeito.
* Usando um valor específico.

### <a name="action-link-template"></a>Modelo de ligação de ação: 

Os modelos de ligação de ação são usados para pré-finir urls HTTP aactivos, que consistem nos espaços reservados `%datafeed` , , , e `%metric` `%timestamp` `%detect_config` `%tagset` . Pode utilizar o modelo para redirecionar de uma anomalia ou de um incidente para um URL específico para perfurar.

:::image type="content" source="../media/action-link-template.png" alt-text="Modelo de ligação de ação" lightbox="../media/action-link-template.png":::

Uma vez preenchido o link de ação, clique em **Ir ao link de ação** na opção de ação da lista de incidentes e no menu de cliques à direita da árvore incidente. Substitua os espaços reservados no modelo de ligação de ação pelos valores correspondentes da anomalia ou incidente.

| Marcador de posição | Exemplos | Comentário |
| ---------- | -------- | ------- |
| `%datafeed` | - | ID de feed de dados |
| `%metric` | - | ID métrico |
| `%detect_config` | - | Detetar iD config |
| `%timestamp` | - | Tempo de tempo de uma anomalia ou fim de tempo de um incidente persistente |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Valores de dimensão de uma anomalia ou anomalia superior de um incidente.   <br> O `filterVal` é utilizado para filtrar valores correspondentes dentro dos suportes quadrados.   |

Exemplos:

* Se o modelo de ligação de ação `https://action-link/metric/%metric?detectConfigId=%detect_config` for,
  * O link de `https://action-link/metric/1234?detectConfigId=2345` ação iria para anomalias ou incidentes sob `1234` métrica e detetaria config `2345` .

* Se o modelo de ligação de ação `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` for, 
    * A ligação de ação seria `https://action-link?Dim1=Val1&Dim2=Val2` quando a anomalia é `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * A ligação de ação seria `https://action-link?Dim2=Val2` quando a anomalia é , uma vez que é ignorada para o valor da `{ "Dim1": "", "Dim2": "Val2" } ` `[Dim1=***&]` dimensão cadeia vazia. 

* Se o modelo de ligação de ação `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` for, 
    * A ligação de ação seria `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` quando a anomalia `{ "Dim1": "Val1", "Dim2": "Val2" }` é, 
    * A ligação de ação seria `https://action-link?filter=Name/Dim2 eq 'Val2'` quando a anomalia `{ "Dim1": "", "Dim2": "Val2" }` é, uma vez, `[Name/Dim1 eq '***' and ]` ignorada para o valor da dimensão da cadeia vazia. 
   
### <a name="data-feed-not-available-alert-settings"></a>Definições de alerta de "feed de dados não disponíveis"

Um feed de dados é considerado como não disponível se nenhum dado for ingerido da fonte dentro do período de carência especificado a partir do momento em que o feed de dados começar a ingestão. Neste caso, é acionado um alerta.

Para configurar um alerta, é preciso [criar um gancho](alerts.md#create-a-hook) primeiro. Os alertas serão enviados através do gancho configurado.

* **Período de graças**: A definição do período de graça é utilizada para determinar quando enviar um alerta se não forem ingeridos pontos de dados. O ponto de referência é o tempo da primeira ingestão. Se uma ingestão falhar, o Metrics Advisor continuará a tentar num intervalo regular especificado pela granularidade. Se continuar a falhar para além do período de graça, será enviado um alerta.

* **Soneca automática**: Quando esta opção está definida para zero, cada marca de tempo com *não disponível* dispara um alerta. Quando é especificada uma definição diferente de zero, os tempos contínuos após o primeiro tempo de marcação com *não disponível* não são ativados de acordo com a definição especificada.

## <a name="next-steps"></a>Passos seguintes
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
- [Ajustar a deteção de anomalias através de comentários](anomaly-feedback.md)
- [Diagnosticar um incidente.](diagnose-incident.md)
