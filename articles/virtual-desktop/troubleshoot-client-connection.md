---
title: Ligações de cliente de ambiente de trabalho remoto no Windows Virtual Desktop - Azure
description: Como resolver problemas ao configurar ligações de cliente num ambiente de inquilino de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: f88dee579e44a01dc1a7404ef6a670de34063552
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833577"
---
# <a name="remote-desktop-client-connections"></a>Ligações cliente do Ambiente de Trabalho Remoto

Utilize este artigo para resolver problemas com ligações de cliente de área de Trabalho Virtual do Windows.

## <a name="provide-feedback"></a>Enviar comentários

Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory.

## <a name="you-cant-open-a-web-client"></a>Não é possível abrir um cliente web

Confirme que existe conectividade de internet ao abrir a outro web site; Por exemplo, [www.Bing.com](https://www.bing.com).

Uso **nslookup** para confirmar o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente ligar-se com outro cliente, como o cliente de ambiente de trabalho remoto para Windows 7 ou Windows 10 e verificação ver se é possível abrir o cliente web.

### <a name="error-opening-another-site-fails"></a>Erro: Abrir outra falha do site

**Causa:** Problemas de rede e/ou falhas.

**CORREÇÃO:** Contacte o suporte de rede.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não é possível resolver o nome

**Causa:** Problemas de rede e/ou falhas.

**CORREÇÃO:** Contacte o suporte de rede

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Erro: Não é possível ligar, mas podem ligar a outros clientes

**Causa:** O browser não está se comportando como esperado e parado de trabalho.

**CORREÇÃO:** Siga estas instruções para resolver problemas relacionados com o navegador.

1. Reinicie o navegador.
2. Cookies do browser clara. Ver [como eliminar ficheiros de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe a cache do browser. Ver [limpar a cache do browser para o seu navegador](https://binged.it/2RKyfdU).
4. Browser aberto no modo privado.

## <a name="web-client-stops-responding-or-disconnects"></a>Cliente Web deixa de responder ou desligada

Tente ligar-se de que a utilizar outro browser ou cliente.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Erro: Outros navegadores e os clientes também funcione incorretamente ou não abrirá

**Causa:** Problemas de sistema de rede e/ou a operação ou falhas

**CORREÇÃO:** Contacte o suporte de equipas.

## <a name="web-client-keeps-prompting-for-credentials"></a>Cliente Web mantém prompt de credenciais

Se o cliente Web mantém prompt de credenciais, siga estas instruções.

1. Confirme o que URL de cliente da web está correto.
2. Confirme que as credenciais para o ambiente de área de Trabalho Virtual do Windows associado ao URL.
3. Cookies do browser clara. Ver [como eliminar ficheiros de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe a cache do browser. Ver [limpar a cache do browser para o seu navegador](https://binged.it/2RKyfdU).
5. Browser aberto no modo privado.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Cliente de ambiente de trabalho remoto para Windows 7 ou Windows 10 irá parar de responder ou não é possível abrir

Utilize os seguintes cmdlets do PowerShell para limpar registos de cliente fora de banda (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navegue para **%AppData%\RdClientRadc** e elimine todo o conteúdo.

Desinstale e reinstale o cliente de ambiente de trabalho remoto para Windows 7 e Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Resolução de problemas de conectividade do utilizador final

Às vezes, os utilizadores podem aceder aos respetivos feed e os recursos locais, mas continua com a configuração, disponibilidade ou problemas de desempenho que impedem-los de aceder a recursos remotos. Nestes casos, o utilizador obtém as mensagens semelhantes a estas:

![Mensagem de erro de ligação de ambiente de trabalho remota.](media/eb76b666808bddb611448dfb621152ce.png)

![Não é possível ligar à mensagem de erro de gateway.](media/a8fbb9910d4672147335550affe58481.png)

Siga estas instruções de resolução de problemas gerais de códigos de erro de ligação de cliente.

1. Confirme o nome de utilizador e a hora quando o problema foi teve.
2. Open **PowerShell** e estabelecer ligação ao inquilino de área de Trabalho Virtual do Windows em que o problema foi relatado.
3. Confirmar a ligação para o inquilino correto com **Get RdsTenant.**
4. Usando **Get-RdsHostPool** e **Get-RdsSessionHost** cmdlets, confirme que a resolução de problemas está a ser feita no pool de anfitrião correto.
5. Execute o comando abaixo para obter uma lista de todas as atividades com falhas de ligação de tipo para a janela de tempo especificado:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Utilizar o **ActivityId** da saída do cmdlet anterior, execute o comando abaixo:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. O comando produz saída semelhante a saída mostrada abaixo. Uso **ErrorCodeSymbolic** e **ErrorMessage** para resolver a causa raiz.

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

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Erro: O_ADD_USER_TO_GROUP_FAILED / Falha ao adicionar utilizador = ≤username≥ para grupo = Remote Desktop Users. Razão: Win32.ERROR_NO_SUCH_MEMBER

**Causa:** VM não foi associada ao domínio em que é o objeto de utilizador.

**CORREÇÃO:** Adicione VM ao domínio correto. Ver [associar uma máquina de virtual do Windows Server a um domínio gerido](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não é possível resolver o nome

**Causa:** Problemas de rede ou falhas.

**CORREÇÃO:** Contacte o suporte de rede

### <a name="error-connectionfailedclientprotocolerror"></a>Erro: ConnectionFailedClientProtocolError

**Causa:** As VMs que o utilizador está a tentar ligar ao não estão associados a um domínio.

**CORREÇÃO:** Junte-se a todas as VMs que fazem parte de um conjunto de anfitrião para o controlador de domínio.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Utilizador se liga, mas não será apresentado nada (sem feed)

Um utilizador pode iniciar a clientes de ambiente de trabalho remoto e é possível efetuar a autenticação, no entanto o usuário não verá qualquer ícones na deteção de web feed.

Confirme que o utilizador os problemas de relatórios foi atribuído a grupos de aplicações ao utilizar esta linha de comandos:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme que o utilizador está a iniciar sessão com as credenciais corretas.

Se o cliente web está a ser utilizado, confirme que não existem não existem problemas de credenciais em cache.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral na área de Trabalho Virtual do Windows e as faixas de escalonamento de resolução de problemas, consulte [descrição geral, comentários e suporte de resolução de problemas](troubleshoot-set-up-overview.md).
- Para resolver problemas ao criar um conjunto de inquilino e o anfitrião num ambiente de área de Trabalho Virtual do Windows, consulte [inquilino e o host a criação do agrupamento](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) na área de Trabalho Virtual do Windows, consulte [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas ao utilizar o PowerShell com a área de Trabalho Virtual do Windows, consulte [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
