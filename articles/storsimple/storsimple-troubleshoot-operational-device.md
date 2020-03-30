---
title: Troubleshoot um dispositivo StorSimple implantado [ Microsoft Docs
description: Descreve como diagnosticar e corrigir erros que ocorrem num dispositivo StorSimple que está atualmente implantado e operacional.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933299"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Resolução de problemas um dispositivo Operacional StorSimple
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Descrição geral
Este artigo fornece orientações úteis de resolução de problemas para resolver problemas de configuração que poderá encontrar após a implementação e operacional do seu dispositivo StorSimple. Descreve problemas comuns, possíveis causas e medidas recomendadas para o ajudar a resolver problemas que poderá experimentar quando executa o Microsoft Azure StorSimple. Esta informação aplica-se tanto ao dispositivo físico StorSimple no local como ao dispositivo virtual StorSimple.

No final deste artigo pode encontrar uma lista de códigos de erro que poderá encontrar durante a operação Microsoft Azure StorSimple, bem como os passos que pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Configurar processo de assistente para dispositivos operacionais
Utilize o assistente de configuração[(Invoke-HcsSetupWizard)][1]para verificar a configuração do dispositivo e tomar medidas corretivas, se necessário.

Quando executa o assistente de configuração num dispositivo previamente configurado e operacional, o fluxo de processo é diferente. Só pode alterar as seguintes entradas:

* Endereço IP, máscara de sub-rede e porta de entrada
* Servidor DNS primário
* Servidor NTP primário
* Configuração opcional de procuração web

O assistente de configuração não realiza as operações relacionadas com a recolha de passwords e o registo do dispositivo.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante as execuções subsequentes do assistente de configuração
A tabela seguinte descreve os erros que poderá encontrar quando executa o assistente de configuração num dispositivo operacional, possíveis causas para os erros e ações recomendadas para resolvê-los. 

| Não. | Mensagem de erro ou condição | Possíveis causas | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: Este dispositivo já foi desativado. |Verá este erro se executar o assistente de configuração num dispositivo que é desativado. |[Contacte o Microsoft Support](storsimple-contact-microsoft-support.md) para os próximos passos. Um dispositivo desativado não pode ser colocado em serviço. Pode ser necessário um reset de fábrica antes de o dispositivo poder ser ativado novamente. |
| 2 |Invocação-HcsSetupWizard : ERROR_INVALID_FUNCTION (Exceção de HRESULT: 0x80070001) |A atualização do servidor DNS está a falhar. As definições de DNS são configurações globais e são aplicadas em todas as interfaces de rede ativadas. |Ative a interface e aplique novamente as definições de DNS. Isto pode perturbar a rede para outras interfaces ativadas porque estas definições são globais. |
| 3 |O dispositivo parece estar online no portal de serviço StorSimple Manager, mas quando tenta completar a configuração mínima e guardar a configuração, a operação falha. |Durante a configuração inicial, o proxy web não foi configurado, mesmo existindo um servidor proxy real no lugar. |Utilize o [cmdlet Test-HcsmConnection][2] para localizar o erro. [Contacte](storsimple-contact-microsoft-support.md) o Microsoft Support se não conseguir corrigir o problema. |
| 4 |Invoke-HcsSetupWizard: O valor não se enquadra no intervalo esperado. |Uma máscara de sub-rede incorreta produz este erro. As possíveis causas são: <ul><li> A máscara de sub-rede está em falta ou vazia.</li><li>O formato de prefixo Ipv6 está incorreto.</li><li>A interface está ativada pela nuvem, mas o portal está em falta ou incorreto.</li></ul>Note que o DATA 0 está automaticamente ativado pela nuvem se estiver configurado através do assistente de configuração. |Para determinar o problema, utilize a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, olhe para a saída. Introduza os valores corretos para a máscara de sub-rede, porta de entrada e prefixo Ipv6, conforme necessário. |

## <a name="error-codes"></a>Códigos de erro
Os erros estão listados por ordem numérica.

| Número de erro | Texto ou descrição de erro | Ação recomendada do utilizador |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao aceder à sua conta de armazenamento. |Espere alguns minutos e tente de novo. Se o erro persistir, contacte o Suporte da Microsoft para os próximos passos. |
| 40017 |A operação de backup falhou, uma vez que um volume especificado na política de backup não foi encontrado no dispositivo. |Tente novamente a operação de backup, se o erro persistir, contacte o Suporte da Microsoft. para os próximos passos. |
| 40018 |A operação de backup falhou, uma vez que nenhum dos volumes especificados na política de backup foi encontrado no dispositivo. |Tente novamente a operação de backup, se o erro persistir, contacte o Suporte da Microsoft. para os próximos passos. |
| 390061 |O sistema está ocupado ou indisponível. |Espere alguns minutos e tente de novo. Se o erro persistir, contacte o Suporte da Microsoft para os próximos passos. |
| 390143 |Ocorreu um erro com o código de erro 390143. (Erro desconhecido.) |Se o erro persistir, contacte o Microsoft Support para os próximos passos. |

## <a name="next-steps"></a>Passos seguintes
Se não conseguir resolver o problema, [contacte](storsimple-contact-microsoft-support.md) o Microsoft Support para obter assistência. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
