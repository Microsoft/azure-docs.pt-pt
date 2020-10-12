---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73747840"
---
### <a name="installation-failures"></a>Falhas na instalação
| **Mensagem de erro da amostra** | **Ação recomendada** |
|--------------------------|------------------------|
|ERRO    Falha de erro ao carregar as Contas. Erro: System.IO.IOException: Não é possível ler dados a partir da ligação de transporte ao instalar e registar o servidor CS.| Certifique-se de que o TLS 1.0 está ativado no computador. |

### <a name="registration-failures"></a>Falhas de registo
As falhas de registo podem ser depuradas ao consultar os registos na pasta **%ProgramData%\ASRLogs**.

| **Mensagem de erro da amostra** | **Ação recomendada** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Mensagem: ACS50008: O token SAML é inválido.<br>ID de rastreio: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de correlação: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97 ><br>Carimbo de data/hora: **2016-12-12 14:50:08Z<br>** | Certifique-se de que a hora no relógio do seu sistema não excede mais de 15 minutos da hora local. Volte a executar o instalador para concluir o registo.|
|**09:35:27** :DRRegistrationException ao tentar obter todos os cofres da recuperação após desastre para o certificado selecionado: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: o token SAML é inválido.<br>ID de rastreio: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de correlação: abe9deb8-3e64-464d-8375-36db9816427a<br>Carimbo de data/hora: **2016-05-19 01:35:39Z**<br> | Certifique-se de que a hora no relógio do seu sistema não excede mais de 15 minutos da hora local. Volte a executar o instalador para concluir o registo.|
|06:28:45: Falha ao criar certificado<br>06:28:45: Não é possível continuar a configuração. Não é possível criar um certificado necessário para autenticar no Site Recovery. Execute Novamente a Configuração | Certifique-se de que está a executar a configuração como administrador local. |
