---
title: Compreender o estado da aplicação em Azure Spring Cloud
description: Conheça as categorias de estado da aplicação em Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569007"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Compreender o estado da aplicação em Azure Spring Cloud

A Azure Spring Cloud UI fornece informações sobre o estado das aplicações de execução.  Existe uma opção **Apps** para cada grupo de recursos numa subscrição que apresenta o estado geral dos tipos de aplicações.  Para cada tipo de aplicação, existe visualização de instâncias de **aplicação**.

## <a name="apps-status"></a>Estado das aplicações
Para ver o estado geral de um tipo de aplicação, selecione **Apps** no painel de navegação esquerdo de um grupo de recursos. O resultado mostra o estado da aplicação implementada:

* O estado de **provisionamento** mostra o estado de provisionamento da implantação
* A instância de **execução** mostra quantas instâncias de aplicações estão a decorrer/quantas instâncias de aplicações são desejadas. Se a aplicação for interrompida, esta coluna mostra *parada*.
* **A Instância Registada** mostra quantas instâncias de aplicações estão registadas em eureka/quantas instâncias de aplicações são desejadas. Se a aplicação for interrompida, esta coluna mostra *parada*.


 ![Estado das aplicações](media/spring-cloud-concept-app-status/apps-ui-status.png)

**O estado de implantação é reportado como um dos seguintes valores:**

| Enum | Definição |
|:--:|:----------------:|
| A executar | O destacamento deve estar a funcionar. |
| Parada | O destacamento deve ser interrompido. |

**O estado de provisionamento só é acessível a partir do CLI.  É reportado como um dos seguintes valores:**

| Enum | Definição |
|:--:|:----------------:|
| Criação | O recurso está a criar. |
| Atualização | O recurso está a atualizar-se. |
| Bem-sucedido | Forneceu recursos com sucesso e implanta o binário. |
| Falhou | Não conseguiu alcançar o objetivo *bem sucedido.* |
| Apagando | O recurso está a ser apagado. Isto impede o funcionamento e o recurso não está disponível neste estado. |

## <a name="app-instances-status"></a>Estado das instâncias da aplicação

Para ver o estado de uma instância específica de uma aplicação implementada, clique no **nome** da aplicação no **UI** apps. Os resultados serão apresentados:
* **Estado**: Se a instância está em execução ou o seu estado
* **DiscoveryStatus**: O estado registado da instância da aplicação no servidor Eureka

 ![Estado das instâncias da aplicação](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**O estado da instância é reportado como um dos seguintes valores:**

| Enum | Definição |
|:--:|:----------------:|
| A iniciar | O binário é implantado com sucesso para a instância dada. Por exemplo, o arranque do ficheiro do frasco pode falhar porque o frasco não pode funcionar corretamente. |
| A executar | O caso funciona. |
| Falhou | A instância da aplicação não começou o binário do utilizador depois de várias tentativas. |
| Terminando | A instância da aplicação está a desligar-se. |

**O estado de descoberta da ocorrência é relatado como um dos seguintes valores:**

| Enum | Definição |
|:--:|:----------------:|
| PARA CIMA | A instância da aplicação está registada em eureka e pronta para receber tráfego |
| OUT_OF_SERVICE | A instância da aplicação está registada em Eureka e capaz de receber tráfego. mas desliga para o tráfego intencionalmente. |
| PARA BAIXO | A instância da aplicação não está registada na Eureka ou está registada, mas não pode receber tráfego. |


## <a name="see-also"></a>Consulte também
* [Prepare uma aplicação java spring para implantação em Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md)