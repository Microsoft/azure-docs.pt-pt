---
title: Verifique a saúde dos Serviços de Domínio de Diretório Ativo azure [ Microsoft Docs
description: Saiba como verificar a saúde de um Azure Ative Directory Domain Services (Azure AD DS) gerido domínio e compreenda as mensagens de estado usando o portal Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655642"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verifique a saúde de um domínio gerido pelo Azure Ative Directory Domain Services

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) executam algumas tarefas de fundo para manter o domínio gerido saudável e atualizado. Estas tarefas incluem a recolha de backups, a aplicação de atualizações de segurança e a sincronização de dados da AD Azure. Se houver problemas com o domínio gerido pelo Azure AD DS, estas tarefas podem não estar concluídas com sucesso. Para rever e resolver quaisquer problemas, pode verificar o estado de saúde de um domínio gerido pelo Azure AD DS utilizando o portal Azure.

Este artigo mostra-lhe como ver o estado de saúde da AD DS azure e compreender as informações ou alertas mostrados.

## <a name="view-the-health-status"></a>Ver o estado de saúde

O estado de saúde de um domínio gerido pela AD DS Azure é visto através do portal Azure. Informações sobre o último tempo de backup e sincronização com AD Azure podem ser vistas, juntamente com quaisquer alertas que indiquem um problema com a saúde do domínio gerido. Para ver o estado de saúde de um domínio gerido pela AD DS Azure, complete os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD**.
1. Selecione o seu domínio gerido pelo Azure AD DS, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione **Health**. A imagem de exemplo seguinte mostra um domínio gerido a DS Azure saudável e o estado da última sincronização de backup e AD Azure:

    ![Visão geral da página de saúde no portal Azure mostrando o estado dos Serviços de Domínio de Diretório Ativo Azure](./media/check-health/health-page.png)

O último carimbo de tempo *avaliado* da página de saúde mostra quando o domínio gerido pela última vez pelo Azure AD DS foi verificado pela última vez. A saúde de um domínio gerido pela AD DS Azure é avaliada a cada hora. Se fizer alterações num domínio gerido pelo Azure AD DS, aguarde até ao próximo ciclo de avaliação para ver o estado de saúde atualizado.

O estado no canto superior direito indica a saúde geral do domínio gerido pela AD DS azure. O estado influencia todos os alertas existentes no seu domínio. O quadro seguinte detalha os indicadores de estado disponíveis:

| Estado | Ícone | Explicação |
| --- | :----: | --- |
| A executar | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | O domínio gerido pelo Azure AD DS está a funcionar corretamente e não tem quaisquer alertas críticos ou de alerta. O domínio pode ter alertas informais. |
| Precisa de atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Não existem alertas críticos sobre o domínio gerido pelo Azure AD DS, mas existem um ou mais alertas de alerta que devem ser abordados. |
| Precisa de atenção (crítica) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Existem um ou mais alertas críticos sobre o domínio gerido pela AD DS azure que deve ser abordado. Pode também ter alertas de aviso e/ou informação. |
| Implantação | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | O domínio Azure AD DS está a ser implantado. |

## <a name="understand-monitors-and-alerts"></a>Compreender monitores e alertas

O estado de saúde de um domínio gerido pela AD DS azure mostra dois tipos de informação - *monitores*e *alertas*. Os monitores mostram o tempo em que as tarefas de fundo foram concluídas. Os alertas fornecem informações ou sugestões para melhorar a estabilidade do domínio gerido.

### <a name="monitors"></a>Monitores

Os monitores são áreas de um domínio gerido pelo Azure AD DS que são verificados regularmente. Se houver alertas ativos para o domínio gerido pelo Azure AD DS, pode fazer com que um dos monitores reporte um problema. Os Serviços de Domínio Da Azure AD dispõem atualmente de monitores para as seguintes áreas:

* Cópia de segurança
* Sincronização com Azure AD

#### <a name="backup-monitor"></a>Monitor de backup

O monitor de backup verifica que as cópias de segurança regulares automatizadas do domínio gerido pelo Azure AD DS são executadas com sucesso. A tabela que se segue detalha o estado do monitor de backup disponível:

| Valor de detalhe | Explicação |
| --- | --- |
| Nunca apoiado | Este estado é normal para novos domínios geridos pela Azure AD DS. A primeira cópia de segurança deve ser criada 24 horas após a implantação do domínio gerido pelo Azure AD DS. Se este estatuto persistir, [abra um pedido de apoio azure][azure-support]. |
| O último reforço foi feito há 1 a 14 dias. | Este intervalo de tempo é o estado esperado para o monitor de reserva. Devem ocorrer cópias de segurança regulares automatizadas neste período. |
| O último reforço foi feito há mais de 14 dias. | Um tempo de espera superior a duas semanas indica que há um problema com as cópias de segurança regulares automatizadas. Os alertas críticos ativos podem impedir que o domínio gerido pelo Azure AD DS seja apoiado. Resolva quaisquer alertas ativos para o domínio gerido pelo Azure AD DS. Se o monitor de backup não atualizar o estado para reportar um backup recente, abra um pedido de [apoio Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronização com monitor AD Azure

Um domínio gerido por Azure AD DS sincroniza regularmente com o Diretório Ativo Azure. O número de utilizadores e objetos de grupo, e o número de alterações efetuadas no diretório da AD Azure desde a última sincronização, afeta o tempo que demora a sincronizar. Se o domínio gerido pela AD DS azure foi sincronizado pela última vez há mais de três dias, verifique e resolva quaisquer alertas ativos. Se o monitor de sincronização não atualizar o estado para mostrar uma sincronização recente depois de abordar quaisquer alertas ativos, abra um pedido de [suporte Azure][azure-support].

### <a name="alerts"></a>Alertas

Os alertas são gerados para problemas num domínio gerido pelo Azure AD DS que precisam de ser endereçados para que o serviço seja executado corretamente. Cada alerta explica o problema e dá um URL que descreve passos específicos para resolver o problema. Para obter mais informações sobre os possíveis alertas e as suas resoluções, consulte alertas de [resolução de problemas](troubleshoot-alerts.md).

Os alertas de estado de saúde são categorizados nos seguintes níveis de gravidade:

 * **Os alertas críticos** são problemas que impactam severamente o domínio gerido pelo Azure AD DS. Estes alertas devem ser abordados imediatamente. A plataforma Azure não consegue monitorizar, gerir, remendar e sincronizar o domínio gerido até que os problemas sejam resolvidos.
 * **Os alertas** de alerta notificam-no de problemas que podem afetar as operações de domínio geridos pelo Azure AD DS se o problema persistir. Estes alertas também oferecem recomendações para garantir o domínio gerido.
 * **Alertas informativos** são notificações que não impactam negativamente o domínio gerido pelo Azure AD DS. Os alertas informativos fornecem algumas informações sobre o que está a acontecer no domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre alertas que são mostrados na página do estado de saúde, consulte [Alertas Resolver no seu domínio gerido][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
