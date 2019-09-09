---
title: Arquitetura do Azure HDInsight com Enterprise Security Package
description: Saiba como planejar a segurança do Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e7983c4da4803965dabaa6a471fbea8a2fba5229
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810954"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Usar Enterprise Security Package no HDInsight

O cluster do Azure HDInsight padrão é um cluster de usuário único. É adequado para a maioria das empresas que têm equipes de aplicativos menores que criam grandes cargas de trabalho de dados. Cada usuário pode criar um cluster dedicado sob demanda e destruí-lo quando não for mais necessário. 

Muitas empresas mudaram para um modelo no qual as equipes de ti gerenciam clusters e várias equipes de aplicativos compartilham clusters. Essas empresas maiores precisam de acesso multiusuário a cada cluster no Azure HDInsight.

O HDInsight conta com um provedor de identidade popular – Active Directory--de uma maneira gerenciada. Ao integrar o HDInsight ao [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md), você pode acessar os clusters usando suas credenciais de domínio. 

As máquinas virtuais (VMs) no HDInsight são ingressadas no domínio em seu domínio fornecido. Portanto, todos os serviços em execução no HDInsight (Apache Ambari, Apache Hive Server, Apache Ranger, Apache Spark Server thrift e outros) funcionam sem problemas para o usuário autenticado. Os administradores podem criar políticas de autorização fortes usando o Apache Ranger para fornecer controle de acesso baseado em função para recursos no cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight no Active Directory

O Apache Hadoop de código-fonte aberto depende do protocolo Kerberos para autenticação e segurança. Portanto, os nós do cluster HDInsight com Enterprise Security Package (ESP) são Unidos a um domínio gerenciado pelo AD DS do Azure. A segurança Kerberos está configurada para os componentes do Hadoop no cluster. 

Os itens a seguir são criados automaticamente:

- Uma entidade de serviço para cada componente do Hadoop
- Uma entidade de máquina para cada computador ingressado no domínio
- Uma UO (unidade organizacional) para cada cluster para armazenar essas entidades de serviço e computador

Para resumir, você precisa configurar um ambiente com:

- Um domínio Active Directory (gerenciado pelo AD DS do Azure). **O nome de domínio deve ter 39 caracteres ou menos para funcionar com o Azure HDInsight.**
- LDAP Seguro (LDAPs) habilitados no AD DS do Azure.
- Conectividade de rede adequada da rede virtual do HDInsight para a rede virtual do Azure AD DS, se você escolher redes virtuais separadas para elas. Uma VM dentro da rede virtual HDInsight deve ter uma linha de visão do Azure AD DS por meio do emparelhamento de rede virtual. Se o HDInsight e o AD DS do Azure forem implantados na mesma rede virtual, a conectividade será fornecida automaticamente e nenhuma ação adicional será necessária.

## <a name="set-up-different-domain-controllers"></a>Configurar controladores de domínio diferentes
Atualmente, o HDInsight dá suporte apenas ao Azure AD DS como o controlador de domínio principal que o cluster usa para comunicação Kerberos. Mas outras configurações de Active Directory complexas são possíveis, desde que essa configuração leve a habilitar o AD DS do Azure para acesso ao HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
O [Azure AD DS](../../active-directory-domain-services/overview.md) fornece um domínio gerenciado que é totalmente compatível com o Windows Server Active Directory. A Microsoft cuida do gerenciamento, aplicação de patches e monitoramento do domínio em uma configuração de alta disponibilidade (HA). Você pode implantar o cluster sem se preocupar com a manutenção de controladores de domínio. 

Usuários, grupos e senhas são sincronizados do Azure AD. A sincronização unidirecional de sua instância do Azure AD para o Azure AD DS permite que os usuários entrem no cluster usando as mesmas credenciais corporativas. 

Para obter mais informações, consulte [Configurar clusters HDInsight com ESP usando o Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Active Directory local ou Active Directory em VMs IaaS

Se você tiver uma instância de Active Directory local ou configurações de Active Directory mais complexas para seu domínio, poderá sincronizar essas identidades com o Azure AD usando Azure AD Connect. Em seguida, você pode habilitar o Azure AD DS nesse locatário de Active Directory. 

Como o Kerberos depende de hashes de senha, você deve [habilitar a sincronização de hash de senha no Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Se você estiver usando a Federação com Serviços de Federação do Active Directory (AD FS) (AD FS), deverá habilitar a sincronização de hash de senha. (Para uma instalação recomendada, consulte [este vídeo](https://youtu.be/qQruArbu2Ew).) A sincronização de hash de senha ajuda na recuperação de desastres caso sua infraestrutura de AD FS falhe e também ajude a fornecer proteção contra credenciais vazadas. Para obter mais informações, consulte [habilitar Sincronização de hash de senha com sincronização de Azure ad Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Usar o Active Directory local ou Active Directory em VMs IaaS sozinho, sem o Azure AD e o Azure AD DS, não é uma configuração com suporte para clusters HDInsight com ESP.

Se a Federação estiver sendo usada e os hashes de senha forem sincronizados corretamente, mas você estiver obtendo falhas de autenticação, verifique se a autenticação de senha de nuvem está habilitada para a entidade de serviço do PowerShell. Caso contrário, você deve definir uma [política de HRD (descoberta de realm inicial)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) para seu locatário do Azure AD. Para verificar e definir a política de HRD:

1. Instale o módulo versão prévia do [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Conecte-se usando credenciais de administrador global (administrador de locatários).
   
   ```powershell
   Connect-AzureAD
   ```

3. Verifique se a entidade de serviço Microsoft Azure PowerShell já foi criada.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Se ele não existir, crie a entidade de serviço.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Crie e anexe a política a essa entidade de serviço.

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

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters HDInsight com o ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Configurar políticas de Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md)
* [Gerenciar clusters HDInsight com o ESP](apache-domain-joined-manage.md) 
