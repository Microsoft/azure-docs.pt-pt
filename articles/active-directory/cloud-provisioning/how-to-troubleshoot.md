---
title: Solução de problemas de provisionamento de nuvem Azure AD Connect
description: Este documento descreve como solucionar problemas que podem surgir com o agente de provisionamento de nuvem.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794240"
---
# <a name="cloud-provisioning-troubleshooting"></a>Solução de problemas de provisionamento de nuvem

O provisionamento de nuvem atinge muitas coisas diferentes e tem várias dependências diferentes.  Naturalmente, isso pode dar ao aumento de vários problemas.  Este documento foi projetado para ajudá-lo a começar a solucionar esses problemas.  Este documento apresentará a você as áreas típicas nas quais você deve se concentrar, como reunir informações adicionais e várias técnicas que podem ser usadas para rastrear problemas.  


## <a name="common-troubleshooting-areas"></a>Áreas de solução de problemas comuns

|Nome|Descrição|
|-----|-----|
|[Problemas do agente](#agent-issues)|Verifique se o agente foi instalado corretamente e se está se comunicando com o Azure AD.|
|[Problemas de sincronização de objeto](#object-synchronization-issues)|Use os logs de provisionamento para solucionar problemas de sincronização de objetos.|
|[Problemas de provisionamento de quarentena](#provisioning-quarantined-issues)|Entenda o problema de quarentena de provisionamento e como corrigi-los.|


## <a name="agent-issues"></a>Problemas do agente
Algumas das primeiras coisas que você deseja verificar com o agente são:


-  Ele está instalado?
-  o agente está em execução localmente?
-  o agente está no portal?
-  o agente está marcado como íntegro?  

Esses itens podem ser verificados no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure

Para verificar se o agente está sendo visto pelo Azure e se está íntegro, siga estas etapas:

1. Inicie sessão no Portal do Azure.
2. À esquerda, selecione **Azure Active Directory**, clique em **Azure ad Connect** e, no centro, selecione **gerenciar provisionamento (versão prévia)** .
3.  Na tela **provisionamento do Azure AD (versão prévia)** , clique em **examinar todos os agentes**.
 ![o provisionamento do Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **tela agentes de provisionamento locais** , você verá os agentes que você instalou.  Verifique se o agente em questão está lá e está marcado como **íntegro**.
 ![agentes de provisionamento](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verificar a porta

Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, você pode usar a seguinte ferramenta:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verificará se os agentes podem se comunicar com o Azure pela porta 443.  Abra um navegador e navegue até a URL acima do servidor em que o agente está instalado.
 ![Serviços](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local

Para verificar se o agente está em execução, siga estas etapas:

1.  No servidor com o agente instalado, abra **Serviços** navegando até ele ou acessando iniciar/executar/Services. msc.
2.  Em **Serviços**, verifique se **Microsoft Azure ad atualizador do agente** e **Microsoft Azure ad agente de provisionamento do Connect** estão lá e se o status está **em execução**.
 ![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Problemas comuns de instalação do agente

Veja a seguir alguns problemas comuns de instalação do agente e qual é a resolução típica.

#### <a name="agent-failed-to-start"></a>Falha ao iniciar o agente

Se você receber uma mensagem de erro que declara:

**Falha ao iniciar o serviço ' Microsfoft Azure AD Connect agente de provisionamento '.  Verifique se você tem privilégios suficientes para iniciar os serviços do sistema.** 

Isso geralmente é causado por uma política de grupo que impediu que as permissões fossem aplicadas ao serviço NT local "conta de logon" criado pelo instalador (NT SERVICE\AADConnectProvisioningAgent) essas permissões são necessárias para iniciar o serviço.

Para resolver isso, use as seguintes etapas:

1.  Faça logon no servidor com uma conta de administrador
2.  Abra os **Serviços** navegando até ele ou acessando iniciar/executar/Services. msc.
3.  Em **Serviços** , clique duas vezes em **Microsoft Azure ad conectar agente de provisionamento**
4. Na guia, altere a "conta de logon" para um administrador de domínio e reinicie o serviço. 

 ![Serviços](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>O agente atinge o tempo limite ou o certificado é inválido

Você poderá receber os erros a seguir se estiver tentando registrar o agente.

 ![Serviços](media/how-to-troubleshoot/troubleshoot4.png)

Isso geralmente é causado pelo agente que está sendo incapaz de se conectar ao serviço de identidade híbrida e requer a configuração de proxy HTTP.  Para resolver isso, configure um proxy de saída. 

O agente de provisionamento dá suporte ao uso de proxy de saída. Você pode configurá-lo editando o arquivo de configuração do agente **C:\Program Files\Microsoft Azure ad Connect Provisionando Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas a ela, até o final do arquivo antes da marca de fechamento `</configuration>`.
Substitua as variáveis [Proxy-Server] e [proxy-Port] pelo nome do servidor proxy e pelos valores de porta.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Falha no registro do agente com erro de segurança

Você pode ver o erro a seguir ao instalar o agente de provisionamento de nuvem.

Isso geralmente é causado pelo agente não conseguir executar os scripts de registro do PowerShell devido a políticas de execução do PowerShell local.

Para resolver isso, altere as políticas de execução do PowerShell no servidor. Você precisa ter políticas de computador e de usuário como "indefinido" ou "RemoteSigned". Se for "irrestrito", você verá esse erro.  Para obter mais informações, consulte [políticas de execução do PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Ficheiros de registo

Por padrão, o agente emite mensagens de erro muito mínimas e informações de rastreamento de pilha. Você pode encontrar esses logs de rastreamento na pasta: **C:\PROGRAMDATA\MICROSOFT\AZURE ad Connect Provisioning Agent\Trace**

Use as etapas a seguir para coletar detalhes adicionais para solucionar problemas relacionados ao agente.

1. Parar o serviço **Microsoft Azure ad conectar o agente de provisionamento**
2. Crie uma cópia do arquivo de configuração original: **c:\Arquivos de programas\microsoft Azure ad Connect provisionamento Agent\AADConnectProvisioningAgent.exe.config**
3. Substitua a seção existente do < System. Diagnostics > pelo seguinte e todas as mensagens de rastreamento vão para o arquivo **ProvAgentTrace. log**

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. Iniciar o agente de provisionamento do Service **Microsoft Azure ad Connect**
5. Você pode usar o seguinte comando para encaudar os problemas de arquivo e depuração: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problemas de sincronização de objeto

A seção a seguir contém informações sobre como solucionar problemas de sincronização de objeto.

### <a name="provisioning-logs"></a>Registos de aprovisionamento

No portal do Azure, os logs de provisionamento podem ser usados para ajudar a rastrear e solucionar problemas de sincronização de objetos.  Para exibir os logs, selecione **logs**.
 ![ndo logs de provisionamento](media/how-to-troubleshoot/log1.png)

Os logs de provisionamento fornecem uma infinidade de informações sobre o estado dos objetos que estão sendo sincronizados entre o ambiente do AD local e o Azure.

 ![Registos de aprovisionamento](media/how-to-troubleshoot/log2.png)

Você pode usar os menus suspensos na parte superior da página para filtrar a exibição para zero em problemas específicos, datas, etc.  Clicar duas vezes em um evento individual fornecerá informações detalhadas adicionais.

 ![Registos de aprovisionamento](media/how-to-troubleshoot/log3.png)

Essas informações fornecerão etapas detalhadas e onde o problema de sincronização está ocorrendo.  Portanto, permitindo que você tenha zero e identifique o ponto exato do problema.


## <a name="provisioning-quarantined-issues"></a>Provisionando problemas em quarentena

O provisionamento de nuvem monitora a integridade de sua configuração e coloca objetos não íntegros em um estado de "quarentena". Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento será marcado como em quarentena.

 ![Quarentena](media/how-to-troubleshoot/quarantine1.png)

Ao clicar no status, você poderá ver informações adicionais sobre a quarentena.  Você também pode obter o código de erro e a mensagem.

 ![Quarentena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Resolvendo uma quarentena

- Use o portal do Azure para reiniciar o trabalho de provisionamento. Na página configuração do agente, selecione **reiniciar provisionamento**.

  ![Quarentena](media/how-to-troubleshoot/quarantine3.png)

- Use Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reinicia. Você pode optar por limpar as caução (para reiniciar o contador de caução que se acumula para o status de quarentena), limpar a quarentena (para remover o aplicativo da quarentena) ou limpar as marcas d' água. Use a seguinte solicitação:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)



