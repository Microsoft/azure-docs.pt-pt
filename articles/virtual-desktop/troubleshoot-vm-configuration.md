---
title: Criação de conjuntos de inquilino e de anfitriões no Desktop Virtual do Windows - Azure
description: Como resolver problemas ao configurar uma sessão de inquilino e anfitrião máquina virtual (VM) num ambiente de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 96a9d8fc7495ea473b0a3250b34251afc5f30c13
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786713"
---
# <a name="tenant-and-host-pool-creation"></a>Criação de inquilinos e conjuntos de anfitriões

Utilize este artigo para resolver os problemas que possa ter quando configurar as máquinas de virtuais de anfitrião de sessão de área de Trabalho Virtual do Windows (VMs).

## <a name="provide-feedback"></a>Enviar comentários

Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory.

## <a name="vms-are-not-joined-to-the-domain"></a>VMs não estão associadas ao domínio

Siga estas instruções se estiver a ter problemas de associar as VMs ao domínio.

- Associar a VM manualmente usando o processo em [associar uma máquina de virtual do Windows Server a um domínio gerido](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) ou utilizando o [modelo de associação de domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente enviar pings para o nome de domínio de linha de comandos na VM.
- Reveja a lista de mensagens de erro de associação de domínio no [resolução de problemas de mensagens de erro de associação de domínio](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Causa:** Ocorreu um erro de digitação feito quando as credenciais foram introduzidas no correções de interface de modelo Azure Resource Manager.

**CORREÇÃO:** Siga estas instruções para corrigir as credenciais.

1. Adicione manualmente as VMs a um domínio.
2. Implementar novamente assim que tem sido confirmadas credenciais. Ver [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Junte-se as VMs a um domínio através de um modelo com [junta-se a uma VM do Windows existente para o domínio do AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Tempo limite a aguardar pela intervenção do utilizador

**Causa:** A conta utilizada para concluir a associação ao domínio pode ter a autenticação multifator (MFA).

**CORREÇÃO:** Siga estas instruções para concluir a associação ao domínio.

1. Remova temporariamente o MFA para a conta.
2. Utilize uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta utilizada durante o aprovisionamento não tem permissões para concluir a operação

**Causa:** A conta que está a ser utilizada não tem permissões para associar ao domínio devido a regulamentos de conformidade e as VMs.

**CORREÇÃO:** Siga estas instruções.

1. Utilize uma conta que seja membro do grupo administrador.
2. Conceda as permissões necessárias para a conta que está a ser utilizada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: Nome de domínio não resolve

**Fazer com que 1:** As VMs estão num grupo de recursos que não está associada a rede virtual (VNET) onde está localizado no domínio.

**Corrigi 1:** Crie VNET peering entre a VNET onde as VMs tenham sido aprovisionadas e a VNET onde o controlador de domínio (DC) está em execução. Ver [criar um peering de rede virtual - Gestor de recursos, subscrições diferentes](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Quando utilizar AadService (AADS), entradas DNS não foram definidas.

**Corrigi 2:** Para definir os serviços de domínio, consulte [ativar o Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agente de ambiente de Trabalho Virtual do Windows e o carregador de inicialização de área de Trabalho Virtual do Windows não estão instalados

A forma recomendada para aprovisionar VMs está a utilizar o Azure Resource Manager **criar e aprovisionar área de Trabalho Virtual do Windows hospedar pool** modelo. O modelo instala automaticamente o agente de ambiente de Trabalho Virtual do Windows e o carregador de inicialização de agente do Windows Virtual Desktop.

Siga estas instruções para confirmar que os componentes são instalados e verificar a existência de mensagens de erro.

1. Confirme se os dois componentes são instalados através da verificação **painel de controlo** > **programas** > **programas e funcionalidades**. Se **agentes de Desktop Virtual do Windows** e **carregador de inicialização de agente do Windows Virtual Desktop** são não estiver visível, eles não estão instalados na VM.
2. Open **Explorador de ficheiros** e navegue até à **C:\Windows\Temp\scriptlogs.log**. Se o ficheiro está em falta, ele indica que o DSC de PowerShell instalado os dois componentes não foi capaz de executar no contexto de segurança fornecido.
3. Se o ficheiro **C:\Windows\Temp\scriptlogs.log** estiver presente, abri-lo e verificar a existência de mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Erro: Agente de ambiente de Trabalho Virtual do Windows e o carregador de inicialização de agente do Windows Virtual Desktop estão em falta. C:\Windows\Temp\scriptlogs.log também está em falta

**Fazer com que 1:** Credenciais fornecidas durante a introdução para o modelo Azure Resource Manager são incorretas ou as permissões eram insuficientes.

**Corrigi 1:** Adicionar manualmente os componentes em falta para as VMs com [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** DSC de PowerShell foi capaz de iniciar e executar, mas não foi concluída porque ele não é possível iniciar sessão na área de Trabalho Virtual do Windows e obter as informações necessárias.

**Corrigi 2:** Certifique-se os itens na lista seguinte.

- Certifique-se de que a conta não tem a MFA.
- Confirme que o nome de inquilino é preciso e o inquilino existe no ambiente de Trabalho Virtual do Windows.
- Confirme que a conta tem, pelo menos, permissões de Contribuidor de RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Erro: Falha na autenticação, erro na C:\Windows\Temp\scriptlogs.log

**Causa:** DSC de PowerShell foi capaz de executar, mas não foi possível ligar à área de Trabalho Virtual do Windows.

**CORREÇÃO:** Certifique-se os itens na lista seguinte.

- Registe manualmente as VMs com o serviço de área de Trabalho Virtual do Windows.
- Confirme a conta utilizada para ligar à área de Trabalho Virtual do Windows com permissões no inquilino para criar conjuntos de anfitrião.
- Confirme a conta não tiver a MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agente de ambiente de Trabalho Virtual do Windows não está a registar com o serviço de área de Trabalho Virtual do Windows

Quando o agente de ambiente de Trabalho Virtual do Windows é instalado pela primeira vez numa sessão de hospedar VMs (ou manualmente ou através do modelo Azure Resource Manager e PowerShell DSC), ele fornece um token de registo. A seção a seguir aborda a resolução de problemas aplicável para o agente de ambiente de Trabalho Virtual do Windows e o token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O estado arquivado no cmdlet Get-RdsSessionHost mostra o estado como não disponível

![Cmdlet Get-RdsSessionHost mostra o estado como indisponível.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se Atualize automaticamente para uma nova versão.

**CORREÇÃO:** Siga estas instruções para atualizar manualmente o agente.

1. Transfira uma nova versão do agente no anfitrião de sessões da VM.
2. Inicie o Gestor de tarefas e, no separador de serviço, pare o serviço de RDAgentBootLoader.
3. Execute o instalador para a nova versão do agente de ambiente de Trabalho Virtual do Windows.
4. Quando lhe for pedido para o token de registo, remova a entrada INVALID_TOKEN e prima seguinte (um novo token não é necessário).
5. Conclua o Assistente de instalação.
6. Abra o Gestor de tarefas e iniciar o serviço de RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro:  Entrada de registo do agente de ambiente de Trabalho Virtual do Windows IsRegistered mostra um valor de 0

**Causa:** Token de registo expirou ou foi gerado com o valor de expiração de 999999.

**CORREÇÃO:** Siga estas instruções para corrigir o agente de erro do registo.

1. Se já existe um token de registo, remova-o com Remove RDSRegistrationInfo.
2. Gere novo token Rds NewRegistrationInfo.
3. Confirme que o parâmetro - ExpriationHours está definido como 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: O agente de área de Trabalho Virtual do Windows não está a reportar um heartbeat ao executar Get-RdsSessionHost

**Fazer com que 1:** RDAgentBootLoader serviço foi parado.

**Corrigi 1:** Inicie o Gestor de tarefas e, se o separador de serviço comunica um estado parado para o serviço de RDAgentBootLoader, inicie o serviço.

**Causa 2:** Porta 443 pode ser fechada.

**Corrigi 2:** Siga estas instruções para abrir a porta 443.

1. Confirmar a porta 443 está aberta ao transferir a ferramenta PSPing [as ferramentas do Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Instale o PSPing no anfitrião de sessões em que está a executar o agente VM.
3. Abra a linha de comandos como administrador e emitir o comando abaixo:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme essas informações PSPing recebido do RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Resolução de problemas com a pilha de lado a lado de área de Trabalho Virtual do Windows

A pilha de lado a lado de área de Trabalho Virtual do Windows é instalada automaticamente com o Windows Server 2019. Utilize o Microsoft Installer (MSI) para instalar a pilha de lado a lado no Microsoft Windows Server 2016 ou Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha de lado a lado de área de Trabalho Virtual do Windows está ativada com **enablesxstackrs.ps1**.

Existem três formas principais a pilha de lado a lado é instalada ou ativada em VMs do agrupamento de host de sessão:

- Com o Azure Resource Manager **criar e aprovisionar conjunto de anfitrião do ambiente de Trabalho Virtual do Windows nova** modelo
- Por que está a ser incluído e ativado na imagem mestre
- Instalado ou ativado manualmente em cada VM (ou com as extensões/PowerShell)

Se estiver a ter problemas com a pilha de lado a lado de área de Trabalho Virtual do Windows, escreva o **qwinsta** comando no prompt de comando para confirmar que a pilha de lado a lado é instalada ou ativada.

A saída de **qwinsta** listará **rdp-sxs** na saída, se a pilha de lado a lado é instalada e ativada.

![Pilha de lado a lado instalado ou ativado com qwinsta listado como rdp-sxs na saída.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas do Registro listadas abaixo e confirme que correspondem aos seus valores. Se não existem chaves do Registro ou valores são sem correspondência, siga as instruções em [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) sobre como reinstalar a pilha de lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

![Código de erro O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha de lado a lado não está instalada no anfitrião de sessões da VM.

**CORREÇÃO:** Siga estas instruções para instalar a pilha de lado a lado em que o anfitrião de sessões de VM.

1. Utilize o protocolo RDP (Remote Desktop) para obter diretamente para a VM de anfitrião de sessão como administrador local.
2. Transferir e importar [módulo do PowerShell de ambiente de trabalho virtuais Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.
3. Instalar a pilha de lado a lado com [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha de lado a lado de área de Trabalho Virtual do Windows que malfunctions

São conhecidos há circunstâncias que podem causar a pilha de lado a lado funcione incorretamente:

- Não está a seguir a ordem correta dos passos para ativar a pilha de lado a lado
- Atualização automática para Windows 10 melhorada versátil disco (EVD)
- Falta a função de anfitrião de sessões de ambiente de trabalho remoto (RDSH)
- Executar enablesxsstackrc.ps1 várias vezes
- Executar enablesxsstackrc.ps1 numa conta que não tem privilégios de administrador local

As instruções nesta secção podem ajudar a desinstalar a pilha de lado a lado de área de Trabalho Virtual do Windows. Depois de desinstalar a pilha de lado a lado, vá para "Registar a VM com o pool de anfitrião de ambiente de trabalho virtuais do Windows" [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) para reinstalar a pilha de lado a lado.

A VM utilizada para executar a remediação tem de estar na mesma sub-rede e domínio da VM com a pilha de lado a lado com funcionamento incorreto.

Siga estas instruções para executar a remediação na mesma sub-rede e domínio:

1. Ligar com o padrão protocolo RDP (Remote Desktop) à VM a partir de onde será aplicada a correção.
2. Baixe o PsExec de https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Deszipe o ficheiro transferido.
4. Inicie a linha de comandos como administrador local.
5. Navegue até à pasta onde PsExec foi descompactado.
6. A partir da linha de comandos, utilize o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname é o nome da máquina da VM com a pilha de lado a lado com funcionamento incorreto.

7. Aceite o contrato de licença do PsExec clicando os Concordo.

    ![Captura de contrato de licença de software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Esta caixa de diálogo serão apresentados apenas na primeira vez em que PsExec é executado.

8. Depois de abre a sessão de linha de comandos na VM com a pilha de lado a lado com funcionamento incorreto, execute qwinsta e confirme que uma entrada com o nome sxs de rdp está disponível. Caso contrário, uma pilha de lado a lado não estiver presente na VM para que o problema não está vinculado a pilha de lado a lado.

    ![Linha de comandos de administrador](media/AdministratorCommandPrompt.png)

9. Execute o seguinte comando, o qual listará os componentes da Microsoft instalados na VM com a pilha de lado a lado com funcionamento incorreto.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com nomes de produtos do passo acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Ambiente de trabalho remoto".

12. Depois de tem sido desinstalados todos os componentes de área de Trabalho Virtual do Windows, siga as instruções do seu sistema operativo:

13. Se o sistema operativo for Windows Server, reinicie a VM que tinha a pilha de lado a lado com funcionamento incorreto (seja com o portal do Azure ou da ferramenta PsExec).

Se o sistema operativo Microsoft Windows 10, prossiga com as instruções abaixo:

14. Da VM a executar o PsExec, abra o Explorador de ficheiros e copie disablesxsstackrc.ps1 para a unidade do sistema da VM com a pilha de lado a lado malfunctioned.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome da máquina da VM com a pilha de lado a lado com funcionamento incorreto.

15. O processo recomendado: a partir da ferramenta PsExec, inicie o PowerShell e navegue para a pasta do passo anterior e execute disablesxsstackrc.ps1. Em alternativa, pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets são feitos em execução, reinicie a VM com a pilha de lado a lado com funcionamento incorreto.

## <a name="remote-licensing-model-is-not-configured"></a>Modelo de licenciamento remoto não está configurado

Se iniciar sessão sessão multi do Windows 10 Enterprise usando uma conta de administrador, poderá receber uma notificação que diz "modo de licenciamento de ambiente de trabalho remoto não está configurado, os serviços de ambiente de trabalho remoto deixa de funcionar em X dias. No servidor de Mediador de ligações, utilize o Gestor de servidor para especificar o modo de licenciamento de ambiente de trabalho remoto." Se vir esta mensagem, que significa que terá de configurar manualmente o modo de licenciamento **por utilizador**.

Para configurar manualmente o modo de licenciamento:  

1. Aceda ao seu **menu Iniciar** caixa de pesquisa, em seguida, localizar e abrir **gpedit. msc** para acessar o editor de diretiva de grupo local. 
2. Aceda a **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Os serviços de ambiente de trabalho remoto** > **anfitrião de sessões de ambiente de trabalho remoto** > **licenciamento**. 
3. Selecione **defina o modo de licenciamento de ambiente de trabalho remoto** e altere-o para **por utilizador**.

Estamos atualmente analisando os problemas de tempo limite de notificação e o período de tolerância e plano para resolvê-los numa atualização futura. 

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral na área de Trabalho Virtual do Windows e as faixas de escalonamento de resolução de problemas, consulte [descrição geral, comentários e suporte de resolução de problemas](troubleshoot-set-up-overview.md).
- Para resolver problemas ao criar um conjunto de inquilino e o anfitrião num ambiente de área de Trabalho Virtual do Windows, consulte [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) na área de Trabalho Virtual do Windows, consulte [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas com ligações de cliente de área de Trabalho Virtual do Windows, consulte [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md).
- Para resolver problemas ao utilizar o PowerShell com a área de Trabalho Virtual do Windows, consulte [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).