---
title: Solucionar problemas com o servidor de configuração durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações de solução de problemas para implantar o servidor de configuração para recuperação de desastre de VMs VMware e servidores físicos no Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 25e2b488d3b6e7e5cabd1a71d1489efaf01231b3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748546"
---
# <a name="troubleshoot-configuration-server-issues"></a>Solucionar problemas do servidor de configuração

Este artigo ajuda você a solucionar problemas ao implantar e gerenciar o servidor de configuração do [Azure site Recovery](site-recovery-overview.md) . O servidor de configuração age como um servidor de gerenciamento. Use o servidor de configuração para configurar a recuperação de desastre de VMs VMware locais e servidores físicos no Azure usando Site Recovery. As seções a seguir discutem as falhas mais comuns que podem ocorrer quando você adiciona um novo servidor de configuração e quando gerencia um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registo

O computador de origem é registrado no servidor de configuração quando você instala o agente de mobilidade. Você pode depurar quaisquer falhas durante esta etapa seguindo estas diretrizes:

1. Abra o arquivo C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no explorador de arquivos, na guia **Exibir** , na seção **Mostrar/ocultar** , marque a caixa de seleção **itens ocultos** .) As falhas podem ser causadas por vários problemas.

2. Procure a cadeia de caracteres **nenhum endereço IP válido encontrado**. Se a cadeia de caracteres for encontrada:
   1. Verifique se a ID de host solicitada é igual à ID do host do computador de origem.
   2. Verifique se o computador de origem tem pelo menos um endereço IP atribuído à NIC física. Para que o registro do agente com o servidor de configuração tenha sucesso, o computador de origem deve ter pelo menos um endereço IP V4 válido atribuído à NIC física.
   3. Execute um dos seguintes comandos no computador de origem para obter todos os endereços IP do computador de origem:
      - Para Windows: `> ipconfig /all`
      - Para Linux: `# ifconfig -a`

3. Se a cadeia de caracteres **nenhum endereço IP válido encontrado** não for encontrada, procure o motivo da cadeia de caracteres **= > nulo**. Esse erro ocorrerá se o computador de origem usar um host vazio para se registrar no servidor de configuração. Se a cadeia de caracteres for encontrada:
    - Depois de resolver os problemas, siga as diretrizes em [registrar o computador de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para repetir o registro manualmente.

4. Se o motivo da cadeia de caracteres **= > NULL** não for encontrado, no computador de origem, abra o arquivo C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no explorador de arquivos, na guia **Exibir** , na seção **Mostrar/ocultar** , marque a caixa de seleção **itens ocultos** .) As falhas podem ser causadas por vários problemas. 

