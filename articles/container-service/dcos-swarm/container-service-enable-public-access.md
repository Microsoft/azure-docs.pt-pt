---
title: (DEPRECIADO) Permitir o acesso à aplicação de contentores Azure DC/OS
description: Como permitir o acesso do público aos contentores DC/OS no Serviço de Contentores Azure.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457387"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(DEPRECIADO) Permitir o acesso do público a uma aplicação do Serviço de Contentores Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Qualquer contentor DC/OS na piscina de [agentes públicos](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) ACS é automaticamente exposto à internet. Por defeito, as portas **80,** **443,** **8080** estão abertas e qualquer contentor (público) que ouça nesses portos esteja acessível. Este artigo mostra-lhe como abrir mais portas para as suas aplicações no Serviço de Contentores Azure.

## <a name="open-a-port-portal"></a>Abrir um porto (portal)
Primeiro, temos de abrir o porto que queremos.

1. Inicie sessão no portal.
2. Encontre o grupo de recursos para o qual implantou o Serviço de Contentores Azure.
3. Selecione o equilibrador de carga do agente (que é nomeado semelhante ao **XXXX-agent-lb-XXXX**).
   
    ![Equilibrador de carga de serviço de contentores Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Clique em **Sondas** **e,** em seguida, adicionar .
   
    ![Sondas de equilíbrio de carga de serviço de contentores Azure](./media/container-service-enable-public-access/add-probe.png)
5. Preencha o formulário da sonda e clique **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da sonda. |
   | Porta |O porto do contentor para testar. |
   | Caminho |(Quando no modo HTTP) O caminho relativo do site para a sonda. HTTPS não suportado. |
   | Intervalo |A quantidade de tempo entre as tentativas da sonda, em segundos. |
   | Limiar com funcionamento incorreto |Número de tentativas de sonda consecutivas antes de considerar o recipiente insalubre. |
6. De volta às propriedades do equilibrador de carga do agente, clique em regras de **equilíbrio de carga** **e,** em seguida, adicionar .
   
    ![Regras do equilibrador de carga de serviço de contentores Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Preencha o formulário do equilíbrior de carga e clique **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo do equilibrador de carga. |
   | Porta |O porto de entrada pública. |
   | Porto backend |O porto público interno do contentor para encaminhar o tráfego para. |
   | Conjunto de back-end |Os contentores desta piscina serão o alvo deste equilibrador de carga. |
   | Teste |A sonda usada para determinar se um alvo na **piscina backend** é saudável. |
   | Persistência da sessão |Determina como o tráfego de um cliente deve ser tratado durante a sessão.<br><br>**Nenhum**: Os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer recipiente.<br>**IP do cliente**: Os pedidos sucessivos do mesmo cliente IP são tratados pelo mesmo recipiente.<br>**IP e protocolo**do cliente : Os pedidos sucessivos do mesmo cliente IP e combinação protocolar são tratados pelo mesmo recipiente. |
   | Tempo limite inativo |(apenas TCP) Em minutos, o tempo para manter um cliente TCP/HTTP aberto sem depender de mensagens *vivas.* |

## <a name="add-a-security-rule-portal"></a>Adicione uma regra de segurança (portal)
Em seguida, precisamos adicionar uma regra de segurança que encaminha o tráfego do nosso porto aberto através da firewall.

1. Inicie sessão no portal.
2. Encontre o grupo de recursos para o qual implantou o Serviço de Contentores Azure.
3. Selecione o grupo de segurança da rede de agentes **públicos** (que é nomeado semelhante ao **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupo de segurança da rede de serviços de contentores Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecione **as regras de segurança de entrada** **e,** em seguida, adicionar .
   
    ![Regras do grupo de segurança da rede de serviços de contentores Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Preencha a regra da firewall para permitir a sua porta pública e clique **OK**.
   
   | Campo | Descrição |
   | --- | --- |
   | Nome |Um nome descritivo da regra da firewall. |
   | Prioridade |Posto prioritário para a regra. Quanto menor o número, maior a prioridade. |
   | Origem |Restringir a gama de endereços IP que se aproxima para ser permitida ou negada por esta regra. Use **Qualquer um** para não especificar uma restrição. |
   | Serviço |Selecione um conjunto de serviços predefinidos para o que esta regra de segurança é para. Caso contrário, use **o Costume** para criar o seu próprio. |
   | Protocolo |Restringir o tráfego com base em **TCP** ou **UDP**. Use **Qualquer um** para não especificar uma restrição. |
   | Intervalo de portas |Quando o **Serviço** é **Personalizado,** especifica a gama de portas que esta regra afeta. Pode utilizar uma única porta, como **80**, ou uma gama como **1024-1500**. |
   | Ação |Permitir ou negar o tráfego que satisfaça os critérios. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a diferença entre [agentes públicos e privados de DC/OS](container-service-dcos-agents.md).

Leia mais informações sobre a gestão dos [seus contentores DC/OS](container-service-mesos-marathon-ui.md).

