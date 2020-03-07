---
title: Azure AD Connect Health and User Privacy [ Azure AD Connect Health and User Privacy ] Microsoft Docs
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
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376412"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacidade do utilizador e Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata da Azure AD Connect Health e da privacidade do utilizador.  Para obter informações sobre o Azure AD Connect e a privacidade do utilizador consulte o artigo [aqui](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Classificação da privacidade do utilizador
A Azure AD Connect Health enquadra-se na categoria de processador de **dados** da classificação rGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados a parceiros-chave e consumidores finais. A Azure AD Connect Health não gera dados do utilizador e não tem controlo independente sobre o que os dados pessoais são recolhidos e como são utilizados. A recuperação de dados, agregação, análise e reporte na Azure AD Connect Health baseiam-se nos dados existentes no local. 

## <a name="data-retention-policy"></a>Política de retenção de dados
A Azure AD Connect Health não gera relatórios, realiza análises ou fornece insights para além de 30 dias. Por isso, a Azure AD Connect Health não armazena, processa ou retém quaisquer dados para além de 30 dias. Este design está em conformidade com os regulamentos do RGPD, os regulamentos de conformidade de privacidade da Microsoft e as políticas de retenção de dados da Azure AD. 

Os servidores com dados ativos do **serviço de saúde não estão atualizados** alertas de **erro** por mais de 30 dias consecutivos sugerem que nenhum dado chegou à Connect Health durante esse período de tempo. Estes servidores serão desativados e não mostrados no portal Connect Health. Para voltar a ativar os servidores, deve desinstalar e [reinstalar o agente de saúde](how-to-connect-health-agent-install.md). Por favor, note que isto não se aplica a **avisos** com o mesmo tipo de alerta. Os avisos indicam que faltam dados parciais do servidor para o qual está alertado. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Desativar a recolha e monitorização de dados na Azure AD Connect Health
O Azure AD Connect Health permite-lhe parar a recolha de dados para cada servidor monitorizado individualmente ou para um serviço monitorizado. Por exemplo, pode parar a recolha de dados para servidores Individuais ADFS (Ative Directory Federation Services) que são monitorizados usando a Azure AD Connect Health. Também pode parar a recolha de dados para toda a instância ADFS que está a ser monitorizada utilizando a Azure AD Connect Health. Quando optar por fazê-lo, os servidores correspondentes são eliminados do portal Azure AD Connect Health, após parar a recolha de dados. 

>[!IMPORTANT]
> Precisa dos privilégios do Administrador Global Azure AD ou do papel de Contribuinte no RBAC para eliminar servidores monitorizados da Azure AD Connect Health.
>
> Remover um servidor ou instância de serviço da Azure AD Connect Health não é uma ação reversível. 

### <a name="what-to-expect"></a>O que esperar?
Se parar a recolha e monitorização de dados para um servidor monitorizado individualmente ou uma instância de um serviço monitorizado, note o seguinte:

- Ao eliminar uma instância de um serviço monitorizado, a instância é removida da lista de serviços de monitorização Azure AD Connect Health no portal. 
- Quando elimina um servidor monitorizado ou uma instância de um serviço monitorizado, o Agente de Saúde NÃO é desinstalado ou removido dos seus servidores. O Agente de Saúde está configurado para não enviar dados para a Azure AD Connect Health. É necessário desinstalar manualmente o Agente de Saúde em servidores previamente monitorizados.
- Se não tiver desinstalado o Agente de Saúde antes de realizar este passo, poderá ver erros nos servidores(s) relacionados com o Agente de Saúde.
- Todos os dados pertencentes à instância do serviço monitorizado são eliminados de acordo com a Política de Retenção de Dados do Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Desativar a recolha e monitorização de dados por exemplo de um serviço monitorizado
Veja [como remover uma instância de serviço da Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Desativar a recolha e monitorização de dados para um servidor monitorizado
Veja [como remover um servidor do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Desativar a recolha e monitorização de dados para todos os serviços monitorizados na Azure AD Connect Health
A Azure AD Connect Health também oferece a opção de parar a recolha de dados de **todos os** serviços registados no inquilino. Recomendamos uma cuidadosa consideração e o reconhecimento total de todos os administradores globais antes de tomar a ação. Assim que o processo começar, o serviço Connect Health deixará de receber, processar e reportar quaisquer dados de todos os seus serviços. Os dados existentes no serviço Connect Health serão conservados por um nível não superior a 30 dias.
Se pretender parar a recolha de dados de servidores específicos, siga os passos na eliminação de servidores específicos. Para impedir a recolha de dados em termos de inquilino, siga as seguintes etapas para parar a recolha de dados e apagar todos os serviços do arrendatário.

1. Clique em **Configurações Gerais** sob configuração na lâmina principal. 
2. Clique no botão **Stop Data Collection** na parte superior da lâmina. As outras opções de configuração do inquilino serão desativadas assim que o processo começar.  
 
   ![Parar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Certifique-se da lista de serviços a bordo que são afetados pela paragem das recolhas de dados. 
4. Introduza o nome exato do inquilino para **ativar** o botão de ação Delete
5. Clique em **Apagar** para desencadear a eliminação de todos os serviços. A Connect Health deixará de receber, processar, reportar quaisquer dados enviados dos seus serviços a bordo. Todo o processo pode demorar até 24 horas. Note que este passo não é reversível. 
6. Após a conclusão do processo, não verá mais nenhum serviço registado na Connect Health. 

   ![Após a recolha de dados parado](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Reativar a recolha e monitorização de dados na Azure AD Connect Health
Para reativar a monitorização no Azure AD Connect Health para um serviço monitorizado previamente eliminado, deve desinstalar e [reinstalar o agente](how-to-connect-health-agent-install.md) de saúde em todos os servidores.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Reativar a recolha e monitorização de dados para todos os serviços monitorizados

A recolha de dados em termos de inquilinos pode ser retomada na Azure AD Connect Health. Recomendamos uma cuidadosa consideração e o reconhecimento total de todos os administradores globais antes de tomar a ação.

>[!IMPORTANT]
> Os seguintes passos estarão disponíveis após 24 horas de ação de desativação.
> Após a visualização da recolha de dados, os dados apresentados de insight e monitorização na Connect Health não mostrarão quaisquer dados antigos recolhidos antes. 

1. Clique em **Configurações Gerais** sob configuração na lâmina principal. 
2. Clique no botão **'Ativar** recolha de dados' na parte superior da lâmina. 
 
   ![Ativar a recolha de dados](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Introduza o nome exato do inquilino para ativar o botão **Ativar.**
4. Clique no botão **'Ativar'** para conceder permissão à recolha de dados no serviço Connect Health. A alteração será aplicada em breve. 
5. Siga o processo de [instalação](how-to-connect-health-agent-install.md) para reinstalar o agente nos servidores a monitorizar e os serviços estarão presentes no portal.  


## <a name="next-steps"></a>Passos seguintes
* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
* [Ligação Azure AD e privacidade do utilizador](reference-connect-user-privacy.md)

