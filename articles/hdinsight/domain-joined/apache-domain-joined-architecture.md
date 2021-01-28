---
title: Arquitetura Azure HDInsight com pacote de segurança empresarial
description: Saiba como planear a segurança Azure HDInsight com o Pacote de Segurança Empresarial.
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 404cd8652a4daf602f3326696ef5df2c3a23a548
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943323"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Utilizar o Pacote de Segurança Enterprise no HDInsight

O cluster padrão Azure HDInsight é um cluster de um único utilizador. É adequado para a maioria das empresas que têm equipas de aplicação mais pequenas construindo grandes cargas de trabalho de dados. Cada utilizador pode criar um cluster dedicado a pedido e destruí-lo quando já não é necessário.

Muitas empresas avançaram para um modelo em que as equipas de TI gerem clusters, e várias equipas de aplicação partilham clusters. Estas grandes empresas precisam de acesso multiuser a cada cluster em Azure HDInsight.

O HDInsight conta com um fornecedor de identidade popular- Ative Directory -- de uma forma gerida. Ao integrar o HDInsight com [os Azure Ative Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md), pode aceder aos clusters utilizando as suas credenciais de domínio.

As máquinas virtuais (VMs) em HDInsight são domínios unidos ao seu domínio fornecido. Assim, todos os serviços em execução em HDInsight (Apache Ambari, apache hive server, Apache Ranger, Apache Spark thrift server, e outros) funcionam perfeitamente para o utilizador autenticado. Os administradores podem então criar políticas de autorização fortes utilizando o Apache Ranger para fornecer controlo de acesso baseado em funções para recursos no cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight no Active Directory

A Apache Hadoop de código aberto conta com o protocolo Kerberos para autenticação e segurança. Portanto, os nós de cluster HDInsight com Pacote de Segurança Empresarial (ESP) são unidos a um domínio que é gerido pela Azure AD DS. A segurança kerberos está configurada para os componentes hadoop no cluster.

As seguintes coisas são criadas automaticamente:

- Um principal de serviço para cada componente Hadoop
- Um diretor de máquina para cada máquina que se juntou ao domínio
- Uma Unidade Organizacional (OU) para cada cluster para armazenar estes diretores de serviço e máquinas

Para resumir, é necessário configurar um ambiente com:

- Um domínio ative directory (gerido por Azure AD DS). **O nome de domínio deve ser de 39 caracteres ou menos para trabalhar com Azure HDInsight.**
- LDAP Seguro (LDAPS) ativado em Azure AD DS.
- Conectividade de rede adequada da rede virtual HDInsight para a rede virtual Azure AD DS, se escolher redes virtuais separadas para eles. Um VM dentro da rede virtual HDInsight deve ter uma linha de visão para Azure AD DS através de um olhar de rede virtual. Se o HDInsight e o AD DS Azure forem implantados na mesma rede virtual, a conectividade é fornecida automaticamente e não são necessárias mais medidas.

## <a name="set-up-different-domain-controllers"></a>Configurar diferentes controladores de domínio

ATUALMENTE, o HDInsight suporta apenas o Azure AD DS como o principal controlador de domínio que o cluster utiliza para a comunicação Kerberos. Mas outras configurações complexas do Ative Directory são possíveis, desde que tal configuração conduza a permitir o acesso Azure AD DS para acesso HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[O Azure AD DS](../../active-directory-domain-services/overview.md) fornece um domínio gerido totalmente compatível com o Windows Server Ative Directory. A Microsoft cuida de gerir, remendar e monitorizar o domínio numa configuração altamente disponível (HA). Pode implantar o seu cluster sem se preocupar em manter controladores de domínio.

Os utilizadores, grupos e palavras-passe são sincronizados a partir do Azure AD. A sincronização unidirecional da sua instância AD Azure para Azure AD DS permite que os utilizadores entrem no cluster utilizando as mesmas credenciais corporativas.

Para obter mais informações, consulte [clusters Configure HDInsight com ESP utilizando Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Diretório ativo ou diretório ativo no IaaS VMs

Se tiver uma instância ative de diretório ou configurações mais complexas do Ative Directory para o seu domínio, pode sincronizar essas identidades com a Azure AD utilizando o Azure AD Connect. Em seguida, pode ativar a Azure AD DS no inquilino ative directory.

Como kerberos depende de hashes de palavra-passe, você deve [ativar a sincronização de haxixe de palavra-passe em Azure AD DS](../../active-directory-domain-services/tutorial-create-instance.md).

Se estiver a utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS), tem de ativar a sincronização de haxixe de palavra-passe. (Para uma configuração recomendada, consulte [este vídeo](https://youtu.be/qQruArbu2Ew).) O hash sync da palavra-passe ajuda na recuperação de desastres no caso da sua infraestrutura AD FS falhar, e também ajuda a fornecer proteção de credencial com fugas. Para obter mais informações, consulte [Ativar a sincronização de haxixe de palavra-passe com a sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Utilizar apenas no local o Ative Directory ou o Ative Directory em IaaS VMs, sem Azure AD e Azure AD DS, não é uma configuração suportada para clusters HDInsight com ESP.

Se a federação estiver a ser utilizada e as hashes de palavra-passe estiverem sincronizadas corretamente, mas está a obter falhas de autenticação, verifique se a autenticação de passwords em nuvem está ativada para o principal do serviço PowerShell. Caso contrário, deve definir uma [política home realm discovery (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) para o seu inquilino AZure AD. Para verificar e definir a política de HRD:

1. Instale o [módulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Conecte-se usando credenciais de administrador global (administrador de inquilinos).

   ```powershell
   Connect-AzureAD
   ```

3. Verifique se o diretor de serviços da Microsoft Azure PowerShell já foi criado.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Se não existe, então crie o diretor de serviço.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Crie e anexe a política a este diretor de serviço.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Próximos passos

- [Configurar clusters HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Configure as políticas de Colmeia Apache para clusters HDInsight com ESP](apache-domain-joined-run-hive.md)
- [Gerir clusters HDInsight com ESP](apache-domain-joined-manage.md)