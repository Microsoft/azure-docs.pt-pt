---
title: Azure Defender para Armazenamento - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para armazenamento.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42e8a1f4ff06f6ca6af4afd428008ca174823c5f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916426"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure Defender para Armazenamento


**O Azure Defender for Storage** é uma camada nativa de inteligência de segurança Azure que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar as suas contas de armazenamento. Utiliza as capacidades avançadas da IA de segurança e da [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) para fornecer alertas e recomendações de segurança contextuais.

Os alertas de segurança são acionados quando ocorrem anomalias de atividade. Estes alertas estão integrados no Azure Security Center, e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|**O Azure Defender for Storage** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos:|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Ficheiros do Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (Armazenamento do Azure Data Lake Gen2)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Outro Gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Quais são os benefícios do Azure Defender para armazenamento?

O Azure Defender for Storage fornece:

- **Segurança nativa do Azure** - Com uma ativação de 1 clique, o Defender for Storage protege os dados armazenados em Azure Blob, Azure Files e Data Lakes. Como serviço nativo da Azure, o Defender for Storage fornece segurança centralizada em todos os ativos de dados geridos pela Azure e está integrado com outros serviços de segurança Azure, como o Azure Sentinel.
- **Rico suíte de deteção** - Powered by Microsoft Threat Intelligence, as deteções no Defender for Storage cobrem as principais ameaças de armazenamento, tais como acesso anónimo, credenciais comprometidas, engenharia social, abuso de privilégios e conteúdo malicioso.
- **Resposta à escala** - As ferramentas de automatização do Centro de Segurança facilitam a prevenção e resposta às ameaças identificadas. Saiba mais nas [respostas do Automatismo aos gatilhos do Centro de Segurança](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Visão geral de alto nível das características do Azure Defender para armazenamento":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Que tipo de alertas o Azure Defender para armazenamento fornece?

Alertas de segurança são acionados quando há:

- **Padrões de acesso suspeitos** - como acesso bem sucedido a partir de um nó de saída tor ou de um IP considerado suspeito pela Microsoft Threat Intelligence
- **Atividades suspeitas** - tais como extração de dados anómalos ou alteração invulgar de permissões de acesso
- **Upload de conteúdo malicioso** - como potenciais ficheiros de malware (baseados na análise da reputação de haxixe) ou hospedagem de conteúdo de phishing

Os alertas incluem detalhes do incidente que os desencadeou, bem como recomendações sobre como investigar e remediar ameaças. Os alertas podem ser exportados para o Azure Sentinel ou para qualquer outro SIEM de terceiros ou qualquer outra ferramenta externa.

> [!TIP]
> É uma boa prática configurar o [Azure Defender para armazenamento](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) no nível de subscrição, mas também pode [configugá-lo em contas de armazenamento individuais.](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal)


## <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de haxixe para malware?

Para determinar se um ficheiro carregado é suspeito, o Azure Defender for Storage utiliza análises de reputação de haxixe suportadas pela [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não digitalizam os ficheiros carregados, antes examinam os registos de armazenamento e comparam os hashes de ficheiros recentemente carregados com os de vírus conhecidos, cavalos de Troia, spyware e ransomware. 

Quando se suspeita que um ficheiro contenha malware, o Security Center apresenta um alerta e pode opcionalmente enviar um e-mail ao proprietário do armazenamento para aprovação para apagar o ficheiro suspeito. Para configurar esta remoção automática de ficheiros que a análise de reputação de haxixe indica conter malware, implemente uma [automatização de fluxo de trabalho para desencadear alertas que contenham "Potencial malware carregado para uma conta de armazenamento".](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)

> [!NOTE]
> Para ativar as capacidades de proteção de ameaças do Security Center, deve ativar o Azure Defender na subscrição que contém as cargas de trabalho aplicáveis.
>
> Pode ativar **o Azure Defender para armazenamento** a nível de subscrição ou ao nível de recursos.



## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu sobre Azure Defender para armazenamento.

Para obter material relacionado, consulte os seguintes artigos: 

- Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança diferente, você pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).
- [Como permitir o Defensor Avançado para Armazenamento](../storage/common/azure-defender-storage-configure.md)
- [A lista de alertas do Azure Defender para armazenamento](alerts-reference.md#alerts-azurestorage)
- [Capacidades de inteligência de ameaça da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)