---
title: Notificação de manutenção planeada - Base de Dados Azure para MySQL - Servidor Único
description: Este artigo descreve a funcionalidade de notificação de manutenção planeada na Base de Dados Azure para MySQL - Servidor Único
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c6d1bfbf1592da3a5e632eb875221225630aed3f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108677"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Notificação de manutenção planeada na Base de Dados Azure para MySQL - Servidor Único

Saiba como se preparar para eventos de manutenção planeados na sua Base de Dados Azure para o MySQL.

## <a name="what-is-a-planned-maintenance"></a>O que é a manutenção planeada?

A Azure Database for MySQL service executa patching automatizado do hardware subjacente, o SISTEMA e o motor da base de dados. O patch inclui novas funcionalidades de serviço, segurança e atualizações de software. Para o motor MySQL, as atualizações de versão menor são automáticas e incluídas como parte do ciclo de remendos. Não é necessária nenhuma ação do utilizador ou configurações para remendar. O patch é testado extensivamente e lançado utilizando práticas de implementação seguras.

Uma manutenção planeada é uma janela de manutenção quando estas atualizações de serviço são implementadas para servidores de uma determinada região de Azure. Durante as manutenções planeadas, é criado um evento de notificação para informar os clientes de quando a atualização do serviço será implementada na região do Azure onde os servidores estão alojados. A duração mínima entre duas manutenção planeadas é de 30 dias. Receberá uma notificação sobre a próxima janela de manutenção com 72 horas de antecedência.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Manutenção planeada – duração e impacto no cliente

Normalmente, as manutenções planeadas para uma determinada região do Azure têm uma duração esperada de 15 horas. A janela também inclui tempo de tampão para executar um plano de reversão, se necessário. Durante a manutenção planeada, pode haver reinício ou falhas no servidor de base de dados, o que pode levar a uma breve indisponibilidade dos servidores de base de dados para os utilizadores finais. A base de dados Azure para servidores MySQL está a funcionar em contentores para que os recomeçamento do servidor de base de dados sejam normalmente rápidos, esperando-se que completem normalmente em 60-120 segundos. Todo o evento de manutenção planeado, incluindo cada servidor, é cuidadosamente monitorizado pela equipa de engenharia. O tempo de falha do servidor depende do tempo de recuperação da base de dados, o que pode fazer com que a base de dados fique online por mais tempo se tiver uma atividade transacional pesada no servidor no momento da falha. Para evitar um tempo de reinício mais longo, é aconselhável evitar transações de longa duração (cargas a granel) durante os eventos de manutenção planeados.

Em resumo, enquanto o evento de manutenção planeado dura 15 horas, o impacto individual do servidor geralmente dura 60 segundos dependendo da atividade transacional no servidor. Uma notificação é enviada 72 horas antes do início da manutenção planeada e outra enquanto a manutenção está em curso para uma determinada região.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Como posso ser notificado sobre a manutenção planeada?

Pode utilizar a funcionalidade de notificações de manutenção planeada para receber alertas para um evento de manutenção planeado. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento e outra enquanto a manutenção está em andamento para uma determinada região.

### <a name="planned-maintenance-notification"></a>Notificação de manutenção planeada

> [!IMPORTANT]
> As notificações de manutenção previstas estão atualmente disponíveis em pré-visualização em todas as **regiões, exceto** no Centro-Oeste dos EUA

**As notificações de manutenção planeadas** permitem-lhe receber alertas para o próximo evento de manutenção planeado para a sua Base de Dados Azure para o MySQL. Estas notificações estão integradas na manutenção planeada [do Service Health](../service-health/overview.md) e permitem-lhe visualizar todas as manutenção programadas para as suas subscrições num só local. Também ajuda a escalar a notificação para o público certo para diferentes grupos de recursos, pois você pode ter diferentes contactos responsáveis por diferentes recursos. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

Faremos todas as tentativas para fornecer **aviso de manutenção planeada** 72 horas para todos os eventos. No entanto, em casos de patches críticos ou de segurança, as notificações podem ser enviadas para mais perto do evento ou ser omitidas.

Pode consultar a notificação de manutenção planeada no portal Azure ou configurar alertas para receber a notificação. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Verifique a notificação de manutenção planeada do portal Azure

1. No [portal Azure](https://portal.azure.com), selecione **Service Health**.
2. Selecione **separador de manutenção planeado**
3. Selecione **Subscrição,** **Região** e **Serviço** para o qual pretende verificar a notificação de manutenção planeada. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Para receber a notificação de manutenção planeada

1. No [portal](https://portal.azure.com), selecione **Service Health**.
2. Na secção **Alertas,** selecione **Alertas de Saúde**.
3. **Selecione + Adicione o alerta de saúde** do serviço e preencha os campos.
4. Preencha os campos necessários. 
5. Escolha o **tipo de Evento,** selecione **manutenção planeada** ou **selecione tudo**
6. Em **Action os grupos** definem como gostaria de receber o alerta (receba um e-mail, desencadeie uma aplicação lógica, etc.)  
7. Certifique-se de que a regra enable após a criação está definida para Sim.
8. Selecione **Criar regra de alerta** para completar o seu alerta

Para obter etapas detalhadas sobre como criar **alertas** de saúde de serviço, consulte os [alertas de registo de atividades da Create nas notificações do serviço](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Posso cancelar ou adiar a manutenção planeada?

A manutenção é necessária para manter o seu servidor seguro, estável e atualizado. O evento de manutenção planeado não pode ser cancelado ou adiado. Uma vez que a notificação é enviada para uma determinada região de Azure, as alterações de programa de remendação não podem ser feitas para qualquer servidor individual nessa região. O patch é lançado para toda a região de uma só vez. Azure Database for MySQL - Serviço de servidor único foi concebido para aplicação nativa em nuvem que não requer controlo granular ou personalização do serviço. Se procura ter a capacidade de agendar a manutenção para os seus servidores, recomendamos que considere [servidores flexíveis](./flexible-server/overview.md).

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Os patches são aplicados ao mesmo tempo em todas as regiões do Azure?

Não, todas as regiões de Azure são remendadas durante os tempos de janela sábias de implantação. A janela de implantação geralmente estende-se das 17:00 às 8:00 horas locais no dia seguinte, numa determinada região de Azure. As regiões de Azure geo-emparelhadas são remendadas em dias diferentes. Para uma elevada disponibilidade e continuidade de negócios de servidores de base de dados, recomenda-se a utilização [de réplicas de leitura de região cruzada.](./concepts-read-replicas.md#cross-region-replication)

## <a name="retry-logic"></a>Repetir a lógica

Um erro transitório, também conhecido como falha transitória, é um erro que se resolverá sozinho. Podem ocorrer [erros transitórios](./concepts-connectivity.md#transient-errors) durante a manutenção. A maioria destes eventos são automaticamente atenuados pelo sistema em menos de 60 segundos. Os erros transitórios devem ser manuseados utilizando [a lógica de repetição](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Passos seguintes

- Para quaisquer perguntas ou sugestões que possa ter sobre trabalhar com a Base de Dados Azure para o MySQL, envie um e-mail para a Base de Dados Azure para a Equipa MySQL em AskAzureDBforMySQL@service.microsoft.com
- Veja [como configurar alertas](howto-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
- [Problemas de conexão de resolução de problemas para Azure Database para MySQL - Servidor Único](howto-troubleshoot-common-connection-issues.md)
- [Lidar com erros transitórios e ligar eficientemente à Base de Dados Azure para MySQL - Servidor Único](concepts-connectivity.md)
