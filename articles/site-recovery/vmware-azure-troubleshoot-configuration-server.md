---
title: Resolver problemas com o servidor de configuração durante a recuperação após desastre de VMs de VMware e servidores físicos para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece informações de resolução de problemas para implementar o servidor de configuração para recuperação após desastre de VMs de VMware e servidores físicos para o Azure ao utilizar o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 3676a1e4bf69f7d31bb347f99787c4e2f08721a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107598"
---
# <a name="troubleshoot-configuration-server-issues"></a>Resolver problemas do servidor de configuração

Este artigo ajuda-o a resolver problemas ao implementar e gerir o [do Azure Site Recovery](site-recovery-overview.md) servidor de configuração. O servidor de configuração atua como um servidor de gestão. Utilize o servidor de configuração para configurar a recuperação após desastre de VMs de VMware no local e servidores físicos no Azure utilizando a recuperação de Site. As secções seguintes abordam as falhas mais comuns que podem ocorrer quando adiciona um novo servidor de configuração e na gestão de um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registo

A máquina de origem regista com o servidor de configuração quando instalar o agente de mobilidade. Pode depurar quaisquer falhas durante este passo ao seguir estas diretrizes:

1. Abra o ficheiro de C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (A pasta ProgramData pode ser uma pasta oculta. Se não vir a pasta ProgramData, no Explorador de ficheiros, no **View** separador a **Mostrar/ocultar** secção, selecione o **oculto itens** caixa de verificação.) Falhas podem ser causadas por vários problemas.

2. Procure a cadeia de caracteres **endereço IP válido não foram encontrados**. Se for encontrada a cadeia de caracteres:
   1. Certifique-se de que o ID de anfitrião pedido é o mesmo que o ID do anfitrião da máquina de origem.
   2. Certifique-se de que a máquina de origem tem, pelo menos, um endereço IP atribuído para o NIC físico. Para o registo do agente com o servidor de configuração com êxito, a máquina de origem tem de ter pelo menos um endereço de v4 IP válido atribuído para o NIC físico.
   3. Execute um dos seguintes comandos no computador de origem para obter todos os endereços IP da máquina de origem:
      - Para Windows: `> ipconfig /all`
      - Para Linux: `# ifconfig -a`

