---
title: Arquitetura Azure HDInsight com pacote de segurança empresarial
description: Saiba como planear a segurança Azure HDInsight com o Pacote de Segurança Empresarial.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365794"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Utilize o pacote de segurança da empresa no HDInsight

O cluster Standard Azure HDInsight é um cluster de um único utilizador. É adequado para a maioria das empresas que têm equipas de aplicação mais pequenas construindo grandes cargas de dados. Cada utilizador pode criar um cluster dedicado a pedido e destruí-lo quando já não é necessário.

Muitas empresas avançaram para um modelo em que as equipas de TI gerem clusters, e várias equipas de aplicação partilham clusters. Estas grandes empresas precisam de acesso multiutilizador a cada cluster em Azure HDInsight.

O HDInsight conta com um fornecedor de identidade popular-- Ative Directory -- de uma forma gerida. Ao integrar o HDInsight com os Serviços de [Domínio de Diretório Ativo Azure (Azure AD DS),](../../active-directory-domain-services/overview.md)pode aceder aos clusters utilizando as suas credenciais de domínio.

As máquinas virtuais (VMs) em HDInsight são domínio sintetizado ao seu domínio fornecido. Assim, todos os serviços em execução no HDInsight (Apache Ambari, servidor Apache Hive, Apache Ranger, Servidor de segunda mão Apache Spark, entre outros) funcionam perfeitamente para o utilizador autenticado. Os administradores podem então criar políticas de autorização fortes usando o Apache Ranger para fornecer um controlo de acesso baseado em papéis para recursos no cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight no Active Directory

Apache Hadoop de código aberto baseia-se no protocolo Kerberos para autenticação e segurança. Portanto, os nós de cluster HDInsight com pacote de segurança empresarial (ESP) são unidos a um domínio que é gerido pelo Azure AD DS. A segurança kerberos está configurada para os componentes Hadoop no cluster.

As seguintes coisas são criadas automaticamente:

- Um diretor de serviço para cada componente Hadoop
- Um diretor de máquinas para cada máquina que está unida ao domínio
- Uma Unidade Organizacional (OU) para cada cluster armazenar estes diretores de serviço e máquinas

Para resumir, é preciso criar um ambiente com:

- Um domínio de Diretório Ativo (gerido por Azure AD DS). **O nome de domínio deve ter 39 caracteres ou menos para trabalhar com o Azure HDInsight.**
- LDAP seguro (LDAPS) ativado em DS AD Azure.
- Conectividade de rede adequada da rede virtual HDInsight para a rede virtual Azure AD DS, se escolher redes virtuais separadas para eles. Um VM dentro da rede virtual HDInsight deve ter uma linha de visão para O DS Azure Através do epeering virtual da rede. Se hDInsight e Azure AD DS forem implantados na mesma rede virtual, a conectividade é fornecida automaticamente e não são necessárias mais medidas.

## <a name="set-up-different-domain-controllers"></a>Configurar diferentes controladores de domínio

AhDInsight suporta atualmente apenas O Azure AD DS como o controlador de domínio principal que o cluster utiliza para a comunicação kerberos. Mas outras configurações complexas do Ative Directory são possíveis, desde que tal configuração conduza a permitir o Acesso AD DS Azure para o HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[O Azure AD DS](../../active-directory-domain-services/overview.md) fornece um domínio gerido que é totalmente compatível com o Diretório Ativo do Windows Server. A Microsoft cuida de gerir, corrigir e monitorizar o domínio numa configuração altamente disponível (HA). Pode implantar o seu cluster sem se preocupar em manter controladores de domínio.

Os utilizadores, grupos e senhas são sincronizados a partir de Azure AD. A sincronização unidirecional da sua instância Azure AD para O DS Azure permite que os utilizadores inscrevam-se no cluster utilizando as mesmas credenciais corporativas.

Para mais informações, consulte [os clusters Configure HDInsight com ESP utilizando O DS Azure](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Diretório Ativo no local ou Diretório Ativo em VMs IaaS

Se tiver uma instância de Diretório Ativo no local ou configurações de Diretório Ativo mais complexas para o seu domínio, pode sincronizar essas identidades com o Azure AD Através do Azure AD Connect. Em seguida, pode ativar o Azure AD DS naquele inquilino do Diretório Ativo.

Como kerberos depende de hashes de senha, você deve ativar a sincronização de hash de [senha no Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

Se estiver a utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS), deve ativar a sincronização de hash de senha. (Para uma configuração recomendada, consulte [este vídeo](https://youtu.be/qQruArbu2Ew).) A sincronização de hash de palavra-passe ajuda na recuperação de desastres no caso da sua infraestrutura AD FS falhar, e também ajuda a fornecer proteção credencial vazada. Para mais informações, consulte [Enable password hash sync with Azure AD Connect sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Utilizar no local o Diretório Ativo ou O Diretório Ativo apenas em VMs IaaS, sem Azure AD e Azure AD DS, não é uma configuração suportada para clusters HDInsight com ESP.

Se a federação estiver a ser utilizada e as hashes de palavra-passe estiverem sincronizadas corretamente, mas está a obter falhas de autenticação, verifique se a autenticação de palavra-passe em nuvem está ativada para o principal de serviço powerShell. Caso contrário, deve definir uma política de [Home Realm Discovery (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) para o seu inquilino Azure AD. Para verificar e definir a política de HRD:

1. Instale o módulo de pré-visualização [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Conecte-se utilizando credenciais de administrador global (administrador de inquilinos).

   ```powershell
   Connect-AzureAD
   ```

3. Verifique se o diretor de serviço microsoft Azure PowerShell já foi criado.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Se não existe, crie o diretor de serviço.

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

## <a name="next-steps"></a>Passos seguintes

- [Configure os clusters HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Configure as políticas da Hive Apache para clusters HDInsight com ESP](apache-domain-joined-run-hive.md)
- [Gerir clusters HDInsight com ESP](apache-domain-joined-manage.md)
