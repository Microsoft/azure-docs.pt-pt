---
title: Encriptação do disco Azure numa rede isolada
description: Neste artigo, saiba mais sobre dicas de resolução de problemas para a Encriptação do Disco Azure da Microsoft em VMs Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 8d8d2b88251f837a23c4e82a90eb4d4eb0043702
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553056"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Encriptação do disco Azure numa rede isolada

Quando a conectividade é restringida por uma definição de firewall, proxy ou grupo de segurança de rede (NSG), a capacidade da extensão para executar as tarefas necessárias pode ser interrompida. Esta perturbação pode resultar em mensagens de estado tais como "O estado de extensão não disponível no VM."

## <a name="package-management"></a>Gestão de pacotes

A encriptação do disco Azure depende de uma série de componentes, que são normalmente instalados como parte do habilibilamento ADE se ainda não estiverem presentes. Quando atrás de uma firewall ou isoladas da Internet, estas embalagens devem ser pré-instaladas ou disponíveis localmente.

Aqui estão os pacotes necessários para cada distribuição. Para obter uma lista completa de distros suportados e tipos de volume, consulte [VMs e sistemas operativos suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems)

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps, grub-pc-bin
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencript, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencript, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencript, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencript
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, criptsetup

No Red Hat, quando é necessário um representante, deve certificar-se de que o gestor de subscrição e o yum estão corretamente configurados. Para obter mais informações, consulte [Como resolver problemas de subscrição e problemas com yum](https://access.redhat.com/solutions/189533).  

Quando as embalagens são instaladas manualmente, também devem ser atualizadas manualmente à medida que as novas versões são lançadas.

## <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições de grupo de segurança de rede que sejam aplicadas devem ainda permitir que o ponto final cumpra os pré-requisitos de configuração de rede documentados para encriptação do disco.  Ver [Encriptação do Disco Azure: Requisitos de rede](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação do disco Azure com Azure AD (versão anterior)

Se utilizar [a Encriptação do Disco Azure com Azure AD (versão anterior),](disk-encryption-overview-aad.md)a [Biblioteca do Diretório Ativo Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) terá de ser instalada manualmente para todos os distros (para além dos pacotes adequados ao distro, conforme [listado acima).](#package-management)

Quando a encriptação estiver a ser ativada com [credenciais AD AZure,](disk-encryption-linux-aad.md)o VM alvo deve permitir a conectividade tanto para os pontos finais do Azure Ative Directory como para os pontos finais do Key Vault. Os pontos finais de autenticação do Azure Ative Directory são mantidos nas secções 56 e 59 da documentação dos [conjuntos de URLs e ip da Microsoft 365.](/microsoft-365/enterprise/urls-and-ip-address-ranges) As instruções do Cofre chave são fornecidas na documentação sobre como aceder ao [cofre da chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

A máquina virtual deve poder aceder ao ponto final do [serviço Azure Instance Metadata,](instance-metadata-service.md) que utiliza um conhecido endereço IP não roteível `169.254.169.254` ( ) que só pode ser acedido a partir do VM.  As configurações proxy que alteram o tráfego HTTP local para este endereço (por exemplo, adicionar um cabeçalho X-Forwarded)não são suportadas.

## <a name="next-steps"></a>Passos seguintes

- Ver mais passos para a [resolução de problemas de encriptação do disco Azure](disk-encryption-troubleshooting.md)
- [Encriptação de dados inativa do Azure](../../security/fundamentals/encryption-atrest.md)
