---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c44b39effdc6d8fcdc144915ec7b51489e3798cd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184224"
---
É importante proteger a sua máquina virtual (VM) para as aplicações que executar. Proteger as VMs pode incluir um ou mais serviços do Azure e os recursos que abrangem o acesso seguro às suas VMs e armazenamento seguro dos seus dados. Este artigo fornece informações que lhe permite proteger a sua VM e aplicações.

## <a name="antimalware"></a>Antimalware

O Panorama de ameaças modernas para ambientes de nuvem é dinâmico, aumentando a pressão para manter a proteção em vigor para cumprir os requisitos de segurança e conformidade. [O Microsoft Antimalware para o Azure](../articles/security/azure-security-antimalware.md) é uma funcionalidade de proteção em tempo real gratuito que ajuda a identificar e remover vírus, spyware e outro software malicioso. Alertas podem ser configurados para ser notificado quando conhecido software malicioso ou indesejável se tentar instalar-se ou executar na sua VM.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[Centro de segurança do Azure](../articles/security-center/security-center-intro.md) ajuda a evitar, detetar e responder a ameaças às suas VMs. Centro de segurança fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Acesso de just-in-time do Centro de segurança pode ser aplicado em toda a implementação de VM para bloquear o tráfego de entrada para as VMs do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário. Quando o just-in-time estiver ativado e um utilizador pedir acesso a uma VM, o Centro de segurança verifica quais as permissões que o utilizador tem para a VM. Se eles tiverem as permissões corretas, o pedido for aprovado e Centro de segurança configura automaticamente os grupos de segurança de rede (NSGs) para permitir o tráfego de entrada para as portas selecionados durante um período limitado de tempo. Após a expiração do tempo, o Centro de segurança restaurar os NSGs para o respetivo estado anterior. 

## <a name="encryption"></a>Encriptação

Para avançada [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) e [VM do Linux](../articles/virtual-machines/linux/encrypt-disks.md) segurança e conformidade, discos virtuais no Azure podem ser encriptados. Discos virtuais em VMs do Windows são encriptados em descanso ao utilizar o BitLocker. Discos virtuais em VMs do Linux são encriptados em descanso ao utilizar o dm-crypt. 

Não existe nenhum custo associado para encriptar discos virtuais no Azure. As chaves criptográficas são armazenadas no Azure Key Vault com a proteção de software, ou pode importar ou gerar as suas chaves em módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 de padrões no nível 2. Estas chaves criptográficas são utilizados para encriptar e desencriptar os discos virtuais anexados a sua VM. Manter o controlo destas chaves criptográficas e pode auditar o seu uso. Um principal de serviço do Azure Active Directory fornece um mecanismo seguro para a emissão estas chaves criptográficas, como as VMs têm a tecnologia e desativar.

## <a name="key-vault-and-ssh-keys"></a>Cofre de chaves e chaves SSH

Segredos e certificados podem ser modelados como recursos e fornecidos pela [Key Vault](../articles/key-vault/key-vault-whatis.md). Pode utilizar o Azure PowerShell para criar cofres de chaves para [VMs do Windows](../articles/virtual-machines/windows/key-vault-setup.md) e a CLI do Azure para [VMs do Linux](../articles/virtual-machines/linux/key-vault-setup.md). Também pode criar chaves de encriptação.

Políticas de acesso do Cofre de chaves concedem permissões para chaves, segredos e certificados em separado. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Em outras palavras, [política de acesso do Cofre de chaves](../articles/key-vault/key-vault-secure-your-key-vault.md) não suporta permissões ao nível do objeto.

Quando ligar às VMs, deve utilizar a criptografia de chave pública para fornecer uma forma mais segura de iniciar sessão para eles. Este processo envolve uma troca de chaves pública e privada usando o comando do secure shell (SSH) para autenticar-se em vez de um nome de utilizador e palavra-passe. As palavras-passe são vulneráveis a ataques, especialmente em VMs com acesso à Internet, tais como servidores web de força bruta. Com um par de chaves secure shell (SSH), pode criar uma [VM do Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) que usa chaves SSH para autenticação, eliminando a necessidade de palavras-passe para início de sessão. Também pode utilizar chaves SSH para ligar a partir de um [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) para uma VM do Linux.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas para os recursos do Azure

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. 

A funcionalidade de identidades geridas para recursos do Azure no Active Directory (Azure AD) resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.  O código que está a executar uma VM pode solicitar um token de dois pontos finais que estão acessíveis apenas a partir de dentro da VM. Para obter mais informações sobre este serviço, reveja os [geridos identidades para recursos do Azure](../articles/active-directory/managed-identities-azure-resources/overview.md) página de descrição geral.   

## <a name="policies"></a>Políticas

[Políticas do Azure](../articles/azure-policy/azure-policy-introduction.md) pode ser usado para definir o comportamento desejado para a sua organização [VMs do Windows](../articles/virtual-machines/windows/policy.md) e [VMs do Linux](../articles/virtual-machines/linux/policy.md). Ao utilizar políticas, uma organização pode impor várias convenções e regras em toda a empresa. Imposição do comportamento desejado pode ajudar a mitigar o risco ao mesmo tempo que contribuem para o sucesso da organização.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Usando [controlo de acesso baseado em funções (RBAC)](../articles/role-based-access-control/overview.md), pode separar responsabilidades dentro da sua equipa e conceder apenas a quantidade de acesso a utilizadores na sua VM que precisam para desempenhar as suas funções. Em vez de dar todas as pessoas sem restrições permissões na VM, pode permitir que apenas determinadas ações. Pode configurar o controlo de acesso para a VM na [portal do Azure](../articles/role-based-access-control/role-assignments-portal.md), utilizando o [CLI do Azure](https://docs.microsoft.com/cli/azure/role), ou[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Passos Seguintes
- Siga os passos para monitorizar a segurança da máquina virtual utilizando o Centro de segurança do Azure [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).