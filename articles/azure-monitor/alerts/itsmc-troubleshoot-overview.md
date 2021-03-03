---
title: Problemas de resolução de problemas no ITSMC
description: Saiba como resolver problemas comuns no Conector de Gestão de Serviços de TI.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a01005231b5d775f79555ec10dedeb3f30b3426a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737210"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Problemas de resolução de problemas no Conector de Gestão de Serviços de TI

Este artigo discute problemas comuns no It Service Management Connector (ITSMC) e como resolvê-los.

O Azure Monitor notifica-o proativamente em alertas quando encontra condições importantes nos seus dados de monitorização. Estes alertas ajudam-no a identificar e a resolver problemas antes que os utilizadores do seu sistema os notem.

Pode selecionar como pretende receber alertas. Pode escolher correio, SMS ou webhook ou até automatizar uma solução. 

Uma alternativa deve ser notificada através do ITSMC. A ITSMC dá-lhe a opção de enviar alertas para um sistema de bilhética externa, como o ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Utilize o painel de instrumentos para analisar incidentes e alterar dados de pedidos

Dependendo da configuração quando configurar uma ligação, o ITSMC pode sincronizar até 120 dias de incidente e alterar dados de pedido. Para obter o esquema de registo de registo de registo para estes dados, consulte os [Dados sincronizados no seu artigo de produto ITSM.](./itsmc-synced-data.md)

Pode visualizar o incidente e alterar os dados do pedido utilizando o painel ITSMC:

![Screenshot que mostra o painel ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o estado do conector. Pode utilizar essa informação como ponto de partida para analisar problemas com as ligações. Para obter mais informações, consulte [a investigação error utilizando o painel de instrumentos](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Use o mapa de serviço para visualizar incidentes

Também pode visualizar os incidentes sincronizados com os computadores afetados no Mapa de Serviço.

O Mapa de Serviços descobre automaticamente os componentes da aplicação nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe visualizar os seus servidores como pensa neles: como sistemas interligados que fornecem serviços críticos. 

O Mapa de Serviços mostra ligações entre servidores, processos e portas em qualquer arquitetura ligada à TCP. Para além da instalação de um agente, não é necessária qualquer configuração. Para obter mais informações, consulte [o Mapa de Serviços.](../vm/service-map.md)

Se estiver a utilizar o Mapa de Serviços, pode ver os itens de balcão de atendimento criados em soluções de Gestão de Serviços de TI (ITSM), como mostra este exemplo:

![Screenshot que mostra o ecrã Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Resolver problemas

As seguintes secções identificam sintomas comuns, possíveis causas e resoluções. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Uma ligação ao sistema ITSM falha e obtém-se uma mensagem "Erro na ligação de poupança"

**Causa**: A causa pode ser uma destas opções:

* As credenciais estão incorretas.
* Privilégios são insuficientes.
* Para ligações ao Gestor de Serviço: A aplicação web foi implementada incorretamente.

**Resolução:**

* Para as ligações ServiceNow, Cherwell e Provance:
  * Certifique-se de que inseriu corretamente o nome de utilizador, palavra-passe, identificação do cliente e segredo do cliente para cada uma das ligações.  
  * Para o ServiceNow, certifique-se de que tem [privilégios suficientes](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) no produto ITSM correspondente.

* Para ligações ao Gestor de Serviços:  
  * Certifique-se de que a aplicação web está implantada com sucesso e que a ligação híbrida é criada. Para verificar se a ligação está estabelecida com sucesso com o computador Do Gestor de Serviços no local, aceda ao URL da aplicação web, conforme descrito na [documentação para fazer uma ligação híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>São criados itens de trabalho duplicados

**Causa**: A causa pode ser uma destas duas opções:

* Mais de uma ação ITSM é definida para o alerta.
* O alerta está resolvido.

**Resolução**: Pode haver duas soluções:

* Certifique-se de que dispõe de um único grupo de ação ITSM por alerta.
* O ITSMC não suporta atualizações de estado dos itens de trabalho correspondentes quando um alerta é resolvido. Criar um novo item de trabalho resolvido.

### <a name="work-items-are-not-created"></a>Os itens de trabalho não são criados

**Causa**: Pode haver várias razões para este sintoma:

* O código foi modificado no lado do ServiceNow.
* As permissões estão mal configuradas.
* Os limites de taxa de ServiceNow são demasiado altos ou demasiado baixos.
* Expirou um token de atualização.
* O ITSMC foi suprimido.

**Resolução**: Verifique o [painel de instrumentos](itsmc-dashboard.md) e reveja os erros na secção para o estado do conector. Em seguida, reveja os [erros comuns e as suas resoluções.](itsmc-dashboard-errors.md)

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Não é possível criar uma ação ITSM para um grupo de ação

**Causa**: Uma instância ITSMC recém-criada ainda não terminou a sincronização inicial.

**Resolução**: Rever os [erros comuns e as suas resoluções.](itsmc-dashboard-errors.md)

### <a name="sync-connection"></a>Ligação sincronizada 

**Causa**: Pode haver várias razões para este sintoma:

* Os modelos não são apresentados como parte da definição de ação.
* Os Incedents/Events não são criados no ServiceNow.

**Resolução**: [Sincronia o conector](itsmc-resync-servicenow.md).
