---
title: Área de Trabalho Remota conexões de cliente na área de trabalho virtual do Windows – Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 9cd754b1810595c3ae82a7e4edfd9a3abe145b3f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816413"
---
# <a name="remote-desktop-client-connections"></a>Ligações cliente do Ambiente de Trabalho Remoto

Use este artigo para resolver problemas com conexões de cliente de área de trabalho virtual do Windows.

## <a name="provide-feedback"></a>Enviar comentários

No momento, não estamos adotando casos de suporte enquanto a área de trabalho virtual do Windows está em versão prévia. Visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de trabalho virtual do Windows com a equipe de produto e os membros ativos da Comunidade.

## <a name="you-cant-open-a-web-client"></a>Não é possível abrir um cliente Web

Confirme se há conectividade com a Internet abrindo outro site da Web; por exemplo, [www.Bing.com](https://www.bing.com).

Use **nslookup** para confirmar se o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente se conectar com outro cliente, como Área de Trabalho Remota Client para Windows 7 ou Windows 10, e verifique se você pode abrir o cliente Web.

### <a name="error-opening-another-site-fails"></a>Erro: Falha ao abrir outro site

**Faz** Problemas de rede e/ou interrupções.

**Soluciona** Contate o suporte de rede.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não pode resolver o nome

**Faz** Problemas de rede e/ou interrupções.

**Soluciona** Contatar o suporte de rede

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Erro: Você não pode se conectar, mas outros clientes podem se conectar

**Faz** O navegador não está se comportando como esperado e parou de funcionar.

**Soluciona** Siga estas instruções para solucionar o problema do navegador.

1. Reinicie o navegador.
2. Limpar cookies do navegador. Consulte [como excluir arquivos de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe a cache do browser. Consulte [Limpar cache do navegador para seu navegador](https://binged.it/2RKyfdU).
4. Abra o navegador em modo privado.

## <a name="web-client-stops-responding-or-disconnects"></a>O cliente Web para de responder ou desconexões

Tente se conectar usando outro navegador ou cliente.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Erro: Outros navegadores e clientes também têm mau funcionamento ou falha ao abrir

**Faz** Problemas ou interrupções de rede e/ou sistema operacional

**Soluciona** Contate as equipes de suporte.

## <a name="web-client-keeps-prompting-for-credentials"></a>O cliente Web continua solicitando credenciais

Se o cliente Web continuar solicitando credenciais, siga estas instruções.

1. Confirme se a URL do cliente Web está correta.
2. Confirme se as credenciais são para o ambiente de área de trabalho virtual do Windows vinculado à URL.
3. Limpar cookies do navegador. Consulte [como excluir arquivos de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe a cache do browser. Consulte [Limpar cache do navegador para seu navegador](https://binged.it/2RKyfdU).
5. Abra o navegador em modo privado.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Área de Trabalho Remota cliente para Windows 7 ou Windows 10 para de responder ou não pode ser aberto

Use os seguintes cmdlets do PowerShell para limpar registros de cliente de OOB (fora de banda).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navegue até **%AppData%\RdClientRadc** e exclua todo o conteúdo.

Desinstale e reinstale o Área de Trabalho Remota Client para Windows 7 e Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Solucionando problemas de conectividade do usuário final

Às vezes, os usuários podem acessar seus recursos locais e de feed, mas ainda têm problemas de configuração, disponibilidade ou desempenho que os impedem de acessar recursos remotos. Nesses casos, o usuário recebe mensagens semelhantes a estas:

![Conexão de Área de Trabalho Remota mensagem de erro.](media/eb76b666808bddb611448dfb621152ce.png)

![Não é possível conectar-se à mensagem de erro do gateway.](media/a8fbb9910d4672147335550affe58481.png)

Siga estas instruções gerais de solução de problemas para códigos de erro de conexão do cliente.

1. Confirme o nome de usuário e a hora em que o problema foi detectado.
2. Abra o **PowerShell** e estabeleça a conexão com o locatário da área de trabalho virtual do Windows em que o problema foi relatado.
3. Confirme a conexão com o locatário correto com **Get-RdsTenant.**
4. Usando os cmdlets **Get-RdsHostPool** e **Get-RdsSessionHost** , confirme se a solução de problemas está sendo feita no pool de hosts correto.
5. Execute o comando a seguir para obter uma lista de todas as atividades com falha do tipo conexão para a janela de tempo especificada:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Usando o **ActivityId** da saída do cmdlet anterior, execute o comando a seguir:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. O comando produz uma saída semelhante à saída mostrada abaixo. Use **ErrorCodeSymbolic** e **ErrorMessage** para solucionar problemas de causa raiz.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Erro: O_ADD_USER_TO_GROUP_FAILED/falha ao adicionar User = ≤ username ≥ a Group = Área de Trabalho Remota users. Razão: Win32.ERROR_NO_SUCH_MEMBER

**Faz** A VM não ingressou no domínio em que o objeto de usuário é.

**Soluciona** Adicione a VM ao domínio correto. Consulte [ingressar uma máquina virtual do Windows Server em um domínio gerenciado](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não pode resolver o nome

**Faz** Problemas de rede ou interrupções.

**Soluciona** Contatar o suporte de rede

### <a name="error-connectionfailedclientprotocolerror"></a>Erro: ConnectionFailedClientProtocolError

**Faz** As VMs às quais o usuário está tentando se conectar não estão associadas ao domínio.

**Soluciona** Ingresse todas as VMs que fazem parte de um pool de hosts no controlador de domínio.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário pode iniciar Área de Trabalho Remota clientes e é capaz de autenticar, no entanto, o usuário não vê nenhum ícone no feed de descoberta da Web.

Confirme se o usuário que relata os problemas foi atribuído a grupos de aplicativos usando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme se o usuário está fazendo logon com as credenciais corretas.

Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um pool de locatários e de host em um ambiente de área de trabalho virtual do Windows, confira [criação de locatário e pool](troubleshoot-set-up-issues.md)de hosts.
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Para percorrer um tutorial de solução de problemas [, consulte o tutorial: Solucionar problemas de implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)de modelo do Resource Manager.
- Para saber mais sobre ações de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
