---
title: Azure AD Connect Health e User Privacy | Microsoft Docs
description: Este documento descreve a privacidade do utilizador com a Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89182365"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacidade do utilizador e Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata do Azure AD Connect Health e da privacidade do utilizador.  Para obter informações sobre a Azure AD Connect e a privacidade do utilizador consulte o artigo [aqui](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Classificação da privacidade do utilizador
A azure AD Connect Health enquadra-se na categoria de processador de **dados** da classificação do RGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados a parceiros-chave e consumidores finais. O Azure AD Connect Health não gera dados do utilizador e não tem controlo independente sobre os dados pessoais recolhidos e como são utilizados. A recuperação de dados, agregação, análise e reporte em Azure AD Connect Health baseiam-se em dados existentes no local. 

## <a name="data-retention-policy"></a>Política de retenção de dados
A azure AD Connect Health não gera relatórios, executa análises ou fornece insights para além de 30 dias. Portanto, o Azure AD Connect Health não armazena, processa ou retém quaisquer dados para além de 30 dias. Este design está em conformidade com os regulamentos do RGPD, os regulamentos de conformidade com a privacidade da Microsoft e as políticas de retenção de dados AZure. 

Servidores com dados ativos **do serviço de saúde não estão atualizados** alertas de **erro** durante mais de 30 dias consecutivos sugerem que nenhum dado chegou à Connect Health durante esse período de tempo. Estes servidores serão desativados e não mostrados no portal Connect Health. Para voltar a ativar os servidores, é necessário desinstalar e [reinstalar o agente de saúde](how-to-connect-health-agent-install.md). Por favor, note que isto não se aplica a **avisos** com o mesmo tipo de alerta. Os avisos indicam que faltam dados parciais no servidor para o qual é alerta. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Desativar a recolha e monitorização de dados em Azure AD Connect Health
O Azure AD Connect Health permite-lhe parar a recolha de dados para cada servidor monitorizado individualmente ou para um exemplo de um serviço monitorizado. Por exemplo, pode parar a recolha de dados para servidores individuais da ADFS (Ative Directory Federation Services) que são monitorizados utilizando a Azure AD Connect Health. Também pode parar a recolha de dados para toda a instância ADFS que está a ser monitorizada usando a Azure AD Connect Health. Quando optar por fazê-lo, os servidores correspondentes são eliminados do portal Azure AD Connect Health, após interromper a recolha de dados. 

>[!IMPORTANT]
> Precisa de privilégios de Administrador Global AD Azure ou do papel de Contribuinte no Azure RBAC para eliminar servidores monitorizados do Azure AD Connect Health.
>
> Remover um servidor ou uma instância de serviço do Azure AD Connect Health não é uma ação reversível. 

### <a name="what-to-expect"></a>O que esperar?
Se parar a recolha e monitorização de dados para um servidor monitorizado individual ou uma instância de um serviço monitorizado, note o seguinte:

- Quando elimina uma instância de um serviço monitorizado, a instância é removida da lista de serviços de monitorização Azure AD Connect Health no portal. 
- Quando elimina um servidor monitorizado ou uma instância de um serviço monitorizado, o Health Agent NÃO é desinstalado ou removido dos seus servidores. O Agente de Saúde está configurado para não enviar dados para a Azure AD Connect Health. É necessário desinstalar manualmente o Agente de Saúde em servidores previamente monitorizados.
- Se não tiver desinstalado o Agente de Saúde antes de realizar este passo, poderá ver eventos de erro no(s) servidor(s) relacionados com o Agente de Saúde.
- Todos os dados pertencentes à instância do serviço monitorizado são eliminados de acordo com a Política de Retenção de Dados do Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Desativar a recolha e monitorização de dados para uma instância de um serviço monitorizado
Veja [como remover uma instância de serviço da Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Desativar a recolha e monitorização de dados para um servidor monitorizado
Veja [como remover um servidor do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Desativar a recolha e monitorização de todos os serviços monitorizados no Azure AD Connect Health
A Azure AD Connect Health também oferece a opção de parar a recolha de dados de **todos os** serviços registados no arrendatário. Recomendamos uma cuidadosa consideração e o reconhecimento total de todos os administradores globais antes de tomar a medida. Assim que o processo começar, o serviço Connect Health deixará de receber, processar e reportar quaisquer dados de todos os seus serviços. Os dados existentes no serviço Connect Health serão retidos por um não mais de 30 dias.
Se pretender parar a recolha de dados de servidor específico, siga os passos na eliminação de servidores específicos. Para impedir a recolha de dados do arrendatário, siga as seguintes etapas para impedir a recolha de dados e apagar todos os serviços do arrendatário.

1. Clique em **Definições Gerais** na configuração na lâmina principal. 
2. Clique no botão **Stop Data Collection** na parte superior da lâmina. As outras opções de configuração do inquilino serão desativadas assim que o processo começar.  
 
   ![Parar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Certifique-se de que a lista de serviços a bordo que são afetados pela paragem das recolhas de dados. 
4. Introduza o nome exato do inquilino para ativar o botão de ação **Eliminar**
5. Clique em **Apagar** para desencadear a eliminação de todos os serviços. A Connect Health deixará de receber, processar, reportar quaisquer dados enviados dos seus serviços de bordo. Todo o processo pode demorar até 24 horas. Note que este passo não é reversível. 
6. Após o processo estar concluído, não verá mais nenhum serviço registado na Connect Health. 

   ![Depois de a recolha de dados ter parado](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ree capacitar a recolha e monitorização de dados em Azure AD Connect Health
Para voltar a ativar a monitorização no Azure AD Connect Health para um serviço monitorizado previamente eliminado, deve desinstalar e [reinstalar o agente de saúde](how-to-connect-health-agent-install.md) em todos os servidores.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Ree capacitar a recolha e monitorização de dados para todos os serviços monitorizados

A recolha de dados em termos de inquilino pode ser retomada no Azure AD Connect Health. Recomendamos uma cuidadosa consideração e o reconhecimento total de todos os administradores globais antes de tomar a medida.

>[!IMPORTANT]
> Os seguintes passos estarão disponíveis após 24 horas de ação de desativação.
> Após a habilitação da recolha de dados, os dados de insight e monitorização apresentados no Connect Health não mostrarão quaisquer dados legados recolhidos antes. 

1. Clique em **Definições Gerais** na configuração na lâmina principal. 
2. Clique no botão **Ativar a Recolha** de Dados na parte superior da lâmina. 
 
   ![Ativar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Introduza o nome exato do inquilino para ativar o botão **Ativar.**
4. Clique no botão **Ativar** para conceder permissão de recolha de dados no serviço Connect Health. A mudança será aplicada em breve. 
5. Siga o [processo de instalação](how-to-connect-health-agent-install.md) para reinstalar o agente nos servidores a monitorizar e os serviços estarão presentes no portal.  


## <a name="next-steps"></a>Passos seguintes
* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect e Privacidade do Utilizador](reference-connect-user-privacy.md)

