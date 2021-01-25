---
title: Resolver problemas do Conector do ITSM
description: Problemas de resolução de problemas no Conector de Gestão de Serviços de TI
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e43c5fb36c5395e12fd0b9c2c67b787a1137f5d0
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761977"
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

## <a name="common-symptoms---how-should-it-be-resolved"></a>Sintomas Comuns - como deve ser resolvido?

A lista abaixo contém sintomas comuns e como deve ser resolvida:

* **Sintoma**: Se uma ligação não se ligar ao sistema ITSM e receber um **Erro na mensagem de ligação.**

    **Causa:** a causa pode ser uma das opções:
    * Credenciais incorretas
     * Privilégios insuficientes
     * A aplicação web deve ser implementada corretamente

    **Resolução:**
    * Para as ligações ServiceNow, Cherwell e Provance:
        * Certifique-se de que inseriu corretamente o nome de utilizador, palavra-passe, identificação do cliente e segredo do cliente para cada uma das ligações.  
        * Para o ServiceNow: Certifique-se de que dispõe de privilégios suficientes no produto ITSM correspondente para escamar a ligação conforme [especificado.](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)
  * Para ligações ao Gestor de Serviços:  
      * Certifique-se de que a aplicação web está implantada com sucesso e que a ligação híbrida é criada. Para verificar se a ligação está estabelecida com sucesso com o computador Do Gestor de Serviços no local, aceda ao URL da aplicação web, conforme descrito na documentação para eprodução da [ligação híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
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