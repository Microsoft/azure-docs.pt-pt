---
title: Segurança do Azure Austrália explicada
description: As informações mais frequentes sobre as regiões australianas e atendem aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575456"
---
# <a name="azure-australia-security-explained"></a>Segurança do Azure Austrália explicada

Este artigo aborda algumas das perguntas e áreas de interesse comuns para agências governamentais da Austrália que investigam, projetam e implantam em Microsoft Azure Austrália.

## <a name="irap-and-certified-cloud-services-list-documents"></a>Documentos de lista de serviços de nuvem IRAP e certificados

O ACSC (centro de segurança australiano) da Austrália fornece uma carta de certificação, um relatório de certificação e um guia do consumidor para o serviço quando ele é adicionado à CCSL (Certified Cloud Services List).

A Microsoft está atualmente listada no CCSL para Azure, Office 365 e Dynamics 365 CRM.

A Microsoft disponibiliza nossos documentos de certificação de auditoria, avaliação e ACSC para clientes e parceiros em uma página específica da Austrália do [portal de confiança do serviço da Microsoft](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-and-protected-certification"></a>A disseminação que limita os marcadores e a certificação protegida

O processo de ter sistemas, incluindo serviços de nuvem, aprovados para uso por organizações governamentais, é definido no [ISM (manual de segurança de informações)](https://acsc.gov.au/infosec/ism/) que é produzido e publicado pelo acsc. O ACSC é a entidade dentro do Directorate de sinais australiano (ASD) que é responsável pela certificação de nuvem e pela segurança cibernético.

Há duas etapas para o processo de aprovação:

1. **Avaliação de segurança (IRAP)** : Um processo no qual os profissionais registrados avaliam sistemas, serviços e soluções em relação aos controles técnicos no ISM e avaliam se os controles foram implementados com eficiência. A avaliação também identifica quaisquer riscos específicos para a autoridade de aprovação a ser considerada antes de emitir uma aprovação para operar (ATO).

1. **Aprovação para operar**: O processo no qual um executivo sênior de uma agência governamental formalmente recognises e aceita o risco residual de um sistema para as informações que ele processa, armazena e comunica. Uma entrada para esse processo é a avaliação de segurança.

A avaliação dos serviços do Azure no nível protegido identifica que a implementação dos controles de segurança necessários para o armazenamento e o processamento dos dados protegidos e abaixo foi confirmada em vigor e está operando de forma eficaz.

## <a name="australian-data-classification-changes"></a>Alterações de classificação de dados australianos

Em 1º de outubro de 2018, o departamento do advogado General anunciou as alterações na PSPF (estrutura de política de segurança de proteção), especificamente um novo [sistema de informações confidencial e classificado](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Classificações PSPF revisadas](media/pspf-classifications.png)

Todas as agências australianas e organizações que operam sob o PSPF são afetadas por essas alterações. A principal alteração que afeta nossas certificações de IRAP/CCSL atuais é que as DLMs (inscrições de de disseminação) atuais foram desativadas. O oficial: A marcação confidencial substitui os vários DLMs usados para a proteção de informações confidenciais. A alteração também introduziu três marcadores de gerenciamento de informações que podem ser aplicados a todas as informações oficiais em todos os níveis de sensibilidade e classificação. A classificação protegida permanece inalterada.

O termo "não classificado" é removido do novo sistema e o termo "não oficial" é aplicado a informações não governamentais, embora não exija uma marcação formal.

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>Escolha uma região do Azure para o oficial: Cargas de trabalho confidenciais e PROTEGIDAs

O Azure oficial: Os serviços confidenciais e protegidos são implantados em todas as quatro regiões do centro de dados da Austrália: Leste da Austrália, sudeste da Austrália, Austrália Central e Austrália Central 2. O relatório de certificação emitido pelo ACSC recomenda que os dados protegidos sejam implantados nas regiões do Azure governamental no Camberra se um serviço estiver disponível lá. Para obter mais informações sobre os serviços do Azure certificados protegidos, consulte a [página da Austrália no portal de confiança do serviço](https://aka.ms/au-irap).

>[!NOTE]
>A Microsoft recomenda que os dados do governo de todas as diferenciação e classificações devem ser implantados nas regiões da Austrália Central e da Austrália Central 2, pois eles são projetados e operados especificamente para as necessidades do governo.

Para obter mais informações sobre a natureza especial das regiões australianas do Azure, consulte [regiões do Azure na Austrália Central](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Como a Microsoft separa dados classificados e oficiais

A Microsoft opera o Azure e o Office 365 na Austrália como se todos os dados forem confidenciais ou classificados, o que gera nossos controles de segurança para essa barra de alto nível.

A infraestrutura que dá suporte ao Azure potencialmente fornece dados de várias classificações. Como supomos que os dados do cliente sejam classificados, os controles apropriados estão em vigor. A Microsoft adotou uma postura de segurança de linha de base para nossos serviços que estão em conformidade com os requisitos de PSPF para armazenar e processar informações classificadas PROTEGIDAs.

Para garantir aos nossos clientes que um locatário no Azure não esteja em risco de outros locatários, a Microsoft implementa controles de defesa aprofundada abrangentes.

Além dos recursos implementados na plataforma de Microsoft Azure, controles adicionais configuráveis do cliente, como criptografia com chaves gerenciadas pelo cliente, virtualização aninhada e acesso administrativo just-in-time, podem reduzir ainda mais o risco. Nas regiões da Austrália do Azure governamental no Camberra, um processo para a lista de permissões formal apenas da Austrália e das organizações de infraestrutura crítica nacional e do governo da Nova Zelândia está em vigor. Essa nuvem de comunidade fornece garantia adicional a organizações que são sensíveis a riscos de colocatários.

O relatório de certificação protegido Microsoft Azure confirma que esses controles são eficazes para o armazenamento e o processamento de dados confidenciais protegidos e seu isolamento.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevância de IRAP/CCSL para Estados governamentais e provedores de infraestrutura crítica

Muitos provedores de estado e fornecedores de infraestrutura crítica incorporam requisitos do governo federal em sua política de segurança e estrutura de garantia. Essas organizações também lidam oficial, oficial: Confidenciais e alguma quantidade de dados confidenciais protegidos, seja de sua interação com o governo federal ou por si só.

O governo australiano está cada vez mais focalizando a política e a legislação sobre a proteção de dados não governamentais que afetam fundamentalmente a segurança e a prosperidade econômica da Austrália. Como tal, as regiões da Austrália do Azure e a certificação CCSL são relevantes para todos esses setores.

![Setores de infraestrutura crítica](media/nci-sectors.png)

As certificações da Microsoft demonstram que os serviços do Azure estavam sujeitos a uma avaliação completa, rigorosa e formal das proteções de segurança em vigor e foram aprovadas para lidar com esses dados altamente confidenciais.

## <a name="location-and-control-of-microsoft-data-centres"></a>Local e controle do Microsoft data centers

Trata-se de um requisito obrigatório de infraestrutura de governo e crítica para saber explicitamente o local e a propriedade do Data Center para os serviços de nuvem processarem seus dados. A Microsoft é exclusiva como um provedor de nuvem de hiperescala no fornecimento de informações abrangentes sobre esses locais e a propriedade.

As regiões do Azure Austrália da Microsoft (Austrália Central e Austrália Central 2) operam dentro das instalações de datacenters CDC. A propriedade de datacenters CDC é controlada pela Austrália com 48% de propriedade da Comunidade Superannuation Corporation, 48% de Propriedade do Infratil (uma Nova Zelândia baseada em Zelândia, uma Austrália dupla e uma nova condição de troca de estoque listada pela infraestrutura de longo prazo fundos de ativos) e 4% de gerenciamento australiano. 

O gerenciamento de datacenters CDC tem garantias contratuais em vigor com o governo australiano que restringir a transferência futura de propriedade e controle. Essa transparência da cadeia de fornecimento e da propriedade por meio da parceria da Microsoft com datacenters CDC está em linha com os princípios da [estratégia de Hospedagem de todo o governo](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) e a definição de um datacentre soberanas certificado.

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>Serviços do Azure incluídos na certificação atual do CCSL

Em junho de 2017, os serviços ACSC certificados 41 do Azure para armazenamento e processamento de dados não classificados: Nível da DLM. Em abril de 2018, 24 desses serviços eram certificados para dados confidenciais protegidos.

A disponibilidade de serviços do Azure certificados pelo ACSC em nossas regiões do Azure na Austrália são as seguintes (os serviços mostrados em negrito são certificados no nível protegido).

|Regiões do Azure na Austrália Central|Serviços não regionais e outras regiões|
|---|---|
|Gerenciamento de API, gateway de aplicativo, Serviços de Aplicativos, **automação**, **portal do Azure**, **backup**, **lote**, **serviços de nuvem**, Cosmos DB, Hubs de eventos, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Análise, **autenticação**multifator, cache Redis, **Gerenciador de recursos**, **barramento de serviço**, **Service Fabric**, **site Recovery**, **banco de dados SQL**, **armazenamento**, Gerenciador de tráfego, **virtual Máquinas**, **rede virtual**, **Gateway de VPN**|**Azure Active Directory**, CDN, catálogo de dados, **exportação de importação**, proteção de **informações**, **Hub IOT**, Machine Learning, serviços de mídia, **hubs de notificação**, Power bi, **Agendador**, centro de **segurança**, Pesquisar, Stream Analytics|
|

A Microsoft publica a [visão geral de Microsoft Azure conformidade](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) que lista todos os serviços dentro do escopo para todos os processos globais, governamentais, do setor e de conformidade e de avaliação regionais que o Azure passa, o que inclui IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>Serviço do Azure não listado ou avaliado em um nível inferior ao necessário

Serviços que não são certificados ou que foram certificados no oficial: Confidencial, mas não o nível protegido, pode ser usado junto com ou como parte de uma solução que hospeda dados protegidos, contanto que os serviços sejam:

- Não está armazenando ou processando dados protegidos não criptografados ou
- Você concluiu uma avaliação de risco e aprovou o serviço para armazenar dados protegidos por conta própria.

Você pode usar um serviço que não está incluído no CCSL para armazenar e processar dados oficiais, mas o ISM exige que você notifique o ACSC escrevendo que você está fazendo isso antes de entrar ou renovar um contrato com um provedor de serviços de nuvem.

Qualquer serviço usado por uma agência para cargas de trabalho PROTEGIDAs deve ser avaliado e aprovado em linha com os processos descritos no ISM e o processo de avaliações de IRAP gerenciado por agência na [estratégia de nuvem segura do DTA](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![Processo de certificação da estratégia de nuvem segura DTA](media/certification.png)

A Microsoft avalia continuamente nossos serviços para garantir que a plataforma seja segura e adequada para uso do governo australiano. Contate a Microsoft se você precisar de assistência com um serviço que não esteja atualmente em CCSL no nível protegido.

Como a Microsoft tem uma variedade de serviços certificados no CCSL tanto na DLM não classificada quanto em classificações PROTEGIDAs, o ISM exige que possamos realizar uma avaliação IRAP de nossos serviços pelo menos a cada dois anos. A Microsoft faz uma avaliação anual, que também é uma oportunidade de incluir serviços adicionais para consideração.

## <a name="certified-protected-gateway-in-azure"></a>Gateway protegido certificado no Azure

A Microsoft não opera um Secure Internet Gateway (SIG) certificado pelo governo devido às restrições no número de SIGs permitidas sob o programa de consolidação do gateway. Mas os recursos esperados e necessários de um SIG podem ser configurados em Microsoft Azure.

Por meio da certificação protegida dos serviços do Azure, o ACSC tem recomendações específicas para as agências para se conectar ao Azure e ao implementar a segmentação de rede entre domínios de segurança, por exemplo, entre o protegido e a Internet. Essas recomendações incluem o uso de grupos de segurança de rede, firewalls e redes virtuais privadas. O ACSC recomenda o uso de um dispositivo de gateway virtual. Há vários dispositivos virtuais disponíveis no Azure que têm um equivalente físico na lista de produtos avaliados da ASD ou que foram avaliados em relação aos perfis de proteção de critérios comuns e estão listados no portal de critérios comuns. Esses produtos são mutuamente reconhecidos pelo ASD como um signatário para a organização de reconhecimento de critérios comuns.

A Microsoft produziu diretrizes sobre a implementação de recursos baseados no Azure que fornecem as funções de segurança necessárias para proteger o limite entre diferentes domínios de segurança, que, quando combinados, formam o equivalente a um SIG certificado. Vários parceiros podem auxiliar no design e na implementação desses recursos, e há uma série de soluções de parceiros disponíveis que fazem o mesmo.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Clearances de segurança e cidadania da equipe de suporte da Microsoft

A Microsoft opera nossos serviços globalmente com pessoal de segurança em tela e treinados. A equipe que tem acesso físico unescorted a instalações em Sydney e Melbourne tem Clearances de segurança de linha de base governamental da Austrália. A equipe nas regiões da Austrália Central e da Austrália Central 2 tem o mínimo de habilitação 1 (NV1) Clearances (conforme apropriado para dados SECRETOs). Esses requisitos de reliberação fornecem garantia adicional aos clientes de que a equipe no data centers operacional Azure é altamente confiável.

A Microsoft tem uma política de acesso a zero, com acesso concedido por meio de um sistema de tempo just-in-time e apenas administração suficiente com base em controles de acesso baseados em função. Na grande maioria dos casos, nossos administradores não exigem acesso nem privilégios aos dados do cliente para solucionar problemas e manter o serviço. Altos níveis de automação e script de tarefas para execução remota negam a necessidade de acesso direto aos dados do cliente.

O departamento do advogado General confirmou que as políticas e os procedimentos de segurança de pessoal da Microsoft no Azure estão consistentes com a intenção do acesso PSPF às disposições de informações no batalha-9.

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Armazenar ITAR (regulamentos internacionais de braços) ou dados EAR (regulamentos de administração de exportação)

Os controles técnicos do Azure que ajudam os clientes a atender suas obrigações por dados controlados por exportação são os mesmos globalmente no Azure. O mais importante é que não há uma estrutura formal de avaliação e certificação para dados controlados por exportação.

Para o Azure governamental e o governo dos EUA do Office 365 para defesa, colocamos medidas contratuais e de processo adicionais em vigor para dar suporte a clientes sujeitos a controles de exportação. Essas cláusulas contratuais adicionais e o suporte Nacional garantido e a administração das regiões do Azure não estão em vigor para a Austrália.

Isso não significa que o Azure na Austrália não possa ser usado para ITAR/EAR, mas você precisa entender claramente as restrições impostas por meio de sua licença de exportação. Você também deve implementar proteções adicionais para atender a essas obrigações antes de usar o Azure para armazenar esses dados. Por exemplo, talvez seja necessário:

- Crie a nacionalidade como um atributo em Azure Active Directory.
- Use a proteção de informações do Azure para impor regras de criptografia nos dados e limitá-la somente para os EUA e quaisquer outras nacionalidades incluídas na licença de exportação.
- Criptografe todos os dados locais antes de armazená-los no Azure usando uma chave do cliente ou mantenha sua própria chave para dados do ITAR.

Como o ITAR não é uma certificação formal, você precisa entender quais são as restrições e limitações associadas à licença de exportação. Em seguida, você pode trabalhar se há controles suficientes no Azure para atender a esses requisitos. Nesse caso, um dos problemas a considerar com atenção é o acesso por nossos engenheiros que podem não ser uma nacionalidade aprovada na licença de exportação.

## <a name="next-steps"></a>Passos Seguintes

 Para configuração em conformidade com o ISM e implementação de conectividade VPN para o Azure Austrália, consulte [Gateway de VPN do Azure](vpn-gateway.md).
