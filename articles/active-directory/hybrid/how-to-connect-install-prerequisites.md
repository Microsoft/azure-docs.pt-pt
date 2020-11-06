---
title: 'Azure AD Connect: Pré-requisitos e hardware Microsoft Docs'
description: Este artigo descreve os pré-requisitos e os requisitos de hardware para Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/05/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f337a66f50338692508ab2e5b4b7d489c735aa20
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420500"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos do Azure AD Connect
Este artigo descreve os pré-requisitos e os requisitos de hardware para a Azure Ative Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, há algumas coisas que precisa.

### <a name="azure-ad"></a>Azure AD
* Precisa de um inquilino da AD AZure. Arranja-se um com um [julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) Pode utilizar um dos seguintes portais para gerir o Azure AD Connect:
  * O [portal Azure.](https://portal.azure.com)
  * O [portal do Escritório.](https://portal.office.com)
* [Adicione e verifique o domínio](../fundamentals/add-custom-domain.md) que pretende utilizar no Azure AD. Por exemplo, se planeia utilizar contoso.com para os seus utilizadores, certifique-se de que este domínio foi verificado e que não está a utilizar apenas o domínio contoso.onmicrosoft.com predefinido.
* Um inquilino da AD Azure permite, por defeito, 50.000 objetos. Quando verifica o seu domínio, o limite aumenta para 300.000 objetos. Se precisar de ainda mais objetos em Azure AD, abra um caso de suporte para aumentar ainda mais o limite. Se precisar de mais de 500.000 objetos, precisa de uma licença, como o Microsoft 365, Azure AD Premium ou Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Prepare os seus dados no local
* Utilize [o IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros como duplicados e problemas de formatação no seu diretório antes de sincronizar com a AD E Microsoft 365.
* Reveja [as funcionalidades de sincronização opcionais que pode ativar em Ad Azure](how-to-connect-syncservice-features.md)e avalie quais as funcionalidades que deve ativar.

### <a name="on-premises-active-directory"></a>Active Directory no local
* A versão de esquema de Ative Directory e o nível funcional da floresta devem ser o Windows Server 2003 ou mais tarde. Os controladores de domínio podem executar qualquer versão desde que a versão do esquema e os requisitos de nível florestal estejam cumpridos.
* Se pretender utilizar a gravação da *palavra-passe* de recurso, os controladores de domínio devem estar no Windows Server 2012 ou posteriormente.
* O controlador de domínio utilizado pela Azure AD deve ser writable. A utilização de um controlador de domínio apenas de leitura (RODC) *não é suportada* , e o Azure AD Connect não segue quaisquer redirecionamentos de escrita.
* Utilizando florestas ou domínios no local utilizando "pontilhados" (o nome contém um período ".") Os nomes da NetBIOS *não são suportados.*
* Recomendamos que [ative o caixote de reciclagem ative directory](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>Política de execução do PowerShell
O Azure Ative Directory Connect executa scripts PowerShell assinados como parte da instalação. Certifique-se de que a política de execução PowerShell permitirá a execução de scripts.

A política de execução recomendada durante a instalação é "RemoteSigned".

Para obter mais informações sobre a definição da política de execução powerShell, consulte [A Política de Execução de Conjuntos](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7).


### <a name="azure-ad-connect-server"></a>Servidor Azure Ad Connect
O servidor Azure AD Connect contém dados de identidade críticos. É importante que o acesso administrativo a este servidor seja devidamente assegurado. Siga as orientações para [garantir o acesso privilegiado.](/windows-server/identity/securing-privileged-access/securing-privileged-access) 

O servidor Azure AD Connect deve ser tratado como um componente de Nível 0, conforme documentado no [modelo de nível administrativo](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) do Diretório Ativo 

Para ler mais sobre a segurança do seu ambiente de Diretório Ativo, consulte [as melhores práticas para garantir o Ative Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Pré-requisitos de instalação

- O Azure AD Connect deve ser instalado num Windows Server 2012 ou mais tarde. 
- O Azure AD Connect não pode ser instalado no Small Business Server ou no Windows Server Essentials antes de 2019 (o Windows Server Essentials 2019 é suportado). O servidor deve estar a utilizar o padrão do Windows Server ou melhor. 
- O servidor Azure AD Connect deve ter um GUI completo instalado. A instalação do Azure AD Connect no Windows Server Core não é suportada. 
- O servidor Azure AD Connect não deve ter a Política do Grupo de Transcrição powerShell ativada se utilizar o assistente Azure AD Connect para gerir a configuração de Serviços da Federação de Diretórios Ativos (AD FS). Pode ativar a transcrição powerShell se utilizar o assistente Azure AD Connect para gerir a configuração de sincronização. 
- Se a AD FS estiver a ser implantada: 
    - Os servidores onde estão instalados O FS ou o Proxy da Aplicação Web devem ser o Windows Server 2012 R2 ou mais tarde. A gestão remota do Windows deve ser ativada nestes servidores para instalação remota. 
    - Tem de configurar os certificados TLS/SSL. Para obter mais informações, consulte [os protocolos SSL/TLS de Gestão e suítes de cifra para](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) certificados AD FS e Managing [SSL em AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Tem de configurar a resolução do nome. 
- Se os seus administradores globais tiverem MFA ativado, o URL https://secure.aadcdn.microsoftonline-p.com *deve* estar na lista de sites fidedignos. É-lhe pedido que adicione este site à lista de sites fidedignos quando é solicitado para um desafio de MFA e não foi adicionado antes. Pode utilizar o Internet Explorer para o adicionar aos seus sites de confiança.

#### <a name="harden-your-azure-ad-connect-server"></a>Endureça o servidor AZure AD Connect 
Recomendamos que endureça o servidor Azure AD Connect para diminuir a superfície de ataque de segurança para este componente crítico do seu ambiente de TI. Seguir estas recomendações ajudará a mitigar alguns riscos de segurança para a sua organização.

- Trate o Azure AD Connect o mesmo que um controlador de domínio e outros recursos tier 0. Para obter mais informações, consulte [o modelo de nível administrativo ative diretório.](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Restringir o acesso administrativo ao servidor Azure AD Connect apenas a administradores de domínio ou a outros grupos de segurança bem controlados.
- Crie uma [conta dedicada para todo o pessoal com acesso privilegiado.](/windows-server/identity/securing-privileged-access/securing-privileged-access) Os administradores não devem estar a navegar na web, a verificar o seu email e a fazer tarefas de produtividade diárias com contas altamente privilegiadas.
- Siga as orientações fornecidas na [Garantia de acesso privilegiado.](/windows-server/identity/securing-privileged-access/securing-privileged-access) 
- Negar a utilização da autenticação NTLM com o servidor AADConnect. Eis algumas formas de o fazer: [Restringir o NTLM no Servidor AADConnect](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) e [restringir a NTLM num domínio](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Certifique-se de que cada máquina tem uma senha de administrador local única. Para obter mais informações, consulte [a Solução de Senha do Administrador Local (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) pode configurar senhas aleatórias únicas em cada estação de trabalho e servidor armazená-las em Ative Directory protegida por um ACL. Apenas os utilizadores autorizados elegíveis podem ler ou solicitar o reset destas senhas de conta de administrador local. Pode obter o LAPS para utilização em estações de trabalho e servidores a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Orientações adicionais para operar um ambiente com LAPS e postos de trabalho privilegiados de acesso (PAWs) podem ser encontrados em [normas operacionais baseadas no princípio da fonte limpa.](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle) 
- Implementar estações de [trabalho privilegiadas dedicadas](/windows-server/identity/securing-privileged-access/privileged-access-workstations) para todos os funcionários com acesso privilegiado aos sistemas de informação da sua organização. 
- Siga estas [diretrizes adicionais](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) para reduzir a superfície de ataque do seu ambiente ative Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server used by Azure AD Connect (SQL Server utilizado pelo Azure AD Connect)
* O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Por predefinição, é instalado um SQL Server 2012 Express LocalDB (uma versão luminosa do SQL Server Express). O SQL Server Express tem um limite de tamanho de 10 GB que lhe permite gerir aproximadamente 100.000 objetos. Se necessitar de gerir um volume mais elevado de objetos de diretório, aponte o assistente de instalação para uma instalação diferente do SQL Server. O tipo de instalação do SQL Server pode ter impacto no [desempenho do Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Se utilizar uma instalação diferente do SQL Server, estes requisitos aplicam-se:
  * O Azure AD Connect suporta todas as versões do SQL Server desde 2012 (com o mais recente pacote de serviços) até ao SQL Server 2019. A base de dados Azure SQL *não é suportada* como base de dados.
  * Deve usar uma colagem SQL insensível. Estas colagens são identificadas com um \_ CI_ em seu nome. A utilização de uma colagem sensível a casos identificada por \_ CS_ em seu nome não é *suportada.*
  * Só pode ter um motor de sincronização por exemplo DE SQL. Partilhar um exemplo SQL com FIM/MIM Sync, DirSync ou Azure AD Sync *não é suportado*.

### <a name="accounts"></a>Contas
* Você deve ter uma conta Azure AD Global Administrator para o inquilino AZURE AD com o que você quer integrar. Esta conta deve ser uma *conta de escola ou organização* e não pode ser uma conta *microsoft*.
* Se utilizar [definições expressas](reference-connect-accounts-permissions.md#express-settings-installation) ou atualizações a partir da DirSync, tem de ter uma conta de Administrador Empresarial para o seu Ative Directory no local.
* Se utilizar o caminho de instalação de definições personalizadas, tem mais opções. Para obter mais informações, consulte [as definições de instalação personalizadas.](reference-connect-accounts-permissions.md#custom-installation-settings)

### <a name="connectivity"></a>Conectividade
* O servidor Azure AD Connect necessita de resolução DNS tanto para intranet como para internet. O servidor DNS deve ser capaz de resolver nomes tanto para o seu Diretório Ativo no local como para os pontos finais Azure AD.
* Se tiver firewalls na sua intranet e precisar de abrir portas entre os servidores AZure AD Connect e os seus controladores de domínio, consulte [as portas AZure AD Connect](reference-connect-ports.md) para obter mais informações.
* Se o seu limite de procuração ou firewall a que os URLs podem ser acedidos, os URLs documentados nos [intervalos de URLs e endereço IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devem ser abertos.
  * Se estiver a utilizar a nuvem da Microsoft na Alemanha ou a nuvem do Governo do Microsoft Azure, consulte as considerações do [serviço de sincronização Azure AD Connect](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 e depois) utiliza por predefinição o TLS 1.2 para encriptar a comunicação entre o motor de sincronização e o AD AZure. Se o TLS 1.2 não estiver disponível no sistema operativo subjacente, o Azure AD Connect recua gradualmente aos protocolos mais antigos (TLS 1.1 e TLS 1.0).
* Antes da versão 1.1.614.0, o Azure AD Connect por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o motor de sincronização e o AD Azure. Para alterar para TLS 1.2, siga os passos em [Enable TLS 1.2 para Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se estiver a utilizar um representante de saída para se ligar à internet, deve ser adicionada a seguinte definição no ficheiro **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** para que o assistente de instalação e a sincronização AD Connect Azure possam ligar-se à internet e ao Azure AD. Este texto deve ser introduzido na parte inferior do ficheiro. Neste código, *&lt; o PROXYADDRESS &gt;* representa o endereço IP ou nome de anfitrião proxy.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Se o seu servidor proxy necessitar de autenticação, a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) deve estar localizada no domínio. Utilize o caminho de instalação de definições personalizadas para especificar uma [conta de serviço personalizada.](how-to-connect-install-custom.md#install-required-components) Também precisa de uma mudança diferente para machine.config. Com esta alteração no machine.config, o assistente de instalação e o motor de sincronização respondem aos pedidos de autenticação do servidor proxy. Em todas as páginas de assistente de instalação, excluindo a página **Configure,** são utilizadas as credenciais do utilizador inscrito. Na página **Configure** no final do assistente de instalação, o contexto é alterado para a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que criou. A secção machine.config deve ser assim:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Se a configuração proxy estiver a ser feita numa configuração existente, o **serviço Microsoft Azure AD Sync** precisa de ser reiniciado uma vez para que o Azure AD Connect leia a configuração de procuração e atualize o comportamento. 
* Quando o Azure AD Connect envia um pedido web para a Azure AD como parte da sincronização do diretório, o Azure AD pode demorar até 5 minutos para responder. É comum que os servidores proxy tenham uma configuração de tempo de intervalo de ligação. Certifique-se de que a configuração está definida para pelo menos 6 minutos ou mais.

Para obter mais informações, consulte a MSDN sobre o [elemento proxy predefinido](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Para obter mais informações quando tiver problemas de conectividade, consulte [problemas de conectividade de resolução de problemas](tshoot-connect-connectivity.md).

### <a name="other"></a>Outro
Opcional: Utilize uma conta de utilizador de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos de componentes
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
O Azure AD Connect depende do Microsoft PowerShell e do .NET Framework 4.5.1. Precisa desta versão ou de uma versão posterior instalada no seu servidor. Dependendo da versão do Windows Server, tome as seguintes ações:

* Windows Server 2012 R2
  * O Microsoft PowerShell é instalado por defeito. nenhuma ação necessária.
  * .NET Framework 4.5.1 e lançamentos posteriores são oferecidos através do Windows Update. Certifique-se de que instalou as últimas atualizações no Windows Server no Painel de Controlo.
* Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no Windows Management Framework 4.0, disponível no [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 e as versões posteriores estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Ativar TLS 1.2 para Azure AD Connect
Antes da versão 1.1.614.0, o Azure AD Connect por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o servidor do motor de sincronização e o Ad AZure. Pode configurar aplicações .NET para utilizar o TLS 1.2 por predefinição no servidor. Para obter mais informações sobre o TLS 1.2, consulte [o Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Certifique-se de que tem o hotfix .NET 4.5.1 instalado para o seu sistema operativo. Para mais informações, consulte [o Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358). Pode ter este hotfix ou um desbloqueio posterior já instalado no seu servidor.

1. Para todos os sistemas operativos, esta tecla de registo e reinicie o servidor.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Se também pretende ativar o TLS 1.2 entre o servidor do motor de sincronização e um servidor SQL remoto, certifique-se de que tem as versões necessárias instaladas para [suporte TLS 1.2 para o Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para a instalação e configuração da federação
### <a name="windows-remote-management"></a>Gestão Remota do Windows
Quando utilizar o Azure AD Connect para implementar O FS AD ou o Proxy da Aplicação Web (WAP), verifique estes requisitos:

* Se o servidor alvo estiver unido ao domínio, certifique-se de que o Windows Remote Managed está ativado.
  * Numa janela de comando PowerShell elevada, utilize o comando `Enable-PSRemoting –force` .
* Se o servidor alvo for uma máquina WAP não-de-domínio, existem alguns requisitos adicionais:
  * Na máquina-alvo (máquina WAP):
    * Certifique-se de que o serviço de Gestão Remota do Windows/WS (WinRM) está a ser gerido através do snap-in dos Serviços.
    * Numa janela de comando PowerShell elevada, utilize o comando `Enable-PSRemoting –force` .
  * Na máquina em que o assistente está em funcionamento (se a máquina-alvo não estiver de domínio ou se for um domínio não fided o domínio):
    * Numa janela de comando PowerShell elevada, utilize o comando `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * No gestor do servidor:
      * Adicione um anfitrião DMZ WAP a uma piscina de máquinas. No gestor do servidor, selecione **Gerir**  >  **Servidores de Adicionar** e, em seguida, use o **separador DNS.**
      * No **separador 'Gestor do Servidor' Todos os Servidores,** clique no servidor WAP e selecione **'Gerir's Como**. Introduza credenciais locais (não domínio) para a máquina WAP.
      * Para validar a conectividade remota powerShell, no separador Gestor do **Servidor Todos os Servidores,** clique com o botão direito no servidor WAP e selecione **Windows PowerShell**. Uma sessão powerShell remota deve ser aberta para garantir que sessões remotas do PowerShell possam ser estabelecidas.

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
* Recomendamos que utilize o mesmo certificado TLS/SSL em todos os nós da sua fazenda AD FS e todos os servidores Proxy de aplicações web.
* O certificado deve ser um certificado X509.
* Você pode usar um certificado auto-assinado em servidores da federação em um ambiente de laboratório de teste. Para um ambiente de produção, recomendamos que obtenha o certificado de uma autoridade de certificados públicos.
  * Se estiver a utilizar um certificado que não seja de confiança pública, certifique-se de que o certificado instalado em cada servidor Proxy da Aplicação Web é confiável tanto no servidor local como em todos os servidores da federação.
* A identidade do certificado deve corresponder ao nome de serviço da federação (por exemplo, sts.contoso.com).
  * A identidade é ou uma extensão de nome alternativo (SAN) do tipo dNSName ou, se não houver entradas SAN, o nome do sujeito é especificado como um nome comum.
  * Várias entradas SAN podem estar presentes no certificado desde que uma delas corresponda ao nome de serviço da federação.
  * Se você está planejando usar Workplace Join, um SAN adicional é necessário com o **valor enterpriseregistration.** seguido pelo sufixo principal do utilizador (UPN) da sua organização, por exemplo, enterpriseregistration.contoso.com.
* Os certificados baseados em teclas de próxima geração da CryptoAPI (GNG) e fornecedores de armazenamento chave (KSPs) não são suportados. Como resultado, deve utilizar um certificado baseado num prestador de serviços criptográfico (CSP) e não num KSP.
* Os certificados wild-card são suportados.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes para servidores da federação
* Configurar registos DNS para o nome AD FS (por exemplo, sts.contoso.com) tanto para a intranet (o seu servidor DNS interno) como para a extranet (DNS público através do seu registo de domínio). Para o registo intranet DNS, certifique-se de que utiliza registos A e não de CNAME. A utilização de registos A é necessária para que a autenticação do Windows funcione corretamente a partir da sua máquina de união de domínios.
* Se estiver a implementar mais de um servidor AD FS ou servidor Proxy de aplicações web, certifique-se de que configura o seu equilibrador de carga e que os registos DNS para o nome AD FS (por exemplo, sts.contoso.com) apontam para o balançador de carga.
* Para a autenticação integrada do Windows funcionar para aplicações de navegador utilizando o Internet Explorer na sua intranet, certifique-se de que o nome AD FS (por exemplo, sts.contoso.com) é adicionado à zona intranet no Internet Explorer. Este requisito pode ser controlado através da Política de Grupo e implementado em todos os seus computadores unidos pelo domínio.

## <a name="azure-ad-connect-supporting-components"></a>Componentes de suporte AD AD AD
O Azure AD Connect instala os seguintes componentes no servidor onde o Azure AD Connect está instalado. Esta lista destina-se a uma instalação básica do Expresso. Se optar por utilizar um servidor SQL diferente na página de **serviços de sincronização instalar,** o SQL Express LocalDB não está instalado localmente.

* Azure AD Connect Health
* Utilitários da linha de comando Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Cliente Nativo
* Pacote de redistribuição Do Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para Azure AD Connect
A tabela a seguir mostra os requisitos mínimos para o computador sincronizado Azure AD Connect.

| Número de objetos no Diretório Ativo | CPU | Memória | Tamanho do disco rígido |
| --- | --- | --- | --- |
| Menos de 10.000 |1,6 GHz |4GB |70 GB |
| 10,000–50,000 |1,6 GHz |4GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Para 100.000 ou mais objetos, a versão completa do SQL Server é necessária | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Mais de 600.000 |1,6 GHz |32 GB |500 GB |

Os requisitos mínimos para computadores que executam servidores de procuração de AD FS ou aplicação web são:

* CPU: Núcleo duplo 1,6 GHz ou superior
* Memória: 2 GB ou superior
* Azure VM: Configuração A2 ou superior

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).