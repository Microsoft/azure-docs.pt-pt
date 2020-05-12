---
title: Sessão de desktop virtual do Windows - Azure
description: Como resolver problemas quando estiver a configurar as máquinas virtuais do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: eeccf0031e28bdcb719c0d534874d2c240ba46d3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117432"
---
# <a name="session-host-virtual-machine-configuration"></a>Configuração da máquina virtual do anfitrião da sessão

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../troubleshoot-vm-configuration.md).

Utilize este artigo para resolver problemas que está a ter ao configurar as máquinas virtuais do Windows Virtual Desktop (VMs).

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="vms-are-not-joined-to-the-domain"></a>VMs não são unidos ao domínio

Siga estas instruções se tiver problemas em juntar VMs ao domínio.

- Junte-se ao VM manualmente utilizando o processo em [Juntar uma máquina virtual do Windows Server a um domínio gerido](../../active-directory-domain-services/join-windows-vm.md) ou utilizando o modelo de [união](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)de domínio .
- Tente pingar o nome de domínio da linha de comando na VM.
- Reveja a lista de mensagens de erro de grupo de domínio em Mensagens de Erro de [Resolução de Problemas](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Causa:** Houve uma tipografia feita quando as credenciais foram introduzidas nas correções de interface do Gestor de Recursos Azure.

**Correção:** Tome uma das seguintes ações para resolver.

- Adicione manualmente os VMs a um domínio.
- Reutilizar o modelo uma vez confirmadas as credenciais. Consulte [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell-2019.md).
- Junte-se a VMs a um domínio usando um modelo com [Junta um VM do Windows existente para domínio AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Timeout à espera da entrada do utilizador

**Causa:** A conta utilizada para completar a adesão ao domínio pode ter a autenticação de vários fatores (MFA).

**Correção:** Tome uma das seguintes ações para resolver.

- Remova temporariamente o MFA para a conta.
- Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta utilizada durante o provisionamento não tem permissões para completar a operação

**Causa:** A conta que está a ser usada não tem permissões para se juntar aos VMs no domínio devido à conformidade e regulamentos.

**Correção:** Tome uma das seguintes ações para resolver.

- Utilize uma conta que seja membro do grupo Administrador.
- Conceda as permissões necessárias para a conta que está a ser utilizada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: O nome do domínio não resolve

**Causa 1:** Os VMs estão numa rede virtual que não está associada à rede virtual (VNET) onde o domínio está localizado.

**Correção 1:** Crie o vNET entre o VNET onde os VMs foram provisionados e o VNET onde o controlador de domínio (DC) está em execução. Ver Criar um peering de [rede virtual - Gestor de Recursos, diferentes subscrições](../../virtual-network/create-peering-different-subscriptions.md).

**Causa 2:** Ao utilizar os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS), a rede virtual não tem as definições do servidor DNS atualizadas para apontar para os controladores de domínio geridos.

**Correção 2:** Para atualizar as definições de DNS para a rede virtual que contém O DS Azure, consulte [as definições de DNS de atualização para a rede virtual Azure](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Causa 3:** As definições do servidor DNS da interface de rede não apontam para o servidor DNS apropriado na rede virtual.

**Correção 3:** Tome uma das seguintes ações para resolver, seguindo os passos em [Alterar servidores DNS].
- Altere as definições do servidor DNS da interface de rede para **Custom** com os passos dos [servidores DoDNs de alteração](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) e especifique os endereços IP privados dos servidores DNS na rede virtual.
- Altere as definições do servidor DNS da interface da rede para **Herdar da rede virtual** com os passos dos [servidores DNS da change](../../virtual-network/virtual-network-network-interface.md#change-dns-servers)e, em seguida, altere as definições do servidor DNS da rede virtual com os passos dos [servidores Change DNS](../../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Não estão instalados o Windows Virtual Desktop Agent e o Windows Virtual Desktop Boot Loader

A forma recomendada de fornecer VMs é usar o Modelo de Conjunto de Conjuntos de Anfitriões de Recursos Azure e fornecer o modelo de piscina de **alojamento virtual do Windows.** O modelo instala automaticamente o Agente de Ambiente de Trabalho Virtual do Windows e o Carregador de Boot do Agente de Ambiente de Trabalho Virtual do Windows.

Siga estas instruções para confirmar que os componentes estão instalados e para verificar se existem mensagens de erro.

1. Confirme que os dois componentes são instalados verificando os programas e funcionalidades **dos Programas**e  >  **Programs**  >  **Funcionalidades**do Painel de Controlo . Se o **Windows Virtual Desktop Agent** e o Windows Virtual Desktop Agent Boot **Loader** não estiverem visíveis, não estão instalados no VM.
2. Abra o Explorador de **Ficheiros** e navegue para **C:\Windows\Temp\ScriptLog.log**. Se o ficheiro estiver em falta, indica que o DSC PowerShell que instalou os dois componentes não foi capaz de ser executado no contexto de segurança fornecido.
3. Se o ficheiro **C:\Windows\Temp\ScriptLog.log** estiver presente, abra-o e verifique se existem mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Erro: O Agente virtual do Windows desktop e o Carregador de Boot do Agente de Ambiente de Trabalho Virtual do Windows estão em falta. C:\Windows\Temp\ScriptLog.log também está em falta

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo do Gestor de Recursos Azure foram incorretas ou as permissões eram insuficientes.

**Correção 1:** Adicione manualmente os componentes em falta aos VMs utilizando [criar uma piscina de anfitriões com powerShell](create-host-pools-powershell-2019.md).

**Causa 2:** O PowerShell DSC conseguiu iniciar e executar, mas não conseguiu concluir, uma vez que não consegue iniciar o Windows Virtual Desktop e obter as informações necessárias.

**Correção 2:** Confirme os itens na lista seguinte.

- Certifique-se de que a conta não tem MFA.
- Confirme que o nome do inquilino é preciso e que o inquilino existe no Windows Virtual Desktop.
- Confirme que a conta tem pelo menos permissões de colaboradorRDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Erro: Autenticação falhou, erro em C:\Windows\Temp\ScriptLog.log

**Causa:** O PowerShell DSC foi capaz de executar, mas não conseguiu ligar-se ao Windows Virtual Desktop.

**Correção:** Confirme os itens na lista seguinte.

- Registe manualmente os VMs com o serviço de ambiente de trabalho virtual Windows.
- Confirme que a conta utilizada para a ligação ao Windows Virtual Desktop tem permissões no inquilino para criar piscinas hospedeiras.
- Confirmar que a conta não tem MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>O Windows Virtual Desktop Agent não está a registar-se no serviço de desktop virtual do Windows

Quando o Windows Virtual Desktop Agent é instalado pela primeira vez em VMs de anfitrião de sessão (manualmente ou através do modelo de Gestor de Recursos Azure e DoC PowerShell), fornece um sinal de registo. A secção seguinte cobre problemas de resolução de problemas aplicáveis ao Windows Virtual Desktop Agent e ao token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O estado arquivado no Get-RdsSessionHost cmdlet mostra o estado como Indisponível

![Get-RdsSessionHost cmdlet mostra o estado como Indisponível.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se atualizar para uma nova versão.

**Correção:** Siga estas instruções para atualizar manualmente o agente.

1. Faça o download de uma nova versão do agente na vm anfitriã da sessão.
2. Gestor de Tarefas de Lançamento e, no Separador de Serviço, pare o serviço RDAgentBootLoader.
3. Execute o instalador para a nova versão do Windows Virtual Desktop Agent.
4. Quando solicitado para o sinal de registo, retire a INVALID_TOKEN de entrada e prima a seguir (não é necessário um novo símbolo).
5. Complete a instalação Assistente.
6. Open Task Manager e inicie o serviço RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro: Windows Virtual Desktop Agent registrado Registo mostra um valor de 0

**Causa:** O sinal de registo expirou ou foi gerado com o valor de validade de 999999.

**Correção:** Siga estas instruções para corrigir o erro de registo do agente.

1. Se já houver um sinal de registo, remova-o com Remove-RDSRegistrationInfo.
2. Gere um novo símbolo com rds-NewRegistrationInfo.
3. Confirme que o parâmetro -ExpriationHours está definido para 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: O agente do Windows Virtual Desktop não está a reportar um batimento cardíaco ao executar o Get-RdsSessionHost

**Causa 1:** O serviço RDAgentBootLoader foi interrompido.

**Correção 1:** Gestor de Tarefas de lançamento e, se o Separador de Serviço reportar um estado de paragem para o serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** O porto 443 pode estar fechado.

**Correção 2:** Siga estas instruções para abrir a porta 443.

1. A porta 443 confirma o download da ferramenta PSPing a partir de [ferramentas Sysinternal](/sysinternals/downloads/psping/).
2. Instale PSPing na sessão anfitrião VM onde o agente está em execução.
3. Abra o pedido de comando como administrador e emita o comando abaixo:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme que o PSPing recebeu informações do RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problemas de resolução de problemas com a stack windows virtual desktop lado a lado

A stack windows virtual desktop lado a lado é instalada automaticamente com o Windows Server 2019. Utilize o Microsoft Installer (MSI) para instalar a pilha lado a lado no Microsoft Windows Server 2016 ou no Windows Server 2012 R2. Para o Microsoft Windows 10, a stack windows virtual desktop lado a lado está ativada com **enablesxstackrs.ps1**.

Existem três formas principais de a pilha lado a lado ser instalada ou ativada em VMs de piscina de anfitrião da sessão:

- Com o Gestor de Recursos Azure Criar e fornecer novo modelo de **piscina de anfitrião do Windows Virtual Desktop**
- Por ser incluído e habilitado na imagem principal
- Instalado sem habilitação ou ativado manualmente em cada VM (ou com extensões/PowerShell)

Se estiver a ter problemas com a stack Windows Virtual Desktop lado a lado, escreva o comando **qwinsta** a partir do pedido de comando para confirmar que a pilha lado a lado está instalada ou ativada.

A saída de **qwinsta** listará **rdp-sxs** na saída se a pilha lado a lado for instalada e ativada.

![Pilha lado a lado instalada ou ativada com qwinsta listada como rdp-sxs na saída.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas de registo listadas abaixo e confirme se os seus valores correspondem. Se faltarem chaves de registo ou se os valores estiverem desajustados, siga as instruções em [Criar uma piscina de anfitriões com](create-host-pools-powershell-2019.md) a PowerShell sobre como reinstalar a pilha lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE código de erro.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha lado a lado não está instalada no VM anfitrião da sessão.

**Correção:** Siga estas instruções para instalar a pilha lado a lado no VM anfitrião da sessão.

1. Utilize o Protocolo de Ambiente de Trabalho Remoto (RDP) para entrar diretamente na vm anfitriãda da sessão como administrador local.
2. Descarregue e importe [O módulo PowerShell do Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez, então execute este cmdlet para iniciar sessão na sua conta:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Instale a pilha lado a lado utilizando [Criar uma piscina de anfitriões com powerShell](create-host-pools-powershell-2019.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma stack de ambiente de trabalho virtual do Windows lado a lado que avaria

Existem circunstâncias conhecidas que podem fazer com que a pilha lado a lado abomine:

- Não seguindo a ordem correta dos passos para ativar a pilha lado a lado
- Atualização automática para o Windows 10 Enhanced Versátil Disc (EVD)
- Faltando a função de anfitrião da sessão de ambiente de trabalho remoto (RDSH)
- Correr permite xsstackrc.ps1 várias vezes
- Correr permite xsstackrc.ps1 numa conta que não tem privilégios administrativos locais

As instruções nesta secção podem ajudá-lo a desinstalar a stack windows virtual desktop lado a lado. Assim que desinstalar a pilha lado a lado, vá para "Registar o VM com a piscina de anfitriões do Windows Virtual Desktop" em [Create a host pool com powerShell](create-host-pools-powershell-2019.md) para reinstalar a pilha lado a lado.

O VM utilizado para executar a reparação deve estar na mesma sub-rede e domínio que o VM com a pilha lado a lado avariada.

Siga estas instruções para executar a reparação a partir da mesma subnete e domínio:

1. Ligue-se ao Protocolo de Ambiente de Trabalho Remoto (RDP) padrão ao VM a partir do local onde será aplicada a correção.
2. Baixar PsExec a partir de https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Desaperte o ficheiro descarregado.
4. Inicie o pedido de comando como administrador local.
5. Navegue para pasta onde o PsExec foi desapertado.
6. A partir do pedido de comando, utilize o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname é o nome da máquina do VM com a pilha lado a lado avariada.

7. Aceite o Acordo de Licença PsExec clicando em Concordar.

    ![Screenshot do contrato de licença de software.](../media/SoftwareLicenseTerms.png)

    >[!Note]
    >Este diálogo só vai aparecer na primeira vez que o PsExec é executado.

8. Após a sessão de solicitação de comando abrir no VM com a pilha lateral avariada, executar qwinsta e confirmar que uma entrada chamada RDP-sxs está disponível. Caso contrário, uma pilha lado a lado não está presente no VM, por isso a questão não está ligada à pilha lado a lado.

    ![Pedido de comando do administrador](../media/AdministratorCommandPrompt.png)

9. Executar o seguinte comando, que listará os componentes da Microsoft instalados no VM com a pilha lado a lado avariada.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com os nomes do produto a partir de um passo acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Remote Desktop".

12. Depois de todos os componentes do Windows Virtual Desktop terem sido desinstalados, siga as instruções do seu sistema operativo:

13. Se o seu sistema operativo for o Windows Server, reinicie o VM que tinha a pilha lateral avariada (quer com o portal Azure quer com a ferramenta PsExec).

Se o seu sistema operativo for o Microsoft Windows 10, continue com as instruções abaixo:

14. Desde o VM que executa o PsExec, abra o File Explorer e copy desactivesxsstackrc.ps1 até à unidade do sistema do VM com a pilha lado a lado avariada.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome da máquina do VM com a pilha lado a lado avariada.

15. O processo recomendado: a partir da ferramenta PsExec, inicie a PowerShell e navegue até à pasta a partir do passo anterior e executar desativar desapilharc.ps1. Em alternativa, pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets terminarem de funcionar, reinicie o VM com a pilha lado a lado avariada.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>O modo de licenciamento remoto do Ambiente de Trabalho não está configurado

Se iniciar sessão no Windows 10 Enterprise multi-sessão através de uma conta administrativa, poderá receber uma notificação que diz: "O modo de licenciamento do Ambiente de Trabalho Remoto não está configurado, os Serviços de Ambiente de Trabalho Remoto deixarão de funcionar em X dias. No servidor 'Corretor de Ligação', utilize o Server Manager para especificar o modo de licenciamento do Ambiente de Trabalho Remoto."

Se o prazo expirar, aparecerá uma mensagem de erro que diz: "A sessão remota foi desligada porque não existem licenças de acesso ao cliente do Remote Desktop disponíveis para este computador."

Se vir alguma destas mensagens, isto significa que a imagem não tem as mais recentes atualizações do Windows instaladas ou que está a definir o modo de licenciamento do Ambiente de Trabalho Remoto através da política do grupo. Siga os passos nas secções seguintes para verificar a definição da política do grupo, identifique a versão da multi-sessão do Windows 10 Enterprise e instale a atualização correspondente.  

>[!NOTE]
>O Windows Virtual Desktop apenas requer uma licença de acesso ao cliente RDS (CAL) quando o seu pool de anfitriões contém os anfitriões da sessão do Windows Server. Para aprender a configurar um RDS CAL, consulte [Licencie a sua implementação RDS com licenças](/windows-server/remote/remote-desktop-services/rds-client-access-license/)de acesso ao cliente .

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Desative a definição de política do modo de licenciamento do ambiente de trabalho remoto

Verifique a definição da política do grupo abrindo o **Administrative Templates**Editor de Política do Grupo no VM e navegando para  >  **modelos administrativos Os componentes do Windows**Remote Desktop Services Remote  >  **Desktop**Session  >  **Hosting**set the Remote Desktop  >  **Licensing**  >  **Licensing Mode**. Se a definição de política do grupo estiver **ativada,** altere-a para **Desativada**. Se já está desativado, então deixe como está.

>[!NOTE]
>Se definir a política de grupo através do seu domínio, desative esta definição em políticas que visam estes VMs multissessões do Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identifique qual a versão do Windows 10 Enterprise multi-sessão que está a usar

Para verificar qual a versão do Windows 10 Enterprise multi-sessão que tem:

1. Inscreva-se na sua conta de administração.
2. Introduza "About" na barra de pesquisa ao lado do menu Iniciar.
3. Selecione **Sobre o seu PC**.
4. Verifique o número ao lado de "Versão". O número deve ser "1809" ou "1903", como mostra a seguinte imagem.

    ![Uma imagem da janela de especificações do Windows. O número da versão é realçado em azul.](../media/windows-specifications.png)

Agora que sabe o número da sua versão, avance para a secção relevante.

### <a name="version-1809"></a>Versão 1809

Se o seu número de versão diz "1809", instale [a atualização KB451607 .](https://support.microsoft.com/help/4516077)

### <a name="version-1903"></a>Versão 1903

Reutilizar o sistema operativo anfitrião com a versão mais recente do Windows 10, imagem da versão 1903 da Galeria Azure.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Não conseguimos ligar-nos ao PC remoto por causa de um erro de segurança.

Se os seus utilizadores virem um erro que diz: "Não conseguimos ligar-nos ao PC remoto devido a um erro de segurança. Se isso continuar a acontecer, peça ajuda ao seu administrador ou suporte técnico", validaas quaisquer políticas existentes que alterem as permissões padrão do PDR. Uma das políticas que pode causar a sua atenção neste erro é "Permitir iniciar sessão através da política de segurança dos Serviços de Secretária Remoto".

Para saber mais sobre esta política, consulte Iniciar sessão através dos [Serviços de Ambiente](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)de Trabalho Remoto .

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview-2019.md)de Resolução de Problemas.
- Para resolver problemas ao criar um inquilino e uma piscina de hospedas num ambiente de ambiente de trabalho virtual windows, consulte [tenant e host pool creation](troubleshoot-set-up-issues-2019.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration-2019.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection-2019.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](../troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup-2019.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](../../azure-resource-manager/templates/deployment-history.md).
