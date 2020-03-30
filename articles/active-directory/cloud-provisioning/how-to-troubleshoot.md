---
title: Azure AD Connect em nuvem que aprovisiona problemas
description: Este artigo descreve como resolver problemas que podem surgir com o agente de fornecimento de nuvens.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549490"
---
# <a name="cloud-provisioning-troubleshooting"></a>Resolução de problemas de fornecimento de nuvens

O fornecimento de nuvens toca muitas coisas diferentes e tem muitas dependências diferentes. Este âmbito amplo pode dar origem a vários problemas. Este artigo ajuda-o a resolver estes problemas. Introduz as áreas típicas para que se concentre, como recolher informações adicionais e as várias técnicas que pode usar para rastrear problemas.


## <a name="common-troubleshooting-areas"></a>Áreas comuns de resolução de problemas

|Nome|Descrição|
|-----|-----|
|[Problemas de agente](#agent-problems)|Verifique se o agente foi instalado corretamente e que comunica com o Azure Ative Directory (Azure AD).|
|[Problemas de sincronização de objetos](#object-synchronization-problems)|Utilize registos de fornecimento para resolver problemas de sincronização de objetos.|
|[Aprovisionamento de problemas de quarentena](#provisioning-quarantined-problems)|Compreenda o fornecimento de problemas de quarentena e como corrigi-los.|


## <a name="agent-problems"></a>Problemas de agente
Algumas das primeiras coisas que quer verificar com o agente são:

-  Está instalado?
-  O agente está a correr localmente?
-  O agente está no portal?
-  O agente está marcado como saudável?

Estes itens podem ser verificados no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação de agente do portal Azure

Para verificar se o agente é visto pelo Azure e é saudável, siga estes passos.

1. Inicie sessão no Portal do Azure.
1. À esquerda, selecione **Azure Ative Directory** > **Azure AD Connect**. No centro, selecione **Gerir o provisionamento (pré-visualização)**.
1. No ecrã **de provisionamento de AD Azure (pré-visualização),** selecione **Rever todos os agentes**.

   ![Reveja todos os agentes](media/how-to-install/install7.png)</br>
 
1. No ecrã dos agentes de **provisionamento no local,** vê-se os agentes instalados. Verifique se o agente em questão está lá e está marcado *como Saudável.*

   ![Rastreio de agentes de provisionamento no local](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verifique a porta

Para verificar se o Azure está a ouvir na porta 443 e que o seu agente pode comunicar com ele, utilize a seguinte ferramenta:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Este teste verifica que os seus agentes podem comunicar com azure sobre a porta 443. Abra um navegador e vá para o URL anterior a partir do servidor onde o agente está instalado.

![Verificação da viabilidade portuária](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local

Para verificar se o agente está a funcionar, siga estes passos.

1. No servidor com o agente instalado, abra **os Serviços** navegando para ele ou **indo** > para Start**Run** > **Services.msc**.
1. No âmbito **dos Serviços,** certifique-se de que o **Microsoft Azure AD Connect Agent Updater** e o **Microsoft Azure AD Connect Provisioning Agent** estão lá e o seu estado está a *funcionar*.

   ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problemas de instalação de agentes comuns

As seguintes secções descrevem alguns problemas de instalação de agentes comuns e resoluções típicas.

#### <a name="agent-failed-to-start"></a>Agente não começou

Pode receber uma mensagem de erro que diz:

**O serviço 'Microsoft Azure AD Connect Provisioning Agent' não começou. Verifique se tem privilégios suficientes para iniciar os serviços do sistema.** 

Este problema é normalmente causado por uma política de grupo que impediu que as permissões fossem aplicadas à conta de log-on do Serviço NT local criada pelo instalador (NT SERVICE\AADConnectProvisioningAgent). Estas permissões são necessárias para iniciar o serviço.

Para resolver este problema, siga estes passos.

1. Inscreva-se no servidor com uma conta de administrador.
1. Serviços **abertos** navegando até ele ou **indo** > para Start**Run** > **Services.msc**.
1. Em **Serviços,** clique duas vezes no **Microsoft Azure AD Connect Provisioning Agent**.
1. No separador **Log On,** altere **esta conta** para um administrador de domínio. Em seguida, reiniciar o serviço. 

   ![Iniciar sessão no separador](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>O tempo de saída do agente ou o certificado é inválido

Pode receber a seguinte mensagem de erro quando tentar registar o agente.

![Mensagem de erro de tempo](media/how-to-troubleshoot/troubleshoot4.png)

Este problema é geralmente causado pelo facto de o agente não poder ligar-se ao Serviço de Identidade Híbrida e requer que configure um proxy HTTP. Para resolver este problema, configure um representante de saída. 

O agente de provisionamento apoia a utilização de um representante de saída. Pode configurá-lo editando o ficheiro *c:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Adicione as seguintes linhas, na extremidade do ficheiro, pouco antes da etiqueta de fecho. `</configuration>`
Substitua as `[proxy-server]` `[proxy-port]` variáveis e com o nome do servidor proxy e os valores de porta.

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

#### <a name="agent-registration-fails-with-security-error"></a>Registo do agente falha com erro de segurança

Pode receber uma mensagem de erro quando instalar o agente de fornecimento de nuvem.

Este problema é normalmente causado pelo facto de o agente não poder executar os scripts de registo powerShell devido às políticas locais de execução da PowerShell.

Para resolver este problema, altere as políticas de execução powerShell no servidor. É necessário definir as políticas de Máquina e Utilizador como *Indefinidas* ou *Remotamente Assinadas*. Se estiverem definidos como *ilimitados,* verás este erro. Para mais informações, consulte as políticas de [execução da PowerShell.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) 

### <a name="log-files"></a>Ficheiros de registo

Por padrão, o agente emite mensagens de erro mínimas e empilha informações de vestígios. Pode encontrar estes registos de rastreio na pasta *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

Para recolher detalhes adicionais para problemas relacionados com o agente de resolução de problemas, siga estes passos.

1. Pare o serviço **Microsoft Azure AD Connect Provisioning Agent**.
1. Crie uma cópia do ficheiro original da config: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Substitua a `<system.diagnostics>` secção existente com a seguinte, e todas as mensagens de rastreio irão para o ficheiro *ProvAgentTrace.log*.

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
1. Inicie o serviço **Microsoft Azure AD Connect Provisioning Agent**.
1. Utilize o seguinte comando para seguir os problemas de ficheiro e depuração. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemas de sincronização de objetos

A secção seguinte contém informações sobre a sincronização de objetos de resolução de problemas.

### <a name="provisioning-logs"></a>Registos de aprovisionamento

No portal Azure, os registos de fornecimento podem ser usados para ajudar a localizar e resolver problemas de sincronização de objetos. Para visualizar os registos, selecione **Registos**.

![Botão de logs](media/how-to-troubleshoot/log1.png)

Os registos de fornecimento fornecem uma grande quantidade de informação sobre o estado dos objetos que estão a ser sincronizados entre o seu ambiente de Diretório Ativo no local e o Azure.

![Tela de registos de provisionamento](media/how-to-troubleshoot/log2.png)

Pode utilizar as caixas de entrega na parte superior da página para filtrar a vista para zero em problemas específicos, como datas. Clique duas vezes num evento individual para ver informações adicionais.

![Fornecimento de informações sobre caixa de entrega de registos](media/how-to-troubleshoot/log3.png)

Esta informação fornece passos detalhados e onde o problema da sincronização está a ocorrer. Desta forma, pode identificar o local exato do problema.


## <a name="provisioning-quarantined-problems"></a>Aprovisionamento de problemas de quarentena

O fornecimento de nuvens monitoriza a saúde da sua configuração e coloca objetos pouco saudáveis em estado de quarentena. Se a maioria ou todas as chamadas efetuadas contra o sistema alvo falharem constantemente devido a um erro, por exemplo, credenciais de administração inválidas, o trabalho de provisionamento é marcado como em quarentena.

![Estado de quarentena](media/how-to-troubleshoot/quarantine1.png)

Ao selecionar o estado, pode ver informações adicionais sobre a quarentena. Também pode obter o código de erro e a mensagem.

![Informações sobre o estado de quarentena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Resolver uma quarentena

- Utilize o portal Azure para reiniciar o trabalho de provisionamento. Na página de configuração do agente, selecione **o fornecimento de Reiniciar**.

  ![Reinício do fornecimento](media/how-to-troubleshoot/quarantine3.png)

- Utilize o Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Terá supor o que reiniciar. Pode optar por limpar:
  - Escrow, para reiniciar o contador de caução que se acumula em relação ao estado de quarentena.
  - Quarentena, para remover a aplicação da quarentena.
  - Marcas de água. 
  
  utilize o seguinte pedido:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)



