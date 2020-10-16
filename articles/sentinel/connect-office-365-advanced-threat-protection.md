---
title: Ligue o Microsoft Defender ao Office 365 (anteriormente Office 365 ATP) dados a Azure Sentinel ! Microsoft Docs
description: Ingerir dados do Microsoft Defender para o Office 365 no Azure Sentinel para ganhar visibilidade e construir cenários de resposta automatizados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 5ac72c9861e06773ffab87dcbf4e730562b798fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91346600"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Ligue alertas do Microsoft Defender para o Office 365 

> [!IMPORTANT]
>
> - **O Microsoft Defender for Office 365** era anteriormente conhecido como **Office 365 Advanced Threat Protection (ATP)**.
>
>     Pode ver o nome antigo ainda em uso no produto (incluindo o seu conector de dados em Azure Sentinel) por um período de tempo.
>
> - A ingestão de alertas do Microsoft Defender para o Office 365 encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[O Microsoft Defender para o Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) protege a sua organização contra ameaças de zero dias e outras ameaças avançadas colocadas por malware desconhecido em mensagens de e-mail, links de URL maliciosos e ferramentas de colaboração. Ao ingerir alertas do Microsoft Defender para o Office 365 no Azure Sentinel, poderá utilizar informações sobre ameaças baseadas em e-mails, partilha de ficheiros e URL nas suas operações de segurança. Em seguida, pode analisar mais abrangentemente os eventos de segurança em toda a sua organização e construir playbooks para uma resposta eficaz e imediata.

O conector importa os seguintes alertas:

- Foi detetado um clique de URL potencialmente malicioso 

- Mensagens de e-mail contendo malware removidas após a entrega

- Mensagens de e-mail contendo URLs phish removidas após a entrega 

- E-mail relatado pelo utilizador como malware ou phish 

- Padrões de envio de e-mail suspeitos detetados 

- Utilizador restrito de envio de e-mail 

Estes alertas podem ser vistos pelos clientes do Office no **Centro de Segurança e Conformidade do Office.**

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho Azure Sentinel quando ativar o conector.

- Você deve ser um administrador global ou um administrador de segurança no inquilino do espaço de trabalho Azure Sentinel.

- Você deve ter uma licença válida para [office 365 ATP Plan 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (incluído com as licenças Office 365 E5, Office 365 A5 e Microsoft 365 E5, e disponível para compra separadamente). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Ligue ao Microsoft Defender para o Office 365

Se o Microsoft Defender for Office 365 for implementado, e se as políticas tiverem sido configuradas, os alertas podem ser facilmente ingeridos no Azure Sentinel.

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Selecione **o Microsoft Defender para o Office 365** (ainda pode ser chamado de *Proteção avançada de ameaças do Office 365)* na galeria de conectores e selecione **a página do conector Open**.

1. Na secção **Configuração,** clique **em 'Ligar'.** 

1. Na secção **Criar incidentes,** clique em **Ativar**.

1. Para utilizar o esquema relevante para consultar os alertas ATP do Office 365, procure o **SecurityAlert** e especifique o **nome provedor** como **OATP**.

1. Selecione o separador **etapas seguintes** para ver e utilizar as amostras de consulta e os modelos de regras de análise agregados com o conector Microsoft Defender para o Office 365.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Microsoft Defender ao Office 365 ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
