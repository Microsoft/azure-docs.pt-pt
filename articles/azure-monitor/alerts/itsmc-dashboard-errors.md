---
title: Erros de estado do conector no painel ITSMC
description: Saiba mais sobre os erros comuns que existem no painel de controlo do serviço de TI.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471521"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Erros de estado do conector no painel ITSMC

O painel de instrumentos de gestão de serviços de TI (ITSMC) apresenta erros que podem ajudá-lo a corrigir problemas no seu conector.

As secções seguintes descrevem erros comuns que aparecem na secção de estado do conector do painel de instrumentos e como pode resolvê-los.

## <a name="unexpected-response"></a>Resposta inesperada

**Erro**: "Resposta inesperada do ServiceNow juntamente com o código de estado de sucesso. Resposta: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultado": [ {"transform_map": "Incidente OMS", "tabela": "incidente", "estado": "erro", "error_message": "{Registo de alvo não encontrado| Mesa inválida| Mesa de encenação inválida" }"

**Causa**: ServiceNow devolve este erro quando:

* Um script personalizado implantado num caso ServiceNow faz com que os incidentes sejam ignorados.
* O código "OMS Integrator app" foi modificado no lado do ServiceNow (por exemplo, através do `onBefore` script).

**Resolução**: Desative todos os scripts personalizados ou modificações de código.

## <a name="exception-update-failure"></a>Falha na atualização de exceções

**Erro**: "{"error":{"message":"Operation Failed", "detail":"A ACL Exception Update Failed due to security constraints"}"

**Causa**: As permissões de ServiceNow estão mal configuradas.

**Resolução**: Verifique se todas as funções estão devidamente atribuídas conforme [especificado.](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)

## <a name="problem-sending-a-request"></a>Problema de envio de um pedido

**Erro:**"Ocorreu um erro ao enviar o pedido."

**Causa**: Uma instância ServiceNow não está disponível.

**Resolução**: Verifique a sua instância no ServiceNow. Pode ser apagado ou indisponível.

## <a name="servicenow-rate-problem"></a>Problema de tarifa ServiceNow

**Erro**: "ServiceDeskHttpBadRequestException: StatusCode=429"

**Causa:** Os limites de taxa de ServiceNow são demasiado altos ou demasiado baixos.

**Resolução**: Aumente ou an cancele os limites de taxa na instância ServiceNow, conforme explicado na [documentação ServiceNow](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Token de atualização inválida

**Erro:** 
  * "AccessToken e RefreshToken inválidos. O utilizador precisa de autenticar novamente."
  * "Não foi possível sincronizar a configuração dos modelos para Evento,Alerta,Incidente. Consulte a Mensagem de Exceção para mais detalhes."

**Causa:** Expira-se um token de atualização.

**Resolução**: Sync ITSMC para gerar um novo token de atualização, como explicado em [Como corrigir manualmente problemas de sincronização](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Conector desaparecido

**Erro**: "Não foi possível criar/atualizar o item de trabalho para alerta {alertName}. CONECTOR ITSM {connectionIdentifier} não existe ou foi eliminado."

**Causa**: ITSMC foi eliminado.

**Resolução**: O ITSMC foi suprimido, mas os grupos de ação definidos de Gestão de Serviços de TI (ITSM) continuam a ser associados. Existem três opções para resolver este problema:

* Localizar e desativar ou eliminar tais grupos de ação.
* [Reconfigure os grupos de ação](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) para utilizar uma instância ITSMC existente.
* [Crie uma nova instância ITSMC](./itsmc-definition.md#create-an-itsm-connection) e [reconfigure os grupos de ação para a utilizar](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Falta de detalhes de conexão

**Erro**:"Algo correu mal. Não consegui obter detalhes de conexão. Este erro aparece quando se define um grupo de ação ITSM.

**Causa:** Tal erro aparece em qualquer uma destas situações:

* Uma instância de conector ITSM recém-criada ainda não terminou a sincronização inicial.
* O conector não foi definido corretamente.

**Resolução:** 

* Quando uma nova instância ITSMC é criada, começa a sincronizar informações a partir do sistema ITSM, tais como modelos de produto de trabalho e itens de trabalho. [Sync ITSMC para gerar um novo token de atualização](./itsmc-resync-servicenow.md).
* [Reveja os seus dados de conexão no ITSMC](./itsmc-connections-servicenow.md#create-a-connection) e verifique se o ITSMC pode [sincronizar](./itsmc-resync-servicenow.md)com sucesso.


## <a name="ip-restrictions"></a>Restrições ip
**Erro**: "Falha na adição da Ligação ITSM denominada "XXX" devido a Mau Pedido. Erro: Mau pedido. Parâmetros inválidos previstos para a ligação. http exceção: Código de Estado Proibido."

**Causa**: O endereço IP da aplicação ITSM não permite ligações ITSM a partir de ferramentas ITSM de parceiros.

**Resolução**: Para enumerar os endereços IP ITSM de forma a permitir ligações ITSM a partir de ferramentas ITSM parceiras, recomendamos que enuseça toda a gama pública de IP da região de Azure onde o seu espaço de trabalho LogAnalytics pertence. [detalhes aqui](https://www.microsoft.com/download/details.aspx?id=56519) Para as regiões EUS/WEU/EUS2/WUS2/US South Central, o cliente só pode listar a etiqueta de rede do ActionGroup.
