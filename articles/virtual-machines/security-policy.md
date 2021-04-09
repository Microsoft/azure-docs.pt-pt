---
title: Políticas seguras e de utilização
description: Saiba mais sobre segurança e políticas para máquinas virtuais em Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 291b9a210bf5f8cc18ccf8a523e282d3fc85aa28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673357"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Proteger e utilizar políticas em máquinas virtuais no Azure

É importante manter a sua máquina virtual (VM) segura para as aplicações que executou. A segurança dos seus VMs pode incluir um ou mais serviços Azure e funcionalidades que cobrem o acesso seguro aos seus VMs e armazenamento seguro dos seus dados. Este artigo fornece informações que lhe permitem manter o seu VM e aplicações seguras.

## <a name="antimalware"></a>Antimalware

O panorama de ameaça moderna para ambientes em nuvem é dinâmico, aumentando a pressão para manter uma proteção eficaz de forma a satisfazer os requisitos de conformidade e segurança. [O Microsoft Antimalware for Azure](../security/fundamentals/antimalware.md) é uma capacidade de proteção gratuita em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Os alertas podem ser configurados para notificá-lo quando o software malicioso ou indesejado tentar instalar-se ou executar no seu VM. Não é suportado em VMs executando Linux ou Windows Server 2008.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Centro de Segurança Azure](../security-center/security-center-introduction.md) ajuda-o a prevenir, detetar e responder a ameaças aos seus VMs. O Security Center fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure, ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e trabalha com um amplo ecossistema de soluções de segurança.

O acesso just-in-time do Security Center pode ser aplicado através da sua implementação de VM para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. Quando o tempo é apenas a tempo e um utilizador solicita acesso a um VM, o Centro de Segurança verifica as permissões que o utilizador tem para o VM. Se tiverem as permissões corretas, o pedido é aprovado e o Centro de Segurança configura automaticamente os Grupos de Segurança da Rede (NSGs) para permitir o tráfego de entrada nas portas selecionadas por um período limitado de tempo. Após o tempo ter expirado, o Centro de Segurança restaura os NSGs aos estados anteriores. 

## <a name="encryption"></a>Encriptação

São oferecidos dois métodos de encriptação para discos geridos. Encriptação ao nível do SO, que é encriptação do disco Azure, e encriptação ao nível da plataforma, que é encriptação do lado do servidor.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

Os discos geridos pelo Azure encriptam automaticamente os seus dados por padrão ao persistirem na nuvem. A encriptação do lado do servidor protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. Os dados em discos geridos azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2.

A encriptação não afeta o desempenho dos discos geridos. Não há nenhum custo adicional para a encriptação.

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

Para saber mais sobre a encriptação do lado do servidor, consulte os artigos para [Windows](./disk-encryption.md) ou [Linux](./disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Para segurança e conformidade do [Windows VM](windows/disk-encryption-overview.md) e [Linux VM](linux/disk-encryption-overview.md) melhoradas, os discos virtuais em Azure podem ser encriptados. Os discos virtuais nos VMs do Windows são encriptados em repouso utilizando o BitLocker. Os discos virtuais em VMs Linux são encriptados em repouso usando a cripta dm. 

Não há nenhuma acusação para encriptar discos virtuais em Azure. As chaves criptográficas são armazenadas no Cofre da Chave Azure utilizando proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificadas para normas fips 140-2 de nível 2. Estas chaves criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar o seu uso. Um diretor de serviço do Azure Ative Directory fornece um mecanismo seguro para a emissão destas chaves criptográficas, uma vez que os VM são ligados e desligados.

## <a name="key-vault-and-ssh-keys"></a>Chave Cofre e Chaves SSH

Segredos e certificados podem ser modelados como recursos e fornecidos pela [Key Vault.](../key-vault/general/basic-concepts.md) Pode utilizar o Azure PowerShell para criar cofres-chave para [VMs Windows](windows/key-vault-setup.md) e o CLI Azure para [Os VMs Linux](linux/key-vault-setup.md). Também pode criar chaves para encriptação.

As principais políticas de acesso ao cofre concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Por outras palavras, [a política de acesso ao cofre não](../key-vault/general/secure-your-key-vault.md) suporta permissões de nível de objetos.

Quando se conecta a VMs, deve utilizar a criptografia de chaves públicas para fornecer uma forma mais segura de iniciar súplica. Este processo envolve uma troca de chaves pública e privada utilizando o comando secure shell (SSH) para autenticar-se em vez de um nome de utilizador e senha. As palavras-passe são vulneráveis a ataques de força bruta, especialmente em VMs virados para a Internet, como servidores web. Com um par de chaves secure shell (SSH), pode criar um [VM Linux](linux/mac-create-ssh-keys.md) que utiliza chaves SSH para autenticação, eliminando a necessidade de senhas de inscrição. Também pode utilizar as teclas SSH para ligar de um [VM do Windows](linux/ssh-from-windows.md) a um Linux VM.

## <a name="managed-identities-for-azure-resources"></a>Identidades geridas dos recursos do Azure

Um desafio comum inerente à criação de aplicações na cloud passa pela gestão das credenciais que estão no seu código para a autenticação nos serviços cloud. Manter essas credenciais protegidas é uma tarefa importante. Idealmente, nunca aparecem nas estações de trabalho dos programadores nem são verificadas no controlo de origem. O Azure Key Vault oferece uma forma de armazenar credenciais, segredos e outras chaves em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. 

A funcionalidade de identidades geridas para recursos do Azure no Active Directory (Azure AD) resolve este problema. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar essa identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.  O seu código que está a ser em execução num VM pode solicitar um token de dois pontos finais que só são acessíveis dentro do VM. Para obter informações mais detalhadas sobre este serviço, reveja as identidades geridas para a página geral [dos recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)   

## <a name="policies"></a>Políticas

[As políticas Azure](../governance/policy/overview.md) podem ser usadas para definir o comportamento desejado para os [VMs](./windows/policy.md) windows e [Linux](./linux/policy.md)da sua organização. Ao usar políticas, uma organização pode impor várias convenções e regras em toda a empresa. A aplicação do comportamento pretendido pode ajudar a mitigar o risco, contribuindo para o sucesso da organização.

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

Utilizando [o controlo de acesso baseado em funções (Azure RBAC) ( Azure RBAC),](../role-based-access-control/overview.md)pode segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores no seu VM de que precisam para desempenhar os seus trabalhos. Em vez de dar permissão ilimitada a todos no VM, só pode permitir certas ações. Pode configurar o controlo de acesso para o VM no [portal Azure,](../role-based-access-control/role-assignments-portal.md)utilizando o [Azure CLI](/cli/azure/role), ou[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Passos seguintes
- Caminhe pelos degraus para monitorizar a segurança da máquina virtual utilizando o Azure Security Center para [Linux](../security/fundamentals/overview.md) ou [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security).