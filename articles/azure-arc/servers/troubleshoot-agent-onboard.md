---
title: Resolução de problemas Azure Arc ativou problemas de ligação ao agente de servidores
description: Este artigo diz como resolver problemas e resolver problemas com o agente da Máquina Conectada que surgem com o Azure Arc ativado servidores ao tentar ligar-se ao serviço.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900685"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Resolução de problemas dos problemas de ligação do agente da máquina conectado

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que podem ocorrer enquanto tenta configurar o Azure Arc ativou o agente de máquinas conectadas para Windows ou Linux. Estão incluídos os métodos de instalação interativos e à escala ao configurar a ligação ao serviço. Para obter informações [gerais, consulte a visão geral dos servidores ativados pelo Arc](./overview.md).

## <a name="agent-verbose-log"></a>Registo verboso do agente

Antes de seguir as etapas de resolução de problemas descritas mais tarde neste artigo, a informação mínima necessária é o registo verboso. Contém a saída dos comandos da ferramenta **azcmagent,** quando o argumento verboso (v) é utilizado. Os ficheiros de registo são escritos `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` para Windows e Linux para `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Segue-se um exemplo do comando para permitir a realização de registos verbosos com o agente máquina conectada para o Windows durante a realização de uma instalação interativa.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Segue-se um exemplo do comando para permitir a realização de registos verbosos com o agente máquina conectada para windows quando efetuar uma instalação à escala utilizando um principal de serviço.

```console
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

>[!NOTE]
>Você deve ter permissões de acesso à *raiz* em máquinas Linux para executar **azcmagent**.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Segue-se um exemplo do comando para permitir a exploração de verbose com o agente máquina conectada para o Linux quando efetuar uma instalação à escala utilizando um principal de serviço.

```bash
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
|Não adquiriu o fluxo de dispositivo de token de autorização  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Configuração do computador de objeto de política *de grupo\ Modelos administrativos\ Sistema\ Perfis do utilizador\ Eliminar perfis de utilizador com mais de um número especificado de dias no reinício do sistema* está ativado. | Verifique se a GPO está ativada e direcionada para a máquina afetada. Consulte a nota de rodapé <sup>[1](#footnote1)</sup> para mais detalhes. |
|Não adquiriu ficha de autorização da SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy ou firewall está bloqueando o acesso ao `login.windows.net` ponto final. |Verifique a conectividade com o ponto final e não esteja bloqueado por uma firewall ou servidor de procuração. |
|Não adquiriu ficha de autorização da SPN |`Invalid client secret is provided` |Segredo principal do serviço errado ou inválido. |Verifique o segredo principal do serviço. |
| Não adquiriu ficha de autorização da SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |ID de serviço incorreto e/ou ID do inquilino. |Verifique o principal de serviço e/ou a identificação do inquilino.|
|Obtenha resposta de recursos ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenciais erradas e/ou permissões |Verifique se você ou o diretor de serviço é membro da **função Azure Connected Machine Onboarding.** |
|Falhou no recurso AzcmagentConnect ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Os fornecedores de recursos Azure não estão registados. |Registe os [fornecedores de recursos.](./agent-overview.md#register-azure-resource-providers) |
|Falhou no recurso AzcmagentConnect ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |O servidor ou firewall proxy está a bloquear o acesso ao `management.azure.com` ponto final. |Verifique a conectividade com o ponto final e não esteja bloqueado por uma firewall ou servidor de procuração. |

<a name="footnote1"></a><sup>1</sup> Se este GPO estiver ativado e se aplicar a máquinas com o agente Máquina Conectada, elimina o perfil do utilizador associado à conta incorporada especificada para o serviço *de hims.* Como resultado, também elimina o certificado de autenticação utilizado para comunicar com o serviço que está em cache na loja de certificados local por 30 dias. Antes do limite de 30 dias, é feita uma tentativa de renovar o certificado. Para resolver este problema, siga os passos para [não registar a máquina](manage-agent.md#unregister-machine) e, em seguida, reregistá-la com o serviço em funcionamento `azcmagent connect` .

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através do [Microsoft Q&A](/answers/topics/azure-arc.html).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.

* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
