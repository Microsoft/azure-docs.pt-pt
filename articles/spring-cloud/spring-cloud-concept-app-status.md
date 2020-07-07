---
title: Compreender o estado das aplicações no Azure Spring Cloud
description: Aprenda as categorias de estado da aplicação em Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82569007"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Compreender o estado das aplicações no Azure Spring Cloud

O Azure Spring Cloud UI fornece informações sobre o estado das aplicações de execução.  Existe uma opção **apps** para cada grupo de recursos numa subscrição que exibe o estado geral dos tipos de aplicação.  Para cada tipo de aplicação, há uma exposição de instâncias de **aplicação**.

## <a name="apps-status"></a>Estado das aplicações
Para visualizar o estado geral de um tipo de aplicação, selecione **Apps** no painel de navegação esquerdo de um grupo de recursos. O resultado mostra o estado da aplicação implementada:

* **O Estado de Provisionamento** mostra o estado de provisionamento da implantação
* **O caso de execução** mostra quantas instâncias de aplicações estão em execução/quantas instâncias de aplicações são desejadas. Se a aplicação for interrompida, esta coluna mostra *parada*.
* **A Instância Registada** mostra quantas instâncias de aplicações estão registadas para eureka/quantas instâncias de aplicações são desejadas. Se a aplicação for interrompida, esta coluna mostra *parada*.


 ![Estado das aplicações](media/spring-cloud-concept-app-status/apps-ui-status.png)

**O estado de implantação é reportado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| A executar | A implantação deve estar a funcionar. |
| Parada | O destacamento deve ser interrompido. |

**O estado de provisionamento só é acessível a partir do CLI.  É reportado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| Criação | O recurso está a criar. |
| Atualização | O recurso está a atualizar-se. |
| Bem-sucedido | Forneceu recursos com sucesso e implanta o binário. |
| Falhou | Não conseguiu alcançar o objetivo *bem sucedido.* |
| Eliminar | O recurso está a ser apagado. Isto impede o funcionamento e o recurso não está disponível neste estado. |

## <a name="app-instances-status"></a>Estatuto de instâncias de aplicações

Para ver o estado de uma instância específica de uma aplicação implementada, clique no **Nome** da aplicação na **UI** apps. Os resultados mostrarão:
* **Estado**: Se a ocorrência está a decorrer ou o seu estado
* **DiscoveryStatus**: O estado registado da aplicação no servidor Eureka

 ![Estatuto de instâncias de aplicações](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**O estado da instância é reportado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| A iniciar | O binário é implantado com sucesso para o caso dado. O arranque do ficheiro do frasco pode falhar porque o frasco não pode funcionar corretamente. |
| A executar | O caso funciona. |
| Falhou | A aplicação não começou o binário do utilizador depois de várias retró concretizagens. |
| Encerramento | A aplicação está a ser encerrada. |

**O estado de descoberta do caso é reportado como um dos seguintes valores:**

| Enumeração | Definição |
|:--:|:----------------:|
| PARA CIMA | A aplicação está registada em eureka e pronta para receber tráfego |
| OUT_OF_SERVICE | A aplicação está registada em Eureka e capaz de receber tráfego. mas para para o tráfego intencionalmente. |
| PARA BAIXO | A aplicação não está registada em Eureka ou está registada, mas não pode receber tráfego. |


## <a name="see-also"></a>Ver também
* [Prepare uma aplicação java spring para implantação em Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md)