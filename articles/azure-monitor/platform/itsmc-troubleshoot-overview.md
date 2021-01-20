---
title: Resolver problemas do Conector do ITSM
description: Problemas de resolução de problemas no Conector de Gestão de Serviços de TI
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2ffe7c8994d32917a08896c7d25f20d4adf09066
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601906"
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
   -  ITSMC: A solução mostra um [resumo de ligações,](itsmc-dashboard.md)artigos de trabalho, computadores e muito mais. Selecione o azulejo que tem a etiqueta **de Estado do Conector.** Ao fazê-lo, leva-o a **registar a pesquisa** com a consulta relevante. Veja os registos com um `LogType_S` de `ERROR` mais informações.
   Pode ver detalhes sobre as mensagens na mesa - [aqui](itsmc-dashboard-errors.md).
   - **Registar Página de Pesquisa:** Ver os erros e informações relacionadas diretamente utilizando a consulta `*ServiceDeskLog_CL*` .

## <a name="common-symptoms---how-it-should-be-resolved"></a>Sintomas Comuns - como deve ser resolvido?

A lista abaixo contém sintomas comuns e como deve ser resolvida:

* **Sintoma**: São criados itens de trabalho duplicados

    **Causa:** a causa pode ser uma das duas opções:
    * Mais de uma ação ITSM são definidas para o alerta.
    * O alerta está resolvido.

    **Resolução**: Pode haver duas soluções:
    * Certifique-se de que dispõe de um único grupo de ação ITSM por alerta.
    * O Conector ITSM não suporta a atualização do estado dos artigos de trabalho correspondente quando um alerta é resolvido. É criado um novo item de trabalho resolvido.
* **Sintoma**: Os itens de trabalho não são criados

    **Causa**: Pode haver algumas razões para este sintoma:
    * Modificação de código no lado do ServiceNow
    * Permissões de configuração errada
    * Os limites da taxa de serviçoNow são demasiado altos/baixos
    * O token refresh está expirado
    * O Conector ITSM foi eliminado

    **Resolução**: Pode verificar o [painel de instrumentos](itsmc-dashboard.md) e rever os erros na secção de estado do conector. Reveja os [erros comuns](itsmc-dashboard-errors.md) e descubra como resolver o erro.

* **Sintoma**: Incapaz de criar a Ação ITSM para o Grupo de Ação

    **Causa**:O conector ITSM recém-criado ainda não terminou o Sync inicial.

    **Resolução**: pode rever os [erros comuns da UI](itsmc-dashboard-errors.md#ui-common-errors) e descobrir como resolver o erro.