---
title: Solução de problemas de provisionamento de nuvem Azure AD Connect
description: Este artigo descreve como solucionar problemas que podem surgir com o agente de provisionamento de nuvem.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549490"
---
# <a name="cloud-provisioning-troubleshooting"></a>Solução de problemas de provisionamento de nuvem

O provisionamento de nuvem atinge muitas coisas diferentes e tem várias dependências diferentes. Esse escopo amplo pode dar ao aumento de vários problemas. Este artigo ajuda você a solucionar esses problemas. Ele apresenta as áreas típicas para você se concentrar, como reunir informações adicionais e as várias técnicas que você pode usar para rastrear problemas.


## <a name="common-troubleshooting-areas"></a>Áreas de solução de problemas comuns

|Nome|Descrição|
|-----|-----|
|[Problemas do agente](#agent-problems)|Verifique se o agente foi instalado corretamente e se ele se comunica com o Azure Active Directory (Azure AD).|
|[Problemas de sincronização de objeto](#object-synchronization-problems)|Use os logs de provisionamento para solucionar problemas de sincronização de objetos.|
|[Provisionando problemas em quarentena](#provisioning-quarantined-problems)|Entenda o provisionamento de problemas de quarentena e como corrigi-los.|


## <a name="agent-problems"></a>Problemas do agente
Algumas das primeiras coisas que você deseja verificar com o agente são:

-  Ele está instalado?
-  O agente está em execução localmente?
-  O agente está no portal?
-  O agente está marcado como íntegro?

Esses itens podem ser verificados no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure

Para verificar se o agente é visto pelo Azure e está íntegro, siga estas etapas.

1. Inicie sessão no Portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure ad Connect**. No centro, selecione **gerenciar provisionamento (versão prévia)** .
1. Na tela **provisionamento do Azure AD (versão prévia)** , selecione **examinar todos os agentes**.

   ![Examinar todos os agentes](media/how-to-install/install7.png)</br>
 
1. Na tela **agentes de provisionamento locais** , você vê os agentes que você instalou. Verifique se o agente em questão está lá e está marcado como *íntegro*.

   ![Tela de agentes de provisionamento local](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verificar a porta

Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, use a seguinte ferramenta:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verifica se os agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor em que o agente está instalado.

![Verificação de acessibilidade de porta](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local

Para verificar se o agente está em execução, siga estas etapas.

1. No servidor com o agente instalado, abra **Serviços** navegando até ele ou acessando **iniciar** > **executar** > **Services. msc**.
1. Em **Serviços**, verifique se **Microsoft Azure ad atualizador do agente** e **Microsoft Azure ad agente de provisionamento do Connect** estão lá e se seu status está *em execução*.

   ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problemas comuns de instalação do agente

As seções a seguir descrevem alguns problemas comuns de instalação de agente e resoluções típicas.

#### <a name="agent-failed-to-start"></a>Falha ao iniciar o agente

Você pode receber uma mensagem de erro que declara:

**Falha ao iniciar o serviço ' Microsoft Azure AD conectar o agente de provisionamento '. Verifique se você tem privilégios suficientes para iniciar os serviços do sistema.** 

Esse problema geralmente é causado por uma política de grupo que impediu que as permissões fossem aplicadas à conta de logon do serviço NT local criada pelo instalador (NT SERVICE\AADConnectProvisioningAgent). Essas permissões são necessárias para iniciar o serviço.

Para resolver esse problema, siga estas etapas.

1. Entre no servidor com uma conta de administrador.
1. Abra os **Serviços** navegando até ele ou acessando **iniciar** > **executar** > **Services. msc**.
1. Em **Serviços**, clique duas vezes em **Microsoft Azure ad conectar agente de provisionamento**.
1. Na guia **fazer logon** , altere **essa conta** para um administrador de domínio. Em seguida, reinicie o serviço. 

   ![Guia fazer logon](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>O agente atinge o tempo limite ou o certificado é inválido

Você pode receber a seguinte mensagem de erro ao tentar registrar o agente.

![Mensagem de erro de tempo limite](media/how-to-troubleshoot/troubleshoot4.png)

Esse problema geralmente é causado pelo agente que não consegue se conectar ao serviço de identidade híbrida e exige que você configure um proxy HTTP. Para resolver esse problema, configure um proxy de saída. 

O agente de provisionamento dá suporte ao uso de um proxy de saída. Você pode configurá-lo editando o arquivo de configuração do agente *C:\Program Files\Microsoft Azure ad Connect Provisionando Agent\AADConnectProvisioningAgent.exe.config*. Adicione as seguintes linhas a ela, em direção ao final do arquivo logo antes da marca de fechamento `</configuration>`.
Substitua as variáveis `[proxy-server]` e `[proxy-port]` pelo nome do servidor proxy e pelos valores de porta.

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

Você pode receber uma mensagem de erro ao instalar o agente de provisionamento de nuvem.

Esse problema é geralmente causado pelo agente não poder executar os scripts de registro do PowerShell devido a políticas de execução do PowerShell local.

Para resolver esse problema, altere as políticas de execução do PowerShell no servidor. Você precisa ter políticas de computador e de usuário definidas como *indefinido* ou *RemoteSigned*. Se eles estiverem definidos como *irrestrito*, você verá esse erro. Para obter mais informações, consulte [políticas de execução do PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Ficheiros de registo

Por padrão, o agente emite mensagens de erro mínimas e informações de rastreamento de pilha. Você pode encontrar esses logs de rastreamento na pasta *C:\PROGRAMDATA\MICROSOFT\AZURE ad Connect Provisioning Agent\Trace*.

Para coletar detalhes adicionais para solucionar problemas relacionados ao agente, siga estas etapas.

1. Pare o serviço **Microsoft Azure ad conectar o agente de provisionamento**.
1. Crie uma cópia do arquivo de configuração original: *C:\Program Files\Microsoft Azure ad Connect Provisionando Agent\AADConnectProvisioningAgent.exe.config*.
1. Substitua a seção de `<system.diagnostics>` existente pelo seguinte, e todas as mensagens de rastreamento vão para o arquivo *ProvAgentTrace. log*.

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
1. Inicie o agente de provisionamento do Service **Microsoft Azure ad Connect**.
1. Use o comando a seguir para caudar os problemas de arquivo e depuração. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemas de sincronização de objeto

A seção a seguir contém informações sobre como solucionar problemas de sincronização de objeto.

### <a name="provisioning-logs"></a>Registos de aprovisionamento

No portal do Azure, os logs de provisionamento podem ser usados para ajudar a rastrear e solucionar problemas de sincronização de objetos. Para exibir os logs, selecione **logs**.

![Botão logs](media/how-to-troubleshoot/log1.png)

Os logs de provisionamento fornecem uma infinidade de informações sobre o estado dos objetos que estão sendo sincronizados entre seu ambiente de Active Directory local e o Azure.

![Tela de logs de provisionamento](media/how-to-troubleshoot/log2.png)

Você pode usar as caixas suspensas na parte superior da página para filtrar a exibição para zero em problemas específicos, como datas. Clique duas vezes em um evento individual para ver informações adicionais.

![Informações da caixa suspensa de logs de provisionamento](media/how-to-troubleshoot/log3.png)

Essas informações fornecem etapas detalhadas e onde o problema de sincronização está ocorrendo. Dessa forma, você pode identificar o ponto exato do problema.


## <a name="provisioning-quarantined-problems"></a>Provisionando problemas em quarentena

O provisionamento de nuvem monitora a integridade de sua configuração e coloca objetos não íntegros em um estado de quarentena. Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento será marcado como em quarentena.

![Estado de quarentena](media/how-to-troubleshoot/quarantine1.png)

Ao selecionar o status, você poderá ver informações adicionais sobre a quarentena. Você também pode obter o código de erro e a mensagem.

![Informações de status de quarentena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Resolver uma quarentena

- Use o portal do Azure para reiniciar o trabalho de provisionamento. Na página configuração do agente, selecione **reiniciar provisionamento**.

  ![Reiniciar o provisionamento](media/how-to-troubleshoot/quarantine3.png)

- Use Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reinicia. Você pode optar por limpar:
  - Caução, para reiniciar o contador de caução acumulado para o status de quarentena.
  - Quarentena, para remover o aplicativo da quarentena.
  - Marcas d' água. 
  
  utilize o seguinte pedido:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)



