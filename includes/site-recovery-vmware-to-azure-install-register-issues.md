---
author: rockboyfor
ms.service: site-recovery
ms.topic: include
origin.date: 10/26/2018
ms.date: 12/10/2018
ms.author: v-yeche
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119740"
---
### <a name="installation-failures"></a>Falhas na instalação
| **Exemplo da mensagem de erro** | **Ação recomendada** |
|--------------------------|------------------------|
|ERRO    Falha de erro ao carregar as Contas. Erro: System.IO.IOException: Não é possível ler dados a partir da ligação de transporte ao instalar e registar o servidor CS.| Certifique-se de que o TLS 1.0 está ativado no computador. |

### <a name="registration-failures"></a>Falhas de registo
As falhas de registo podem ser depuradas ao consultar os registos na pasta **%ProgramData%\ASRLogs**.

| **Exemplo da mensagem de erro** | **Ação recomendada** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Mensagem: ACS50008: SAML token é inválido.<br>ID de rastreio: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de correlação: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Carimbo de data/hora: **2016-12-12 14:50:08Z<br>** | Certifique-se de que a hora no relógio do seu sistema não excede mais de 15 minutos da hora local. Volte a executar o instalador para concluir o registo.|
|**09:35:27** : DRRegistrationException ao tentar obter todos os Cofre de recuperação após desastre para o certificado selecionado:: Emitiu Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception: ACS50008: SAML token é inválido.<br>ID de rastreio: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de correlação: abe9deb8-3e64-464d-8375-36db9816427a<br>Carimbo de data/hora: **2016-05-19 01:35:39Z**<br> | Certifique-se de que a hora no relógio do seu sistema não excede mais de 15 minutos da hora local. Volte a executar o instalador para concluir o registo.|
|06:28:45: Falha ao criar certificado<br>06:28:45: Não é possível continuar a configuração. Não é possível criar um certificado necessário para autenticar no Site Recovery. Execute Novamente a Configuração | Certifique-se de que está a executar o programa de configuração como administrador local. |