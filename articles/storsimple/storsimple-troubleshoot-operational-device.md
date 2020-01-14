---
title: Solucionar problemas de um dispositivo StorSimple implantado | Microsoft Docs
description: Descreve como diagnosticar e corrigir erros que ocorrem em um dispositivo StorSimple que está atualmente implantado e operacional.
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
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933299"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Solucionar problemas de um dispositivo StorSimple operacional
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Visão geral
Este artigo fornece diretrizes de solução de problemas úteis para resolver problemas de configuração que você pode encontrar após o dispositivo StorSimple ser implantado e operacional. Ele descreve problemas comuns, possíveis causas e etapas recomendadas para ajudá-lo a resolver problemas que podem ocorrer ao executar o Microsoft Azure StorSimple. Essas informações se aplicam tanto ao dispositivo físico local do StorSimple quanto ao dispositivo virtual StorSimple.

No final deste artigo, você pode encontrar uma lista de códigos de erro que podem ser encontrados durante Microsoft Azure StorSimple operação, bem como as etapas que você pode seguir para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo do assistente de instalação para dispositivos operacionais
Use o assistente de instalação ([Invoke-HcsSetupWizard][1]) para verificar a configuração do dispositivo e tomar uma ação corretiva, se necessário.

Quando você executa o assistente de instalação em um dispositivo previamente configurado e operacional, o fluxo do processo é diferente. Você pode alterar apenas as seguintes entradas:

* Endereço IP, máscara de sub-rede e gateway
* Servidor DNS primário
* Servidor NTP primário
* Configuração opcional de proxy Web

O assistente de instalação não executa as operações relacionadas à coleta de senhas e ao registro de dispositivos.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante execuções subsequentes do assistente de instalação
A tabela a seguir descreve os erros que você pode encontrar ao executar o assistente de instalação do em um dispositivo operacional, as possíveis causas dos erros e as ações recomendadas para resolvê-los. 

| Não. | Mensagem de erro ou condição | Possíveis causas | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: este dispositivo já foi desativado. |Você verá esse erro se executar o assistente de instalação em um dispositivo que está desativado. |[Contacte o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para saber quais os próximos passos. Um dispositivo desativado não pode ser colocado no serviço. Uma redefinição de fábrica pode ser necessária antes que o dispositivo possa ser ativado novamente. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (exceção de HRESULT: 0x80070001) |A atualização do servidor DNS está falhando. As configurações de DNS são configurações globais e são aplicadas em todas as interfaces de rede habilitadas. |Habilite a interface e aplique as configurações de DNS novamente. Isso pode interromper a rede para outras interfaces habilitadas porque essas configurações são globais. |
| 3 |O dispositivo parece estar online no portal do serviço de StorSimple Manager, mas quando você tenta concluir a configuração mínima e salvar a configuração, a operação falha. |Durante a configuração inicial, o proxy Web não foi configurado, embora haja um servidor proxy real em vigor. |Use o [cmdlet Test-HcsmConnection][2] para localizar o erro. [Entre em contato com suporte da Microsoft](storsimple-contact-microsoft-support.md) se não for possível corrigir o problema. |
| 4 |Invoke-HcsSetupWizard: o valor não se enquadra no intervalo esperado. |Uma máscara de sub-rede incorreta produz esse erro. As possíveis causas são: <ul><li> A máscara de sub-rede está ausente ou vazia.</li><li>O formato do prefixo IPv6 está incorreto.</li><li>A interface está habilitada para a nuvem, mas o gateway está ausente ou incorreto.</li></ul>Observe que DATA 0 é habilitado para nuvem automaticamente se configurado por meio do assistente de instalação. |Para determinar o problema, use sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, examine a saída. Insira os valores corretos para a máscara de sub-rede, o gateway e o prefixo IPv6, conforme necessário. |

## <a name="error-codes"></a>Códigos de erro
Os erros são listados em ordem numérica.

| Número do erro | Texto ou descrição do erro | Ação do usuário recomendada |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao acessar sua conta de armazenamento. |Aguarde alguns minutos e tente novamente. Se o erro persistir, entre em contato com Suporte da Microsoft para as próximas etapas. |
| 40017 |A operação de backup falhou porque um volume especificado na política de backup não foi encontrado no dispositivo. |Repita a operação de backup, se o erro persistir, entre em contato com Suporte da Microsoft. para as próximas etapas. |
| 40018 |A operação de backup falhou porque nenhum dos volumes especificados na política de backup foi encontrado no dispositivo. |Repita a operação de backup, se o erro persistir, entre em contato com Suporte da Microsoft. para as próximas etapas. |
| 390061 |O sistema está ocupado ou indisponível. |Aguarde alguns minutos e tente novamente. Se o erro persistir, entre em contato com Suporte da Microsoft para as próximas etapas. |
| 390143 |Ocorreu um erro com o código de erro 390143. (Erro desconhecido.) |Se o erro persistir, entre em contato com Suporte da Microsoft para as próximas etapas. |

## <a name="next-steps"></a>Passos seguintes
Se não for possível resolver o problema, [entre em contato com suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
