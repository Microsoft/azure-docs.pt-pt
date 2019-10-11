---
title: Conceitos de gerenciamento para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre como administrar um Azure Active Directory Domain Services domínio gerenciado e o comportamento de contas de usuário e senhas
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: b82927efa9054e71379d01993d1669527bc71402
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249428"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Conceitos de gerenciamento para contas de usuário, senhas e administração no Azure Active Directory Domain Services

Quando você cria e executa um domínio gerenciado Azure Active Directory Domain Services (AD DS), há algumas diferenças no comportamento em comparação com um ambiente de AD DS local tradicional. Você usa as mesmas ferramentas administrativas do Azure AD DS como um domínio autogerenciado, mas não pode acessar diretamente os controladores de domínio (DC). Há também algumas diferenças no comportamento de políticas de senha e hashes de senha, dependendo da origem da criação da conta de usuário.

Este artigo conceitual fornece detalhes sobre como administrar um domínio gerenciado do Azure AD DS e o comportamento diferente de contas de usuário, dependendo da maneira como eles são criados.

## <a name="domain-management"></a>Gerenciamento de domínio

No Azure AD DS, os controladores de domínio (DCs) que contêm todos os recursos, como usuários e grupos, credenciais e políticas, fazem parte do serviço gerenciado. Para redundância, dois DCs são criados como parte de um domínio gerenciado do Azure AD DS. Você não pode entrar nesses DCs para executar tarefas de gerenciamento. Em vez disso, você cria uma VM de gerenciamento que é unida ao domínio gerenciado AD DS do Azure e, em seguida, instala suas ferramentas de gerenciamento de AD DS regulares. Você pode usar o Centro Administrativo do Active Directory ou snap-ins do MMC (console de gerenciamento Microsoft), como o DNS ou objetos Política de Grupo, por exemplo.

## <a name="user-account-creation"></a>Criação de conta de usuário

As contas de usuário podem ser criadas no Azure AD DS de várias maneiras. A maioria das contas de usuário é sincronizada no Azure AD, que também pode incluir a conta de usuário sincronizada de um ambiente de AD DS local. Você também pode criar manualmente as contas diretamente no AD DS do Azure. Alguns recursos, como a sincronização de senha inicial ou a política de senha, se comportam de forma diferente dependendo de como e onde as contas de usuário são criadas.

* A conta de usuário pode ser sincronizada no Azure AD. Isso inclui contas de usuário somente em nuvem criadas diretamente no Azure AD e contas de usuário híbridos sincronizadas de um ambiente de AD DS local usando Azure AD Connect.
    * A maioria das contas de usuário no Azure AD DS são criadas por meio do processo de sincronização do Azure AD.
* A conta de usuário pode ser criada manualmente em um domínio gerenciado AD DS do Azure e não existe no Azure AD.
    * Se você precisar criar contas de serviço para aplicativos que só são executados no Azure AD DS, você pode criá-los manualmente no domínio gerenciado. Como a sincronização é unidirecional do Azure AD, as contas de usuário criadas no Azure AD DS não são sincronizadas de volta para o Azure AD.

## <a name="password-policy"></a>Política de senha

O AD DS do Azure inclui uma política de senha padrão que define as configurações de itens como o bloqueio de conta, a duração máxima da senha e a complexidade da senha. Configurações como política de bloqueio de conta se aplicam a todos os usuários no Azure AD DS, independentemente de como o usuário foi criado, conforme descrito na seção anterior. Algumas configurações, como o comprimento mínimo da senha e a complexidade da senha, só se aplicam a usuários criados diretamente no Azure AD DS.

Você pode criar suas próprias políticas de senha personalizadas para substituir a política padrão no Azure AD DS. Essas políticas personalizadas podem ser aplicadas a grupos de usuários específicos, conforme necessário.

Para obter mais informações sobre as diferenças em como as políticas de senha são aplicadas dependendo da origem da criação do usuário, consulte [políticas de bloqueio de senha e conta em domínios gerenciados][password-policy].

## <a name="password-hashes"></a>Hashes de senha

Para autenticar usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para autenticação Kerberos e NTLM (NT LAN Manager). O Azure AD não gera ou armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena nenhuma credencial de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente esses hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

Para contas de usuário somente em nuvem, os usuários devem alterar suas senhas antes de poderem usar o Azure AD DS. Esse processo de alteração de senha faz com que os hashes de senha para autenticação Kerberos e NTLM sejam gerados e armazenados no Azure AD.

Para usuários sincronizados de um ambiente de AD DS local usando Azure AD Connect, [habilite a sincronização de hashes de senha][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect sincroniza somente os hashes de senha herdados quando você habilita o Azure AD DS para seu locatário do Azure AD. Os hashes de senha herdados não serão usados se você usar apenas Azure AD Connect para sincronizar um ambiente de AD DS local com o Azure AD.
>
> Se seus aplicativos herdados não usam autenticação NTLM ou associações simples LDAP, recomendamos que você desabilite a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [desabilitar pacotes de criptografia fracos e sincronização de hash de credencial NTLM][secure-domain].

Uma vez configurado adequadamente, os hashes de senha utilizáveis são armazenados no domínio gerenciado AD DS do Azure. Se você excluir o domínio gerenciado AD DS do Azure, todos os hashes de senha armazenados nesse ponto também serão excluídos. As informações de credenciais sincronizadas no Azure AD não poderão ser reutilizadas se você criar posteriormente um domínio gerenciado do Azure AD DS-você deve reconfigurar a sincronização de hash de senha para armazenar os hashes de senha novamente. As VMs previamente Unidas ao domínio ou os usuários não poderão autenticar imediatamente – o AD do Azure precisa gerar e armazenar os hashes de senha no novo domínio gerenciado do Azure AD DS. Para obter mais informações, consulte [processo de sincronização de hash de senha para Azure AD DS e Azure ad Connect][azure-ad-password-sync].

## <a name="next-steps"></a>Passos seguintes

Para começar, [crie um domínio gerenciado do Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
