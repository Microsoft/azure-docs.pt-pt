---
title: Resolver problemas de um dispositivo StorSimple implementado | Documentos da Microsoft
description: Descreve como diagnosticar e corrigir os erros que ocorrem num dispositivo StorSimple que está atualmente implementada e operacional.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8ad3f09bf46caf426b2008b583ebd2ff78522462
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302522"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Resolver problemas de um dispositivo StorSimple operacional
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a migração, consulte [FAQ: Mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Este artigo fornece orientações de resolução de problemas úteis para a resolução de problemas de configuração que poderão surgir depois do dispositivo StorSimple está implementado e operacional. Ele descreve problemas comuns, as causas possíveis e passos recomendados para o ajudar a resolver problemas que podem ocorrer ao executar o Microsoft Azure StorSimple. Estas informações aplicam-se para o dispositivo físico do StorSimple no local e o dispositivo virtual StorSimple.

No final deste artigo, que pode encontrar uma lista de códigos de erro que podem surgir durante a operação do Microsoft Azure StorSimple, bem como passos que pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo do Assistente de configuração para dispositivos operacionais
Utilize o Assistente de configuração ([Invoke-HcsSetupWizard][1]) para verificar a configuração do dispositivo e tomar medidas corretivas, se necessário.

Quando executa o Assistente de configuração num dispositivo anteriormente configurado e operacional, o fluxo de processo é diferente. Pode alterar as seguintes entradas:

* Endereço IP, máscara de sub-rede e gateway
* Servidor DNS primário
* Servidor NTP primário
* Configuração do proxy web opcional

O Assistente de configuração não efetua as operações relacionadas com a coleção e o dispositivo de registo de palavra-passe.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante as execuções subsequentes do Assistente de configuração
A tabela seguinte descreve os erros que poderão surgir quando executar o Assistente de configuração num dispositivo operacional, as causas possíveis para os erros e as ações recomendadas para resolvê-los. 

| Não. | Mensagem de erro ou condição | Causas possíveis | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1 |Erro 350032: Este dispositivo já foi desativado. |Verá este erro se executar o Assistente de configuração num dispositivo que está desativado. |[Contacte o Suporte da Microsoft](storsimple-contact-microsoft-support.md) para saber quais os próximos passos. Um dispositivo desativado não pode ser colocado no serviço. Uma reposição de fábrica de mensagens em fila pode ser necessária antes do dispositivo pode ser ativado novamente. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (exceção de HRESULT: 0x80070001) |A atualização do servidor DNS está a falhar. As definições de DNS são definições globais e são aplicadas em todas as interfaces de rede ativada. |Ativar a interface e aplicar novamente as definições de DNS. Isto pode resultar na interrupção da rede para outras interfaces ativados uma vez que estas definições são globais. |
| 3 |O dispositivo é exibido estar online no portal de serviço do StorSimple Manager, mas quando tentar concluir a configuração mínima e guardar a configuração, a operação falhar. |Durante a configuração inicial, o proxy da web não foi configurado, mesmo que havia um servidor proxy real no local. |Utilize o [cmdlet Test-HcsmConnection] [ 2] para localizar o erro. [Contacte o Microsoft Support](storsimple-contact-microsoft-support.md) se não for possível corrigir o problema. |
| 4 |Invoke-HcsSetupWizard: Valor não se encontra dentro do intervalo esperado. |Uma máscara de sub-rede incorreto produz este erro. As causas possíveis são: <ul><li> A máscara de sub-rede está em falta ou vazio.</li><li>O formato de prefixo de Ipv6 está incorreto.</li><li>A interface é a capacidade de cloud, mas o gateway está em falta ou incorreta.</li></ul>Observe que a DATA 0 é automaticamente capacidade de cloud se configurada por meio do Assistente de configuração. |Para determinar o problema, utilize a sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, observar os resultados. Introduza os valores corretos para a máscara de sub-rede, o gateway e o prefixo de Ipv6, conforme necessário. |

## <a name="error-codes"></a>Códigos de erro
Erros estão listados em ordem numérica.

| Número de erro | Texto de erro ou descrição | Ação do utilizador recomendada |
|:--- |:--- |:--- |
| 10502 |Foi encontrado um erro ao aceder à sua conta de armazenamento. |Aguarde alguns minutos e, em seguida, tente novamente. Se o erro persistir, o problema contactar suporte da Microsoft para passos seguintes. |
| 40017 |A operação de cópia de segurança falhou porque um volume especificado na política de cópia de segurança não foi encontrado no dispositivo. |Repita a cópia de segurança operação, se o erro persistir, contacte Support da Microsoft. Para obter passos seguintes. |
| 40018 |A operação de cópia de segurança falhou porque foi encontrado nenhum dos volumes especificados na política de cópia de segurança no dispositivo. |Repita a cópia de segurança operação, se o erro persistir, contacte Support da Microsoft. Para obter passos seguintes. |
| 390061 |O sistema está ocupado ou indisponível. |Aguarde alguns minutos e, em seguida, tente novamente. Se o erro persistir, o problema contactar suporte da Microsoft para passos seguintes. |
| 390143 |Ocorreu um erro com o código de erro 390143. (Erro desconhecido.) |Se o erro persistir, contacte a Microsoft Support para passos seguintes. |

## <a name="next-steps"></a>Passos Seguintes
Se não conseguir resolver o problema [contacte o Microsoft Support](storsimple-contact-microsoft-support.md) para obter assistência. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
