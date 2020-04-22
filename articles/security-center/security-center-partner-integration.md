---
title: Integrar soluções de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758084"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. As vantagens incluem:

- **Implementação simplificada**: o Centro de Segurança oferece aprovisionamento integrado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidade, o Security Center pode fornecer o agente nas suas máquinas virtuais. Para aparelhos de firewall, o Security Center pode cuidar de grande parte da configuração de rede necessária.
- **Deteções integradas**: Os eventos de segurança a partir de soluções parceiras são automaticamente recolhidos, agregados e apresentados como parte de alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Gestão e monitorização do estado de funcionamento unificadas**: os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções de parceiros rapidamente. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidade por [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e Microsoft Application Gateway Web application firewall.

> [!NOTE]
> O Security Center não instala o agente Log Analytics em aparelhos virtuais parceiros porque a maioria dos fornecedores de segurança proíbem agentes externos a funcionar nos seus aparelhos.

Para saber mais sobre a integração de ferramentas de digitalização de vulnerabilidades da Qualys, incluindo um scanner incorporado disponível para clientes de nível padrão, consulte: 

- [Scanner de vulnerabilidade integrado para máquinas virtuais.](built-in-vulnerability-assessment.md)
- [Implementando uma solução](partner-vulnerability-assessment.md)de verificação de vulnerabilidade seletiva de vulnerabilidade seletiva do parceiro.

O Security Center também oferece análise seleção de vulnerabilidades para o seu:

* Bases de dados SQL - veja Explore relatórios de avaliação de [vulnerabilidade sintetizadores no dashboard](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports) de avaliação de vulnerabilidades
* Imagens do Registo de Contentores Azure - ver integração do Registo de [Contentores Azure com o Centro de Segurança (Pré-visualização)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Também pode ligar outras fontes de dados de segurança, incluindo computadores que executam no local ou em outras nuvens.

[![Integração de soluções de parceiros](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

1. Do [portal Azure,](https://azure.microsoft.com/features/azure-portal/) **Open Security Center.**

1. A partir do menu do Security Center, selecione **soluções**de segurança .

Na página de Soluções de **Segurança,** pode ver a saúde de soluções de segurança integradas do Azure e executar tarefas básicas de gestão.

### <a name="connected-solutions"></a>Soluções ligadas

A secção **de soluções Connected** inclui soluções de segurança que estão atualmente ligadas ao Centro de Segurança. Mostra também o estado de saúde de cada solução.  

![Soluções ligadas](./media/security-center-partner-integration/connected-solutions.png)

O estatuto de uma solução parceira pode ser:

* **Saudável** (verde) - sem problemas de saúde.
* **Pouco saudável** (vermelho) - há um problema de saúde que requer atenção imediata.
* **Deixou de reportar** (laranja) - a solução deixou de reportar a sua saúde.
* **Não reportado** (cinzento) - a solução ainda não reportou nada e não há dados de saúde disponíveis. O estado de uma solução pode não ser reportado se foi conectado recentemente e ainda está em funcionamento.

> [!NOTE]
> Se os dados do estado de saúde não estiverem disponíveis, o Centro de Segurança mostra a data e hora do último evento recebido para indicar se a solução está ou não a reportar. Se não houver dados de saúde disponíveis e não forem recebidos alertas nos últimos 14 dias, o Centro de Segurança indica que a solução não é saudável ou não reporta.
>
>

Selecione **VISTA** para informações e opções adicionais, tais como:

   - Consola de **solução** - Abre a experiência de gestão para esta solução.
   - **Link VM** - Abre a página De Aplicações de Link. Aqui pode ligar recursos à solução de parceiros.
   - **Eliminar solução**
   - **Configurar**

   ![Detalhe de solução de parceiros](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Soluções detetadas

O Security Center descobre automaticamente soluções de segurança em funcionamento em Azure, mas não ligadas ao Centro de Segurança e exibe as soluções na secção **Soluções Descobertas.** Estas soluções incluem soluções Azure, como a [Azure AD Identity Protection,](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluções parceiras.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. Consulte [o Preço](security-center-pricing.md) para saber mais sobre os níveis de preços.
>

Selecione **CONNECT** sob uma solução para integrar com o Centro de Segurança e seja notificado de alertas de segurança.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para obter informações relacionadas, consulte os seguintes artigos:

* [Alertas e recomendações de segurança para exportação.](continuous-export.md) Aprenda a configurar uma integração com o Azure Sentinel, ou qualquer outro SIEM.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.