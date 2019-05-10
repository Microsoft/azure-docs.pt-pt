---
title: Melhorar a sua conformidade a normas com o Centro de segurança do Azure | Documentos da Microsoft
description: 'Tutorial: Saiba como melhorar a sua conformidade a normas com o Centro de segurança do Azure.'
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2019
ms.author: monhaber
ms.openlocfilehash: 111a32983472d18c1435feaf2456e5b9416b5112
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442270"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhore a sua conformidade regulamentar
---

Centro de segurança do Azure ajuda a simplificar o processo para requisitos de conformidade a normas, utilizar o dashboard de conformidade a normas. No dashboard do Centro de segurança faculta informações aprofundadas a sua postura de conformidade com base nas avaliações contínuas de seu ambiente do Azure. As avaliações realizadas pelo centro de segurança analisam os fatores de risco no seu ambiente de cloud híbrida em conformidade com as melhores práticas de segurança. Essas avaliações são mapeadas para controlos de conformidade de um conjunto de suporte de padrões. No dashboard de conformidade a normas, terá uma visão clara do Estado de todas essas avaliações no seu ambiente no contexto de um determinado padrão ou regulamentação. Quando tomar decisões sobre as recomendações e reduzir os fatores de risco no seu ambiente, pode ver a sua postura de conformidade melhorar.

Neste tutorial, vai aprender a:

-   Avaliar a conformidade a normas com o dashboard de conformidade a normas

-   Melhorar a sua postura de conformidade, efetuando a ação nas recomendações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar as funcionalidades abrangidas neste tutorial, tem de ter o escalão de preço Standard do Centro de segurança. Pode experimentar o Centro de segurança Standard sem encargos.
Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar a conformidade a normas

Centro de segurança avalia continuamente a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. Essas avaliações são apresentadas como recomendações, que se concentrar em melhorar sua higiene de troca de segurança. No dashboard de conformidade a normas, pode ver um conjunto de normas de conformidade com os seus requisitos, onde os requisitos de suporte são mapeados para avaliações de segurança aplicáveis. Isto permite-lhe ver a sua postura de conformidade em relação ao padrão, com base no estado dessas avaliações.

A vista de dashboard de conformidade a normas pode ajudar a se concentrar sua atenção nas lacunas em conformidade com o padrão ou regulamento, que é importante para. Esta vista focada também permite-lhe monitorizar continuamente sua pontuação de conformidade ao longo do tempo na nuvem dinâmica e de ambientes híbridos.

>[!NOTE]
> Os padrões normativos atualmente suportados são: Itens de configuração do Azure, PCI DSS 3.2, ISO 27001 e SOC TSP. Padrões adicionais serão refletidas no dashboard à medida que desenvolve.
1.  Na segurança Center menu principal, em **política e conformidade** selecionar **conformidade a normas**. <br>
Na parte superior do ecrã, verá um dashboard com uma visão geral do seu estado de conformidade com o conjunto de normas de conformidade suportadas. Pode ver a sua pontuação de conformidade geral e o número de passagem versus avaliações associadas a cada padrão a falhar.

    ![confiança elevada de descrição de computador](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Selecione um separador para um padrão de conformidade que são relevante para si. Verá a lista de todos os controles para esse padrão. Para os controles aplicável, pode ver os detalhes de passagem e avaliações associadas a esse controle a falhar. Alguns controles estão desativadas. Esses controles não tem qualquer avaliações de centro de segurança associadas a eles. Precisa para analisar os requisitos para estas e avaliá-los no seu ambiente por conta própria. Alguns deles podem ser relacionados com o processo e não técnico.

    ![separador de conformidade](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Selecione o **todos os** separador para ver uma vista de todas as recomendações do Centro de segurança relevantes e seus padrões associados. Esta vista pode ser útil para identificar todos os padrões diferentes afetados por uma recomendação específica. <br> Potencialmente, pode utilizar esta vista para dar prioridade às recomendações que tem de resolver. Por exemplo, se vir que a recomendação **ativar a MFA para contas com permissões de proprietário na sua subscrição** está a falhar em vários recursos e está associado a vários padrões, em seguida, resolver esta recomendação irá ter um impacto elevado na sua pontuação de conformidade geral.

    ![impacto de pontuação de conformidade](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Para gerar e transferir um relatório PDF que resumem o status de conformidade para um padrão específico, clique em **transferir relatório**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliações de centro de segurança e é organizado de acordo com os controles esse padrão específico. O relatório pode ser partilhado com os intervenientes relevantes e pode servir para providenciar evidências para auditores internos e externos.

    ![transferir](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Melhorar a sua postura de conformidade

Tendo em conta as informações no dashboard de conformidade a normas, pode melhorar a sua postura de conformidade ao como resolver recomendações diretamente no dashboard.

1.  Clique em qualquer um das avaliações de falha que são apresentados no dashboard para ver os detalhes para essa recomendação. Cada recomendação inclui um conjunto de passos de remediação que deverão ser seguidas para resolver o problema.

2.  Pode selecionar um recurso específico para ver mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, no **padrão de itens de configuração do Azure** separador, pode clicar na recomendação **requer transferência segura para a conta de armazenamento**.

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. À medida que clicar para as informações de recomendação e selecione um recurso de mau estado de funcionamento, isso leva diretamente para a experiência de ativação **armazenamento transferência segura** no portal do Azure.<br>Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Depois de tomar providências para resolver recomendações, irá ver o impacto no relatório de dashboard de conformidade porque melhora a sua pontuação de conformidade.

    > [!NOTE]
    > Avaliações são executadas aproximadamente a cada 12 horas, verá o impacto nos seus dados de conformidade apenas após a execução de avaliações.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre a utilização do dashboard de conformidade a normas do Centro de segurança para:

-   Ver e monitorizar a sua postura de conformidade em relação às normas e regulamentos que são importantes para si.

-   Melhore o seu estado de conformidade, como resolver recomendações relevantes e assistir a melhorar a pontuação de conformidade.

O dashboard de conformidade a normas pode simplificar substancialmente o processo de conformidade e reduzir significativamente o tempo necessário para a coleta de evidências de conformidade para o seu ambiente do Azure e híbrido.

Para saber mais sobre o Centro de segurança, veja:

-   [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.

-   [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md)– Saiba como utilizar as recomendações no Centro de segurança do Azure para o ajudar a proteger os seus recursos do Azure.

-   [Melhorar a sua pontuação segura no Centro de segurança do Azure](security-center-secure-score.md)– Saiba como atribuir prioridades a vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança mais.

-   [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
