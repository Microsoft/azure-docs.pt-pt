---
title: Resolução de problemas Azure Arc para problemas de ligação de agentes de servidores
description: Este artigo diz como resolver problemas e resolver problemas com o agente da Máquina Conectada que surgem com o Azure Arc para servidores (pré-visualização) quando se tenta ligar ao serviço.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262019"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Resolução de problemas dos problemas de ligação do agente da máquina conectado

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que podem ocorrer enquanto tenta configurar o Arco Azure para servidores (pré-visualização) Agente de máquinas conectadas para Windows ou Linux. Estão incluídos os métodos de instalação interativos e à escala ao configurar a ligação ao serviço. Para obter informações [gerais, consulte o Arc para obter uma visão geral dos servidores.](./overview.md)

## <a name="agent-verbose-log"></a>Registo verboso do agente

Antes de seguir as etapas de resolução de problemas descritas mais tarde neste artigo, a informação mínima necessária é o registo verboso. Contém a saída dos comandos da ferramenta **azcmagent,** quando o argumento verboso (v) é utilizado. Os ficheiros de registo são escritos `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` para Windows e Linux para `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Segue-se um exemplo do comando para permitir a realização de registos verbosos com o agente máquina conectada para o Windows durante a realização de uma instalação interativa.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Segue-se um exemplo do comando para permitir a realização de registos verbosos com o agente máquina conectada para windows quando efetuar uma instalação à escala utilizando um principal de serviço.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Segue-se um exemplo do comando para permitir a exploração de verbose com o agente máquina conectada para o Linux ao realizar uma instalação interativa.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Segue-se um exemplo do comando para permitir a exploração de verbose com o agente máquina conectada para o Linux quando efetuar uma instalação à escala utilizando um principal de serviço.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemas de ligação do agente ao serviço

A tabela que se segue enumera alguns dos erros e sugestões conhecidos sobre como resolver problemas e resolvê-los.

|Mensagem |Erro |Causa provável |Solução |
|--------|------|---------------|---------|
|Não adquiriu o fluxo de dispositivo de token de autorização |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Não pode chegar ao `login.windows.net` ponto final | Verifique a conectividade com o ponto final. |
|Não adquiriu o fluxo de dispositivo de token de autorização |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy ou firewall está bloqueando o acesso ao `login.windows.net` ponto final. | Verifique a conectividade com o ponto final e não esteja bloqueado por uma firewall ou servidor de procuração. |
|Não adquiriu ficha de autorização da SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy ou firewall está bloqueando o acesso ao `login.windows.net` ponto final. |Verifique a conectividade com o ponto final e não esteja bloqueado por uma firewall ou servidor de procuração. |
|Não adquiriu ficha de autorização da SPN |`Invalid client secret is provided` |Segredo principal do serviço errado ou inválido. |Verifique o segredo principal do serviço. |
| Não adquiriu ficha de autorização da SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |ID de serviço incorreto e/ou ID do inquilino. |Verifique o principal de serviço e/ou a identificação do inquilino.|
|Obtenha resposta de recursos ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenciais erradas e/ou permissões |Verifique se você ou o diretor de serviço é membro da **função Azure Connected Machine Onboarding.** |
|Falhou no recurso AzcmagentConnect ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Os fornecedores de recursos Azure não estão registados. |Registe os [fornecedores de recursos.](./agent-overview.md#register-azure-resource-providers) |
|Falhou no recurso AzcmagentConnect ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |O servidor ou firewall proxy está a bloquear o acesso ao `management.azure.com` ponto final. |Verifique a conectividade com o ponto final e não esteja bloqueado por uma firewall ou servidor de procuração. |

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através do [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.

* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.