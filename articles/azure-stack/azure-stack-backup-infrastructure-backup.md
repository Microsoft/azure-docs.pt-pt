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
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 005a9e3903ffa16882f8d07529bf5c4924837db5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790853"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Cópia de segurança e recuperação de dados para o Azure Stack com o serviço de cópia de segurança da infraestrutura

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar cópias de segurança e restaurar dados de serviço com o serviço de cópia de segurança da infraestrutura e de configuração. Cada instalação do Azure Stack contém uma instância do serviço. Pode utilizar cópias de segurança criadas pelo serviço para a reimplementação da Cloud do Azure Stack para restaurar a identidade, segurança e dados do Azure Resource Manager.

Pode ativar a cópia de segurança quando estiver pronto para colocar a sua cloud em produção. Não ative a cópia de segurança se planeja realizar um teste e validação por um longo período de tempo.

Antes de ativar seu serviço de cópia de segurança, certifique-se de que tem [requisitos no local](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> O serviço de cópia de segurança de infraestrutura não inclui dados de utilizador e aplicações. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

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

- **Localização de armazenamento**  
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
