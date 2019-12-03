---
title: Verificar a integridade de Azure Active Directory Domain Services | Microsoft Docs
description: Saiba como verificar a integridade de um domínio gerenciado Azure Active Directory Domain Services (Azure AD DS) e entender as mensagens de status usando o portal do Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 501214f87a65c71436e262608f7e9b3471cc9775
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705415"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Verificar a integridade de um domínio gerenciado Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) executa algumas tarefas em segundo plano para manter o domínio gerenciado íntegro e atualizado. Essas tarefas incluem fazer backups, aplicar atualizações de segurança e sincronizar dados do Azure AD. Se houver problemas com o domínio gerenciado AD DS do Azure, essas tarefas poderão não ser executadas com êxito. Para examinar e resolver quaisquer problemas, você pode verificar o status de integridade de um domínio gerenciado do Azure AD DS usando o portal do Azure.

Este artigo mostra como exibir o status de integridade do AD DS do Azure e entender as informações ou os alertas mostrados.

## <a name="view-the-health-status"></a>Exibir o status de integridade

O status de integridade de um domínio gerenciado do Azure AD DS é exibido usando o portal do Azure. As informações sobre o último backup e a sincronização com o Azure AD podem ser vistas, junto com todos os alertas que indicam um problema com a integridade do domínio gerenciado. Para exibir o status de integridade de um domínio gerenciado AD DS do Azure, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione seu domínio gerenciado AD DS do Azure, como *aadds.contoso.com*.
1. No lado esquerdo da janela de recursos do AD DS do Azure, selecione **integridade**. A captura de tela de exemplo a seguir mostra um domínio gerenciado AD DS do Azure íntegro e o status do último backup e a sincronização do AD do Azure:

    ![Visão geral da página de integridade no portal do Azure mostrando o status de Azure Active Directory Domain Services](./media/check-health/health-page.png)

O carimbo de data/hora *último avaliado* da página de integridade mostra quando o domínio gerenciado do Azure AD DS foi verificado pela última vez. A integridade de um domínio gerenciado do Azure AD DS é avaliada a cada hora. Se você fizer alterações em um domínio gerenciado AD DS do Azure, aguarde até o próximo ciclo de avaliação para exibir o status de integridade atualizado.

O status no canto superior direito indica a integridade geral do domínio gerenciado do Azure AD DS. O status considera todos os alertas existentes em seu domínio. A tabela a seguir detalha os indicadores de status disponíveis:

| Estado | cone | Explicação |
| --- | :----: | --- |
| A executar | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | O domínio gerenciado do AD DS do Azure está sendo executado corretamente e não tem nenhum alerta crítico ou de aviso. O domínio pode ter alertas informativos. |
| Requer atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Não há alertas críticos no domínio gerenciado AD DS do Azure, mas há um ou mais alertas de aviso que devem ser resolvidos. |
| Requer atenção (crítico) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Há um ou mais alertas críticos no domínio gerenciado AD DS do Azure que devem ser resolvidos. Você também pode ter alertas de aviso e/ou informativos. |
| Implantar | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | O domínio AD DS do Azure está sendo implantado. |

## <a name="understand-monitors-and-alerts"></a>Entender monitores e alertas

O status de integridade de um domínio gerenciado AD DS do Azure mostra dois tipos de informações – monitores e alertas. Monitores mostram a hora em que as tarefas de segundo plano de núcleo foram concluídas. Os alertas fornecem informações ou sugestões para melhorar a estabilidade do domínio gerenciado.

### <a name="monitors"></a>Monitores

Monitores são áreas de um domínio gerenciado AD DS do Azure que são verificados regularmente. Se houver alertas ativos para o domínio gerenciado AD DS do Azure, ele poderá fazer com que um dos monitores relate um problema. O Azure AD Domain Services atualmente monitora as seguintes áreas:

* Backup
* Sincronização com o Azure AD

#### <a name="backup-monitor"></a>Monitor de backup

O monitor de backup verifica se os backups regulares automatizados do domínio gerenciado do Azure AD DS foram executados com êxito. A tabela a seguir detalha o status do monitor de backup disponível:

| Valor de detalhe | Explicação |
| --- | --- |
| Nunca foi feito backup | Esse estado é normal para novos domínios gerenciados do Azure AD DS. O primeiro backup deve ser criado 24 horas depois que o domínio gerenciado AD DS do Azure for implantado. Se esse status persistir, [abra uma solicitação de suporte do Azure][azure-support]. |
| O último backup foi levado de 1 a 14 dias atrás | Esse intervalo de tempo é o status esperado para o monitor de backup. Backups regulares automatizados devem ocorrer neste período. |
| O último backup foi feito há mais de 14 dias. | Um TimeSpan com mais de duas semanas indica que há um problema com os backups regulares automatizados. Os alertas críticos ativos podem impedir o backup do Azure AD DS domínio gerenciado. Resolva todos os alertas ativos para o domínio gerenciado AD DS do Azure. Se o monitor de backup não atualizar o status para relatar um backup recente, [abra uma solicitação de suporte do Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Sincronização com o monitor do Azure AD

Um domínio gerenciado do Azure AD DS é sincronizado regularmente com o Azure Active Directory. O número de usuários e objetos de grupo e o número de alterações feitas no diretório do AD do Azure desde a última sincronização, afeta o tempo necessário para a sincronização. Se o domínio gerenciado do Azure AD DS foi sincronizado pela última vez há três dias, verifique e resolva todos os alertas ativos. Se o monitor de sincronização não atualizar o status para mostrar uma sincronização recente, [abra uma solicitação de suporte do Azure][azure-support].

### <a name="alerts"></a>Alertas

Os alertas são gerados para problemas em um domínio gerenciado AD DS do Azure que precisam ser resolvidos para que o serviço seja executado corretamente. Cada alerta explica o problema e fornece uma URL que descreve as etapas específicas para resolver o problema. Para obter mais informações sobre os possíveis alertas e suas resoluções, consulte [Solucionando problemas de alertas](troubleshoot-alerts.md).

Os alertas de status de integridade são categorizados nos seguintes níveis de severidade:

 * **Alertas críticos** são problemas que afetam seriamente o domínio gerenciado AD DS do Azure. Esses alertas devem ser resolvidos imediatamente. A plataforma Azure não pode monitorar, gerenciar, aplicar patch e sincronizar o domínio gerenciado até que os problemas sejam resolvidos.
 * **Alertas de aviso** notificam sobre problemas que podem afetar as operações de domínio gerenciado AD DS do Azure se o problema persistir. Esses alertas também oferecem recomendações para proteger o domínio gerenciado.
 * **Alertas informativos** são notificações que não afetam negativamente o domínio gerenciado AD DS do Azure. Os alertas informativos fornecem informações sobre o que está acontecendo no domínio gerenciado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre alertas mostrados na página status de integridade, consulte [resolver alertas em seu domínio gerenciado][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
