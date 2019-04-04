---
title: Cópia de segurança e recuperação de dados para o Azure Stack com o serviço de cópia de segurança da infraestrutura | Documentos da Microsoft
description: Pode criar cópias de segurança e restaurar dados de serviço com o serviço de cópia de segurança da infraestrutura e de configuração.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: da1ae76925ffeba7a1df57b4121f8cfe20b2887f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882111"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Cópia de segurança e recuperação de dados para o Azure Stack com o serviço de cópia de segurança da infraestrutura

*Aplica-se a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar cópias de segurança e restaurar dados de serviço com o serviço de cópia de segurança da infraestrutura e de configuração. Cada instalação do Azure Stack contém uma instância do serviço. Pode utilizar cópias de segurança criadas pelo serviço para a reimplementação da Cloud do Azure Stack para restaurar a identidade, segurança e dados do Azure Resource Manager. 

Pode ativar a cópia de segurança quando estiver pronto para colocar a sua cloud em produção. Não ative a cópia de segurança se planeja realizar um teste e validação por um longo período de tempo.

Antes de ativar seu serviço de cópia de segurança, certifique-se de que tem [requisitos no local](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> O serviço de cópia de segurança de infraestrutura não inclui dados de utilizador e aplicações. Consulte a [proteger as VMs implementadas no Azure Stack](user/azure-stack-manage-vm-protect.md) para obter mais informações sobre como proteger a VM de IaaS com base em aplicações. Para obter uma compreensão abrangente de como proteger as aplicações no Azure Stack, consulte a [considerações sobre o Azure Stack para o whitepaper de recuperação de desastre e continuidade empresariais](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>O serviço de cópia de segurança da infraestrutura

Os serviços de contenham as seguintes funcionalidades.

| Funcionalidade                                            | Descrição                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Serviços de infraestrutura de cópia de segurança                     | Coordene a cópia de segurança entre um subconjunto de serviços de infraestrutura no Azure Stack. Se houver um desastre, os dados podem ser restaurados como parte da reimplementação. |
| Compressão e encriptação de dados de cópia de segurança exportados | Dados de cópia de segurança são comprimidos e encriptados pelo sistema antes de serem exportados para a localização de armazenamento externo fornecida pelo administrador.                |
| Monitorização de tarefas de cópia de segurança                              | Sistema notifica quando tarefas de cópia de segurança falharão e remediação de passos.                                                                                                |
| Experiência de gestão de cópia de segurança                       | Ativar cópia de segurança oferece suporte a RP de cópia de segurança.                                                                                                                         |
| Recuperação de nuvem                                     | Se existir uma perda catastrófica de dados, cópias de segurança podem ser utilizadas para restaurar principais do Azure Stack informações como parte da implementação.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Verifique os requisitos para o serviço de cópia de segurança da infraestrutura

- **Localização do armazenamento**  
  Precisa de uma partilha de ficheiros acessível a partir do Azure Stack que pode conter sete cópias de segurança. Cada cópia de segurança é cerca de 10 GB. A partilha deve ser capaz de armazenar 140 GB de cópias de segurança. Para obter mais informações sobre como selecionar uma localização de armazenamento para o serviço de cópia de segurança de infraestrutura do Azure Stack, veja [requisitos do controlador de cópia de segurança](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenciais**  
  Precisa de uma conta de utilizador de domínio e as credenciais, por exemplo, pode usar as credenciais de administrador do Azure Stack.
- **Certificado de encriptação**  
  Ficheiros de cópia de segurança são encriptados utilizando a chave pública no certificado. Certifique-se armazenar este certificado numa localização segura. 


## <a name="next-steps"></a>Passos Seguintes

Saiba como [ativar cópia de segurança para o Azure Stack a partir do portal de administração](azure-stack-backup-enable-backup-console.md).

Saiba como [ativar a cópia de segurança para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

Saiba como [cópia de segurança do Azure Stack](azure-stack-backup-back-up-azure-stack.md )

Saiba como [recuperar da perda catastrófica de dados](azure-stack-backup-recover-data.md)
