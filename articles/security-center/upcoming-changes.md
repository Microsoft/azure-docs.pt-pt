---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751004"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

| Mudança planeada                                                                                                                                                        | Data estimada para alteração |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 recomendações que se movem entre controlos de segurança](#21-recommendations-moving-between-security-controls)                                                           | abril de 2021                |
| [Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | abril de 2021                |
| [A implementação do legado da ISO 27001 está a ser substituída pela nova ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | junho de 2021                 |
| [As recomendações da AWS serão divulgadas para disponibilidade geral (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **agosto de** 2021           |
| [Melhorias à recomendação de classificação de dados sql](#enhancements-to-sql-data-classification-recommendation)                                                     | 2º trimestre de 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 recomendações que se movem entre controlos de segurança 

**Data estimada para a alteração:** abril de 2021

As seguintes recomendações estão a ser transferidas para um controlo de segurança diferente. Os controlos de segurança são grupos lógicos de recomendações de segurança relacionadas, e refletem as suas superfícies de ataque vulneráveis. Este movimento garante que cada uma destas recomendações está no controlo mais adequado para cumprir o seu objetivo. 

Saiba quais as recomendações em cada controlo de segurança nos controlos de segurança e nas suas recomendações.

|Recomendação |Alteração e impacto  |
|---------|---------|
|A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL<br>A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL<br>As vulnerabilidades nas bases de dados do SQL devem ser remediadas<br>As vulnerabilidades nas bases de dados SQL em VMs devem ser remediadas     |Movendo-se de vulnerabilidades remediate (no valor de 6 pontos)<br>para remediar as configurações de segurança (no valor de 4 pontos).<br>Dependendo do seu ambiente, estas recomendações terão um impacto reduzido na sua pontuação.|
|Deve haver mais de um proprietário atribuído à sua subscrição<br>As variáveis de conta de automação devem ser encriptadas<br>Dispositivos IoT - Processo auditado deixou de enviar eventos<br>Dispositivos IoT - Falha de validação da linha de base do sistema operativo<br>Dispositivos IoT - Atualização de suíte de cifra TLS necessária<br>Dispositivos IoT - Portas abertas no dispositivo<br>Dispositivos IoT - A política de firewall permissiva numa das cadeias foi encontrada<br>Dispositivos IoT - Regra de firewall permissiva na cadeia de entrada foi encontrada<br>Dispositivos IoT - Regra de firewall permissiva na cadeia de saída foi encontrada<br>Os registos de diagnóstico no IoT Hub devem ser ativados<br>Dispositivos IoT - Agente que envia mensagens subutilizadas<br>Dispositivos IoT - A política de filtro IP predefinido deve ser Deny<br>Dispositivos IoT - Regra do filtro IP grande gama DE IP<br>Dispositivos IoT - Intervalos e tamanho de mensagem do agente devem ser ajustados<br>Dispositivos IoT - Credenciais de autenticação idênticas<br>Dispositivos IoT - Processo auditado deixou de enviar eventos<br>Dispositivos IoT - A configuração de base do sistema operativo (OS) deve ser corrigida|Passar para **implementar as melhores práticas de segurança.**<br>Quando uma recomendação se desloca para o controlo de segurança de segurança implementar as melhores práticas, que não vale pontos, a recomendação já não afeta a sua pontuação segura.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas

**Data estimada para a alteração:** abril de 2021

As duas recomendações que se seguem estão a ser depreciadas:

- **A versão OS deve ser atualizada para as suas funções de serviço na nuvem** - Por padrão, o Azure atualiza periodicamente o seu SO convidado para a mais recente imagem suportada dentro da família OS que especificou na sua configuração de serviço (.cscfg), como o Windows Server 2016.
- **Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes** - As avaliações desta recomendação não são tão abrangentes como gostaríamos que fossem. A versão atual desta recomendação acabará por ser substituída por uma versão melhorada que esteja mais alinhada com as necessidades de segurança dos nossos clientes.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>A implementação do legado da ISO 27001 está a ser substituída pela nova ISO 27001:2013

A implementação do legado da ISO 27001 será removida do painel de conformidade regulamentar do Centro de Segurança. Se estiver a acompanhar o cumprimento do ISO 27001 com o Security Center, a bordo a nova norma ISO 27001:2013 para todos os grupos de gestão ou subscrições relevantes, e o legado atual ISO 27001 será em breve removido do painel de instrumentos.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Painel de conformidade regulamentar do Centro de Segurança mostra a mensagem sobre a remoção do legado da IMPLEMENTAÇÃO ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>As recomendações da AWS serão divulgadas para disponibilidade geral (GA)

**Data estimada para a alteração:** agosto de 2021

O Azure Security Center protege cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

As recomendações provenientes do AWS Security Hub têm estado em pré-visualização desde que os conectores de nuvem foram introduzidos. As recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura, mas devem continuar a ser remediadas sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Com esta mudança, dois conjuntos de recomendações da AWS passarão para GA:

- [Controlos PCI DSS do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controlos de benchmark da CIS AWS Foundations do Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando estes são GA e as avaliações são executadas nos seus recursos AWS, os resultados impactarão a sua pontuação segura combinada para todos os seus recursos multi e híbridos em nuvem.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Melhorias à recomendação de classificação de dados sql

**Data estimada para a alteração:** 2º trimestre de 2021

A recomendação **Os dados sensíveis nas suas bases de dados SQL devem ser classificados** no controlo de segurança de classificação de **dados Apply** será substituído por uma nova versão mais alinhada com a estratégia de classificação de dados da Microsoft. Como resultado, o ID da recomendação também mudará (atualmente, é b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)