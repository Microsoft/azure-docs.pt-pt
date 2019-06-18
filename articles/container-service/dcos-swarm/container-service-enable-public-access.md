---
title: (PRETERIDO) Ativar o acesso à aplicação de contentor do Azure. o DC/OS
description: Como ativar o acesso público para os contentores do DC/OS no Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457387"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(PRETERIDO) Ativar o acesso público a uma aplicação do Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Qualquer contentor de DC/OS no ACS [conjunto de agentes públicos](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automaticamente é exposto à internet. Por predefinição, as portas **80**, **443**, **8080** estão abertos, e qualquer contêiner (público) a escutar as portas estão acessíveis. Este artigo mostra como abrir mais portas para as suas aplicações no Azure Container Service.

## <a name="open-a-port-portal"></a>Abrir uma porta (portal)
Em primeiro lugar, é necessário abrir a porta que queremos.

1. Inicie sessão no portal.
2. Encontre o grupo de recursos que implementou o serviço de contentor do Azure.
3. Selecione o Balanceador de carga do agente (que tem o nome semelhante **XXXX-agent-lb-XXXX**).
   
    ![Balanceador de carga do serviço de contentor do Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Clique em **sondas** e, em seguida **adicionar**.
   
    ![Sondas do Balanceador de carga do serviço de contentor do Azure](./media/container-service-enable-public-access/add-probe.png)
5. Preencha o formulário de pesquisa e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Name |Um nome descritivo da sonda. |
   | Port |A porta do contentor para testar. |
   | Caminho |(Quando estiver no modo HTTP) O caminho relativo do site para a sonda. HTTPS não suportado. |
   | Interval |A quantidade de tempo entre a sonda tenta, em segundos. |
   | Limiar de mau estado de funcionamento |Número de sonda consecutiva tenta antes de considerar o contentor de mau estado de funcionamento. |
6. De volta às propriedades do Balanceador de carga do agente, clique em **regras de balanceamento de carga** e, em seguida **Add**.
   
    ![Regras do Balanceador de carga de serviço de contentor do Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Preencha o formulário de Balanceador de carga e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Name |Um nome descritivo do Balanceador de carga. |
   | Port |A porta de entrada pública. |
   | Porta de back-end |A porta interna público do contentor para encaminhar o tráfego para. |
   | Conjunto back-end |Os contentores neste conjunto será o destino para este Balanceador de carga. |
   | Sonda |A sonda utilizada para determinar se um destino na **conjunto back-end** está em bom estado. |
   | Persistência da sessão |Determina como o tráfego de um cliente deve ser tratado durante a sessão.<br><br>**Nenhum**: Solicitações sucessivas do mesmo cliente podem ser processadas por qualquer contentor.<br>**Cliente IP**: Solicitações sucessivas do mesmo IP de cliente são processadas pelo mesmo contentor.<br>**Cliente IP e protocolo**: Solicitações sucessivas da mesma combinação de IP e protocolo de cliente são processadas pelo mesmo contentor. |
   | Tempo limite de inatividade |(Apenas para o TCP) Em minutos, o tempo para manter um cliente TCP/HTTP abrir sem depender *keep-alive* mensagens. |

## <a name="add-a-security-rule-portal"></a>Adicionar uma regra de segurança (portal)
Em seguida, precisamos de adicionar uma regra de segurança que encaminha o tráfego da nossa porta aberta através da firewall.

1. Inicie sessão no portal.
2. Encontre o grupo de recursos que implementou o serviço de contentor do Azure.
3. Selecione o **pública** grupo de segurança de rede do agente (que tem o nome semelhante ao **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupo de segurança de rede de serviço de contentor do Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecione **regras de segurança de entrada** e, em seguida **Add**.
   
    ![Regras de grupo de segurança de rede de serviço de contentor do Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Preencha a regra de firewall para permitir que a porta pública e clique em **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Name |Um nome descritivo da regra de firewall. |
   | Prioridade |Classificação de prioridade para a regra. Menor o número maior será a prioridade. |
   | source |Restringir o intervalo de endereços IP recebido para ser permitido ou negado por esta regra. Uso **qualquer** não especificar uma restrição. |
   | Serviço |Selecione um conjunto de serviços predefinidos, que esta regra de segurança destina-se. Caso contrário, utilize **personalizado** para criar seus próprios. |
   | Protocol |Restringir o tráfego com base na **TCP** ou **UDP**. Uso **qualquer** não especificar uma restrição. |
   | Intervalo de portas |Quando **serviço** é **personalizado**, especifica o intervalo de portas que afeta esta regra. Pode utilizar uma porta única, tal como **80**, ou um intervalo, como **1024 1500**. |
   | Ação |Permitir ou negar o tráfego que cumpre os critérios. |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a diferença entre [públicas e privadas agentes do DC/OS](container-service-dcos-agents.md).

Leia mais informações sobre [gerir os seus contentores do DC/OS](container-service-mesos-marathon-ui.md).

