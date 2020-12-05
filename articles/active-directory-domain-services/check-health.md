---
title: Verifique a saúde dos Serviços de Domínio do Diretório Ativo Azure ! Microsoft Docs
description: Saiba como verificar a saúde de um domínio gerido pelo Azure Ative Directory (Azure AD DS) e compreenda as mensagens de estado utilizando o portal Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620329"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verifique a saúde de um domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure

AZure Ative Directory Domain Services (Azure AD DS) executa algumas tarefas de fundo para manter o domínio gerido saudável e atualizado. Estas tarefas incluem a tomada de backups, a aplicação de atualizações de segurança e a sincronização de dados a partir do Azure AD. Se existirem problemas com o domínio gerido Azure AD DS, estas tarefas podem não estar concluídas com sucesso. Para rever e resolver quaisquer problemas, pode verificar o estado de saúde de um domínio gerido utilizando o portal Azure.

Este artigo mostra-lhe como ver o estado de saúde da Azure AD DS e compreender as informações ou alertas mostrados.

## <a name="view-the-health-status"></a>Ver o estado de saúde

O estado de saúde de um domínio gerido é visto usando o portal Azure. Informações sobre o último tempo de backup e sincronização com Azure AD podem ser vistas, juntamente com quaisquer alertas que indiquem um problema com a saúde do domínio gerido. Para visualizar o estado de saúde de um domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione **Health**. O exemplo seguinte mostra um domínio gerido saudável e o estado da última cópia de segurança e sincronização AD AD:

    ![Visão geral da página de saúde no portal Azure mostrando o estado dos Serviços de Domínio do Diretório Ativo Azure](./media/check-health/health-page.png)

O último tempo *de* verificação da página de saúde mostra quando o domínio gerido foi verificado pela última vez. A saúde de um domínio gerido é avaliada a cada hora. Se escamar alguma alteração num domínio gerido, aguarde até ao próximo ciclo de avaliação para visualizar o estado de saúde atualizado.

O estado no direito superior indica a saúde geral do domínio gerido. O estado fatores todos os alertas existentes no seu domínio. O quadro que se segue detalha os indicadores de estado disponíveis:

| Estado | Ícone | Explicação |
| --- | :----: | --- |
| Em Execução | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | O domínio gerido está a funcionar corretamente e não tem quaisquer alertas críticos ou de aviso. O domínio pode ter alertas informativos. |
| Precisa de atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Não existem alertas críticos sobre o domínio gerido, mas há um ou mais alertas que devem ser abordados. |
| Precisa de atenção (crítica) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Há um ou mais alertas críticos sobre o domínio gerido que devem ser abordados. Pode também ter alertas e/ou alertas informativos. |
| Implantação | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | O domínio gerido está a ser implantado. |

## <a name="understand-monitors-and-alerts"></a>Compreender monitores e alertas

O estado de saúde de um domínio gerido mostra dois tipos de informação - *monitores* e *alertas*. Os monitores mostram a hora em que as tarefas de fundo foram concluídas. Os alertas fornecem informações ou sugestões para melhorar a estabilidade do domínio gerido.

### <a name="monitors"></a>Monitores

Os monitores são áreas de um domínio gerido que são verificadas regularmente. Se houver alertas ativos para o domínio gerido, pode fazer com que um dos monitores reporte um problema. A Azure AD DS tem atualmente monitores para as seguintes áreas:

* Backup
* Sincronização com Azure AD

#### <a name="backup-monitor"></a>Monitor de reserva

O monitor de backup verifica se as cópias de segurança regulares automatizadas do domínio gerido são executadas com sucesso. A tabela a seguir detalha o estado do monitor de backup disponível:

| Valor de detalhe | Explicação |
| --- | --- |
| Nunca recuou | Este estado é normal para novos domínios geridos. A primeira cópia de segurança deve ser criada 24 horas após a implantação do domínio gerido. Se este estado persistir, abra um pedido de [apoio ao Azure][azure-support]. |
| O último reforço foi feito há 1 a 14 dias. | Este intervalo de tempo é o estado esperado para o monitor de reserva. Neste período, devem ocorrer cópias de segurança regular automatizadas. |
| O último reforço foi tirado há mais de 14 dias. | Um período de tempo superior a duas semanas indica que há um problema com as cópias de segurança automáticas regulares. Alertas críticos ativos podem impedir que o domínio gerido seja apoiado. Resolva quaisquer alertas ativos para o domínio gerido. Se o monitor de cópia de segurança não atualizar o estado para reportar uma cópia de segurança recente, [abra um pedido de suporte Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronização com monitor Azure AD

Um domínio gerido sincroniza regularmente com o Azure Ative Directory. O número de utilizadores e objetos de grupo, e o número de alterações efeccionada no diretório AD Azure desde a última sincronização, afeta o tempo que demora a sincronizar. Se o domínio gerido foi sincronizado pela última vez há mais de três dias, verifique e resolva quaisquer alertas ativos. Se o monitor de sincronização não atualizar o estado para mostrar uma sincronização recente depois de abordar quaisquer alertas ativos, [abra um pedido de suporte do Azure][azure-support].

### <a name="alerts"></a>Alertas

Os alertas são gerados para problemas num domínio gerido que precisa de ser abordado para que o serviço seja executado corretamente. Cada alerta explica o problema e dá um URL que descreve passos específicos para resolver o problema. Para obter mais informações sobre os possíveis alertas e suas resoluções, consulte [alertas de resolução de problemas.](troubleshoot-alerts.md)

Os alertas de estado de saúde são categorizados nos seguintes níveis de gravidade:

 * **Alertas críticos** são problemas que afetam severamente o domínio gerido. Estes alertas devem ser abordados imediatamente. A plataforma Azure não consegue monitorizar, gerir, corrigir e sincronizar o domínio gerido até que os problemas sejam resolvidos.
 * **Os alertas** notificam-no de problemas que podem afetar as operações de domínio geridos se o problema persistir. Estes alertas também oferecem recomendações para garantir o domínio gerido.
 * **Alertas informativos** são notificações que não afetam negativamente o domínio gerido. Os alertas informativos fornecem algumas informações sobre o que está a acontecer no domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre alertas que sejam mostrados na página do estado de saúde, consulte [alertas resolvedores no seu domínio gerido][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
