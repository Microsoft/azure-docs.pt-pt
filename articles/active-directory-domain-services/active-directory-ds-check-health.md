---
title: Serviços de domínio do Azure AD - Verifique o estado de funcionamento do seu domínio gerido | Documentos da Microsoft
description: Verifique o estado de funcionamento do seu domínio gerido com a página de estado de funcionamento no portal do Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: 03cbf5dd466af5413aee5e0f5f285bc53de292ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417591"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Verifique o estado de funcionamento de um domínio gerido do Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Descrição geral da página de estado de funcionamento
Usando a página de estado de funcionamento no portal do Azure, é capazes de se manter atualizado sobre o que acontece no seu domínio gerido. Este artigo explica os elementos da página de estado de funcionamento.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Como ver o estado de funcionamento do seu domínio gerido
1. Navegue para o [página do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no domínio que pretende ver o estado de funcionamento.
3. No painel de navegação do lado esquerdo, clique em **estado de funcionamento**.

A imagem seguinte ilustra uma página de estado de funcionamento de exemplo: ![Página de estado de funcionamento de exemplo](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Estado de funcionamento do seu domínio gerido é avaliado a cada hora. Depois de efetuar alterações ao seu domínio gerido, aguarde até o próximo ciclo de avaliação para ver o estado de funcionamento atualizado do seu domínio gerido. O carimbo de hora "Avaliado pela última vez" no canto superior direito mostra quando o estado de funcionamento do seu domínio gerido foi avaliado pela última vez.
>

### <a name="status-of-your-managed-domain"></a>Estado do seu domínio gerido
O estado no canto superior direito do seu estado de funcionamento da página indica o estado de funcionamento global do seu domínio gerido. O Estado tenha em consideração o todos os alertas existentes no seu domínio. Também pode ver o estado do seu domínio na página Descrição geral do Azure AD Domain Services.

| Estado | Ícone | Explicação |
| --- | :----: | --- |
| A executar | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | O domínio gerido está a funcionar sem problemas e não tem quaisquer alertas críticas ou de aviso. Este domínio pode ter alertas informativos. |
| Necessita de atenção (aviso) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Não existem sem alertas críticos no seu domínio gerido, mas existem um ou mais alertas de aviso que precisam ser abordadas. |
| Necessita de atenção (crítico) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Existem um ou mais alertas críticos no seu domínio gerido. Também pode ter alertas de aviso e/ou informativas. |
| A implementar | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | O domínio está no processo de que está sendo implantado. |

## <a name="monitors"></a>Monitores
Monitores são aspectos do seu domínio gerido que o Azure AD Domain Services monitoriza regularmente. É a melhor forma de manter seus monitores em bom estado de funcionamento resolver quaisquer alertas ativos para o seu domínio gerido.

Serviços de domínio do Azure AD monitora o seguinte:
 - Cópia de segurança
 - Sincronização com o Azure AD

### <a name="the-backup-monitor"></a>O monitor de "cópia de segurança"
Isto monitoriza se estão a ser efetuadas cópias de segurança regulares do seu domínio gerido. A tabela seguinte explica o que esperar na coluna detalhes do monitor de cópia de segurança:

| Valor de detalhe | Explicação |
| --- | --- |
|"Nunca uma cópia de segurança" | Este estado é normal para um domínio gerido criado recentemente. Em geral, a primeira cópia de segurança é criada a 24 horas após o seu domínio gerido está aprovisionado. Se o seu domínio gerido não é criado recentemente ou ver neste estado durante um período anormal de tempo, [contacte o suporte](active-directory-ds-contact-us.md). |
| Última cópia de segurança foi efetuada a 1 de 14 dias atrás | Em geral, este valor é esperado para o monitor de cópia de segurança. |
| Última cópia de segurança foi efetuada há mais de 14 dias. | Qualquer altura mais de duas semanas é um tempo anormalmente longo, desde a última cópia de segurança. Alertas críticos ativos podem impedir que o domínio gerido a cópia de segurança em intervalos regulares. Primeiro, resolva qualquer ativo alertas para o seu domínio gerido e, em seguida, se ainda o problema persiste, [contacte o suporte](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>O monitor de "sincronização com o Azure AD"
Microsoft monitoriza a frequência com que o seu domínio gerido está sincronizado com o Azure Active Directory. O número de objetos (utilizadores e grupos) e o número de alterações feitas no diretório do Azure AD, uma vez que a última sincronização pode ambos afetar o tempo que pode demorar um período de sincronização. Se o seu domínio gerido última sincronização há, mais de três dias [contacte o suporte](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Alertas
São gerados alertas para problemas no seu domínio gerido, que precisam ser resolvidos para que os serviços de domínio do Azure AD para serem executados. Cada alerta explica o problema e um URL de resolução que descreve os passos específicos para resolver o problema. Para ver todos os alertas e resolução das, visite o [resolver problemas relacionados com alertas](active-directory-ds-troubleshoot-alerts.md) artigo.

### <a name="alert-severity"></a>Gravidade do alerta
Alertas são categorizados em três diferentes níveis de gravidade: crítico, aviso e informativas.

 * **Alertas críticos** são problemas que afetar gravemente o seu domínio gerido. Estes alertas devem ser resolvidos imediatamente, como a Microsoft não é possível monitorizar, gerir, corrigir e sincronizar o domínio gerido. 
 * **Alertas de aviso** notificá-lo dos problemas que possam afetar o seu domínio gerido no futuro. Estes alertas oferecem recomendações para proteger o seu domínio gerido.
 * **Alertas informativos** são notificações que são não afetar negativamente o seu domínio. Alertas informativos são projetadas para mantê-lo informado sobre o que acontece no seu domínio e o Azure AD Domain Services.

## <a name="next-steps"></a>Passos Seguintes
- [Resolver alertas no seu domínio gerido](active-directory-ds-troubleshoot-alerts.md)
- [Leia mais sobre o Azure AD Domain Services](active-directory-ds-overview.md)
- [Contacte a equipa de produto](active-directory-ds-contact-us.md)