5. Pesquisar a solicitação post da cadeia de caracteres **: (7)-não foi possível conectar-se ao servidor**. Se a cadeia de caracteres for encontrada:
    1. Resolva os problemas de rede entre o computador de origem e o servidor de configuração. Verifique se o servidor de configuração pode ser acessado da máquina de origem usando ferramentas de rede como ping, traceroute ou navegador da Web. Verifique se o computador de origem pode acessar o servidor de configuração por meio da porta 443.
    2. Verifique se as regras de firewall no computador de origem bloqueiam a conexão entre o computador de origem e o servidor de configuração. Trabalhe com seus administradores de rede para desbloquear problemas de conexão.
    3. Certifique-se de que as pastas listadas em [site Recovery exclusões de pastas de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sejam excluídas do software antivírus.
    4. Quando os problemas de rede forem resolvidos, tente o registro novamente seguindo as diretrizes em [registrar o computador de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se a solicitação post da cadeia de caracteres **: (7)-não foi possível se conectar ao servidor** não for encontrada, no mesmo arquivo de log, procure a solicitação de cadeia de caracteres **: (60)-o certificado de mesmo nível não pode ser autenticado com determinados certificados de autoridade de certificação**. Esse erro pode ocorrer porque o certificado do servidor de configuração expirou ou o computador de origem não dá suporte a protocolos SSL TLS 1,0 ou posteriores. Também poderá ocorrer se um firewall bloquear a comunicação SSL entre o computador de origem e o servidor de configuração. Se a cadeia de caracteres for encontrada: 
    1. Para resolver, conecte-se ao endereço IP do servidor de configuração usando um navegador da Web no computador de origem. Use o URI https:\/\/< endereço IP do servidor de configuração\>: 443/. Verifique se o computador de origem pode acessar o servidor de configuração por meio da porta 443.
    2. Verifique se as regras de firewall na máquina de origem precisam ser adicionadas ou removidas para que o computador de origem se comunique com o servidor de configuração. Devido à variedade de softwares de firewall que podem estar em uso, não podemos listar todas as configurações de firewall necessárias. Trabalhe com seus administradores de rede para desbloquear problemas de conexão.
    3. Certifique-se de que as pastas listadas em [site Recovery exclusões de pastas de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sejam excluídas do software antivírus.  
    4. Depois de resolver os problemas, repita o registro seguindo as diretrizes em [registrar o computador de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. No Linux, se o valor da plataforma no < INSTALLATION_DIR\>/etc/drscout.conf estiver corrompido, o registro falhará. Para identificar esse problema, abra o arquivo/var/log/ua_install. log. Procure a configuração de anulação da cadeia de caracteres **, pois VM_PLATFORM valor é nulo ou não é o VMware/Azure**. A plataforma deve ser definida para o **VMware** ou o **Azure**. Se o arquivo drscout. conf estiver corrompido, recomendamos que você [desinstale o agente de mobilidade](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) e reinstale o agente de mobilidade. Se a desinstalação falhar, conclua as seguintes etapas: a. Abra o arquivo Installation_Directory/Uninstall.sh e comente a chamada para a função **StopServices** .
    b. Abra o arquivo Installation_Directory/VX/bin/Uninstall.sh e comente a chamada para a função **stop_services** .
    c. Abra o arquivo Installation_Directory/FX/Uninstall.sh e comente a seção inteira que está tentando parar o serviço FX.
    d. [Desinstale](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) o agente de mobilidade. Após a desinstalação bem-sucedida, reinicialize o sistema e Tente reinstalar o agente de mobilidade.

## <a name="installation-failure-failed-to-load-accounts"></a>Falha na instalação: falha ao carregar as contas

Esse erro ocorre quando o serviço não pode ler dados da conexão de transporte quando está instalando o agente de mobilidade e registrando-se com o servidor de configuração. Para resolver o problema, verifique se o TLS 1,0 está habilitado no computador de origem.

## <a name="vcenter-discovery-failures"></a>falhas de descoberta do vCenter

Para resolver as falhas de descoberta do vCenter, adicione o servidor do vCenter às configurações de proxy da lista de byPass. 

- Baixe a ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o conteúdo do usuário do sistema.
- Abra o Internet Explorer no conteúdo do usuário do sistema executando a seguinte linha de comando PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicione configurações de proxy no IE e reinicie o serviço tmanssvc.
- Para definir as configurações de proxy DRA, execute o CD C:\Program Files\Microsoft Azure Site Recovery Provider
- Em seguida, execute DRCONFIGURATOR. EXE/configure/AddBypassUrls [Adicionar endereço IP/FQDN do vCenter Server fornecido durante a etapa **configurar vCenter Server/vSphere ESXi Server** da [implantação do servidor de configuração](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Alterar o endereço IP do servidor de configuração

É altamente recomendável que você não altere o endereço IP de um servidor de configuração. Verifique se todos os endereços IP atribuídos ao servidor de configuração são endereços IP estáticos. Não use endereços IP de DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: token SAML inválido

Para evitar esse erro, verifique se a hora no relógio do sistema não é diferente da hora local em mais de 15 minutos. Volte a executar o instalador para concluir o registo.

## <a name="failed-to-create-a-certificate"></a>Falha ao criar um certificado

Não é possível criar um certificado necessário para autenticar Site Recovery. Execute a instalação novamente depois de garantir que você está executando a instalação como um administrador local.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Falha ao ativar a licença do Windows da avaliação standard do servidor para o servidor Standard

1. Como parte da implantação do servidor de configuração por meio do OVF, é usada uma licença de avaliação, que é válida por 180 dias. Você precisa ativar esta licença antes de expirar. Ou seja, isso pode resultar em um desligamento frequente do servidor de configuração e, portanto, causar empecilho às atividades de replicação.
2. Se não for possível ativar a licença do Windows, entre em contato com a [equipe de suporte do Windows](https://aka.ms/Windows_Support) para resolver o problema.

## <a name="register-source-machine-with-configuration-server"></a>Registrar computador de origem com o servidor de configuração

### <a name="if-the-source-machine-runs-windows"></a>Se o computador de origem executar o Windows

Execute o seguinte comando no computador de origem:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Definição | Detalhes
--- | ---
Utilização | UnifiedAgentConfigurator. exe/CSEndPoint < endereço IP do servidor de configuração\>/PassphraseFilePath < caminho do arquivo de senha\>
Logs de configuração do agente | Localizado em%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Use qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. O local da frase secreta. Use qualquer caminho de arquivo UNC ou local válido.

### <a name="if-the-source-machine-runs-linux"></a>Se o computador de origem executar o Linux

Execute o seguinte comando no computador de origem:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Definição | Detalhes
--- | ---
Utilização | CD/usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh-i < endereço IP do servidor de configuração\>-P < caminho do arquivo de frase secreta\>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Use qualquer endereço IP válido.
-P |  Obrigatório. O caminho de arquivo completo do arquivo no qual a frase secreta é salva. Use qualquer pasta válida.

## <a name="unable-to-configure-the-configuration-server"></a>Não é possível configurar o servidor de configuração

Se você instalar aplicativos diferentes do servidor de configuração na máquina virtual, talvez não seja possível configurar o destino mestre. 

O servidor de configuração deve ser um servidor de finalidade única e usá-lo como um servidor compartilhado não é suportado. 

Para obter mais informações, consulte as perguntas frequentes de configuração em [implantar um servidor de configuração](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Remover as entradas obsoletas de itens protegidos do banco de dados do servidor de configuração 

Para remover o computador protegido obsoleto no servidor de configuração, use as etapas a seguir. 
 
1. Para determinar o computador de origem e o endereço IP da entrada obsoleta: 

    1. Abra o MYSQL cmdline no modo de administrador. 
    2. Execute os comandos a seguir. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Isso retorna a lista de computadores registrados junto com seus endereços IP e a última pulsação. Localize o host que tem pares de replicação obsoletos.

2. Abra um prompt de comando com privilégios elevados e navegue até C:\ProgramData\ASR\home\svsystems\bin. 
4. Para remover os detalhes de hosts registrados e as informações de entrada obsoletas do servidor de configuração, execute o comando a seguir usando o computador de origem e o endereço IP da entrada obsoleta. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Se você tiver uma entrada do servidor de origem "onlocal-VM01" com um endereço IP de 10.0.0.4, em vez disso, use o comando a seguir.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Reinicie os seguintes serviços no computador de origem para se registrar novamente no servidor de configuração. 
 
    - Serviço de Aplicação InMage Scout
    - Agente do InMage Scout VX-sentinela/posto avançado

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>A atualização falha quando os serviços falham ao parar

A atualização do servidor de configuração falha quando determinados serviços não param. 

Para identificar o problema, navegue até C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile no servidor de configuração. Se você encontrar os seguintes erros, use as etapas abaixo para resolver o problema: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Para resolver o problema:

Interrompa manualmente os seguintes serviços:

- cxprocessserver
- Agente do InMage Scout VX – sentinela/posto avançado, 
- Agente de Serviços de Recuperação do Microsoft Azure, 
- Serviço de Site Recovery Microsoft Azure, 
- tmansvc
  
Para atualizar o servidor de configuração, execute a [instalação unificada](service-updates-how-to.md#links-to-currently-supported-update-rollups) novamente.

## <a name="azure-active-directory-application-creation-failure"></a>Falha na criação do aplicativo Azure Active Directory

Você não tem permissões suficientes para criar um aplicativo no Azure Active Directory (AAD) usando o modelo [OVA (aplicativo de virtualização aberta)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) .

Para resolver o problema, entre no portal do Azure e siga um destes procedimentos:

- Solicite a função de desenvolvedor do aplicativo no AAD. Para obter mais informações sobre a função de desenvolvedor de aplicativos, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Verifique se o sinalizador o **usuário pode criar aplicativo** está definido como *true* no AAD. Para obter mais informações, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Servidor de processo/destino mestre não é possível se comunicar com o servidor de configuração 

Os módulos do servidor de processo (PS) e de destino mestre (MT) não podem se comunicar com o servidor de configuração (CS) e seu status é mostrado como não conectado no portal do Azure.

Normalmente, isso se deve a um erro com a porta 443. Use as etapas a seguir para desbloquear a porta e reabilitar a comunicação com o CS.

**Verifique se o agente MARS está sendo invocado pelo agente de destino mestre**

Para verificar se o agente de destino mestre pode criar uma sessão TCP para o IP do servidor de configuração, procure um rastreamento semelhante ao seguinte nos logs do agente de destino mestre:

TCP \<substituir IP por IP CS aqui >: 52739 \<substituir IP por IP CS aqui >: 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//substituir IP por IP de CS aqui

Se você encontrar rastreamentos semelhantes aos seguintes nos logs do agente MT, o agente MT está relatando erros na porta 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Esse erro pode ser encontrado quando outros aplicativos também usam a porta 443 ou devido a uma configuração de firewall bloqueando a porta.

Para resolver o problema:

- Verifique se a porta 443 não está bloqueada pelo firewall.
- Se a porta estiver inacessível devido a outro aplicativo usando essa porta, pare e desinstale o aplicativo.
  - Se parar o aplicativo não for viável, configure um novo CS limpo.
- Reinicie o servidor de configuração.
- Reinicie o serviço IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>O servidor de configuração não está conectado devido a entradas incorretas do UUID

Esse erro pode ocorrer quando há várias entradas de UUID de instância do servidor de configuração (CS) no banco de dados. O problema geralmente ocorre quando você clona a VM do servidor de configuração.

Para resolver o problema:

1. Remova a VM do CS obsoleta/antiga do vCenter. Para obter mais informações, consulte [remover servidores e desabilitar a proteção](site-recovery-manage-registration-and-protection.md).
2. Entre na VM do servidor de configuração e conecte-se ao banco de dados MySQL svsdb1. 
3. Execute a seguinte consulta:

    > [!IMPORTANT]
    >
    > Verifique se você está inserindo os detalhes do UUID do servidor de configuração clonado ou a entrada obsoleta do servidor de configuração que não é mais usado para proteger as máquinas virtuais. Inserir um UUID incorreto resultará na perda das informações de todos os itens protegidos existentes.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Atualize a página do Portal.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Ocorre um loop de entrada infinito ao inserir suas credenciais

Depois de inserir o nome de usuário e a senha corretos no servidor de configuração OVF, a entrada do Azure continuará solicitando as credenciais corretas.

Esse problema pode ocorrer quando a hora do sistema está incorreta.

Para resolver o problema:

Defina a hora correta no computador e tente entrar novamente. 
 