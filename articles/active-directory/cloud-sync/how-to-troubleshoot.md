---
title: Azure AD Connect cloud sync resolução de problemas
description: Este artigo descreve como resolver problemas que podem surgir com o agente de provisão de nuvens.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530321"
---
# <a name="cloud-sync-troubleshooting"></a>Resolução de problemas de sincronização de nuvem

A sincronização da cloud abrange muitos aspetos diferentes e tem muitas dependências diferentes. Este âmbito amplo pode dar azo a vários problemas. Este artigo ajuda-o a resolver estes problemas. Apresenta as áreas típicas nas quais se deve focar, como reunir informações adicionais e as várias técnicas que pode utilizar para monitorizar problemas.


## <a name="common-troubleshooting-areas"></a>Áreas comuns de resolução de problemas

|Nome|Descrição|
|-----|-----|
|[Problemas com o agente](#agent-problems)|Verifique se o agente foi instalado corretamente e que comunica com o Azure Ative Directory (Azure AD).|
|[Problemas de sincronização de objetos](#object-synchronization-problems)|Utilize registos de provisionamento para resolver problemas de sincronização de objetos.|
|[Provisionamento de problemas em quarentena](#provisioning-quarantined-problems)|Compreenda o fornecimento de problemas de quarentena e como corrigi-los.|


## <a name="agent-problems"></a>Problemas com o agente
Algumas das primeiras coisas que quer verificar com o agente são:

-  Está instalado?
-  O agente está a correr localmente?
-  O agente está no portal?
-  O agente está marcado como saudável?

Estes itens podem ser verificados no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente do portal Azure

Para verificar se o agente é visto pela Azure e está saudável, siga estes passos.

1. Inicie sessão no portal do Azure.
1. À esquerda, selecione **Azure Ative Directory**  >  **Azure AD Connect**. No centro, **selecione Gerir a sincronização**.
1. No ecrã **de sincronização de nuvem Azure AD Connect,** selecione **Reveja todos os agentes**.

   ![Rever todos os agentes](media/how-to-install/install-7.png)</br>
 
1. No **ecrã dos agentes de provisionamento no local, vê-se** os agentes que instalou. Verifique se o agente em questão está lá e está marcado *como Saudável*.

   ![Tela de agentes de provisionamento no local](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Verifique a porta

Verifique se o Azure está a ouvir na porta 443 e que o seu agente pode comunicar com ele. 

Este teste verifica se os seus agentes podem comunicar com a Azure sobre a porta 443. Abra um browser e vá para o URL anterior a partir do servidor onde o agente está instalado.

![Verificação da capacidade de porta](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>No servidor local

Para verificar se o agente está a correr, siga estes passos.

1. No servidor com o agente instalado, abra **os Serviços** navegando para ele ou indo para **Start**  >  **Run**  >  **Services.msc**.
1. Em **Serviços**, certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estão lá e o seu estado está a *funcionar*.

   ![Tela de serviços](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Problemas de instalação de agentes comuns

As seguintes secções descrevem alguns problemas de instalação de agentes comuns e resoluções típicas.

#### <a name="agent-failed-to-start"></a>Agente não começou

Pode receber uma mensagem de erro que diz:

**O serviço 'Microsoft Azure AD Connect Provisioning Agent' não foi iniciado. Verifique se tem privilégios suficientes para iniciar os serviços do sistema.** 

Este problema é normalmente causado por uma política de grupo que impedia que as permissões fossem aplicadas à conta de login do Serviço NT local criada pelo instalador (NT SERVICE\AADConnectProvisioningAgent). Estas permissões são necessárias para iniciar o serviço.

Para resolver este problema, siga estes passos.

1. Inscreva-se no servidor com uma conta de administrador.
1. **Serviços abertos** navegando para ele ou indo para **Start**  >  **Run**  >  **Services.msc**.
1. Em **Serviços**, clique duas vezes **no Microsoft Azure AD Connect Provisioning Agent**.
1. No **separador Registar-se,** altere **esta conta** para um administrador de domínio. Em seguida, reinicie o serviço. 

   ![Faça login no separador](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>O tempo de saída do agente ou o certificado é inválido

Pode receber a seguinte mensagem de erro quando tentar registar o agente.

![Mensagem de erro de tempo de saída](media/how-to-troubleshoot/troubleshoot-4.png)

Normalmente, este problema é causado pelo facto de o agente não conseguir ligar ao Serviço de Identidade Híbrido e requer a configuração de um proxy HTTP. Para resolver este problema, configure um proxy de saída. 

O agente de provisionamento apoia o uso de um representante de saída. Pode configurgê-lo editando o ficheiro configurar o agente *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Adicione as seguintes linhas nele, para o final do ficheiro, pouco antes da `</configuration>` etiqueta de fecho.
Substitua as variáveis `[proxy-server]` e pelo nome do servidor `[proxy-port]` proxy e pelos valores da porta.

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

#### <a name="agent-registration-fails-with-security-error"></a>O registo do agente falha com erro de segurança

Pode receber uma mensagem de erro quando instalar o agente de provisionamento em nuvem.

Este problema é normalmente causado pelo facto de o agente não conseguir executar os scripts de registo PowerShell devido às políticas locais de execução do PowerShell.

Para resolver este problema, altere as políticas de execução powerShell no servidor. É necessário que as políticas da Máquina e do Utilizador tenham as políticas *indefinidas* ou *remotas.* Se estiverem definidos como *"Sem Restrições",* verás este erro. Para obter mais informações, consulte [as políticas de execução powerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>Ficheiros de registo

Por predefinição, o agente emite mensagens de erro mínimas e informações de rastreio de pilha. Pode encontrar estes registos de vestígios na pasta **C:\ProgramData\Microsoft\Azure AD Connect Provision Agent\Trace**.

Para reunir detalhes adicionais para resolver problemas relacionados com o agente, siga estes passos.

1.  Instale o módulo PowerShell AADCloudSyncTools, conforme descrito [aqui.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Utilize o `Export-AADCloudSyncToolsLogs` cmdlet PowerShell para capturar a informação.  Pode utilizar os seguintes interruptores para afinar a sua recolha de dados.
      - SkipVerboseTrace apenas para exportar registos atuais sem capturar registos verbosos (padrão = falso)
      - RastreioDurationMins para especificar uma duração de captura diferente (padrão = 3 minutos)
      - OutputPath para especificar uma trajetória de saída diferente (padrão = Documentos do utilizador)


## <a name="object-synchronization-problems"></a>Problemas de sincronização de objetos

A secção seguinte contém informações sobre a sincronização de objetos de resolução de problemas.

### <a name="provisioning-logs"></a>Registos de aprovisionamento

No portal Azure, os registos de provisionamento podem ser usados para ajudar a localizar e resolver problemas de sincronização de objetos. Para visualizar os registos, **selecione Registos**.

![Botão de registos](media/how-to-troubleshoot/log-1.png)

Os registos de provisão fornecem uma grande quantidade de informações sobre o estado dos objetos que estão a ser sincronizados entre o ambiente do Ative Directory e o Azure.

![Tela de registos de provisionamento](media/how-to-troubleshoot/log-2.png)

Pode utilizar as caixas de entrega no topo da página para filtrar a vista para zero em problemas específicos, tais como datas. Clique duas vezes num evento individual para ver informações adicionais.

![Informação sobre caixas de provisionamento de registos](media/how-to-troubleshoot/log-3.png)

Estas informações fornecem passos detalhados e onde está a ocorrer o problema de sincronização. Desta forma, pode identificar o local exato do problema.


## <a name="provisioning-quarantined-problems"></a>Provisionamento de problemas em quarentena

A sincronização da nuvem monitoriza a saúde da sua configuração e coloca objetos insalubres num estado de quarentena. Se a maioria ou todas as chamadas feitas contra o sistema alvo falharem consistentemente devido a um erro, por exemplo, credenciais de administração inválidas, o trabalho de sincronização está marcado como em quarentena.

![Estado de quarentena](media/how-to-troubleshoot/quarantine-1.png)

Ao selecionar o estado, pode ver informações adicionais sobre a quarentena. Também pode obter o código de erro e a mensagem.

![Screenshot que mostra informações adicionais sobre a quarentena.](media/how-to-troubleshoot/quarantine-2.png)

Clicar no estado irá trazer opções adicionais:
    
   - ver registos de provisionamento
   - agente ver
   - quarentena clara

![Screenshot que mostra as opções do menu de clique direito.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Resolva uma quarentena
Há duas maneiras diferentes de resolver uma quarentena.  A saber:

  - quarentena clara - limpa a marca de água e executa uma sincronização delta
  - reiniciar o trabalho de provisionamento - limpa a marca de água e executa uma sincronização inicial

#### <a name="clear-quarantine"></a>Quarentena clara
Para limpar a marca de água e executar uma sincronização delta no trabalho de provisionamento uma vez verificado, basta clicar com o direito no estado e selecionar **uma quarentena clara**.

Devia ver que a quarentena está a limpar.

![Screenshot que mostra o aviso de que a quarentena está a limpar.](media/how-to-troubleshoot/quarantine-5.png)

Então devia ver o estado do seu agente como saudável.

![Informações sobre o estado de quarentena](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Reiniciar o trabalho de provisionamento
Utilize o portal Azure para reiniciar o trabalho de provisionamento. Na página de configuração do agente, **selecione Recomeça o provisionamento**.

  ![Reiniciar o provisionamento](media/how-to-troubleshoot/quarantine-3.png)

- Utilize o Microsoft Graph para [reiniciar o trabalho de provisionamento](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Terá controlo total sobre o que reinicia. Pode optar por limpar:
  - Caugações, para reiniciar o contador de cauções que se acumula para o estado de quarentena.
  - Quarentena, para remover a aplicação da quarentena.
  - Marcas de água. 
  
  utilize o seguinte pedido:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Reparação da conta de serviço Cloud Sync
Se precisar de reparar a conta de serviço sincronizado na nuvem, pode utilizar o `Repair-AADCloudSyncToolsAccount` .  


   1.  Utilize os passos de instalação [aqui](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) descritos para começar e, em seguida, continuar com os passos restantes.
   2.  A partir de uma sessão Do Windows PowerShell com privilégios administrativos, escreva ou copie e cole o seguinte: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Insira as suas credenciais de administração global Azure AD
   4. Digite ou copie e cole o seguinte: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Uma vez concluída esta informação, deve dizer que a conta foi reparada com sucesso.

## <a name="next-steps"></a>Passos seguintes 

- [Limitações conhecidas](how-to-prerequisites.md#known-limitations)
- [Códigos de erro](reference-error-codes.md)
