---
title: Encriptação de disco azure em uma rede isolada
description: Este artigo fornece dicas de resolução de problemas para encriptação do disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460125"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Encriptação de disco azure em uma rede isolada

Quando a conectividade é restringida por uma firewall, requisito de procuração ou configurações de grupo de segurança de rede (NSG), a capacidade da extensão para executar as tarefas necessárias pode ser interrompida. Esta perturbação pode resultar em mensagens de estado como "Estado de extensão não disponível no VM".

## <a name="package-management"></a>Gestão de pacotes

A encriptação do disco azure depende de uma série de componentes, que são normalmente instalados como parte da ativação ADE se ainda não estiverem presentes. Quando atrás de uma firewall ou isoladas da Internet, estas embalagens devem ser pré-instaladas ou disponíveis localmente.

Aqui estão os pacotes necessários para cada distribuição. Para obter uma lista completa de distros e tipos de volume [suportados, consulte VMs suportados e sistemas operativos](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup,cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuide, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuide, at, patch, cryptsetup-reencrypt
- **aberturaSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, criptosetup

No Red Hat, quando é necessário um proxy, deve certificar-se de que o gestor de subscrição e o yum estão configurados corretamente. Para mais informações, consulte Como resolver problemas de [subscrição-manager e problemas de yum](https://access.redhat.com/solutions/189533).  

Quando as embalagens são instaladas manualmente, também devem ser atualizadas manualmente à medida que novas versões forem lançadas.

## <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições do grupo de segurança da rede que são aplicadas devem ainda permitir que o ponto final cumpra os pré-requisitos de configuração da rede documentadopara a encriptação do disco.  Ver Encriptação do [Disco Azure: Requisitos de networking](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação de disco azure com AD Azure (versão anterior)

Se utilizar encriptação de [disco azure com AD Azure (versão anterior),](disk-encryption-overview-aad.md)a Biblioteca de [Diretórios Ativos Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) terá de ser instalada manualmente para todos os distros (além das embalagens adequadas para o distro, tal como [listado acima).](#package-management)

Quando a encriptação está a ser ativada com [credenciais De AD Azure,](disk-encryption-linux-aad.md)o VM alvo deve permitir a conectividade tanto aos pontos finais do Diretório Ativo Azure como aos pontos finais do Cofre chave. Os pontos finais de autenticação do Diretório Ativo Azure atuais são mantidos nas secções 56 e 59 do [Office 365 URLs e](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) documentação de endereços IP. As instruções do cofre chave são fornecidas na documentação sobre como aceder ao [Cofre chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância Azure 

A máquina virtual deve poder aceder ao ponto final do [serviço de metadados de instância](instance-metadata-service.md) `169.254.169.254`seletiva, que utiliza um endereço IP não resaída conhecido ( ) que só pode ser acedido a partir do VM.  As configurações proxy que alteram o tráfego local http para este endereço (por exemplo, adicionar um cabeçalho X-Forwarded-For) não são suportadas.

## <a name="next-steps"></a>Passos seguintes

- Veja mais passos para a resolução de problemas de encriptação do [disco Azure](disk-encryption-troubleshooting.md)
- [Encriptação de dados azure em repouso](../../security/fundamentals/encryption-atrest.md)
