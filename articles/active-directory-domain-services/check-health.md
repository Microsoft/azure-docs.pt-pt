---
title: Azure AD Domain Services-verificar a integridade do domínio gerenciado | Microsoft Docs
description: Verifique a integridade do seu domínio gerenciado usando a página integridade no portal do Azure.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234161"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Verificar a integridade de um domínio gerenciado Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Visão geral da página de integridade
Usando a página de integridade em seu portal do Azure, você poderá se manter atualizado sobre o que está acontecendo em seu domínio gerenciado. Este artigo percorre os elementos da página de integridade.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Como exibir a integridade do seu domínio gerenciado
1. Navegue até a [página Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no domínio do qual você deseja exibir a integridade.
3. No painel de navegação à esquerda, clique em **integridade**.

A figura a seguir ilustra uma página de exemplo de integridade: ![Página de integridade de exemplo](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> A integridade do seu domínio gerenciado é avaliada a cada hora. Depois de fazer alterações no domínio gerenciado, aguarde o próximo ciclo de avaliação para exibir a integridade atualizada do seu domínio gerenciado. O carimbo de data/hora "último avaliado" no canto superior direito mostra quando a integridade do seu domínio gerenciado foi avaliada pela última vez.
>

### <a name="status-of-your-managed-domain"></a>Status do seu domínio gerenciado
O status na parte superior direita da página de integridade indica a integridade geral do seu domínio gerenciado. Os fatores de status em todos os alertas existentes em seu domínio. Você também pode exibir o status do seu domínio na página Visão geral de Azure AD Domain Services.

| Estado | Ícone | Explicação |
| --- | :----: | --- |
| A executar | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Seu domínio gerenciado está funcionando sem problemas e não tem nenhum alerta crítico ou de aviso. Este domínio pode ter alertas informativos. |
| Requer atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Não há alertas críticos em seu domínio gerenciado, mas há um ou mais alertas de aviso que precisam ser resolvidos. |
| Requer atenção (crítico) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Há um ou mais alertas críticos em seu domínio gerenciado. Você também pode ter alertas de aviso e/ou informativos. |
| Implantar | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Seu domínio está em processo de implantação. |

## <a name="monitors"></a>Monitores
Monitores são aspectos do seu domínio gerenciado que Azure AD Domain Services monitores regularmente. A melhor maneira de manter seus monitores em um estado íntegro é resolver quaisquer alertas ativos para seu domínio gerenciado.

O Azure AD Domain Services atualmente monitora o seguinte:
 - Cópia de segurança
 - Sincronização com o Azure AD

### <a name="the-backup-monitor"></a>O monitor de ' backup '
Isso monitora se os backups regulares do seu domínio gerenciado estão sendo executados. A tabela a seguir explica o que esperar na coluna de detalhes do monitor de backup:

| Valor de detalhe | Explicação |
| --- | --- |
|"Nunca foi feito backup" | Esse estado é normal para um domínio gerenciado recém-criado. Em geral, o primeiro backup é criado 24 horas depois que o domínio gerenciado é provisionado. Se o domínio gerenciado não for criado recentemente ou se você vir esse estado por um período anormal, [contate o suporte](contact-us.md). |
| O último backup foi levado de 1 a 14 dias atrás | Em geral, esse valor é esperado para o monitor de backup. |
| O último backup foi feito há mais de 14 dias. | Qualquer tempo por mais de duas semanas é um tempo muito longo desde o último backup. Alertas críticos ativos podem impedir que o backup do domínio gerenciado seja feito regularmente. Primeiro, resolva todos os alertas ativos para seu domínio gerenciado e, se o problema persistir, [contate o suporte](contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>O monitor ' sincronização com o Azure AD '
A Microsoft monitora com que frequência seu domínio gerenciado é sincronizado com Azure Active Directory. O número de objetos (usuários & grupos) e o número de alterações feitas no diretório do AD do Azure desde a última sincronização podem afetar o tempo que um período de sincronização pode tomar. Se o seu domínio gerenciado foi sincronizado pela última vez há três dias, [contate o suporte](contact-us.md).

## <a name="alerts"></a>Alertas
Os alertas são gerados para problemas em seu domínio gerenciado que precisam ser resolvidos para que Azure AD Domain Services seja executado. Cada alerta explica o problema e fornece uma URL de resolução que descreve as etapas específicas para resolver o problema. Para exibir todos os alertas e suas resoluções, visite o artigo [solucionar problemas de alertas](troubleshoot-alerts.md) .

### <a name="alert-severity"></a>Severidade do alerta
Os alertas são categorizados em três níveis diferentes de severidade: crítico, aviso e informativo.

 * **Alertas críticos** são problemas que afetam seriamente o domínio gerenciado. Esses alertas devem ser resolvidos imediatamente, pois a Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado. 
 * **Alertas de aviso** notificam sobre problemas que podem afetar seu domínio gerenciado no futuro. Esses alertas oferecem recomendações para proteger seu domínio gerenciado.
 * **Alertas** informativos são notificações que não afetam negativamente seu domínio. Os alertas informativos são projetados para manter você mais experiente sobre o que está acontecendo em seu domínio e Azure AD Domain Services.

## <a name="next-steps"></a>Passos Seguintes
- [Resolver alertas em seu domínio gerenciado](troubleshoot-alerts.md)
- [Leia mais sobre Azure AD Domain Services](overview.md)
- [Contate a equipe do produto](contact-us.md)
