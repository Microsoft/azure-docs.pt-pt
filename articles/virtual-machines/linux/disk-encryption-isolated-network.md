---
title: Encriptação de disco azure em uma rede isolada
description: Este artigo fornece dicas de resolução de problemas para encriptação do disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: e91f4f1d1a16bec9d2da0365b185319aac8892be
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207722"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Encriptação de disco azure em uma rede isolada

Quando a conectividade é restringida por uma firewall, o requisito de proxy ou definições de grupo (NSG) de segurança de rede, a capacidade da extensão para efetuar tarefas necessárias pode ser interrompida. Este interrupção pode resultar em mensagens de estado, tais como o "Estado da extensão não está disponível na VM."

## <a name="package-management"></a>Gestão de pacotes

A encriptação do disco azure depende de uma série de componentes, que são normalmente instalados como parte da ativação ADE se ainda não estiverem presentes. Quando atrás de uma firewall ou isoladas da Internet, estas embalagens devem ser pré-instaladas ou disponíveis localmente.

Aqui estão os pacotes necessários para cada distribuição. Para obter uma lista completa de distros e tipos de volume [suportados, consulte VMs suportados e sistemas operativos](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup,cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuide, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuide, at, patch, cryptsetup-reencrypt
- **aberturaSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, criptosetup

Em Red Hat, quando um proxy for necessário, deve certificar-se de que o Gestor de subscrições e yum estão configuradas corretamente. Para mais informações, consulte Como resolver problemas de [subscrição-manager e problemas de yum](https://access.redhat.com/solutions/189533).  

Quando as embalagens são instaladas manualmente, também devem ser atualizadas manualmente à medida que novas versões forem lançadas.

## <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições do grupo de segurança da rede que são aplicadas devem ainda permitir que o ponto final cumpra os pré-requisitos de configuração da rede documentadopara a encriptação do disco.  Ver Encriptação do [Disco Azure: Requisitos de networking](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação de disco azure com AD Azure (versão anterior)

Se utilizar encriptação de [disco azure com AD Azure (versão anterior),](disk-encryption-overview-aad.md)a Biblioteca de [Diretórios Ativos Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) terá de ser instalada manualmente para todos os distros (além das embalagens adequadas para o distro, tal como [listado acima).](#package-management)

Quando a encriptação está a ser ativada com [credenciais De AD Azure,](disk-encryption-linux-aad.md)o VM alvo deve permitir a conectividade tanto aos pontos finais do Diretório Ativo Azure como aos pontos finais do Cofre chave. Os pontos finais de autenticação do Diretório Ativo Azure atuais são mantidos nas secções 56 e 59 do [Office 365 URLs e](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) documentação de endereços IP. As instruções do cofre chave são fornecidas na documentação sobre como aceder ao [Cofre chave Azure atrás de uma firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância Azure 

A máquina virtual deve poder aceder ao ponto final do [serviço de metadados de instância](instance-metadata-service.md) seletiva, que utiliza um endereço IP não resaída conhecido (`169.254.169.254`) que só pode ser acedido a partir do VM.  As configurações proxy que alteram o tráfego local http para este endereço (por exemplo, adicionar um cabeçalho X-Forwarded-For) não são suportadas.

## <a name="next-steps"></a>Passos seguintes

- Veja mais passos para a resolução de problemas de encriptação do [disco Azure](disk-encryption-troubleshooting.md)
- [Encriptação de dados azure em repouso](../../security/fundamentals/encryption-atrest.md)
