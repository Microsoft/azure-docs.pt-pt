---
title: Azure Defender para Armazenamento - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para armazenamento.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9b2855f82927b6f1707fd748f097dd357818ac4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341979"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure Defender para armazenamento

**O Azure Defender for Storage** deteta atividade potencialmente prejudicial nas suas contas de Armazenamento Azure. Os seus dados podem ser protegidos quer sejam armazenados como recipientes blob, partilhas de ficheiros ou lagos de dados.

Esta camada de proteção permite-lhe lidar com ameaças *sem* exigir que seja um perito em segurança e ajuda-o a gerir os seus sistemas de monitorização de segurança.


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|**O Azure Defender for Storage** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos:|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Ficheiros do Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (Armazenamento do Azure Data Lake Gen2)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Outro Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Que tipo de alertas o Azure Defender para armazenamento fornece?

Alertas de segurança são acionados quando há:

- **Atividade suspeita** - por exemplo, a conta de armazenamento foi acedida com sucesso a partir de um endereço IP que é conhecido como um nó de saída ativa de Tor
- **Comportamento anómalo** - por exemplo, alterações no padrão de acesso a uma conta de armazenamento
- **Potencial malware carregado** - análise de reputação de haxixe indica que um ficheiro carregado contém malware

Os alertas incluem detalhes do incidente que os desencadeou, bem como recomendações sobre como investigar e remediar ameaças.

> [!TIP]
> Pode simular alertas de armazenamento seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de haxixe para malware?

Para determinar se um ficheiro carregado é suspeito, o Azure Defender for Storage utiliza análises de reputação de haxixe suportadas pela [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não digitalizam os ficheiros carregados, antes examinam os registos de armazenamento e comparam os hashes de ficheiros recentemente carregados com os de vírus conhecidos, cavalos de Troia, spyware e ransomware. 

Quando se suspeita que um ficheiro contenha malware, o Security Center apresenta um alerta e pode opcionalmente enviar um e-mail ao proprietário do armazenamento para aprovação para apagar o ficheiro suspeito. Para configurar esta remoção automática de ficheiros que a análise de reputação de haxixe indica conter malware, implemente uma [automatização de fluxo de trabalho para desencadear alertas que contenham "Potencial malware carregado para uma conta de armazenamento".](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)

> [!NOTE]
> Para ativar as capacidades de proteção de ameaças do Security Center, deve ativar o Azure Defender na subscrição que contém as cargas de trabalho aplicáveis.
>
> Pode ativar **o Azure Defender para armazenamento** a nível de subscrição ou ao nível de recursos.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Azure Defender para armazenamento.

Para obter material relacionado, consulte os seguintes artigos: 

- Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança diferente, você pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).
- [Como permitir o Defensor Avançado para Armazenamento](../storage/common/azure-defender-storage-configure.md)
- [A lista de alertas do Azure Defender para armazenamento](alerts-reference.md#alerts-azurestorage)
- [Capacidades de inteligência de ameaça da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)