3. Se a cadeia de caracteres **endereço IP válido não encontrado** não for encontrada, procure a cadeia **motivo = > nulo**. Este erro ocorre se o computador de origem utilizar um anfitrião está vazio para registar com o servidor de configuração. Se for encontrada a cadeia de caracteres:
    - Depois de resolver os problemas, siga as diretrizes [registar a máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para repetir o registo manualmente.

4. Se a cadeia de caracteres **motivo = > nulo** não for encontrada, na máquina de origem, abra o ficheiro de C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (A pasta ProgramData pode ser uma pasta oculta. Se não vir a pasta ProgramData, no Explorador de ficheiros, no **View** separador a **Mostrar/ocultar** secção, selecione o **oculto itens** caixa de verificação.) Falhas podem ser causadas por vários problemas. 

5. Procure a cadeia **publicar pedido: (7) – não foi possível ligar ao servidor**. Se for encontrada a cadeia de caracteres:
    1. Resolva os problemas de rede entre o computador de origem e o servidor de configuração. Certifique-se de que o servidor de configuração está acessível a partir do computador de origem, utilizando ferramentas de rede, como o ping, traceroute ou um navegador da web. Certifique-se de que a máquina de origem pode contactar o servidor de configuração através da porta 443.
    2. Verifique se qualquer de regras de firewall no bloco de máquina de origem a ligação entre a máquina de origem e o servidor de configuração. Trabalhar com os administradores de rede para desbloquear os problemas de ligação.
    3. Certifique-se de que as pastas listadas na [exclusões de pastas de recuperação de sites de programas de antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) não inclui o software antivírus.
    4. Quando resolver problemas de rede, repita o registo ao seguir as diretrizes [registar a máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se a cadeia de caracteres **publicar pedido: (7) – não foi possível ligar ao servidor** não for encontrada, no mesmo ficheiro de registo, procure a cadeia **pedido: (60) - certificado de ponto a ponto não pode ser autenticado com fornecido certificados de AC**. Este erro pode ocorrer porque o certificado de servidor de configuração expirou ou a máquina de origem não suporta o TLS 1.0 ou posteriores protocolos SSL. Também poderá ocorrer se um firewall bloquear a comunicação SSL entre o computador de origem e o servidor de configuração. Se for encontrada a cadeia de caracteres: 
    1. Para resolver, ligue ao servidor de configuração endereço IP com um navegador da web na máquina de origem. Utilizar o URI https:\/\/< endereço IP do servidor de configuração\>: 443 /. Certifique-se de que a máquina de origem pode contactar o servidor de configuração através da porta 443.
    2. Verifique se quaisquer regras de firewall no computador de origem tem de ser adicionado ou removido máquina de origem comunicar com o servidor de configuração. Devido à variedade de software de firewall que poderá estar em utilização, nós não é possível listar todas as configurações de firewall necessárias. Trabalhar com os administradores de rede para desbloquear os problemas de ligação.
    3. Certifique-se de que as pastas listadas na [exclusões de pastas de recuperação de sites de programas de antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) não inclui o software antivírus.  
    4. Depois de resolver os problemas, e repita o registo seguintes diretrizes [registar a máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. No Linux, se o valor da plataforma no < INSTALLATION_DIR\>/etc/drscout.conf está danificado, registo falha. Para identificar este problema, abra o ficheiro de /var/log/ua_install.log. Procure a cadeia de caracteres **abortar a configuração como valor VM_PLATFORM é nulo ou não é o VmWare/Azure**. A plataforma deve ser definida para o **VmWare** ou **Azure**. Se o ficheiro drscout está danificado, recomendamos que [desinstalar o agente de mobilidade](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) e, em seguida, reinstale o agente de mobilidade. Se a desinstalação falhar, conclua os seguintes passos:
    1. Abra o ficheiro de Installation_Directory/uninstall.sh e comente a chamada para o **StopServices** função.
    2. Abra o ficheiro de Installation_Directory/Vx/bin/uninstall.sh e comente a chamada para o **stop_services** função.
    3. Abra o ficheiro de Installation_Directory/Fx/uninstall.sh e comente a secção de inteira que está a tentar parar o serviço de Fx.
    4. [Desinstalar](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) o agente de mobilidade. Após a desinstalação com êxito, reinicialize o sistema e, em seguida, tente reinstalar o agente de mobilidade.

## <a name="installation-failure-failed-to-load-accounts"></a>Falha na instalação: Falha ao carregar contas

Este erro ocorre quando o serviço não é possível ler dados a partir da ligação de transporte, quando está a instalar o agente de mobilidade e registar com o servidor de configuração. Para resolver o problema, certifique-se de que o TLS 1.0 está ativado no seu computador de origem.

## <a name="vcenter-discovery-failures"></a>falhas de deteção do vCenter

Para resolver falhas de deteção do vCenter, adicione o servidor de vCenter para as definições de proxy de lista de omissão. 

- Transferir a ferramenta PsExec da [aqui](https://aka.ms/PsExec) para aceder a conteúdo de utilizador do sistema.
- Abra o Internet Explorer no conteúdo de utilizador do sistema, executando a seguinte linha de comandos psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar definições de proxy no IE e reinicie o serviço de tmanssvc.
- Para configurar as definições de proxy do DRA, execute cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Em seguida, execute DRCONFIGURATOR. EXE / configurar /AddBypassUrls [adicionar o endereço de IP/FQDN do vCenter Server fornecido durante **configurar servidor vCenter Server/vSphere ESXi** passo [implementação de servidor de configuração](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Alterar o endereço IP do servidor de configuração

Recomendamos vivamente que não altere o endereço IP de um servidor de configuração. Certifique-se de que todos os endereços IP que estão atribuídos ao servidor de configuração são endereços IP estáticos. Não utilize endereços IP de DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML token é inválido

Para evitar este erro, certifique-se de que a hora no relógio do sistema não é diferente de na hora local em mais de 15 minutos. Volte a executar o instalador para concluir o registo.

## <a name="failed-to-create-a-certificate"></a>Falha ao criar um certificado

Não é possível criar um certificado necessário para autenticar o Site Recovery. Execute novamente a configuração depois de assegurar que está a executar o programa de configuração como administrador local.

## <a name="failure-to-activate-windows-licence-from-server-standard-evaluation-to-server-standard"></a>Falha ao ativar a licença do Windows da versão de avaliação padrão do servidor para o servidor Standard

1. Como parte da implementação de servidor de configuração através do OVF, é utilizada uma licença de avaliação, que é válido por 180 dias. Tem de ativar esta licença antes de este obtém a expirou. Caso contrário, isso pode resultar em frequente encerramento do servidor de configuração e, portanto, fazer com que hinderance para atividades de replicação.
2. Se é possível ativar a licença do Windows, contacte [equipa de suporte do Windows](https://aka.ms/Windows_Support) para resolver o problema.

## <a name="register-source-machine-with-configuration-server"></a>Registar a máquina de origem com o servidor de configuração

### <a name="if-the-source-machine-runs-windows"></a>Se a máquina de origem executar o Windows

Execute o seguinte comando na máquina de origem:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Definição | Detalhes
--- | ---
Utilização | UnifiedAgentConfigurator.exe /CSEndPoint < endereço IP do servidor de configuração\> /PassphraseFilePath < caminho de ficheiro da frase de acesso\>
Registos de configuração do agente | Localizado em % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. A localização da frase de acesso. Utilize qualquer válido UNC ou caminho de ficheiro local.

### <a name="if-the-source-machine-runs-linux"></a>Se a máquina de origem executar o Linux

Execute o seguinte comando na máquina de origem:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Definição | Detalhes
--- | ---
Utilização | CD /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i < endereço IP do servidor de configuração\> - P < caminho de ficheiro da frase de acesso\>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
-P |  Obrigatório. O caminho completo do ficheiro em que a frase de acesso é guardado. Utilize qualquer pasta válido.

## <a name="unable-to-configure-the-configuration-server"></a>Não é possível configurar o servidor de configuração

Se instalar aplicações que não o servidor de configuração na máquina virtual, poderá estar não é possível configurar o destino principal. 

O servidor de configuração tem de ser um servidor de objetivo único e usá-lo como um servidor compartilhado não é suportado. 

Para obter mais informações, veja a FAQ de configuração no [implementar um servidor de configuração](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Remover as entradas obsoletas dos itens protegidos a partir do banco de dados do servidor de configuração 

Para remover a máquina protegida obsoleta no servidor de configuração, utilize os seguintes passos. 
 
1. Para determinar a máquina de origem e o endereço IP da entrada obsoleta: 

    1. Abra o cmdline MYSQL no modo de administrador. 
    2. Execute os seguintes comandos. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Esta ação devolve a lista de máquinas registadas, juntamente com seus endereços IP e o último heartbeat. Localize o anfitrião que tenha de pares de replicação obsoletos.

2. Abra uma linha de comandos elevada e navegue para C:\ProgramData\ASR\home\svsystems\bin. 
4. Para remover os detalhes de anfitriões registados e as informações de entrada obsoleta do servidor de configuração, execute o seguinte comando com a máquina de origem e o endereço IP da entrada obsoleta. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Se tiver uma entrada de servidor de origem de "OnPrem VM01" com um ipaddress de 10.0.0.4 em seguida, utilize o seguinte comando em vez disso.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Reinicie os serviços seguintes no computador de origem para voltar a registar com o servidor de configuração. 
 
    - Serviço InMage Scout Application
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Falha de atualização quando os serviços não se parar

A atualização de servidor de configuração falha quando determinados serviços não parar. 

Para identificar o problema, navegue até ao C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile no servidor de configuração. Se encontrar seguintes erros, utilize os passos abaixo para resolver o problema: 

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

Pare manualmente os seguintes serviços:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost, 
- Agente de serviços de recuperação do Microsoft Azure, 
- Serviço do Microsoft Azure Site Recovery, 
- tmansvc
  
Para atualizar o servidor de configuração, execute o [a configuração unificada](service-updates-how-to.md#links-to-currently-supported-update-rollups) novamente.

## <a name="azure-active-directory-application-creation-failure"></a>Falha de criação de aplicações do Azure Active Directory

Não tem permissões suficientes para criar uma aplicação no Azure Active Directory (AAD) com o [aplicação de Virtualização aberto (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) modelo.

Para resolver o problema, inicie sessão no portal do Azure e faça o seguinte:

- Solicite a função de programador da aplicação no AAD. Para obter mais informações sobre a função de programador da aplicação, consulte [permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Certifique-se de que o **utilizador pode criar o aplicativo** sinalizador é definido como *verdadeiro* no AAD. Para obter mais informações, consulte [como: Utilizar o portal para criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Servidor/principal de processo destino não conseguem comunicar com o servidor de configuração 

O servidor de processos (PS) e módulos de destino mestre (TA) não conseguem comunicar com o servidor de configuração (CS) e o respetivo estado é mostrado como não ligado no portal do Azure.

Normalmente, isso é devido a um erro com a porta 443. Utilize os seguintes passos para desbloquear a porta e volte a ativar a comunicação com o CS.

**Certifique-se de que o agente MARS está sendo solicitado pelo agente de destino principal**

Para verificar que o agente de destino mestre pode criar uma sessão TCP para o IP do servidor de configuração, procure um rastreio semelhante ao seguinte nos registos do agente de destino principal:

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT / / IP substitua com o IP de CS aqui

Se encontrar rastreios semelhante ao seguinte nos registos do agente de MT, o agente de MT está a comunicar erros na porta 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Este erro pode ser encontrado durante a outros aplicativos também são através da porta 443 ou devido a uma definição de firewall a bloquear a porta.

Para resolver o problema:

- Certifique-se de que a porta 443 não está bloqueada pela firewall.
- Se a porta está inacessível devido a outro aplicativo usando essa porta, pare e desinstalar a aplicação.
  - Se não for possível parar o aplicativo, configure um novo CS limpo.
- Reinicie o servidor de configuração.
- Reinicie o serviço IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Servidor de configuração não está ligado devido a entradas UUID incorretas

Este erro pode ocorrer quando existem várias entradas de UUID de instância a (CS) do servidor de configuração na base de dados. O erro ocorre normalmente quando clona a VM do servidor de configuração.

Para resolver o problema:

1. Remova obsoleta/antigo CS VM do vCenter. Para obter mais informações, consulte [remover servidores e desativar proteção](site-recovery-manage-registration-and-protection.md).
2. Inicie sessão na VM do servidor de configuração e ligue à base de dados MySQL svsdb1. 
3. Execute a seguinte consulta:

    > [!IMPORTANT]
    >
    > Certifique-se de que se está a indicar os detalhes do UUID do servidor de configuração clonados ou a entrada obsoleta do servidor de configuração que já não é utilizado para proteger máquinas virtuais. Introduzir um UUID incorreto resultará na perda de informações para todos os itens protegidos existentes.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Atualize a página de portal.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Um sinal de infinito em loop ocorre quando introduzir as suas credenciais

Depois de introduzir o nome de utilizador correto e a palavra-passe no servidor de configuração OVF, Azure, iniciar sessão continua solicitar as credenciais corretas.

Este problema pode ocorrer quando a hora do sistema está incorrecta.

Para resolver o problema:

Defina a hora correta no computador e repita o início de sessão. 
 