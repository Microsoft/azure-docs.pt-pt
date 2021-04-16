---
title: Resolução de problemas Azure Arc ativou problemas de ligação ao agente de servidores
description: Este artigo diz como resolver problemas e resolver problemas com o agente da Máquina Conectada que surgem com o Azure Arc ativado servidores ao tentar ligar-se ao serviço.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498004"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Resolução de problemas Azure Arc ativou problemas de ligação ao agente de servidores

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que podem ocorrer enquanto tenta configurar o Azure Arc ativou o agente de máquinas conectadas para Windows ou Linux. Estão incluídos os métodos de instalação interativos e à escala ao configurar a ligação ao serviço. Para obter informações [gerais, consulte a visão geral dos servidores ativados pelo Arc](./overview.md).

## <a name="agent-error-codes"></a>Códigos de erro do agente

Se receber um erro ao configurar o agente de servidores ativado pelo Arco Azure, a tabela seguinte pode ajudá-lo a identificar a causa provável e sugeriu passos para resolver o seu problema. Você precisará do `AZCM0000` código de erro ("0000" pode ser qualquer número de 4 dígitos) impresso na consola ou saída de script para prosseguir.

| Código de erro | Causa provável | Remediação sugerida |
|------------|----------------|-----------------------|
| AZCM0000 | A ação foi bem sucedida. | N/D |
| AZCM0001 | Ocorreu um erro desconhecido | Contacte o Microsoft Support para mais assistência |
| AZCM0011 | O utilizador cancelou a ação (CTRL+C) | Recandidutar o comando anterior |
| AZCM0012 | O sinal de acesso fornecido é inválido | Obtenha um novo token de acesso e tente novamente |
| AZCM0013 | As etiquetas fornecidas são inválidas | Verifique se as etiquetas estão incluídas em cotações duplas, separadas por vírgulas, e se quaisquer nomes ou valores com espaços estão incluídos em cotações únicas: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | A nuvem é inválida | Especificar uma nuvem suportada: `AzureCloud` ou `AzureUSGovernment` |
| AZCM0015 | O ID de correlação especificado não é um GUID válido | Fornecer um GUID válido para `--correlation-id` |
| AZCM0016 | Faltando um parâmetro obrigatório | Reveja a saída para identificar quais os parâmetros em falta |
| AZCM0017 | O nome do recurso é inválido | Especifique um nome que utilize apenas caracteres alfanuméricos, hífens e/ou sublinhados. O nome não pode terminar com um hífen ou sublinhado. |
| AZCM0018 | O comando foi executado sem privilégios administrativos. | Revendo o comando com privilégios de administrador ou raiz numa sessão de comando elevada ou consola. |
| AZCM0041 | As credenciais fornecidas são inválidas | Para os logins do dispositivo, verifique se a conta de utilizador especificada tem acesso ao arrendatário e subscrição onde o recurso do servidor será criado. Para os logins principais do serviço, verifique o ID do cliente e o segredo para a correção, a data de validade do segredo, e que o diretor de serviço é do mesmo inquilino onde o recurso do servidor será criado. |
| AZCM0042 | A criação do recurso do servidor ativado pelo Arco falhou | Verifique se o principal utilizador/serviço especificado tem acesso para criar recursos do servidor ativados pelo Arc no grupo de recursos especificado. |
| AZCM0043 | A eliminação do recurso do servidor ativado pelo Arco falhou | Verifique se o principal utilizador/serviço especificado tem acesso para eliminar os recursos do servidor ativados pelo Arc no grupo de recursos especificado. Se o recurso já não existir em Azure, utilize a `--force-local-only` bandeira para prosseguir. |
| AZCM0044 | Um recurso com o mesmo nome já existe | Especifique um nome diferente para o parâmetro ou elimine o servidor ativo arc `--resource-name` existente em Azure e tente novamente. |
| AZCM0061 | Incapaz de chegar ao serviço de agente | Verifique se está a executar o comando num contexto elevado de utilizador (administrador/raiz) e que o serviço HIMDS está a funcionar no seu servidor. |
| AZCM0062 | Ocorreu um erro ao ligar o servidor | Reveja outros códigos de erro na saída para obter informações mais específicas. Se o erro ocorreu após a criação do recurso Azure, é necessário eliminar o servidor Arc do seu grupo de recursos antes de voltar a tentar. |
| AZCM0063 | Ocorreu um erro ao desligar o servidor | Reveja outros códigos de erro na saída para obter informações mais específicas. Se continuar a encontrar este erro, pode eliminar o recurso em Azure e, em seguida, executar `azcmagent disconnect --force-local-only` no servidor para desligar o agente. |
| AZCM0064 | O serviço de agente não está a responder | Verifique o estado do `himds` serviço para se certificar de que está em funcionamento. Inicie o serviço se não estiver a funcionar. Se estiver a funcionar, espere um minuto e tente de novo. |
| AZCM0065 | Ocorreu um erro de comunicação do agente interno | Contacte o Microsoft Support para obter assistência |
| AZCM0066 | O serviço web do agente não está a responder ou indisponível | Contacte o Microsoft Support para obter assistência |
| AZCM0067 | O agente já está ligado ao Azure. | Siga os passos [para desligar o agente](manage-agent.md#unregister-machine) primeiro e, em seguida, tente novamente. |
| AZCM0068 | Ocorreu um erro interno ao desligar o servidor do Azure | Contacte o Microsoft Support para obter assistência |
| AZCM0081 | Ocorreu um erro ao descarregar o certificado de identidade gerido pelo Azure Ative Directory | Se esta mensagem for encontrada enquanto tenta ligar o servidor ao Azure, o agente não será capaz de comunicar com o serviço Azure Arc. Elimine o recurso em Azure e tente ligar novamente. |
| AZCM0101 | O comando não foi analisado com sucesso | Corra `azcmagent <command> --help` para rever a sintaxe de comando correta |
| AZCM0102 | Incapaz de recuperar o nome de anfitrião do computador | Corra `hostname` para verificar se há mensagens de erro ao nível do sistema e, em seguida, contacte o Microsoft Support. |
| AZCM0103 | Ocorreu um erro ao gerar teclas RSA | Contacte o Microsoft Support para obter assistência |
| AZCM0104 | Falhou na leitura da informação do sistema | Verifique se a identidade utilizada para executar `azcmagent` tem privilégios de administrador/raiz no sistema e tente novamente. |

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
