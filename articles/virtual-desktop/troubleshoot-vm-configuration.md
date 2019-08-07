---
title: Criação de locatários e pool de hosts na área de trabalho virtual do Windows – Azure
description: Como resolver problemas quando você estiver configurando uma VM (máquina virtual) de locatário e de sessão em um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 4e5c5f14042f7059f3d802a5e72cbf5c6a126614
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816329"
---
# <a name="tenant-and-host-pool-creation"></a>Criação de inquilinos e conjuntos de anfitriões

Use este artigo para solucionar problemas que você está tendo ao configurar as VMs (máquinas virtuais) do host de sessão de área de trabalho virtual do Windows.

## <a name="provide-feedback"></a>Enviar comentários

No momento, não estamos adotando casos de suporte enquanto a área de trabalho virtual do Windows está em versão prévia. Visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de trabalho virtual do Windows com a equipe de produto e os membros ativos da Comunidade.

## <a name="vms-are-not-joined-to-the-domain"></a>As VMs não ingressaram no domínio

Siga estas instruções se você estiver tendo problemas para ingressar VMs no domínio.

- Ingresse a VM manualmente usando o processo em [ingressar uma máquina virtual do Windows Server em um domínio gerenciado](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) ou usando o [modelo de ingresso no domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente executar o ping no nome de domínio da linha de comando na VM.
- Examine a lista de mensagens de erro de ingresso no domínio em [Solucionando problemas de mensagens de erro de ingresso no domínio](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Faz** Houve um erro de digitação quando as credenciais foram inseridas nas correções da interface do modelo de Azure Resource Manager.

**Soluciona** Siga estas instruções para corrigir as credenciais.

1. Adicione manualmente as VMs a um domínio.
2. Reimplantar depois que as credenciais tiverem sido confirmadas. Consulte [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Ingresse VMs em um domínio usando um modelo com [une uma VM do Windows existente ao domínio do AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Tempo limite aguardando entrada do usuário

**Faz** A conta usada para concluir o ingresso no domínio pode ter a autenticação multifator (MFA).

**Soluciona** Siga estas instruções para concluir o ingresso no domínio.

1. Remova temporariamente a MFA da conta.
2. Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta usada durante o provisionamento não tem permissões para concluir a operação

**Faz** A conta que está sendo usada não tem permissões para unir VMs ao domínio devido a conformidade e regulamentos.

**Soluciona** Siga estas instruções.

1. Use uma conta que seja membro do grupo de administradores.
2. Conceda as permissões necessárias para a conta que está sendo usada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: O nome de domínio não resolve

**Causa 1:** As VMs estão em um grupo de recursos que não está associado à rede virtual (VNET) em que o domínio está localizado.

**Correção 1:** Crie um emparelhamento VNET entre a VNET em que as VMs foram provisionadas e a VNET em que o DC (controlador de domínio) está em execução. Consulte [criar um emparelhamento de rede virtual – Gerenciador de recursos, assinaturas diferentes](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Ao usar AadService (AADS), as entradas DNS não foram definidas.

**Correção 2:** Para definir os serviços de domínio, consulte [habilitar Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>O agente de área de trabalho virtual do Windows e o carregador de inicialização do Windows Virtual Desktop não estão instalados

A maneira recomendada para provisionar VMs é usar a Azure Resource Manager criar e provisionar o modelo **de pool de hosts da área de trabalho virtual do Windows** O modelo instala automaticamente o agente de área de trabalho virtual do Windows e o carregador de inicialização do agente de desktop virtual do Windows.

Siga estas instruções para confirmar se os componentes estão instalados e para verificar se há mensagens de erro.

1. Confirme se os dois componentes estão instalados marcando em **painel** > de controle**programas** > programas**e recursos**. Se o **agente de área de trabalho virtual do Windows** e o carregador de inicialização do **Windows Virtual Desktop Agent** não estiverem visíveis, eles não serão instalados na VM.
2. Abra o **Explorador de arquivos** e navegue até **C:\Windows\Temp\scriptlogs.log**. Se o arquivo estiver ausente, isso indica que a DSC do PowerShell que instalou os dois componentes não pôde ser executada no contexto de segurança fornecido.
3. Se o arquivo **C:\Windows\Temp\scriptlogs.log** estiver presente, abra-o e verifique se há mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Erro: O agente de área de trabalho virtual do Windows e o carregador de inicialização do Windows Virtual Desktop Agent estão ausentes. C:\Windows\Temp\scriptlogs.log também está ausente

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo de Azure Resource Manager estavam incorretas ou as permissões eram insuficientes.

**Correção 1:** Adicione manualmente os componentes ausentes às VMs usando [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** O DSC do PowerShell foi capaz de iniciar e executar, mas não foi concluído, pois não conseguiu entrar na área de trabalho virtual do Windows e obter as informações necessárias.

**Correção 2:** Confirme os itens na lista a seguir.

- Verifique se a conta não tem MFA.
- Confirme se o nome do locatário é preciso e se o locatário existe na área de trabalho virtual do Windows.
- Confirme se a conta tem pelo menos permissões de colaborador de RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Erro: Falha na autenticação, erro em C:\Windows\Temp\scriptlogs.log

**Faz** O DSC do PowerShell foi capaz de executar, mas não pôde se conectar à área de trabalho virtual do Windows.

**Soluciona** Confirme os itens na lista a seguir.

- Registre manualmente as VMs com o serviço de área de trabalho virtual do Windows.
- Confirme que a conta usada para conexão com a área de trabalho virtual do Windows tem permissões no locatário para criar pools de hosts.
- A conta de confirmação não tem MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>O agente de área de trabalho virtual do Windows não está se registrando no serviço de área de trabalho virtual do Windows

Quando o agente de área de trabalho virtual do Windows é instalado pela primeira vez em VMs de host de sessão (manualmente ou por meio do modelo de Azure Resource Manager e DSC do PowerShell), ele fornece um token de registro. A seção a seguir aborda a solução de problemas aplicáveis ao agente de área de trabalho virtual do Windows e ao token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O status arquivado no cmdlet Get-RdsSessionHost mostra status como indisponível

![O cmdlet Get-RdsSessionHost mostra o status como indisponível.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Faz** O agente não é capaz de se atualizar para uma nova versão.

**Soluciona** Siga estas instruções para atualizar manualmente o agente.

1. Baixe uma nova versão do agente na VM host da sessão.
2. Inicie o Gerenciador de tarefas e, na guia serviço, interrompa o serviço RDAgentBootLoader.
3. Execute o instalador para a nova versão do agente de área de trabalho virtual do Windows.
4. Quando for solicitado o token de registro, remova a entrada INVALID_TOKEN e pressione Avançar (um novo token não é necessário).
5. Conclua o assistente de instalação.
6. Abra o Gerenciador de tarefas e inicie o serviço RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro:  A entrada do registro isregister do Windows Virtual Desktop Agent mostra um valor de 0

**Faz** O token de registro expirou ou foi gerado com o valor de expiração de 999999.

**Soluciona** Siga estas instruções para corrigir o erro de registro do agente.

1. Se já houver um token de registro, remova-o com Remove-RDSRegistrationInfo.
2. Gerar novo token com RDS-NewRegistrationInfo.
3. Confirme se o parâmetro-ExpriationHours está definido como 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: O agente de área de trabalho virtual do Windows não relata uma pulsação ao executar Get-RdsSessionHost

**Causa 1:** O serviço RDAgentBootLoader foi interrompido.

**Correção 1:** Inicie o Gerenciador de tarefas e, se a guia serviço relatar um status parado para o serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** A porta 443 pode estar fechada.

**Correção 2:** Siga estas instruções para abrir a porta 443.

1. Confirme se a porta 443 está aberta baixando a ferramenta PSPing de [Ferramentas do Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Instale o PSPing na VM host da sessão em que o agente está em execução.
3. Abra o prompt de comando como administrador e emita o comando a seguir:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme se o PSPing recebeu informações de volta do RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Solucionando problemas com a pilha lado a lado da área de trabalho virtual do Windows

A pilha lado a lado da área de trabalho virtual do Windows é instalada automaticamente com o Windows Server 2019. Use o MSI (Microsoft Installer) para instalar a pilha lado a lado no Microsoft Windows Server 2016 ou no Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha lado a lado da área de trabalho virtual do Windows é habilitada com **enablesxstackrs. ps1**.

Há três maneiras principais pelas quais a pilha lado a lado é instalada ou habilitada nas VMs do pool de hosts de sessão:

- Com a Azure Resource Manager **criar e provisionar o novo modelo de pool de hosts de área de trabalho virtual**
- Ao ser incluído e habilitado na imagem mestra
- Instalado ou habilitado manualmente em cada VM (ou com extensões/PowerShell)

Se você estiver tendo problemas com a pilha lado a lado da área de trabalho virtual do Windows, digite o comando **Qwinsta** no prompt de comando para confirmar se a pilha lado a lado está instalada ou habilitada.

A saída de **Qwinsta** listará o **RDP-SxS** na saída se a pilha lado a lado estiver instalada e habilitada.

![Pilha lado a lado instalada ou habilitada com Qwinsta listado como RDP-SxS na saída.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas de registro listadas abaixo e confirme se seus valores correspondem. Se as chaves do registro estiverem ausentes ou se os valores forem incompatíveis, siga as instruções em [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) sobre como reinstalar a pilha lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

![Código de erro O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Faz** A pilha lado a lado não está instalada na VM host da sessão.

**Soluciona** Siga estas instruções para instalar a pilha lado a lado na VM host de sessão.

1. Use protocolo RDP (RDP) para obter diretamente a VM host da sessão como administrador local.
2. Baixe e importe [o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.
3. Instale a pilha lado a lado usando [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha lado a lado da área de trabalho virtual do Windows que não funciona corretamente

Há circunstâncias conhecidas que podem causar o mau funcionamento da pilha lado a lado:

- Não seguindo a ordem correta das etapas para habilitar a pilha lado a lado
- Atualização automática para o disco versátil aprimorado do Windows 10 (EVD)
- Função de Host da Sessão da Área de Trabalho Remota (RDSH) ausente
- Executando enablesxsstackrc. ps1 várias vezes
- Executando enablesxsstackrc. ps1 em uma conta que não tem privilégios de administrador local

As instruções nesta seção podem ajudá-lo a desinstalar a pilha lado a lado da área de trabalho virtual do Windows. Depois de desinstalar a pilha lado a lado, vá para "registrar a VM com o pool de hosts da área de trabalho virtual do Windows" em [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) para reinstalar a pilha lado a lado.

A VM usada para executar a correção deve estar na mesma sub-rede e domínio que a VM com a pilha lado a lado com funcionamento inadequado.

Siga estas instruções para executar a correção da mesma sub-rede e domínio:

1. Conecte-se com o protocolo RDP padrão (RDP) à VM de onde a correção será aplicada.
2. Baixe o PsExec https://docs.microsoft.com/sysinternals/downloads/psexec de.
3. Descompacte o arquivo baixado.
4. Inicie o prompt de comando como administrador local.
5. Navegue até a pasta em que o PsExec foi descompactado.
6. No prompt de comando, use o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname é o nome do computador da VM com a pilha lado a lado defeituosa.

7. Aceite o contrato de licença do PsExec clicando em Concordo.

    ![Captura de tela do contrato de licença de software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Esta caixa de diálogo será mostrada apenas na primeira vez que o PsExec for executado.

8. Depois que a sessão de prompt de comando for aberta na VM com a pilha lado a lado com problemas de funcionamento, execute Qwinsta e confirme se uma entrada chamada RDP-SxS está disponível. Caso contrário, uma pilha lado a lado não está presente na VM, portanto, o problema não está vinculado à pilha lado a lado.

    ![Prompt de comando do administrador](media/AdministratorCommandPrompt.png)

9. Execute o comando a seguir, que listará os componentes da Microsoft instalados na VM com a pilha lado a lado com funcionamento inadequado.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com nomes de produtos da etapa acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Área de Trabalho Remota".

12. Depois que todos os componentes da área de trabalho virtual do Windows tiverem sido desinstalados, siga as instruções para seu sistema operacional:

13. Se o seu sistema operacional for o Windows Server, reinicie a VM que tinha a pilha lado a lado com problemas (seja com portal do Azure ou da ferramenta PsExec).

Se o seu sistema operacional for o Microsoft Windows 10, continue com as instruções abaixo:

14. Na VM que executa o PsExec, abra o explorador de arquivos e copie disablesxsstackrc. ps1 para a unidade do sistema da VM com a pilha lado a lado com defeito.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome do computador da VM com a pilha lado a lado defeituosa.

15. O processo recomendado: na ferramenta PsExec, inicie o PowerShell e navegue até a pasta da etapa anterior e execute disablesxsstackrc. ps1. Como alternativa, você pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets forem concluídos em execução, reinicie a VM com a pilha lado a lado com problemas.

## <a name="remote-licensing-model-is-not-configured"></a>O modelo de licenciamento remoto não está configurado

Se você entrar no Windows 10 Enterprise Multi-Session usando uma conta administrativa, poderá receber uma notificação dizendo que "o modo de licenciamento Área de Trabalho Remota não está configurado, Serviços de Área de Trabalho Remota deixará de funcionar em X dias. No servidor do agente de conexão, use Gerenciador do Servidor para especificar o modo de licenciamento de Área de Trabalho Remota ". Se você vir essa mensagem, isso significa que você precisa configurar manualmente o modo de licenciamento para **por usuário**.

Para configurar manualmente o modo de licenciamento:  

1. Vá para a caixa de pesquisa do **menu iniciar** e localize e abra **gpedit. msc** para acessar o editor de política de grupo local. 
2. Vá para **configuração** > do computador**modelos administrativos** > **componentes** > do Windows**serviços de área de trabalho remota** host da sessão da área de trabalho remota >  >  **Licenciamento**. 
3. Selecione **definir o modo de licenciamento área de trabalho remota** e altere-o para **por usuário**.

Estamos procurando os problemas de tempo limite de notificação e período de carência e pretendemos solucioná-los em uma atualização futura. 

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um pool de locatários e de host em um ambiente de área de trabalho virtual do Windows, confira [criação de locatário e pool](troubleshoot-set-up-issues.md)de hosts.
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas [, consulte o tutorial: Solucionar problemas de implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)de modelo do Resource Manager.
- Para saber mais sobre ações de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).