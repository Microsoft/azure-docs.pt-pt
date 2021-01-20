---
title: Erros comuns
description: Este documento contém informações sobre erros comuns que existem no painel de instrumentos
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 9521c13b21317bb0a782b0bea0b08312ff24b113
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610357"
---
# <a name="errors-in-the-connector-status"></a>Erros no estado do conector

Na lista de estado do conector pode encontrar erros que o podem ajudar a corrigir problemas no seu conector ITSM.

## <a name="status-common-errors"></a>Erros comuns do estado

nesta secção pode encontrar os erros comuns apresentados na secção de estado do conector e como deve resolvê-lo:

* **Erro**: "Resposta inesperada do ServiceNow juntamente com o código de estado de sucesso. Resposta: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultado": [ {"transform_map": "Incidente OMS", "tabela": "incidente", "estado": "erro", "error_message": "{Registo de alvo não encontrado| Mesa inválida| Mesa de encenação inválida" }"

    **Causa:** Tal erro é devolvido do ServiceNow quando:
  * Um script personalizado implementado no caso ServiceNow faz com que os incidentes sejam ignorados.
  * O próprio código "OMS Integrator App" foi modificado no lado do ServiceNow, por exemplo, no script onBefore.

  **Resolução**: Desative todos os scripts personalizados ou modificações de código da trajetória de importação de dados.

* **Erro**: "{"error":{"message":"Operation Failed", "detail":"A ACL Exception Update Failed due to security constraints"}"

    **Causa**: ServiceNow permissões configuração errada

    **Resolução**: Verifique se todas as funções foram devidamente atribuídas conforme [especificado.](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)

* **Erro:**"Ocorreu um erro ao enviar o pedido."

    **Causa**: "Instância de ServiçoNow indisponível"

    **Resolução**: Verifique a sua instância no Serviço Agora que pode estar apagada ou indisponível.

* **Erro**: "ServiceDeskHttpBadRequestException: StatusCode=429"

    **Causa**: Os limites da taxa de serviçoNow são demasiado altos/baixos.

    **Resolução**: Aumente ou an cancele os limites de taxa na instância ServiceNow, conforme [explicado aqui.](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)

* **Erro**: "AccessToken e RefreshToken inválidos. O utilizador precisa de autenticar novamente."

    **Causa:** O token refresh expirou.

    **Resolução**: Sincronia o Conector ITSM para gerar um novo token de atualização, conforme [explicado aqui](./itsmc-resync-servicenow.md).

* **Erro**: "Não foi possível criar/atualizar o item de trabalho para alerta {alertName}. CONECTOR ITSM {connectionIdentifier} não existe ou foi eliminado."

    **Causa**: O conector ITSM foi eliminado.

    **Resolução**: O conector ITSM foi suprimido, mas ainda existem grupos de ação ITSM definidos. Existem 2 opções para resolver este problema:
  * Localizar e desativar ou apagar tal ação
  * [Reconfigure o grupo de ação](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) para utilizar um conector ITSM existente.
  * [Crie um novo conector ITSM](./itsmc-definition.md#create-an-itsm-connection) e [reconfigure o grupo de ação para o utilizar](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Erros Comuns da UI

* **Erro**:"Algo correu mal. Não consegui obter detalhes de conexão. Este erro apresentado quando o cliente define o grupo de ação ITSM.

    **Causa**: O conector ITSM recém-criado ainda não terminou o Sync inicial.

    **Resolução**: Quando um novo conector ITSM é criado, o CONECTOR ITSM começa a sincronizar informações do sistema ITSM, tais como modelos de produto de trabalho e itens de trabalho. Sync o CONECTOR ITSM para gerar um novo token de atualização, como explicado [aqui.](./itsmc-resync-servicenow.md)
