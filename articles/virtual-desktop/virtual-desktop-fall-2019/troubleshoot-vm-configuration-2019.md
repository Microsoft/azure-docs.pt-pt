---
title: Resolução de problemas Windows Virtual Desktop (clássico) anfitrião da sessão - Azure
description: Como resolver problemas quando está a configurar o Windows Virtual Desktop (clássico) hospeda máquinas virtuais.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 90b4c574a03d8dee50beff60304fb5c1f3b52945
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008632"
---
# <a name="windows-virtual-desktop-classic-session-host-virtual-machine-configuration"></a>Windows Virtual Desktop (clássico) sessão anfitrião configuração de máquina virtual

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../troubleshoot-vm-configuration.md).

Utilize este artigo para resolver problemas que está a ter ao configurar as máquinas virtuais de sessão de desktop virtual do Windows (VMs).

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="vms-are-not-joined-to-the-domain"></a>VMs não são unidos ao domínio

Siga estas instruções se tiver problemas em juntar VMs ao domínio.

- Junte-se ao VM manualmente utilizando o processo em [Juntar uma máquina virtual do Windows Server a um domínio gerido](../../active-directory-domain-services/join-windows-vm.md) ou utilizando o modelo de união de [domínios](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente pingar o nome de domínio da linha de comando em VM.
- Reveja a lista de mensagens de erro de junção de domínio em [Mensagens de Erro de Resolução de Problemas](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Causa:** Houve um erro feito quando as credenciais foram inseridas nas correções de interface do Azure Resource Manager.

**Correção:** Tome uma das seguintes ações para resolver.

- Adicione manualmente os VMs a um domínio.
- Reimplantar o modelo uma vez confirmadas as credenciais. Consulte [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell-2019.md).
- Junte VMs a um domínio utilizando um modelo com [a Junção de um VM do Windows existente ao domínio AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Intervalo de tempo à espera da entrada do utilizador

**Causa:** A conta utilizada para completar a junção de domínio pode ter autenticação multi-factor (MFA).

**Correção:** Tome uma das seguintes ações para resolver.

- Remova temporariamente o MFA para a conta.
- Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta utilizada durante o provisionamento não tem permissões para completar a operação

**Causa:** A conta que está a ser utilizada não tem permissões para juntar VMs ao domínio devido ao cumprimento e regulamentos.

**Correção:** Tome uma das seguintes ações para resolver.

- Utilize uma conta que seja membro do grupo administrador.
- Conceda as permissões necessárias à conta que está a ser utilizada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: Nome de domínio não resolve

**Causa 1:** Os VMs estão numa rede virtual que não está associada à rede virtual (VNET) onde o domínio está localizado.

**Correção 1:** Crie o par VNET entre o VNET onde foram a provisionados VMs e o VNET onde o controlador de domínio (DC) está em funcionamento. Ver [Criar um espremo de rede virtual - Gestor de Recursos, diferentes subscrições](../../virtual-network/create-peering-different-subscriptions.md).

**Causa 2:** Ao utilizar os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS), a rede virtual não tem as definições do servidor DNS atualizadas para apontar para os controladores de domínio geridos.

**Correção 2:** Para atualizar as definições de DNS para a rede virtual que contém Azure AD DS, consulte [as definições de DNS de atualização para a rede virtual Azure](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Causa 3:** As definições do servidor DNS da interface de rede não apontam para o servidor DNS apropriado na rede virtual.

**Correção 3:** Tome uma das seguintes ações para resolver, seguindo os passos em [Alterar servidores DNS].
- Altere as definições do servidor DNS da interface de rede para **Personalizar** com os passos dos [servidores Change DNS](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) e especifique os endereços IP privados dos servidores DNS na rede virtual.
- Altere as definições do servidor DNS da interface de rede para **herdar a partir da rede virtual** com os passos dos [servidores Change DNS,](../../virtual-network/virtual-network-network-interface.md#change-dns-servers)em seguida, altere as definições do servidor DNS da rede virtual com os passos dos [servidores Change DNS](../../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop Agent e Windows Virtual Desktop Boot Loader não estão instalados

A forma recomendada de providenciar VMs está a usar o Azure Resource Manager **Create e o modelo de piscina virtual do Windows Desktop.** O modelo instala automaticamente o Windows Virtual Desktop Agent e o Windows Virtual Desktop Agent Boot Loader.

Siga estas instruções para confirmar a instalação dos componentes e para verificar se existem mensagens de erro.

1. Confirme que os dois componentes estão instalados através da verificação nos Programas e Funcionalidades **dos Programas do Painel de**  >  **Programs**  >  **Controlo.** Se **o Windows Virtual Desktop Agent** e o Windows Virtual Desktop Agent Boot **Loader** não estiverem visíveis, não estão instalados no VM.
2. Abra **o Explorador de Ficheiros** e navegue para **C:\Windows\Temp\ScriptLog.log**. Se o ficheiro faltar, indica que o DSC PowerShell que instalou os dois componentes não foi capaz de funcionar no contexto de segurança fornecido.
3. Se o ficheiro **C:\Windows\Temp\ScriptLog.log** estiver presente, abra-o e verifique se há mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Erro: O Windows Virtual Desktop Agent e o Windows Virtual Desktop Agent Boot Loader estão desaparecidos. C:\Windows\Temp\ScriptLog.log também está em falta

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo do Gestor de Recursos Azure estavam incorretas ou as permissões eram insuficientes.

**Correção 1:** Adicione manualmente os componentes em falta aos VMs utilizando [criar uma piscina de hospedeiro com PowerShell](create-host-pools-powershell-2019.md).

**Causa 2:** O PowerShell DSC foi capaz de iniciar e executar, mas não conseguiu completar, uma vez que não pode iniciar sôm no Windows Virtual Desktop e obter as informações necessárias.

**Correção 2:** Confirme os itens na lista a seguir.

- Certifique-se de que a conta não tem MFA.
- Confirme que o nome do inquilino é preciso e que o inquilino existe no Windows Virtual Desktop.
- Confirme que a conta tem pelo menos permissões de contribuinte RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Erro: A autenticação falhou, erro em C:\Windows\Temp\ScriptLog.log

**Causa:** O PowerShell DSC foi capaz de executar, mas não conseguiu ligar-se ao Windows Virtual Desktop.

**Correção:** Confirme os itens na lista a seguir.

- Registe manualmente os VMs com o serviço de ambiente de trabalho virtual do Windows.
- Confirme a conta utilizada para a ligação ao Windows Virtual Desktop tem permissões no arrendatário para criar piscinas hospedeiras.
- Confirmar a conta não tem MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>O Windows Virtual Desktop Agent não está a registar-se com o serviço de desktop virtual do Windows

Quando o Windows Virtual Desktop Agent é instalado pela primeira vez em VMs de anfitrião de sessão (manualmente ou através do modelo Azure Resource Manager e PowerShell DSC), fornece um token de registo. A secção seguinte abrange problemas de resolução de problemas aplicáveis ao Windows Virtual Desktop Agent e ao token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O estado arquivado no Get-RdsSessionHost cmdlet mostra o estado como Indisponível

> [!div class="mx-imgBorder"]
> ![O cmdlet Get-RdsSessionHost mostra o estado como Indisponível.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se atualizar para uma nova versão.

**Correção:** Siga estas instruções para atualizar manualmente o agente.

1. Faça o download de uma nova versão do agente no VM do anfitrião da sessão.
2. Launch Task Manager e, no Separador de Serviço, pare o serviço RDAgentBootLoader.
3. Execute o instalador para a nova versão do Windows Virtual Desktop Agent.
4. Quando solicitado para o token de inscrição, retire o INVALID_TOKEN de entrada e pressione em seguida (não é necessário um novo token).
5. Complete o assistente de instalação.
6. Abra o Gestor de Tarefas e inicie o serviço RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro: A entrada do registo do Agente virtual do Windows Desktop é registada mostra um valor de 0

**Causa:** O token de inscrição expirou ou foi gerado com o valor de validade de 999999.

**Correção:** Siga estas instruções para corrigir o erro de registo do agente.

1. Se já houver um token de registo, remova-o com Remove-RDSRegistrationInfo.
2. Gere um novo token com o Rds-NewRegistrationInfo.
3. Confirme que o parâmetro -ExpriationHours está definido para 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: O agente virtual do Windows não está a reportar um batimento cardíaco ao executar Get-RdsSessionHost

**Causa 1:** O serviço RDAgentBootLoader foi interrompido.

**Correção 1:** Launch Task Manager e, se o Separador de Serviço reportar um estado de paragem para o serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** O porto 443 pode estar fechado.

**Correção 2:** Siga estas instruções para abrir a porta 443.

1. Confirme que a porta 443 está aberta descarregando a ferramenta PSPing a partir de [ferramentas Sysinternal](/sysinternals/downloads/psping/).
2. Instale PSPing no VM do anfitrião da sessão onde o agente está em execução.
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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problemas de resolução de problemas com a pilha virtual do Windows Desktop lado a lado

A stack virtual do Windows Desktop lado a lado é automaticamente instalada com o Windows Server 2019. Utilize o Microsoft Installer (MSI) para instalar a pilha lado a lado no Microsoft Windows Server 2016 ou no Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha virtual do Windows Desktop lado a lado está ativada com **enablesxstackrs.ps1**.

Existem três formas principais de instalar ou ativar a pilha lado a lado na piscina de anfitriões da sessão:

- Com o Azure Resource Manager Criar e providenciar novo modelo **de piscina de anfitriões virtual do Windows Desktop**
- Por ser incluído e ativado na imagem principal
- Instalado ou ativado manualmente em cada VM (ou com extensões/PowerShell)

Se tiver problemas com a pilha virtual do Windows Desktop lado a lado, digite o comando **qwinsta** a partir da indicação de comando para confirmar que a pilha lado a lado está instalada ou ativada.

A saída de **qwinsta** listará **rdp-sxs** na saída se a pilha lado a lado for instalada e ativada.

> [!div class="mx-imgBorder"]
> ![Pilha lado a lado instalada ou ativada com qwinsta listada como rdp-sxs na saída.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas de registo listadas abaixo e confirme que os seus valores coincidem. Se faltam as teclas de registo ou se os valores estiverem desajustados, siga as instruções na [Criar uma piscina de anfitrião com o PowerShell](create-host-pools-powershell-2019.md) sobre como reinstalar a pilha lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE código de erro.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha lado a lado não está instalada no VM do anfitrião da sessão.

**Correção:** Siga estas instruções para instalar a pilha lado a lado no VM do anfitrião da sessão.

1. Utilize o Protocolo de Ambiente de Trabalho Remoto (RDP) para entrar diretamente no VM do anfitrião da sessão como administrador local.
2. Faça o download e importe [o módulo Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do Windows para utilizar na sessão PowerShell se ainda não o fez, em seguida, execute este cmdlet para iniciar sessão na sua conta:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Instale a pilha lado a lado utilizando [Criar uma piscina de anfitrião com PowerShell](create-host-pools-powershell-2019.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha virtual do Windows Desktop lado a lado que funciona mal

Existem circunstâncias conhecidas que podem causar o mau funcionamento da pilha lado a lado:

- Não seguindo a ordem correta dos passos para permitir a pilha lado a lado
- Atualização automática para o Windows 10 Disco Versátil Melhorado (EVD)
- Faltando o papel do Anfitrião da Sessão de Desktop Remoto (RDSH)
- Executando enablesxsstackrc.ps1 várias vezes
- Executar enablesxsstackrc.ps1 numa conta que não tem privilégios de administração local

As instruções desta secção podem ajudá-lo a desinstalar a pilha virtual do Windows Desktop lado a lado. Assim que desinstalar a pilha lado a lado, vá para "Registar o VM com o conjunto de anfitriões virtual do Windows" em [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell-2019.md) para reinstalar a pilha lado a lado.

O VM utilizado para executar a reparação deve estar na mesma sub-rede e domínio que o VM com a pilha avariada lado a lado.

Siga estas instruções para executar a remediação a partir da mesma sub-rede e domínio:

1. Ligue-se com o Protocolo de Ambiente de Trabalho Remoto padrão (RDP) ao VM de onde será aplicada a correção.
2. Descarregue o PsExec a partir de https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Desaperte o ficheiro descarregado.
4. Inicie o comando como administrador local.
5. Navegue para a pasta onde o PsExec estava desapertado.
6. A partir do comando, utilize o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname é o nome da máquina do VM com a pilha avariada lado a lado.

7. Aceite o Contrato de Licença PsExec clicando em Concordar.

    > [!div class="mx-imgBorder"]
    > ![Imagem de imagem do contrato de licença de software.](../media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Este diálogo aparecerá apenas na primeira vez que o PsExec for executado.

8. Após a sessão de solicitação de comando abrir no VM com a pilha avariada lado a lado, corra qwinsta e confirme que está disponível uma entrada chamada RDP-sxs. Caso contrário, uma pilha lado a lado não está presente no VM, por isso o problema não está ligado à pilha lado a lado.

    > [!div class="mx-imgBorder"]
    > ![Pedido de comando do administrador](../media/AdministratorCommandPrompt.png)

9. Executar o seguinte comando, que irá listar os componentes da Microsoft instalados no VM com a pilha avariada lado a lado.

    ```cmd
        wmic product get name
    ```

10. Executar o comando abaixo com os nomes do produto a partir do degrau acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Remote Desktop".

12. Depois de todos os componentes do Windows Virtual Desktop terem sido desinstalados, siga as instruções do seu sistema operativo:

13. Se o seu sistema operativo for o Windows Server, reinicie o VM que tinha a pilha avariada lado a lado (seja com o portal Azure ou a partir da ferramenta PsExec).

Se o seu sistema operativo for o Microsoft Windows 10, continue com as instruções abaixo:

14. Desde o VM que executa o PsExec, abra o File Explorer e copie disablesxsstackrc.ps1 para a unidade do sistema do VM com a pilha lateral avariada.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome da máquina do VM com a pilha avariada lado a lado.

15. O processo recomendado: a partir da ferramenta PsExec, inicie o PowerShell e navegue para a pasta a partir do passo anterior e corra disablesxsstackrc.ps1. Em alternativa, pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets estiverem a funcionar, reinicie o VM com a pilha lateral avariada.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>O modo de licenciamento remoto do ambiente de trabalho não está configurado

Se iniciar sessão multi-sção no Windows 10 Enterprise utilizando uma conta administrativa, poderá receber uma notificação que diz: "O modo de licenciamento de desktop remoto não está configurado, os Serviços de Desktop Remoto deixarão de funcionar em X dias. No servidor 'Corretor de Ligação', utilize o Gestor do Servidor para especificar o modo de licenciamento do Ambiente de Trabalho Remoto.

Se o prazo expirar, aparecerá uma mensagem de erro que diz: "A sessão remota foi desligada porque não existem licenças de acesso ao cliente de ambiente de trabalho remoto disponíveis para este computador."

Se vir qualquer uma destas mensagens, isto significa que a imagem não tem as atualizações mais recentes do Windows instaladas ou que está a definir o modo de licenciamento remote desktop através da política de grupo. Siga os passos nas próximas secções para verificar a definição da política de grupo, identifique a versão de várias sessões do Windows 10 Enterprise e instale a atualização correspondente.

>[!NOTE]
>O Windows Virtual Desktop só requer uma licença de acesso ao cliente RDS (CAL) quando o seu pool de anfitriões contiver anfitriões de sessão do Windows Server. Para aprender a configurar um RDS CAL, consulte [Licenciar a sua implementação RDS com licenças de acesso](/windows-server/remote/remote-desktop-services/rds-client-access-license/)ao cliente.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Desative a definição de política de grupo de modo de licenciamento de ambiente de trabalho remoto

Verifique a definição de política de grupo abrindo o Editor de Política de Grupo no VM e navegando para **os modelos**  >  **administrativos Windows Componentes Componentes**  >  **Remotos Desktop Services Remote Desktop**Session  >  **Host**  >  **Licensing**  >  **Definir o modo de licenciamento de ambiente de trabalho remoto**. Se a definição de política de grupo estiver **ativada,** altere-a para **Desativada**. Se já está desativado, então deixe-o como está.

>[!NOTE]
>Se definir a política de grupo através do seu domínio, desative esta definição em políticas que direcionem estes VMs multi-sessão do Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identifique qual versão da multi-sessão do Windows 10 Enterprise está a utilizar

Para verificar qual a versão da multi-sessão do Windows 10 Enterprise tem:

1. Inscreva-se na sua conta de administração.
2. Introduza "Sobre" na barra de pesquisa ao lado do menu Iniciar.
3. Selecione **Sobre o seu PC**.
4. Verifique o número ao lado de "Versão". O número deve ser "1809" ou "1903", como mostra a seguinte imagem.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da janela de especificações do Windows. O número da versão é realçado em azul.](../media/windows-specifications.png)

Agora que conhece o número da sua versão, avance para a secção relevante.

### <a name="version-1809"></a>Versão 1809

Se o seu número de versão disser "1809", instale [a atualização KB451607](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versão 1903

Recolocar o sistema operativo anfitrião com a versão mais recente do Windows 10, versão 1903 da Galeria Azure.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Não conseguimos ligar-nos ao PC remoto por causa de um erro de segurança.

Se os seus utilizadores virem um erro que diga: "Não conseguimos ligar-nos ao PC remoto devido a um erro de segurança. Se isto continuar a acontecer, peça ajuda ao seu administrador ou suporte técnico", valide quaisquer políticas existentes que alterem permissões RDP predefinidas. Uma política que pode causar a impressão deste erro é "Permitir iniciar sessão através da política de segurança dos Serviços de Ambiente de Trabalho Remoto.".

Para saber mais sobre esta política, consulte [Iniciar sessão através de Serviços de Ambiente de Trabalho Remoto](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview-2019.md).
- Para resolver problemas enquanto cria um inquilino e piscina de hospedagem em um ambiente de desktop virtual windows, consulte [o Inquilino e a criação de piscina de anfitrião.](troubleshoot-set-up-issues-2019.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection-2019.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](../troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup-2019.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](../../azure-resource-manager/templates/deployment-history.md)da visualização .
