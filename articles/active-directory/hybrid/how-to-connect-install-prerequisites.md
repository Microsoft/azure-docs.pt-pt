---
title: 'Azure AD Connect: Pré-requisitos e hardware | Documentos da Microsoft'
description: Este tópico descreve os pré-requisitos e os requisitos de hardware para o Azure AD Connect
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
ms.topic: conceptual
ms.date: 12/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21fe7c70c09ad41faf628db45d82b995c8f2515
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311445"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos para o Azure AD Connect
Este tópico descreve os pré-requisitos e os requisitos de hardware para o Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, existem algumas coisas que precisa.

### <a name="azure-ad"></a>Azure AD
* Um inquilino do Azure AD. Obtém uma com um [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). Pode utilizar um dos portais de seguintes para gerir o Azure AD Connect:
  * O [portal do Azure](https://portal.azure.com).
  * O [portal do Office](https://portal.office.com).  
* [Adicionar e verificar o domínio](../active-directory-domains-add-azure-portal.md) que pretende utilizar no Azure AD. Por exemplo, se planeja usar contoso.com para os seus utilizadores, em seguida, certifique-se de que este domínio foi verificado e não apenas a utilizar o domínio de padrão de contoso.onmicrosoft.com.
* Permite a um inquilino do Azure AD por objetos predefinidos 50k. Quando verificar o seu domínio, o limite foi aumentado para objetos de 300 k. Se precisar de objetos ainda mais no Azure AD, em seguida, terá de abrir um pedido de suporte para que o limite aumentou ainda mais. Se precisar de mais de 500 objetos de k, em seguida, precisa de uma licença, como o Office 365, Azure AD básico, Azure AD Premium ou Enterprise Mobility e segurança.

### <a name="prepare-your-on-premises-data"></a>Prepare os seus dados no local
* Uso [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros, tais como duplicados e problemas de formatação no seu diretório antes de sincronizar com o Azure AD e o Office 365.
* Revisão [funcionalidades de sincronização opcional pode ativar no Azure AD](how-to-connect-syncservice-features.md) e avaliar quais recursos deve habilitar.

### <a name="on-premises-active-directory"></a>Active Directory no local
* O AD florestas e de versão funcional nível de esquema tem de ser Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde que são cumpridos os requisitos de nível de esquema e de floresta.
* Se planeia utilizar a funcionalidade **repetição de escrita de palavra-passe**, em seguida, os controladores de domínio tem de estar no Windows Server 2008 R2 ou posterior.
* O controlador de domínio utilizado pelo Azure AD tem de ser gravável. É **nepodporuje** para utilizar um RODC (controlador de domínio só de leitura) e o Azure AD Connect não siga qualquer redirecionamentos de escrita.
* É **nepodporuje** utilizar florestas/domínios no local com o "pontuada" (nome contenha um ponto final ".") Nomes NetBios.
* É recomendado [ativar a Reciclagem do Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor do Azure AD Connect
* O Azure AD Connect não pode ser instalado no Small Business Server ou Windows Server Essentials, antes de 2019 (Windows Server Essentials 2019 é suportado). O servidor tem de utilizar Windows Server standard ou superior.
* O servidor do Azure AD Connect tem de ter uma GUI completa instalada. É **nepodporuje** para instalar no server core.
* O Azure AD Connect tem de estar instalado no Windows Server 2008 R2 ou posterior. Este servidor pode ser um controlador de domínio ou um servidor membro, ao utilizar as definições rápidas. Se utilizar definições personalizadas, em seguida, o servidor também pode ser autónomo e não tem de ser associado a um domínio.
* Se instalar o Azure AD Connect no Windows Server 2008 R2, em seguida, certifique-se aplicar as correções mais recentes do Windows Update. A instalação não é possível iniciar com um servidor sem patch.
* Se planeia utilizar a funcionalidade **sincronização de palavra-passe**, em seguida, o servidor do Azure AD Connect tem de estar no Windows Server 2008 R2 SP1 ou posterior.
* Se planeja usar uma **conta de serviço gerida de grupo**, em seguida, o servidor do Azure AD Connect tem de estar no Windows Server 2012 ou posterior.
* O servidor do Azure AD Connect tem de ter [.NET Framework 4.5.1](#component-prerequisites) ou posterior e [Microsoft PowerShell 3.0](#component-prerequisites) ou posterior instalado.
* O servidor do Azure AD Connect não pode ter a política de grupo de transcrição do PowerShell ativada.
* Se os serviços de Federação do Active Directory está a ser implementado, os servidores onde o AD FS ou o Proxy de aplicações Web estão instaladas tem de ser Windows Server 2012 R2 ou posterior. [Gestão remota do Windows](#windows-remote-management) tem de estar ativada nestes servidores para instalação remota.
* Se os serviços de Federação do Active Directory está a ser implementado, precisa [certificados SSL](#ssl-certificate-requirements).
* Se os serviços de Federação do Active Directory está a ser implementado, então tem de configurar [resolução de nomes](#name-resolution-for-federation-servers).
* Se os administradores globais tem a MFA ativada, em seguida, o URL **https://secure.aadcdn.microsoftonline-p.com** tem de constar da lista de sites fidedignos. Lhe for pedido para adicionar este site à lista de sites fidedignos, quando lhe for pedido para um desafio MFA e adicionou não antes. Pode utilizar o Internet Explorer para adicioná-lo aos seus sites fidedignos.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server utilizada pelo Azure AD Connect
* O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Por predefinição, é instalado um SQL Server 2012 Express LocalDB (uma versão leve do SQL Server Express). SQL Server Express tem um limite de tamanho de 10GB que permite-lhe gerir aproximadamente 100 000 objetos. Se precisar de gerir um volume maior de objetos de diretório, tem de apontar o Assistente de instalação para uma instalação diferente do SQL Server.
* Se utilizar um servidor SQL separado, em seguida, estes requisitos aplicam-se:
  * O Azure AD Connect suporta todas as versões do Microsoft SQL Server do SQL Server 2008 (com Service Pack mais recente) para o SQL Server 2017. Base de dados do Microsoft Azure SQL **nepodporuje** como uma base de dados.
  * Tem de utilizar um agrupamento do SQL maiúsculas de minúsculas. Esses agrupamentos são identificados com um \_CI_ no respetivo nome. É **nepodporuje** para utilizar um agrupamento de maiúsculas e minúsculas, identificado por \_CS_ no respetivo nome.
  * Só pode ter um motor de sincronização por instância de SQL. É **nepodporuje** para partilhar uma instância do SQL com a sincronização do FIM/MIM, o DirSync ou o Azure AD Sync.

### <a name="accounts"></a>Contas
* Uma conta de Administrador Global do Azure AD para o inquilino do Azure AD que pretende integrar. Esta conta tem de ser um **conta escolar ou da organização** e não pode ser um **conta Microsoft**.
* Se utilizar definições expressas ou atualizar do DirSync, tem de ter uma conta de administrador de empresa para o Active Directory no local.
* [Contas no Active Directory](reference-connect-accounts-permissions.md) se utilizar o caminho de instalação de definições personalizadas ou uma conta de administrador de empresa para o Active Directory no local.

### <a name="connectivity"></a>Conectividade
* O servidor do Azure AD Connect precisa de resolução de DNS para a intranet e internet. O servidor DNS tem de ser capaz de resolver nomes tanto para o Active Directory no local e os pontos de extremidade do Azure AD.
* Se tiver firewalls na sua Intranet e terá de abrir portas entre os servidores do Azure AD Connect e os controladores de domínio, em seguida, veja [as de portas do Azure AD Connect](reference-connect-ports.md) para obter mais informações.
* Se o seu proxy ou firewall limitar quais URLs podem ser acedidos, então os URLs documentados em [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) tem de ser aberto.
  * Se estiver a utilizar a Cloud da Microsoft na Alemanha ou para a nuvem do Microsoft Azure Government, veja [considerações de instâncias do serviço de sincronização do Azure AD Connect](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 e o depois) por padrão usa a TLS 1.2 para encriptar a comunicação entre o motor de sincronização e o Azure AD. Se o TLS 1.2 não está disponível no sistema operacional subjacente, o Azure AD Connect incrementalmente retrocede para protocolos mais antigos (TLS 1.1 e TLS 1.0).
* Anterior à versão 1.1.614.0, o Azure AD Connect, por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o motor de sincronização e o Azure AD. Para alterar para TLS 1.2, siga os passos em [ativar o TLS 1.2 para o Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se estiver a utilizar um proxy de saída para ligar à Internet, a definição seguinte no **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** ficheiro tem de ser adicionado para o Assistente de instalação e o Azure AD Ligar a sincronização para conseguir estabelecer ligação à Internet e do Azure AD. Este texto têm de ser introduzido na parte inferior do ficheiro. Nesse código, &lt;PROXYADDRESS&gt; representa o nome de anfitrião ou endereço IP do proxy real.

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

* Se o servidor proxy requer autenticação, em seguida, o [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) têm de estar localizados no domínio e tem de utilizar o caminho de instalação de definições personalizadas para especificar um [conta de serviço personalizado](how-to-connect-install-custom.md#install-required-components). Também precisa de uma alteração diferente em Machine. config. Com esta alteração no Machine. config, o motor de assistente e sincronização de instalação responder a pedidos de autenticação do servidor proxy. Em todas as páginas do Assistente de instalação, excluindo os **configurar** página, com sessão iniciada do usuário são utilizadas credenciais. Sobre o **configurar** página no final do Assistente de instalação, o contexto está definida para o [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que foi criado por si. A secção de Machine. config deve ter este aspeto.

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

* Quando o Azure AD Connect envia uma solicitação da web para o Azure AD como parte da sincronização de diretórios, do Azure AD pode demorar até 5 minutos a responder. É comum para os servidores de proxy para que a configuração de tempo limite de inatividade da ligação. Certifique-se de que a configuração é definida, pelo menos, 6 minutos ou mais.

Para obter mais informações, consulte o MSDN o [predefinido proxy elemento](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obter mais informações quando tiver problemas de conectividade, consulte [resolver problemas de conectividade](tshoot-connect-connectivity.md).

### <a name="other"></a>Outros
* Opcional: Uma conta de utilizador de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos do componente
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
O Azure AD Connect depende do Microsoft PowerShell e .NET Framework 4.5.1. Precisa esta versão ou uma versão posterior instalado no seu servidor. Dependendo da versão do Windows Server, efetue o seguinte:

* Windows Server 2012R2
  * Microsoft PowerShell está instalado por predefinição. Não é necessário realizar qualquer ação.
  * .NET framework 4.5.1 e versões posteriores são oferecidas por meio do Windows Update. Certifique-se de que instalou as atualizações mais recentes para o Windows Server, no painel de controlo.
* Windows Server 2008 R2 e Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4.0**, disponível no [Microsoft Download Center](https://www.microsoft.com/downloads).
  * Estão disponíveis no .NET framework 4.5.1 e versões posteriores [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Ativar o TLS 1.2 para o Azure AD Connect
Anterior à versão 1.1.614.0, o Azure AD Connect, por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o servidor de motor de sincronização e o Azure AD. Pode alterá-lo ao configurar as aplicações de .net para utilizar TLS 1.2 por predefinição no servidor. Podem encontrar mais informações sobre TLS 1.2 no [consultoria de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Não é possível ativar o TLS 1.2 antes do Windows Server 2008 R2 ou posterior. Certifique-se de que tem a correção do .net 4.5.1 instalada para o seu sistema operativo, consulte [consultoria de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Pode ter esta correção ou versão posterior já instaladas no seu servidor.
2. Se usar o Windows Server 2008 R2, certifique-se de que TLS 1.2 está ativada. No servidor Windows Server 2012 e versões posteriores, TLS 1.2 já deve estar ativada.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Para todos os sistemas operativos, definir esta chave de registo e reinicie o servidor.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Se também pretender ativar o TLS 1.2 entre o servidor de motor de sincronização e um SQL Server remoto, em seguida, confirme se tem as versões necessárias instaladas para [suporte de TLS 1.2 para Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para Federação de instalação e configuração
### <a name="windows-remote-management"></a>Gestão remota do Windows
Ao utilizar o Azure AD Connect para implementar serviços de Federação do Active Directory ou o Proxy de aplicações Web, verifique estes requisitos:

* Se o servidor de destino é associado a um domínio, em seguida, certifique-se de que o Managed remoto do Windows está ativado
  * Numa janela de comando PSH elevada, utilize o comando `Enable-PSRemoting –force`
* Se o servidor de destino for um domínio não associados a um computador WAP, em seguida, existem alguns requisitos adicionais
  * No computador de destino (máquina WAP):
    * Certifique-se o winrm (gestão remota do Windows / WS-Management) serviço está em execução por meio do snap-in Serviços
    * Numa janela de comando PSH elevada, utilize o comando `Enable-PSRemoting –force`
  * Na máquina em que o assistente está a executar (se a máquina de destino não de domínio não fidedigno ou associado ao domínio):
    * Numa janela de comando PSH elevada, utilize o comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * No Gestor de servidores:
      * Adicionar anfitrião de rede de Perímetro WAP ao agrupamento de máquina (Gestor de servidor -> Gerir -> adicionar servidores... Utilize o separador DNS)
      * Separador de Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor WAP e escolha gerenciar como..., introduza as credenciais do local (não de domínio) para a máquina WAP
      * Para validar a conectividade PSH remota, no separador de Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor WAP e escolha o Windows PowerShell. Uma sessão PSH remota deve ser aberto para garantir sessões do PowerShell remotos podem ser estabelecidas.

### <a name="ssl-certificate-requirements"></a>Requisitos de certificado SSL
* É vivamente recomendado utilizar o mesmo certificado SSL em todos os nós do farm do AD FS e todos os servidores de proxy de aplicação Web.
* O certificado tem de ser uma X509 certificado.
* Pode utilizar um certificado autoassinado em servidores de Federação num ambiente de laboratório de teste. No entanto, para um ambiente de produção, recomendamos que obtenha o certificado de uma AC pública.
  * Se utilizar um certificado que não é publicamente fidedigno, certifique-se de que o certificado instalado em cada servidor de Proxy de aplicações Web é confiável no servidor local e em todos os servidores de Federação
* A identidade do certificado tem de corresponder ao nome do serviço de Federação (por exemplo, sts.contoso.com).
  * A identidade for de qualquer uma extensão de nome alternativo (SAN) de assunto do tipo dNSName ou, se não houver nenhuma entrada de SAN, o nome do requerente especificado como um nome comum.  
  * Várias entradas de SAN podem ser presentes no certificado, desde que uma delas corresponde ao nome de serviço de Federação.
  * Se estiver a planear utilizar a associação à área de trabalho, uma SAN adicional é necessário com o valor **enterpriseregistration.** seguido pelo sufixo de nome Principal de utilizador (UPN) da sua organização, por exemplo, **enterpriseregistration.contoso.com**.
* Certificados com base nas chaves do CryptoAPI next generation (CNG) e provedores de armazenamento de chaves não são suportados. Isso significa que tem de utilizar um certificado com base num CSP (provedor de serviços de criptografia) e não um KSP (fornecedor de armazenamento de chaves).
* Os certificados de caráter universal são suportados.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes de servidores de Federação
* Configure registos DNS para o nome de serviço de Federação (por exemplo, sts.contoso.com) de AD FS para a intranet (o servidor DNS interno) e extranet (DNS público por meio de sua entidade de registo do domínio). Para o registo DNS de intranet, certifique-se de que use A registros e não os registos CNAME. Isto é necessário para a autenticação do windows funcione corretamente a partir do seu computador associado ao domínio.
* Se estiver a implementar mais de um servidor do AD FS ou servidor de Proxy de aplicações Web, em seguida, certifique-se de que configurou o seu Balanceador de carga e que os registos DNS para o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) apontam para o Balanceador de carga.
* Para a autenticação integrada do windows a trabalhar para aplicativos de navegador usando o Internet Explorer na sua intranet, certifique-se de que o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) é adicionado à zona da intranet no IE. Isso pode ser controlado através da política de grupo e implementado em todos os computadores associados a um domínio.

## <a name="azure-ad-connect-supporting-components"></a>O Azure AD Connect, componentes de suporte
Segue-se uma lista de componentes do Azure AD Connect instala no servidor onde está instalado o Azure AD Connect. Esta lista é para uma instalação básica do Express. Se optar por utilizar um SQL Server diferente na página de serviços de sincronização de instalação, em seguida, SQL Express LocalDB não está instalado localmente.

* Azure AD Connect Health
* Utilitários da linha de comando do Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Pacote de redistribuição do Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para o Azure AD Connect
A tabela abaixo mostra os requisitos mínimos para o computador de sincronização do Azure AD Connect.

| Número de objetos no Active Directory | CPU | Memória | Tamanho de disco rígido |
| --- | --- | --- | --- |
| Menos de 10 000 |1.6 GHz |4GB |70 GB |
| 10,000–50,000 |1.6 GHz |4GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| Para 100 000 ou mais objetos é necessária a versão completa do SQL Server | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| Mais do que 600.000 |1.6 GHz |32 GB |500 GB |

Os requisitos mínimos para computadores que executam o AD FS ou servidores de aplicações Web é o seguinte:

* CPU: Dual núcleos de 1,6 GHz ou superior
* MEMÓRIA: 2 GB ou superior
* VM do Azure: Configuração de a2 ou superior

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
