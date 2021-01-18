---
title: Resolver problemas do Conector do ITSM
description: Problemas de resolução de problemas no Conector de Gestão de Serviços de TI
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 14f1056bf761eb7b591d04db34610468058bc255
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562868"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Resolver problemas do Conector do ITSM

Este artigo discute problemas comuns no CONECTOR ITSM e como resolvê-los.

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Para obter mais informações sobre o alerta, consulte a visão geral dos alertas no Microsoft Azure.
O cliente pode selecionar como pretende ser notificado no alerta, seja por correio, SMS, Webhook ou até mesmo para automatizar uma solução. Outra opção a ser notificada é a utilização do ITSM.
A ITSM dá-lhe a opção de enviar os alertas para o sistema de bilhética externa, como o ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar o incidente e alterar os dados do pedido

Dependendo da configuração quando configurar uma ligação, o ITSMC pode sincronizar até 120 dias de incidente e alterar dados de pedido. O esquema de registo de registo destes dados é fornecido na Secção de [Informações Adicionais](./itsmc-synced-data.md) deste artigo.

Pode visualizar o incidente e alterar os dados do pedido utilizando o painel ITSMC:

![Screenshot que mostra o painel ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o estado do conector, que pode usar como ponto de partida para analisar problemas com as ligações.

Para obter mais informações sobre a investigação do painel de instrumentos, consulte [a Investigação de Erro utilizando o painel de instrumentos](./itsmc-dashboard.md).

### <a name="service-map"></a>Mapa de serviço

Também pode visualizar os incidentes sincronizados com os computadores afetados no Mapa de Serviço.

O Mapa de Serviços descobre automaticamente os componentes da aplicação nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe visualizar os seus servidores como pensa neles: como sistemas interligados que fornecem serviços críticos. O Mapa de Serviços mostra ligações entre servidores, processos e portas em qualquer arquitetura ligada à TCP. Para além da instalação de um agente, não é necessária qualquer configuração. Para obter mais informações, consulte [o Mapa de Serviços.](../insights/service-map.md)

Se estiver a utilizar o Mapa de Serviços, pode ver os itens de balcão de atendimento criados em soluções ITSM, como mostrado aqui:

![Screenshot que mostra o ecrã Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>Resolução de problemas ligações ITSM

- Se uma ligação não conseguir ligar-se ao sistema ITSM e receber um **Erro na mensagem de ligação de poupança,** tome os seguintes passos:
   - Para as ligações ServiceNow, Cherwell e Provance:  
     - Certifique-se de que inseriu corretamente o nome de utilizador, palavra-passe, identificação do cliente e segredo do cliente para cada uma das ligações.  
     - Certifique-se de que tem privilégios suficientes no produto ITSM correspondente para esogir a ligação.  
   - Para ligações ao Gestor de Serviços:  
     - Certifique-se de que a aplicação web está implantada com sucesso e que a ligação híbrida é criada. Para verificar se a ligação está estabelecida com sucesso com o computador Do Gestor de Serviços no local, aceda ao URL da aplicação web, conforme descrito na documentação para eprodução da [ligação híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Se o Log Analytics alertar o fogo mas os itens de trabalho não forem criados no produto ITSM, se os itens de configuração não forem criados/ligados a itens de trabalho, ou para outras informações, consulte estes recursos:
   -  ITSMC: A solução mostra um resumo de ligações, itens de trabalho, computadores e muito mais. Selecione o azulejo que tem a etiqueta **de Estado do Conector.** Ao fazê-lo, leva-o a **registar a pesquisa** com a consulta relevante. Veja os registos com um `LogType_S` de `ERROR` mais informações.
   - **Registar Página de Pesquisa:** Ver os erros e informações relacionadas diretamente utilizando a consulta `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Implementação de aplicativos web do Gestor de Serviço de Resolução de Problemas

-   Se tiver problemas com a implementação de aplicações web, certifique-se de que tem permissões para criar/implementar recursos na subscrição.
-   Se obtém uma **referência de Objeto não definida como uma instância de um** erro de objeto quando executar o [script](itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos na secção configuração do **utilizador.**
-   Se não criar o espaço de nome de retransmissão de ônibus de serviço, certifique-se de que o fornecedor de recursos necessário está registado na subscrição. Se não estiver registado, crie manualmente o espaço de nome do retransmissor de autocarro de serviço a partir do portal Azure. Também pode criá-lo quando [criar a ligação híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection) no portal Azure.