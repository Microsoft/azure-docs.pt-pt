---
title: Resolução de problemas um dispositivo StorSimple implantado | Microsoft Docs
description: Descreve como diagnosticar e corrigir erros que ocorrem num dispositivo StorSimple que está atualmente implantado e operacional.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956794"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Resolução de problemas de um dispositivo StorSimple operacional
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](./index.yml) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Descrição Geral
Este artigo fornece orientações úteis para resolver problemas de configuração que poderá encontrar após a implementação do seu dispositivo StorSimple e operacional. Descreve problemas comuns, possíveis causas e medidas recomendadas para o ajudar a resolver problemas que poderá experimentar quando executar o Microsoft Azure StorSimple. Estas informações aplicam-se tanto ao dispositivo físico StorSimple no local como ao dispositivo virtual StorSimple.

No final deste artigo pode encontrar uma lista de códigos de erro que poderá encontrar durante a operação Microsoft Azure StorSimple, bem como os passos que pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo de assistente de configuração para dispositivos operacionais
Utilize o assistente de configuração[(Invoke-HcsSetupWizard)][1]para verificar a configuração do dispositivo e tomar medidas corretivas se necessário.

Quando executou o assistente de configuração num dispositivo previamente configurado e operacional, o fluxo de processo é diferente. Pode alterar apenas as seguintes entradas:

* Endereço IP, máscara de sub-rede e gateway
* Servidor PRIMÁRIO DNS
* Servidor NTP primário
* Configuração opcional de procuração web

O assistente de configuração não realiza as operações relacionadas com a recolha de senhas e o registo do dispositivo.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante as execuções subsequentes do assistente de configuração
A tabela a seguir descreve os erros que poderá encontrar quando executar o assistente de configuração num dispositivo operacional, possíveis causas para os erros e ações recomendadas para os resolver. 

| N.º | Mensagem de erro ou condição | Possíveis causas | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: Este dispositivo já foi desativado. |Verá este erro se executar o assistente de configuração num dispositivo que está desativado. |[Contacte o Microsoft Support](./storsimple-8000-contact-microsoft-support.md) para os próximos passos. Um dispositivo desativado não pode ser colocado em serviço. Pode ser necessário um reset de fábrica antes de o dispositivo poder ser novamente ativado. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION (Exceção da HRESULT: 0x80070001) |A atualização do servidor DNS está a falhar. As definições de DNS são configurações globais e são aplicadas em todas as interfaces de rede ativadas. |Ative a interface e aplique novamente as definições de DNS. Isto pode perturbar a rede para outras interfaces ativadas porque estas definições são globais. |
| 3 |O dispositivo parece estar online no portal de serviço StorSimple Manager, mas quando tenta completar a configuração mínima e salvar a configuração, a operação falha. |Durante a configuração inicial, o proxy da web não foi configurado, mesmo que houvesse um servidor de procuração real no lugar. |Utilize o [cmdlet Test-HcsmConnection][2] para localizar o erro. [Contacte o Microsoft Support](./storsimple-8000-contact-microsoft-support.md) se não conseguir corrigir o problema. |
| 4 |Invoke-HcsSetupWizard: O valor não se enquadra no intervalo esperado. |Uma máscara de sub-rede incorreta produz este erro. As causas possíveis são: <ul><li> A máscara da sub-rede está desaparecida ou vazia.</li><li>O formato prefixo Ipv6 está incorreto.</li><li>A interface está ativada pela nuvem, mas o portal está em falta ou está incorreto.</li></ul>Note que o DATA 0 está automaticamente ativado na nuvem se configurado através do assistente de configuração. |Para determinar o problema, utilize a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, olhe para a saída. Introduza os valores corretos para a máscara de sub-rede, gateway e prefixo Ipv6, conforme necessário. |

## <a name="error-codes"></a>Códigos de erro
Os erros estão listados por ordem numérica.

| Número de erro | Texto ou descrição de erro | Ação recomendada do utilizador |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao aceder à sua conta de armazenamento. |Espere alguns minutos e tente de novo. Se o erro persistir, contacte o Microsoft Support para os próximos passos. |
| 40017 |A operação de backup falhou, uma vez que um volume especificado na política de backup não foi encontrado no dispositivo. |Reda o funcionamento da cópia de segurança, se o erro persistir, contacte o Microsoft Support. para os próximos passos. |
| 40018 |A operação de backup falhou, uma vez que nenhum dos volumes especificados na política de backup foi encontrado no dispositivo. |Reda o funcionamento da cópia de segurança, se o erro persistir, contacte o Microsoft Support. para os próximos passos. |
| 390061 |O sistema está ocupado ou indisponível. |Espere alguns minutos e tente de novo. Se o erro persistir, contacte o Microsoft Support para os próximos passos. |
| 390143 |Ocorreu um erro com o código de erro 390143. (Erro desconhecido.) |Se o erro persistir, contacte o Microsoft Support para os próximos passos. |

## <a name="next-steps"></a>Passos seguintes
Se não conseguir resolver o problema, contacte o [Microsoft Support](./storsimple-8000-contact-microsoft-support.md) para obter assistência. 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)