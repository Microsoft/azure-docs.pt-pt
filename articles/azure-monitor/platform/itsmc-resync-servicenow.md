---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Reinicie a ligação ao ServiceNow para que os alertas no Microsoft Azure possam voltar a ligar para o ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2f0889732ac756e16465659ee2e5b52335d1148f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539257"
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

- Se os dados do ServiceNow não estiverem a ser sincronizados com o Log Analytics, certifique-se de que a instância Do ServiceNow não está a dormir. Os casos de dev do ServiceNow às vezes dormem quando estão inativos por muito tempo. Se não é isso que está a acontecer, reporte o problema.
- Se o Log Analytics alertar o fogo mas os itens de trabalho não forem criados no produto ITSM, se os itens de configuração não forem criados/ligados a itens de trabalho, ou para outras informações, consulte estes recursos:
   -  ITSMC: A solução mostra um resumo de ligações, itens de trabalho, computadores e muito mais. Selecione o azulejo que tem a etiqueta **de Estado do Conector.** Ao fazê-lo, leva-o a **registar a pesquisa** com a consulta relevante. Veja os registos com um `LogType_S` de `ERROR` mais informações.
   - **Registar Página de Pesquisa:** Ver os erros e informações relacionadas diretamente utilizando a consulta `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Implementação de aplicativos web do Gestor de Serviço de Resolução de Problemas

-   Se tiver problemas com a implementação de aplicações web, certifique-se de que tem permissões para criar/implementar recursos na subscrição.
-   Se obtém uma **referência de Objeto não definida como uma instância de um** erro de objeto quando executar o [script](itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos na secção configuração do **utilizador.**
-   Se não criar o espaço de nome de retransmissão de ônibus de serviço, certifique-se de que o fornecedor de recursos necessário está registado na subscrição. Se não estiver registado, crie manualmente o espaço de nome do retransmissor de autocarro de serviço a partir do portal Azure. Também pode criá-lo quando [criar a ligação híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection) no portal Azure.

### <a name="how-to-manually-fix-sync-problems"></a>Como corrigir manualmente problemas de sincronização

O Azure Monitor pode ligar-se a fornecedores de gestão de serviços de TI de terceiros (ITSM). O ServiceNow é um desses fornecedores.

Por razões de segurança, poderá necessitar de atualizar o token de autenticação utilizado para a sua ligação com o ServiceNow.
Utilize o seguinte processo de sincronização para reativar a ligação e refrescar o token:


1. Procure a solução no banner de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Screenshot que mostra o banner de pesquisa superior e onde selecionar as soluções relevantes.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. No ecrã da solução, escolha "Select All" no filtro de subscrição e, em seguida, filtre por "ServiceDesk"

    ![Screenshot que mostra onde escolher Select All e onde filtrar por ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução da sua ligação ITSM.
1. Selecione a ligação ITSM no banner esquerdo.

    ![Screenshot que mostra onde selecionar ligações ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector da lista. 
    1. Clique no nome do Conector para o configurar
    1. Eliminar quaisquer conectores que já não sejam utilizados

    1. Atualizar os campos de acordo com [estas definições](./itsmc-connections.md) com base no tipo de parceiro

    1. Clique em sincronização

       ![Screenshot que realça o botão Sync.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em guardar

        ![Nova ligação](media/itsmc-resync-servicenow/save-8bit.png)

f.    Reveja as notificações para ver se o processo começou.
