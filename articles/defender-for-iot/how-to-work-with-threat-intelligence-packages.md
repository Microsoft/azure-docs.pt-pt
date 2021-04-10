---
title: Atualizar dados de inteligência de ameaças
description: O pacote de dados de inteligência de ameaça é fornecido com cada novo Defender para a versão IoT, ou se necessário entre lançamentos.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784055"
---
# <a name="threat-intelligence-research-and-packages"></a>Pesquisa e pacotes de inteligência de ameaça

As equipas de segurança da Microsoft realizam pesquisas de inteligência e vulnerabilidade de ameaças do ICS. Estas equipas incluem MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) e Section 52 (especialistas em domínio IoT/OT/ICS que rastreiam zero dias específicos do ICS, malware de engenharia inversa, campanhas e adversários).

As equipas fornecem deteção de segurança, análise e resposta à Microsoft:

- Infraestruturas e serviços em nuvem.
- Produtos e dispositivos tradicionais.
- Recursos corporativos internos.

As equipas de segurança beneficiam de:

- Proteção contra ameaças conhecidas e relevantes.
- Insights que ajudam a triagem e priorizam.
- Uma compreensão do contexto completo das ameaças antes de serem afetadas.
- Dados mais relevantes, precisos e accuíveis.

Esta inteligência adiciona informações contextuais para enriquecer a análise da plataforma da Microsoft e suporta os serviços geridos da empresa para a investigação de resposta a incidentes e violação. Os pacotes de inteligência de ameaça contêm assinaturas (incluindo assinaturas de malware), CVEs e outros conteúdos de segurança.

Você pode baixar pacotes a partir da página **'Atualizações'** no portal Azure Defender para IoT.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Faça o download de atualizações através do portal Azure Defender para IoT.":::

## <a name="update-threat-intelligence-data"></a>Atualizar dados de inteligência de ameaças

Os pacotes de inteligência de ameaça são fornecidos com cada nova atualização da versão Defender para IoT, ou se necessário entre lançamentos.

Os pacotes que descarrega do portal Defender para IoT podem ser enviados manualmente para sensores individuais. Se a consola de gestão no local gerir os seus sensores, pode transferir pacotes de inteligência de ameaças para a consola de gestão e empurrá-los para vários sensores simultaneamente.

Para atualizar um pacote num único sensor:

1. Aceda à página Azure Defender para **atualizações** IoT.

2. Descarregue e guarde o pacote **de Inteligência ameaça.**

3. Inscreva-se na consola do sensor.

4. No menu lateral, selecione **Definições do Sistema**.

5. Selecione **dados de inteligência de ameaça** e, em seguida, selecione **Update**.

6. Faça o upload do novo pacote.

Para atualizar um pacote em vários sensores simultaneamente:

1. Aceda à página Azure Defender para **atualizações** IoT.

2. Descarregue e guarde o pacote **de Inteligência ameaça.**

3. Inscreva-se na consola de gestão.

4. No menu lateral, selecione **Definições do Sistema**.

5. Na secção **de Configuração do Motor do Sensor,** selecione os sensores que devem receber os pacotes atualizados.  

6. Na secção **Select Threat Intelligence Data,** selecione o sinal mais **+** ().

7. Faça o upload do pacote.

## <a name="see-also"></a>Ver também

[Versões de atualização](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
