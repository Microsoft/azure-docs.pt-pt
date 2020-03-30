---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14a6703b3e256d33ab3b18e1821587cc3eb293db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79381747"
---
É importante manter a sua máquina virtual (VM) segura para as aplicações que executa. A segurança dos seus VMs pode incluir um ou mais serviços Azure e funcionalidades que cobrem o acesso seguro aos seus VMs e armazenamento seguro dos seus dados. Este artigo fornece informações que lhe permitem manter o seu VM e aplicações seguros.

## <a name="antimalware"></a>Antimalware

O moderno cenário de ameaça para ambientes de nuvens é dinâmico, aumentando a pressão para manter uma proteção eficaz de forma a satisfazer os requisitos de conformidade e segurança. O [Microsoft Antimalware para o Azure](../articles/security/fundamentals/antimalware.md) é uma capacidade de proteção gratuita em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Os alertas podem ser configurados para notificá-lo quando conhecidos software malicioso ou indesejado tenta instalar-se ou executar no seu VM. Não é suportado em VMs executando Linux ou Windows Server 2008.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Azure Security Center](../articles/security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder a ameaças aos seus VMs. O Security Center fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com um vasto ecossistema de soluções de segurança.

O acesso just-in-time do Security Center pode ser aplicado através da sua implementação vm para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VMs quando necessário. Quando o tempo está apenas ativo e um utilizador pede acesso a um VM, o Security Center verifica quais as permissões que o utilizador tem para o VM. Se tiverem as permissões corretas, o pedido é aprovado e o Security Center configura automaticamente os Grupos de Segurança da Rede (NSGs) para permitir o tráfego de entrada nas portas selecionadas por um período limitado de tempo. Após o tempo expirado, o Centro de Segurança restaura os NSGs aos seus estados anteriores. 

## <a name="encryption"></a>Encriptação

São oferecidos dois métodos de encriptação para discos geridos. Encriptação ao nível dos OS, que é encriptação do disco Azure, e encriptação ao nível da plataforma, que é encriptação do lado do servidor.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os discos geridos pelo Azure encriptam automaticamente os seus dados por padrão quando os persistem na nuvem. A encriptação do lado do servidor protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Os dados em discos geridos pelo Azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2.

A encriptação não afeta o desempenho dos discos geridos. Não há custos adicionais para a encriptação.

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

Para saber mais sobre encriptação do lado do servidor, consulte os artigos para [Windows](../articles/virtual-machines/windows/disk-encryption.md) ou [Linux](../articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Para uma segurança e conformidade melhoradas do [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) e [Linux VM,](../articles/virtual-machines/linux/disk-encryption-overview.md) os discos virtuais em Azure podem ser encriptados. Os discos virtuais nos VMs do Windows são encriptados em repouso utilizando o BitLocker. Os discos virtuais nos VMs linux são encriptados em repouso usando dm-criptografia. 

Não há nenhum custo para encriptar discos virtuais em Azure. As chaves criptográficas são armazenadas no Cofre de Chaves Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificados para normas de nível 2 FIPS 140-2. Estas teclas criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar a sua utilização. Um diretor de serviço de Diretório Ativo Azure fornece um mecanismo seguro para a emissão destas chaves criptográficas, uma vez que os VMs são ligados e desligados.

## <a name="key-vault-and-ssh-keys"></a>Cofre chave e chaves SSH

Segredos e certificados podem ser modelados como recursos e fornecidos pela [Key Vault](../articles/key-vault/key-vault-whatis.md). Pode utilizar o Azure PowerShell para criar cofres chave para [VMs Windows](../articles/virtual-machines/windows/key-vault-setup.md) e o Azure CLI para [VMs Linux](../articles/virtual-machines/linux/key-vault-setup.md). Também pode criar chaves para encriptação.

As políticas de acesso ao cofre chave concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Por outras palavras, a [política de acesso ao cofre chave](../articles/key-vault/key-vault-secure-your-key-vault.md) não suporta permissões ao nível do objeto.

Quando se conecta a VMs, deve utilizar criptografia de chave pública para fornecer uma forma mais segura de iniciar sessão. Este processo envolve uma troca de chaves públicas e privadas utilizando o comando de concha segura (SSH) para autenticar-se em vez de um nome de utilizador e senha. As palavras-passe são vulneráveis a ataques de força bruta, especialmente em VMs virados para a Internet, como servidores web. Com um par de chaves de concha segura (SSH), pode criar um [VM Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) que utiliza chaves SSH para autenticação, eliminando a necessidade de senhas para iniciar sessão. Também pode utilizar teclas SSH para ligar de um [VM do Windows](../articles/virtual-machines/linux/ssh-from-windows.md) a um VM Linux.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas para os recursos do Azure

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. 

A funcionalidade de identidades geridas para recursos do Azure no Active Directory (Azure AD) resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.  O seu código que está em execução num VM pode solicitar um símbolo de dois pontos finais que só são acessíveis dentro do VM. Para obter informações mais detalhadas sobre este serviço, reveja as identidades geridas para a página geral de [recursos do Azure.](../articles/active-directory/managed-identities-azure-resources/overview.md)   

## <a name="policies"></a>Políticas

As [políticas do Azure](../articles/azure-policy/azure-policy-introduction.md) podem ser usadas para definir o comportamento desejado para os [VMs](../articles/virtual-machines/windows/policy.md) windows e [VMs Linux](../articles/virtual-machines/linux/policy.md)da sua organização. Ao usar políticas, uma organização pode impor várias convenções e regras em toda a empresa. A aplicação do comportamento desejado pode ajudar a mitigar o risco, contribuindo para o sucesso da organização.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Utilizando [o controlo de acesso baseado em funções (RBAC),](../articles/role-based-access-control/overview.md)pode segregar tarefas dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores no seu VM de que necessitam para desempenhar em seu trabalho. Em vez de dar permissões sem restrições a todos no VM, só pode permitir certas ações. Pode configurar o controlo de acesso para o VM no [portal Azure,](../articles/role-based-access-control/role-assignments-portal.md)utilizando o [Azure CLI](https://docs.microsoft.com/cli/azure/role)ou[o Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Passos seguintes
- Caminhe pelas escadas para monitorizar a segurança da máquina virtual utilizando o Azure Security Center para [Linux](../articles/security/fundamentals/overview.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
