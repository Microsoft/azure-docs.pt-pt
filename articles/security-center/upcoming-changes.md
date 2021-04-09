---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773577"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

| Mudança planeada                                                                                                                                                        | Data estimada para alteração |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | março de 2021                |
| [Depreciação de 11 alertas do Azure Defender](#deprecation-of-11-azure-defender-alerts)                                                                                   | março de 2021                |
| [21 recomendações que se movem entre controlos de segurança](#21-recommendations-moving-between-security-controls)                                                           | abril de 2021                |
| [Duas outras recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | abril de 2021                |
| [As recomendações da AWS serão divulgadas para disponibilidade geral (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | abril de 2021                |
| [Melhorias à recomendação de classificação de dados sql](#enhancements-to-sql-data-classification-recommendation)                                                     | 2º trimestre de 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas 

**Data estimada para a alteração:** março de 2021

As duas recomendações que se seguem deverão ser depreadas em fevereiro de 2021:

- **As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema**. Isto pode resultar num ligeiro impacto na sua pontuação segura.
- **O agente de monitorização deve ser instalado nas suas máquinas**. Esta recomendação diz respeito apenas a máquinas no local e parte da sua lógica será transferida para outra recomendação, **as questões de saúde do agente Log Analytics devem ser resolvidas nas suas máquinas.** Isto pode resultar num ligeiro impacto na sua pontuação segura.

Recomendamos verificar as suas configurações contínuas de automação de exportação e fluxo de trabalho para ver se estas recomendações estão incluídas nas suas recomendações. Além disso, quaisquer dashboards ou outras ferramentas de monitorização que possam estar a usá-los devem ser atualizados em conformidade.

Saiba mais sobre estas recomendações na página de referência das [recomendações de segurança](recommendations-reference.md).

### <a name="deprecation-of-11-azure-defender-alerts"></a>Depreciação de 11 alertas do Azure Defender

**Data estimada para a alteração:** março de 2021

No próximo mês, os onze alertas do Azure Defender listados abaixo serão depreciados.

- Novos alertas irão substituir estes dois alertas e fornecer uma melhor cobertura:

    | AlertaType                | Nome de Alertadisplay                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - Kit de ferramentas MicroBurst "Get-AzureDomainInfo" executado |
    | ARM_MicroBurstRunbook    | PREVIEW - O conjunto de ferramentas MicroBurst "Get-AzurePasswords" executado  |
    |                          |                                                                          |

- Estes nove alertas dizem respeito a um conector de proteção de identidade do Diretório Ativo Azure que já foi depreciado:

    | AlertaType           | Nome de Alertadisplay              |
    |---------------------|-------------------------------|
    | Localização desconhecida  | Propriedades de inícios de sessão desconhecidos |
    | Anónimos      | Endereço IP anónimo          |
    | InfectuadoDeviceLogin | Endereço IP ligado a malware     |
    | ImpossibleTravel    | Viagem atípica               |
    | MaliciousIP         | Endereço IP malicioso          |
    | Fugas de informação   | Fuga de credenciais            |
    | PasswordSpray       | Spray de palavra-passe                |
    | Fugas de informação   | Inteligência de ameaça Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 recomendações que se movem entre controlos de segurança 

**Data estimada para a alteração:** abril de 2021

As seguintes recomendações estão a ser transferidas para um controlo de segurança diferente. Os controlos de segurança são grupos lógicos de recomendações de segurança relacionadas, e refletem as suas superfícies de ataque vulneráveis. Este movimento garante que cada uma destas recomendações está no controlo mais adequado para cumprir o seu objetivo. 

Saiba quais as recomendações em cada controlo de segurança nos controlos de segurança e nas suas recomendações.

|Recomendação |Alteração e impacto  |
|---------|---------|
|A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL<br>A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL<br>As vulnerabilidades nas bases de dados do SQL devem ser remediadas<br>As vulnerabilidades nas bases de dados SQL em VMs devem ser remediadas     |Movendo-se de vulnerabilidades remediate (no valor de 6 pontos)<br>para remediar as configurações de segurança (no valor de 4 pontos).<br>Dependendo do seu ambiente, estas recomendações terão um impacto reduzido na sua pontuação.|
|Deve haver mais de um proprietário atribuído à sua subscrição<br>As variáveis de conta de automação devem ser encriptadas<br>Dispositivos IoT - Processo auditado deixou de enviar eventos<br>Dispositivos IoT - Falha de validação da linha de base do sistema operativo<br>Dispositivos IoT - Atualização de suíte de cifra TLS necessária<br>Dispositivos IoT - Portas abertas no dispositivo<br>Dispositivos IoT - A política de firewall permissiva numa das cadeias foi encontrada<br>Dispositivos IoT - Regra de firewall permissiva na cadeia de entrada foi encontrada<br>Dispositivos IoT - Regra de firewall permissiva na cadeia de saída foi encontrada<br>Os registos de diagnóstico no IoT Hub devem ser ativados<br>Dispositivos IoT - Agente que envia mensagens subutilizadas<br>Dispositivos IoT - A política de filtro IP predefinido deve ser Deny<br>Dispositivos IoT - Regra do filtro IP grande gama DE IP<br>Dispositivos IoT - Intervalos e tamanho de mensagem do agente devem ser ajustados<br>Dispositivos IoT - Credenciais de autenticação idênticas<br>Dispositivos IoT - Processo auditado deixou de enviar eventos<br>Dispositivos IoT - A configuração de base do sistema operativo (OS) deve ser corrigida|Passar para **implementar as melhores práticas de segurança.**<br>Quando uma recomendação se desloca para o controlo de segurança de segurança implementar as melhores práticas, que não vale pontos, a recomendação já não afeta a sua pontuação segura.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas outras recomendações do controlo de segurança "Aplicar atualizações do sistema" que estão a ser depreciadas

**Data estimada para a alteração:** abril de 2021

As duas recomendações que se seguem estão a ser depreciadas:

- **A versão OS deve ser atualizada para as suas funções de serviço na nuvem** - Por padrão, o Azure atualiza periodicamente o seu SO convidado para a mais recente imagem suportada dentro da família OS que especificou na sua configuração de serviço (.cscfg), como o Windows Server 2016.
- **Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes** - As avaliações desta recomendação não são tão abrangentes como gostaríamos que fossem. A versão atual desta recomendação acabará por ser substituída por uma versão melhorada que esteja mais alinhada com as necessidades de segurança dos nossos clientes.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>As recomendações da AWS serão divulgadas para disponibilidade geral (GA)

**Data estimada para a alteração:** abril de 2021

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