---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 53c9dea83fc6d413d7e82194696ffedabcc8cf7b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830152"
---
É importante manter sua VM (máquina virtual) segura para os aplicativos que você executa. Proteger suas VMs pode incluir um ou mais serviços e recursos do Azure que abrangem o acesso seguro às suas VMs e o armazenamento seguro de seus dados. Este artigo fornece informações que permitem que você mantenha a VM e os aplicativos seguros.

## <a name="antimalware"></a>Antimalware

O panorama de ameaças moderna para ambientes de nuvem é dinâmico, aumentando a pressão para manter a proteção efetiva a fim de atender aos requisitos de conformidade e segurança. [O Microsoft antimalware para Azure](../articles/security/fundamentals/antimalware.md) é um recurso de proteção em tempo real gratuito que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Os alertas podem ser configurados para notificá-lo quando um software mal-intencionado ou indesejado conhecido tentar se instalar ou executar em sua VM. Não há suporte para ele em VMs que executam o Linux ou o Windows Server 2008.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

A [central de segurança do Azure](../articles/security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças às suas VMs. A central de segurança fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um amplo ecossistema de soluções de segurança.

O acesso just-in-time da central de segurança pode ser aplicado em sua implantação de VM para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. Quando just-in-time está habilitado e um usuário solicita acesso a uma VM, a central de segurança verifica quais permissões o usuário tem para a VM. Se eles tiverem as permissões corretas, a solicitação será aprovada e a central de segurança configurará automaticamente os NSGs (grupos de segurança de rede) para permitir o tráfego de entrada para as portas selecionadas por um período limitado. Depois que o tempo expirou, a central de segurança restaura o NSGs para seus Estados anteriores. 

## <a name="encryption"></a>Encriptação

Para maior segurança e conformidade da VM do [Windows](../articles/virtual-machines/windows/encrypt-disks.md) e da VM do [Linux](../articles/virtual-machines/linux/disk-encryption-overview.md) , os discos virtuais no Azure podem ser criptografados. Os discos virtuais em VMs do Windows são criptografados em repouso usando o BitLocker. Os discos virtuais em VMs do Linux são criptografados em repouso usando DM-cript. 

Não há nenhum custo para criptografar discos virtuais no Azure. As chaves de criptografia são armazenadas em Azure Key Vault usando a proteção de software ou você pode importar ou gerar suas chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Essas chaves de criptografia são usadas para criptografar e descriptografar discos virtuais anexados à sua VM. Você mantém o controle dessas chaves de criptografia e pode auditar seu uso. Uma entidade de serviço Azure Active Directory fornece um mecanismo seguro para emitir essas chaves de criptografia à medida que as VMs são ativadas e desativadas.

## <a name="key-vault-and-ssh-keys"></a>Chaves Key Vault e SSH

Os segredos e certificados podem ser modelados como recursos e fornecidos pelo [Key Vault](../articles/key-vault/key-vault-whatis.md). Você pode usar Azure PowerShell para criar cofres de chaves para [VMs do Windows](../articles/virtual-machines/windows/key-vault-setup.md) e o CLI do Azure para VMs do [Linux](../articles/virtual-machines/linux/key-vault-setup.md). Você também pode criar chaves para criptografia.

As políticas de acesso do cofre de chaves concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Em outras palavras, a [política de acesso do cofre de chaves](../articles/key-vault/key-vault-secure-your-key-vault.md) não oferece suporte a permissões de nível de objeto.

Ao se conectar a VMs, você deve usar a criptografia de chave pública para fornecer uma maneira mais segura de entrar neles. Esse processo envolve uma troca de chaves pública e privada usando o comando SSH (Secure Shell) para se autenticar em vez de um nome de usuário e senha. As senhas são vulneráveis a ataques de força bruta, especialmente em VMs voltadas para a Internet, como servidores Web. Com um par de chaves SSH (Secure Shell), você pode criar uma [VM do Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) que usa chaves SSH para autenticação, eliminando a necessidade de senhas para entrar. Você também pode usar chaves SSH para se conectar de uma [VM do Windows](../articles/virtual-machines/linux/ssh-from-windows.md) a uma VM do Linux.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas para os recursos do Azure

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. 

A funcionalidade de identidades geridas para recursos do Azure no Active Directory (Azure AD) resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.  Seu código em execução em uma VM pode solicitar um token de dois pontos de extremidade acessíveis somente de dentro da VM. Para obter informações mais detalhadas sobre esse serviço, examine a página Visão geral de [identidades gerenciadas para recursos do Azure](../articles/active-directory/managed-identities-azure-resources/overview.md) .   

## <a name="policies"></a>Políticas

[As políticas do Azure](../articles/azure-policy/azure-policy-introduction.md) podem ser usadas para definir o comportamento desejado para as VMs do [Windows](../articles/virtual-machines/windows/policy.md) e VMs do [Linux](../articles/virtual-machines/linux/policy.md)de sua organização. Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco enquanto contribui para o sucesso da organização.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Usando o [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md), você pode separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários em sua VM que eles precisam para executar seus trabalhos. Em vez de fornecer a todos permissões irrestritas na VM, você pode permitir apenas determinadas ações. Você pode configurar o controle de acesso para a VM no [portal do Azure](../articles/role-based-access-control/role-assignments-portal.md), usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/role)ou[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Passos seguintes
- Percorra as etapas para monitorar a segurança da máquina virtual usando a central de segurança do Azure para [Linux](../articles/security/fundamentals/overview.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
