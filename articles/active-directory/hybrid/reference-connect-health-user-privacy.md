---
title: Estado de funcionamento e a privacidade dos utilizadores do Azure AD Connect | Documentos da Microsoft
description: Este documento descreve a privacidade dos utilizadores com o Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27e5ae48a8194c05a19e5164ee2cc6be68967a62
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112760"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacidade do utilizador e o Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata da privacidade do Azure AD Connect Health e utilizador.  Para obter informações sobre privacidade do Azure AD Connect e o utilizador, consulte o artigo [aqui](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Classificação de privacidade do utilizador
O Azure AD Connect Health recai sobre o **processador dos dados** categoria de classificação de GDPR. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados principais parceiros e consumidores finais. O Azure AD Connect Health não gera dados de utilizador e não tem independente controle sobre quais os dados pessoais são recolhidos e como são utilizadas. Obtenção de dados, a agregação, análise e relatórios no Azure AD Connect Health baseiam-se nos dados de no local existentes. 

## <a name="data-retention-policy"></a>Política de retenção de dados
O Azure AD Connect Health não gerar relatórios, realizar análises ou fornecer informações mais de 30 dias. Por conseguinte, o Azure AD Connect Health não armazenar, processar ou reter dados mais de 30 dias. Esse design está em conformidade com os regulamentos de GDPR, regulamentos de conformidade de privacidade do Microsoft e políticas de retenção de dados do Azure AD. 

Servidores com o Active Directory **dados de estado de funcionamento do serviço não estão atualizados** **erro** alertas para mais de 30 dias consecutivos sugerem que não existem dados atingiu Connect Health durante esse intervalo de tempo. Estes servidores serão desativados e não apresentados no portal do Connect Health. Para reativar os servidores, tem de desinstalar e [reinstale o agente de estado de funcionamento](how-to-connect-health-agent-install.md). Tenha em atenção que isto não se aplica aos **avisos** com o mesmo tipo de alerta. Avisos indicam que os dados parciais estão em falta no servidor que é alertado. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Desativar a recolha de dados e a monitorização no Azure AD Connect Health
O Azure AD Connect Health permite-lhe parar a recolha de dados para cada servidor monitorizado individual ou para uma instância de um serviço monitorizado. Por exemplo, pode parar a recolha de dados para servidores individuais do ADFS (Serviços de Federação do Active Directory) que são monitorizados com o Azure AD Connect Health. Também pode parar a recolha de dados para a toda instância de AD FS que está a ser monitorizada com o Azure AD Connect Health. Quando optar por fazê-lo, os servidores correspondentes são eliminados do portal do Azure AD Connect Health, depois de parar a recolha de dados. 

>[!IMPORTANT]
> Tem privilégios de Administrador Global do AD do Azure ou a função de Contribuidor no RBAC delete monitorizado servidores do Azure AD Connect Health.
>
> Remover um servidor ou instância de serviço do Azure AD Connect Health não é uma ação reversível. 

### <a name="what-to-expect"></a>O que esperar?
Se parar a recolha de dados e monitorização para um servidor monitorizado individual ou uma instância de um serviço monitorado, tenha em atenção o seguinte:

- Quando elimina uma instância de um serviço monitorado, a instância é removida a partir da lista do serviço de monitorização do Azure AD Connect Health no portal do. 
- Quando elimina um servidor monitorizado ou uma instância de um serviço monitorizado, o agente de estado de funcionamento não está desinstalado ou removido do seus servidores. O agente de estado de funcionamento é configurado para não enviar dados para o Azure AD Connect Health. Terá de desinstalar manualmente o agente de estado de funcionamento nos servidores monitorizados anteriormente.
- Se não o desinstalou o agente de estado de funcionamento antes de executar este passo, poderá ver eventos de erro no servidor ou servidores relacionados com o agente de estado de funcionamento.
- Todos os dados que pertencem à instância do serviço monitorizado é eliminados de acordo com a política de retenção de dados do Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Desativar a recolha de dados e a monitorização para uma instância de um serviço monitorizado
Ver [como remover uma instância de serviço do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Desativar a recolha de dados e a monitorização para um servidor monitorizado
Ver [como remover um servidor do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Desativar a recolha de dados e monitoramento de todos os serviços monitorizados no Azure AD Connect Health
O Azure AD Connect Health fornece também a opção para parar a recolha de dados do **todos os** registado serviços no inquilino. Recomendamos consideração cuidadosa e confirmação completa de todos os administradores globais antes de efetuar a ação. Depois do processo é iniciado, serviço de integridade de ligar deixará de receber, processamento e relatórios de todos os dados de todos os seus serviços. Serão retidos dados existentes no serviço do Connect Health para não mais de 30 dias.
Se pretender parar a recolha de dados de servidor específico, siga os passos na eliminação de servidores específicos. Para parar a recolha de dados tenant-wise, siga os passos seguintes para parar a recolha de dados e eliminar todos os serviços do inquilino.

1. Clique em **definições gerais** em configuração do painel principal. 
2. Clique em **parar a recolha de dados** botão na parte superior do painel. As outras opções de definições de configuração do inquilino serão desativadas uma vez que o processo é iniciado.  
 
   ![Parar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Certifique-se a lista de serviços de integração que são afetados pela parar a coleções de dados. 
4. Introduza o nome de inquilino exato para ativar a **eliminar** botão de ação
5. Clique em **eliminar** para acionar a eliminação de todos os serviços. Ligar estado de funcionamento deixará de receber, processamento, todos os dados enviados dos seus serviços de integração de relatórios. Todo o processo de pode demorar até 24 horas. Tenha em atenção que este passo não é reversível. 
6. Depois de concluído o processo, não verá quaisquer serviços registrados no Connect Health mais. 

   ![Após a interrupção da recolha de dados](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Volte a ativar a recolha de dados e a monitorização no Azure AD Connect Health
Para voltar a Ativar monitorização no Azure AD Connect Health para um serviço monitorado excluído anteriormente, tem de desinstalar e [reinstale o agente de estado de funcionamento](how-to-connect-health-agent-install.md) em todos os servidores.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Volte a ativar a recolha de dados e monitoramento de todos os serviços monitorizados

Pode ser retomada a recolha de dados tenant-Wise no Azure AD Connect Health. Recomendamos consideração cuidadosa e confirmação completa de todos os administradores globais antes de efetuar a ação.

>[!IMPORTANT]
> As etapas a seguir estarão disponíveis após 24 horas de desativar a ação.
> Depois de ativar de recolha de dados, a informação apresentada e a monitorização dos dados no Connect Health não apresentará quaisquer dados herdados coletados antes. 

1. Clique em **definições gerais** em configuração do painel principal. 
2. Clique em **ativar a recolha de dados** botão na parte superior do painel. 
 
   ![Ativar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Introduza o nome de inquilino exato para ativar a **ativar** botão.
4. Clique em **ativar** botão para conceder permissão de recolha de dados no serviço do Connect Health. A alteração será aplicada em breve. 
5. Siga os [processo de instalação](how-to-connect-health-agent-install.md) reinstalar o agente nos servidores a serem monitoradas e os serviços estarão presentes no portal.  


## <a name="next-steps"></a>Passos Seguintes
* [Rever a política de Privacy da Microsoft no Centro de fidedignidade](https://www.microsoft.com/trustcenter)
* [Do Azure AD Connect e privacidade do utilizador](reference-connect-user-privacy.md)

