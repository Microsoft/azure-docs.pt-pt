---
title: Problemas de resolução de problemas com o servidor de configuração durante a recuperação de desastres de VMware VMs e servidores físicos para O Azure usando a Recuperação do Site Azure [ Microsoft Docs
description: Este artigo fornece informações de resolução de problemas para a implementação do servidor de configuração para recuperação de desastres de VMware VMs e servidores físicos para o Azure, utilizando a Recuperação do Site Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 0383a512dfb7c2bb1ae2422b9ade1e3c7387a70c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478299"
---
# <a name="troubleshoot-configuration-server-issues"></a>Resolver problemas do servidor de configuração

Este artigo ajuda-o a resolver problemas quando implementa e gere o servidor de configuração de recuperação do [site Azure.](site-recovery-overview.md) O servidor de configuração funciona como um servidor de gestão. Utilize o servidor de configuração para configurar a recuperação de desastres de VMware VMware no local e servidores físicos para o Azure utilizando a Recuperação do Site. As seguintes secções discutem as falhas mais comuns que poderá experimentar quando adiciona um novo servidor de configuração e quando gere um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registo

A máquina de origem regista-se no servidor de configuração quando instala o agente de mobilidade. Pode desincomodá-lo durante este passo seguindo estas orientações:

1. Abra o ficheiro C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (A pasta ProgramData pode ser uma pasta escondida. Se não vir a pasta ProgramData, no File Explorer, no separador **'Ver',** na secção **Mostrar/ocultar,** selecione a caixa de verificação de **itens Ocultas.)** Falhas podem ser causadas por múltiplos problemas.

2. Procure a cadeia **Sem endereço IP válido encontrado**. Se a corda for encontrada:
   1. Verifique se o ID do anfitrião solicitado é o mesmo que o ID do hospedeiro da máquina de origem.
   2. Verifique se a máquina de origem tem pelo menos um endereço IP atribuído ao NIC físico. Para que o registo do agente com o servidor de configuração tenha sucesso, a máquina de origem deve ter pelo menos um endereço IP v4 válido atribuído ao NIC físico.
   3. Executar um dos seguintes comandos na máquina de origem para obter todos os endereços IP da máquina de origem:
      - Para janelas:`> ipconfig /all`
      - Para Linux:`# ifconfig -a`

3. Se não for encontrado o fio **Sem endereço IP válido** encontrado, procure a corda **Reason=>NULL**. Este erro ocorre se a máquina de origem utilizar um hospedeiro vazio para se registar com o servidor de configuração. Se a corda for encontrada:
    - Depois de resolver os problemas, siga as diretrizes no [Registo da máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para voltar a tentar o registo manualmente.

4. Se a **razão de cadeia=>NULO** não for encontrada, na máquina de origem, abra o Ficheiro C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (A pasta ProgramData pode ser uma pasta escondida. Se não vir a pasta ProgramData, no File Explorer, no separador **'Ver',** na secção **Mostrar/ocultar,** selecione a caixa de verificação de **itens Ocultas.)** Falhas podem ser causadas por múltiplos problemas. 

5. Procure o pedido de post de **corda: (7) - Não consegui ligar ao servidor**. Se a corda for encontrada:
    1. Resolva os problemas de rede entre a máquina de origem e o servidor de configuração. Verifique se o servidor de configuração é acessível a partir da máquina de origem utilizando ferramentas de rede como ping, traceroute ou um navegador web. Certifique-se de que a máquina de origem pode chegar ao servidor de configuração através da porta 443.
    2. Verifique se as regras de firewall na máquina de origem bloqueiam a ligação entre a máquina de origem e o servidor de configuração. Trabalhe com os administradores da sua rede para desbloquear quaisquer problemas de ligação.
    3. Certifique-se de que as pastas listadas na pasta de [Recuperação do Site excluem os programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) do software antivírus.
    4. Quando os problemas de rede forem resolvidos, tente novamente o registo seguindo as diretrizes no [Registo da máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se o pedido de post de **cordas: (7) - Não foi possível ligar ao servidor,** no mesmo ficheiro de registo, procurar o pedido de **cadeia: (60) - O certificado de pares não pode ser autenticado com certificados CA dado.** Este erro pode ocorrer porque o certificado de servidor de configuração expirou ou a máquina de origem não suporta protocolos TLS 1.0 ou posteriores. Também pode ocorrer se uma firewall bloquear a comunicação TLS entre a máquina de origem e o servidor de configuração. Se a corda for encontrada: 
    1. Para resolver, ligue-se ao endereço IP do servidor de configuração utilizando um navegador web na máquina de origem. Utilize o URI\/ \/ https:<\>endereço IP do servidor de configuração :443/. Certifique-se de que a máquina de origem pode chegar ao servidor de configuração através da porta 443.
    2. Verifique se quaisquer regras de firewall na máquina de origem precisam de ser adicionadas ou removidas para que a máquina de origem fale com o servidor de configuração. Devido à variedade de software de firewall que pode estar a ser usado, não podemos listar todas as configurações de firewall necessárias. Trabalhe com os administradores da sua rede para desbloquear quaisquer problemas de ligação.
    3. Certifique-se de que as pastas listadas na pasta de [Recuperação do Site excluem os programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) do software antivírus.  
    4. Depois de resolver os problemas, tente novamente o registo seguindo as seguintes diretrizes no [Registo da máquina de origem com o servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. No Linux, se o valor da\>plataforma em <INSTALLATION_DIR /etc/drscout.conf for corrompido, o registo falha. Para identificar este problema, abra o ficheiro /var/log/ua_install.log. Procure a configuração de aborto da cadeia, uma vez **que VM_PLATFORM valor é nulo ou não é VmWare/Azure**. A plataforma deve ser definida para **VmWare** ou **Azure**. Se o ficheiro drscout.conf for corrompido, recomendamos que [desinstale o agente](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) de mobilidade e, em seguida, reinstale o agente de mobilidade. Se a não instalação falhar, complete os seguintes passos: a. Abra o ficheiro Installation_Directory/desinstalar.sh e comente a chamada para a função **StopServices.**
    b. Abra o ficheiro Installation_Directory/Vx/bin/uninstall.sh e comente a chamada para a função **stop_services.**
    c. Abra o ficheiro Installation_Directory/Fx/desinstalar.sh e comentar toda a secção que está a tentar parar o serviço Fx.
    d. [Desinstale](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) o agente de mobilidade. Depois de uma desinstalação bem sucedida, reinicie o sistema e tente reinstalar o agente de mobilidade.

## <a name="installation-failure-failed-to-load-accounts"></a>Falha na instalação: Falha nas contas de carregamento

Este erro ocorre quando o serviço não consegue ler os dados da ligação de transporte quando está a instalar o agente de mobilidade e a registar-se com o servidor de configuração. Para resolver o problema, certifique-se de que o TLS 1.0 está ativado na sua máquina de origem.

## <a name="vcenter-discovery-failures"></a>falhas na descoberta do vCenter

Para resolver falhas de descoberta vCenter, adicione o servidor vCenter às definições de proxy da lista byPass. 

- Baixe a ferramenta PsExec [daqui](https://aka.ms/PsExec) para aceder ao conteúdo do utilizador do Sistema.
- Open Internet Explorer no conteúdo do utilizador do sistema executando a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicione as definições de procuração no Serviço IE e reinicie o serviço tmanssvc.
- Para configurar as definições de proxy da DRA, executar cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Em seguida, executar DRCONFIGURATOR. EXE /configure /AddBypassUrls (adicionar endereço IP/FQDN do servidor vCenter fornecido durante **o Configure vCenter Server/vSphere ESXi passo** do servidor de [configuração](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Alterar o endereço IP do servidor de configuração

Recomendamos vivamente que não altere o endereço IP de um servidor de configuração. Certifique-se de que todos os endereços IP atribuídos ao servidor de configuração são endereços IP estáticos. Não utilize endereços IP dHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Ficha saml é inválida

Para evitar este erro, certifique-se de que o tempo no relógio do seu sistema não é diferente da hora local em mais de 15 minutos. Volte a executar o instalador para concluir o registo.

## <a name="failed-to-create-a-certificate"></a>Falhou na criação de um certificado

Não é possível criar um certificado que seja necessário para autenticar a Recuperação do Site. Refaça a configuração depois de garantir que está a executar a configuração como administrador local.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Falha em ativar licença do Windows da AVALIAÇÃO Padrão do Servidor para o Padrão do Servidor

1. Como parte da implementação do servidor de Configuração através do OVF, é utilizada uma licença de avaliação, que é válida por 180 dias. Tens de ativar esta Licença antes que isto expire. Caso contrário, isto pode resultar num encerramento frequente do servidor de configuração e, assim, causar obstáculos às atividades de replicação.
2. Se não conseguir ativar a licença do Windows, contacte a equipa de [suporte do Windows](https://aka.ms/Windows_Support) para resolver o problema.

## <a name="register-source-machine-with-configuration-server"></a>Registe a máquina de origem com o servidor de configuração

### <a name="if-the-source-machine-runs-windows"></a>Se a máquina de origem tiver windows

Executar o seguinte comando na máquina de origem:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Definição | Detalhes
--- | ---
Utilização | UnificadaAgentConfigurator.exe /CSEndPoint <endereço\> IP do servidor de configuração /PassphraseFilePath <caminho de ficheiro de frase de passe\>
Registos de configuração do agente | Localizado em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
/PassphraseFilePath |  Obrigatório. A localização da frase-passe. Utilize qualquer caminho de ficheiro sem e não abertos à base de dados válido ou local.

### <a name="if-the-source-machine-runs-linux"></a>Se a máquina de origem correr linux

Executar o seguinte comando na máquina de origem:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Definição | Detalhes
--- | ---
Utilização | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <endereço\> IP do servidor de configuração -P <caminho de ficheiro de frase de passe\>
-i | Parâmetro obrigatório. Especifica o endereço IP do servidor de configuração. Utilize qualquer endereço IP válido.
-P |  Obrigatório. O caminho completo do ficheiro em que a frase-passe é guardada. Utilize qualquer pasta válida.

## <a name="unable-to-configure-the-configuration-server"></a>Incapaz de configurar o servidor de configuração

Se instalar aplicações que não o servidor de configuração na máquina virtual, poderá não conseguir configurar o alvo principal. 

O servidor de configuração deve ser um servidor de uma única finalidade e usá-lo como um servidor partilhado não é suportado. 

Para mais informações, consulte a configuração FAQ em Implementar um servidor de [configuração](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Remova as entradas velhas para itens protegidos da base de dados do servidor de configuração 

Para remover a máquina protegida no servidor de configuração, utilize os seguintes passos. 
 
1. Para determinar a máquina de origem e o endereço IP da entrada estanho: 

    1. Abra a cmdline MYSQL no modo administrador. 
    2. Execute os seguintes comandos. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Isto devolve a lista de máquinas registadas juntamente com os seus endereços IP e o último batimento cardíaco. Encontre o hospedeiro que tem pares de replicação.

2. Abra um pedido de comando elevado e navegue para C:\ProgramData\ASR\home\svsystems\bin. 
4. Para remover os detalhes dos anfitriões registados e as informações de entrada do servidor de configuração, execute o seguinte comando utilizando a máquina de origem e o endereço IP da entrada estagnada. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Se tiver uma entrada de servidor de origem de "OnPrem-VM01" com um endereço ip de 10.0.0.4, utilize o seguinte comando.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Reinicie os seguintes serviços na máquina de origem para voltar a registar-se com o servidor de configuração. 
 
    - Serviço de Aplicação InMage Scout
    - Agente VX de Escuteiro InMage - Sentinel/Posto Avançado

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>A atualização falha quando os serviços não param

A atualização do servidor de configuração falha quando determinados serviços não param. 

Para identificar o problema, navegue para C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile no servidor de configuração. Se encontrar erros que se seguem, utilize os passos abaixo para resolver o problema: 

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

Para resolver a questão:

Parar manualmente os seguintes serviços:

- cxprocessserver
- Agente VX de Escuteiro InMage – Sentinel/Posto Avançado, 
- Agente de Serviços de Recuperação do Microsoft Azure, 
- Serviço de Recuperação de Sites do Microsoft Azure, 
- tmansvc
  
Para atualizar o servidor de configuração, volte a executar a [configuração unificada.](service-updates-how-to.md#links-to-currently-supported-update-rollups)

## <a name="azure-active-directory-application-creation-failure"></a>Falha na criação de candidaturas do Azure Ative Directory

Não tem permissões suficientes para criar uma aplicação no Azure Ative Directory (AAD) utilizando o modelo [Open Virtualization Application (OVA).](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
)

Para resolver o problema, inscreva-se no portal Azure e faça um dos seguintes:

- Solicite o papel de Desenvolvedor de Aplicações em AAD. Para obter mais informações sobre o papel de Desenvolvedor de Aplicações, consulte [permissões de funções de administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Verifique se o Utilizador pode criar uma bandeira de **aplicação** está definida como *verdadeira* em AAD. Para mais informações, consulte [Como: Utilize o portal para criar uma aplicação e um diretor de serviço seletiva seletiva que possam aceder a recursos.](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>O servidor de processo/Alvo Principal não consegue comunicar com o servidor de configuração 

Os módulos do servidor de processos (PS) e Master Target (MT) não conseguem comunicar com o servidor de configuração (CS) e o seu estado é mostrado como não ligado no portal Azure.

Normalmente, isto deve-se a um erro com a porta 443. Utilize os seguintes passos para desbloquear a porta e reativar a comunicação com o CS.

**Verifique se o agente MARS está a ser invocado pelo agente Master Target**

Para verificar se o Agente Alvo Principal pode criar uma sessão de TCP para o IP do servidor de configuração, procure um traço semelhante ao seguinte nos registos do agente Alvo Principal:

TCP \<Substitua IP por CS IP aqui \<>:52739 Substitua IP por CS IP aqui>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT // Substitua ip por CS IP aqui

Se encontrar vestígios semelhantes aos seguintes registos de agentes mT, o Agente MT está a reportar erros na porta 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Este erro pode ser encontrado quando outras aplicações também estão a utilizar a porta 443 ou devido a uma definição de firewall que bloqueia a porta.

Para resolver a questão:

- Verifique se a porta 443 não está bloqueada pela sua firewall.
- Se a porta estiver inacessível devido a outra aplicação que utilize essa porta, pare e desinstale a aplicação.
  - Se parar a aplicação não for viável, configurar um novo CS limpo.
- Reiniciar o servidor de configuração.
- Reinicie o serviço IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>O servidor de configuração não está ligado devido a entradas uUID incorretas

Este erro pode ocorrer quando existem várias entradas de UUID no servidor de configuração (CS) na base de dados. O problema ocorre frequentemente quando clona o VM do servidor de configuração.

Para resolver a questão:

1. Remova o VM CS velho/velho do vCenter. Para mais informações, consulte [Remover servidores e desativar a proteção](site-recovery-manage-registration-and-protection.md).
2. Inscreva-se no VM do servidor de configuração e ligue-se à base de dados MySQL svsdb1. 
3. Executar a seguinte consulta:

    > [!IMPORTANT]
    >
    > Verifique se está a introduzir os detalhes do UUID do servidor de configuração clonado ou a entrada em altura do servidor de configuração que já não é utilizado para proteger as máquinas virtuais. A introdução de um UUID incorreto resultará na perda da informação para todos os itens protegidos existentes.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Refresque a página do portal.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Um sinal infinito em loop ocorre ao introduzir as suas credenciais

Depois de introduzir o nome de utilizador e a palavra-passe corretos no servidor de configuração OVF, o sinal do Azure continua a ser solicitado para obter as credenciais corretas.

Este problema pode ocorrer quando o tempo do sistema está errado.

Para resolver a questão:

Detete o tempo correto no computador e tente novamente a placa. 
 