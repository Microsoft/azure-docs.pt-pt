---
title: Segurança do Azure Austrália explicada
description: As informações mais frequentes sobre as regiões australianas e atendem aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571435"
---
# <a name="azure-australia-security-explained"></a>Segurança do Azure Austrália explicada

Este artigo aborda algumas das perguntas e áreas de interesse comuns para agências governamentais da Austrália que investigam, projetam e implantam em Microsoft Azure Austrália.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP e documentos CCSL (lista de serviços de nuvem) certificados

O ACSC (centro de segurança da Austrália) da australiana fornece uma carta de certificação, um relatório de certificação e um guia do consumidor para o serviço quando ele é adicionado ao CCSL.

A Microsoft está atualmente listada no CCSL para Azure, Office 365 e Dynamics 365 CRM.

A Microsoft disponibiliza nossos documentos de certificação de auditoria, avaliação e ACSC para clientes e parceiros em uma página específica da Austrália do [portal de confiança do serviço da Microsoft](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>A disseminação que limita os marcadores (DLM) e a certificação protegida

O processo de ter sistemas, incluindo serviços de nuvem, aprovados para uso por organizações governamentais, é definido no [ISM (manual de segurança de informações)](https://acsc.gov.au/infosec/ism/) produzido e publicado pelo acsc (centro de segurança da Austrália). O ACSC (centro de segurança da Austrália) é a entidade dentro da ASD responsável por certificação de nuvem e segurança cibernético.

Há duas etapas para o processo de aprovação:

1. Avaliação de segurança (IRAP) – um processo em que os profissionais registrados avaliam sistemas, serviços e soluções em relação aos controles técnicos no ISM e avaliam se os controles foram implementados com eficiência. A avaliação também identifica quaisquer riscos específicos para a autoridade de aprovação a ser considerada antes de emitir uma aprovação para operar (ATO).

1. Aprovação para operar – o processo em que um executivo sênior de uma agência governamental formalmente recognises e aceita o risco residual de um sistema para as informações que ele processa, armazena e comunica.  Uma entrada para esse processo é a avaliação de segurança.

A avaliação dos serviços do Azure no nível protegido identifica que a implementação dos controles de segurança necessários para o armazenamento e o processamento dos dados protegidos e abaixo foi confirmada em vigor e está operando com eficiência.

## <a name="australian-data-classification-changes"></a>Alterações de classificação de dados australianos

Em 1º de outubro de 2018, o departamento do advogado General anunciou as alterações na PSPF (estrutura de política de segurança de proteção), especificamente um novo [sistema de informações confidencial e classificado](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx).

![Classificações PSPF revisadas](media/pspf-classifications.png)

Todas as agências australianas e organizações que operam sob o PSPF são afetadas por essas alterações. A principal alteração que afeta nossas certificações de IRAP/CCSL atuais é que a DLM (inscrições de limitação de disseminação) atual foi desativada. A marcação **oficial: Confidenciais** substitui os vários DLMs usados para a proteção de informações confidenciais. A alteração também introduziu três marcadores de gerenciamento de informações que podem ser aplicados a todas as informações oficiais em todos os níveis de sensibilidade e classificação. A classificação **protegida** permanece inalterada.

O termo não classificado é removido do novo sistema e o termo não oficial é aplicado a informações não governamentais, embora não exija uma marcação formal.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Escolhendo uma região do Azure para o meu oficial: Cargas de trabalho confidenciais e PROTEGIDAs

O Azure **oficial: Os** serviços confidenciais e **protegidos** são implantados em todas as quatro regiões de data center australianas (leste da Austrália, Austrália do sudeste, Austrália Central e Austrália Central 2); no entanto, o relatório de certificação emitido pela O ACSC recomenda que os dados **protegidos** sejam implantados nas regiões do Azure governamental no Camberra se um serviço estiver disponível lá. Informações mais detalhadas sobre os serviços do Azure certificados **protegidos** estão disponíveis na [página da Austrália no STP](https://aka.ms/au-irap).

>[!NOTE]
>A Microsoft recomenda que os dados do governo de todas as diferenciação e classificações sejam implantados nas regiões da Austrália Central e da Austrália Central 2, pois são projetados e operados especificamente para as necessidades do governo.

Mais informações sobre a natureza especial das regiões australianas do Azure estão disponíveis em [regiões Austrália Central do Azure](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Como a Microsoft separa dados classificados e oficiais

A Microsoft opera o Azure e o Office 365 na Austrália como se todos os dados forem confidenciais e/ou classificados, elevando nossos controles de segurança para essa barra de alto nível.

A infraestrutura que dá suporte ao Azure está potencialmente servindo dados de várias classificações.  Mas como nossa suposição é que os dados do cliente são classificados, os controles apropriados são estabelecidos como tal. A Microsoft adotou uma postura de segurança de linha de base para nossos serviços que estão em conformidade com os requisitos de PSPF para armazenar e processar informações classificadas protegidas.

Para garantir aos nossos clientes que um locatário no Azure não esteja em risco de outros locatários, a Microsoft implementa controles de defesa detalhada abrangentes.

Além dos recursos implementados na plataforma de Microsoft Azure, controles adicionais configuráveis do cliente, como criptografia com chaves gerenciadas pelo cliente, virtualização aninhada e acesso administrativo just-in-time, podem reduzir ainda mais o risco. Dentro das regiões da Austrália do Azure governamental no Camberra, um processo para a lista de permissões formal somente australiano & Nova Zelândia governo e organizações de infraestrutura crítica nacionais estão em vigor. Essa nuvem de comunidade fornece garantia adicional a organizações que são sensíveis a riscos de colocatários.

O relatório de certificação **protegido** Microsoft Azure confirma que esses controles são eficazes para o armazenamento e o processamento de dados confidenciais **protegidos** e seu isolamento.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Relevância de IRAP/CCSL para Estados governamentais e provedores de infraestrutura crítica

Muitos provedores de estado e fornecedores de infraestrutura crítica incorporam requisitos do governo federal em sua política de segurança e estrutura de garantia. Essas organizações também lidam **oficial**, **oficial: Confidencialidade** e alguma quantidade de dados confidenciais **protegidos** , seja de sua interação com o governo federal ou por seus próprios direitos.

O governo australiano está cada vez mais focalizando a política e a legislação sobre a proteção de dados não governamentais que afetam fundamentalmente a segurança e a prosperidade econômica da Austrália. Dessa forma, as regiões da Austrália do Azure e a certificação CCSL são relevantes para todos esses setores.

![Setores de infraestrutura crítica](media/nci-sectors.png)

As certificações da Microsoft demonstram que os serviços do Azure foram sujeitos a uma avaliação completa, rigorosa e formal das proteções de segurança em vigor e foram aprovados para lidar com esses dados altamente confidenciais.

## <a name="location-and-control-of-microsoft-data-centres"></a>Local e controle do Microsoft data centers

É um requisito obrigatório do governo e da infraestrutura crítica saber explicitamente o local e a propriedade do Data Center para os serviços de nuvem processarem seus dados.  A Microsoft é exclusiva como um provedor de nuvem de hiperescala no fornecimento de informações abrangentes sobre esses locais e a propriedade.

As regiões do Azure Austrália da Microsoft (Austrália Central e Austrália Central 2) são operadas dentro das instalações de datacenters CDC.  A propriedade de datacenters CDC é controlada por 48% da propriedade da Comunidade Superannuation Corporation, 48% de Propriedade do Infratil (uma troca de ações com base em NZ, Austrália dupla e Nova Zelândia listada de ativos de infraestrutura de longo prazo) e 4% de gerenciamento australiano.  

O gerenciamento de datacenters CDC tem garantias contratuais em vigor com o governo australiano restringindo a transferência futura de propriedade e controle. Essa transparência da cadeia de fornecimento e da propriedade por meio da parceria da Microsoft com datacenters CDC, está em linha com os princípios do [todo da estratégia de hospedagem governamental](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) e a definição de um datacentre **soberanas certificado**.

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>Os serviços do Azure que estão incluídos na certificação atual do CCSL

Em junho de 2017, os serviços acsc certificados 41 do Azure para armazenamento e processamento de dados **não classificados: Nível** da DLM. Em abril de 2018, 24 desses serviços eram certificados para dados confidenciais **protegidos** .

A disponibilidade de serviços ACSC certificados do Azure em nossas regiões do Azure na Austrália é a seguinte (as que estão em negrito são certificadas em **Protected**):

|Regiões do Azure da Austrália Central|Serviços não regionais e outras regiões|
|---|---|
|Gerenciamento de API, gateway de aplicativo, Serviços de Aplicativos, **automação, portal do Azure, backup, lote, serviços de nuvem**, Cosmos DB, hubs de eventos, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, log Analytics, multifator  **Autenticação**, cache Redis, **Gerenciador de recursos, barramento de serviço, Service Fabric, site Recovery, banco de dados SQL, armazenamento**, Gerenciador de tráfego, **máquinas virtuais, rede virtual, gateway de VPN**|**Azure Active Directory**, CDN, catálogo de dados, **exportação de importação, proteção de informações, Hub IOT**, Machine Learning, serviços de mídia, **hubs de notificação**, Power bi, centro de **segurança, Agendador**, pesquisa Stream Analytics|
|

A Microsoft publica a [visão geral de Microsoft Azure conformidade](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) que lista todos os serviços dentro do escopo para todos os processos de avaliação e de conformidade global, governamental, do setor e regional que o Azure passa, incluindo IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Serviço do Azure não listado ou avaliado em um nível inferior do que eu preciso

Serviços que não são certificados ou que foram certificados no **oficial: Confidenciais** , mas não **protegidos**, podem ser usados junto com ou como parte de uma solução que hospeda dados **protegidos** , desde que os serviços sejam:
1. Não está armazenando ou processando dados **protegidos** não criptografados ou
1. Você concluiu uma avaliação de risco e aprovou o serviço para armazenar dados **protegidos por** conta própria.

Se você quiser usar um serviço que não está incluído no CCSL para armazenar e processar dados **oficiais** , você pode, mas o ISM exige que você notifique o acsc de escrever que você está fazendo isso, antes de entrar ou renovar um contrato com um provedor de serviços de nuvem.

Qualquer serviço que esteja sendo usado por uma Agência para cargas de trabalho protegidas ainda precisa ser avaliado e aprovado em linha com os processos descritos no ISM e o processo de avaliações de IRAP gerenciado por agência na estratégia de [nuvem segura do DTA](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf).

![Processo de certificação da estratégia de nuvem segura DTA](media/certification.png)

A Microsoft avalia continuamente nossos serviços para garantir que a plataforma seja segura e adequada para uso do governo australiano, portanto, entre em contato com a Microsoft se você precisar de assistência com um serviço que não esteja atualmente no CCSL **protegido**.

Como a Microsoft tem uma variedade de serviços certificados no CCSL tanto para a **DLM** não classificada quanto para as classificações protegidas, o ISM exige que possamos realizar uma avaliação IRAP de nossos serviços pelo menos a cada dois anos. A Microsoft faz uma avaliação anual, que também é a oportunidade de incluir serviços adicionais para consideração.

## <a name="certified-protected-gateway-in-azure"></a>Gateway protegido certificado no Azure

A Microsoft não opera em um provedor de Internet seguro (SIG) certificado pelo governo devido a restrições no número de SIGs permitidas sob o programa de consolidação do gateway.  Mas os recursos esperados e necessários de um SIG podem ser configurados em Microsoft Azure.

Por meio da certificação **protegida** dos serviços do Azure, o acsc tem recomendações específicas para as agências para se conectar ao Azure e ao implementar a segmentação de rede entre domínios de segurança, por exemplo, entre **protegido** e o IP. Essas recomendações incluem o uso de grupos de segurança de rede, firewalls e redes virtuais privadas.  O ACSC recomenda o uso de um dispositivo de gateway virtual. Há vários dispositivos virtuais disponíveis no Azure que têm um equivalente físico na lista de produtos avaliados da ASD ou que foram avaliados em relação aos perfis de proteção de critérios comuns e estão listados no portal de critérios comuns. Esses produtos são mutuamente reconhecidos pelo ASD como um signatário para a CCRA (organização de reconhecimento de critérios comuns).

A Microsoft produziu diretrizes sobre a implementação de recursos baseados no Azure que fornecem as funções de segurança necessárias para proteger o limite entre diferentes domínios de segurança, que, quando combinados, formam o equivalente a um SIG certificado. Há uma série de parceiros que podem ajudar no design e na implementação desses recursos, bem como em uma série de soluções de parceiros disponíveis que fazem o mesmo.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Clearances de segurança e cidadania da equipe de suporte da Microsoft

A Microsoft opera nossos serviços globalmente com pessoal de segurança em tela e treinados.  A equipe que tem acesso físico unescorted a instalações em Sydney e Melbourne tem Clearances de segurança de linha de base governamental da Austrália. A equipe nas regiões da Austrália Central e da Austrália Central 2 tem o mínimo de habilitação 1 (NV1) Clearances ( conforme apropriado para dados secretos). Isso fornece garantia adicional aos clientes que o pessoal no data centers operacional Azure é altamente confiável.

A Microsoft tem uma política de acesso sem fim com acesso concedido por meio de um sistema de administração just-in-time e apenas suficiente com base em controles de acesso baseados em função. Na grande maioria dos casos, nossos administradores não exigem acesso nem privilégios aos dados do cliente para solucionar problemas e manter o serviço.  Altos níveis de automação e script de tarefas para execução remota negam a necessidade de acesso direto aos dados do cliente.

O departamento do advogado-General confirmou que as políticas e os procedimentos de segurança de pessoal da Microsoft no Azure estão consistentes com a intenção do acesso PSPF às disposições de informações no batalha-9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Armazenar ITAR (regulamentos internacionais de braços) ou dados EAR (regulamentos de administração de exportação)

Os controles técnicos do Azure que ajudam os clientes a atender suas obrigações por dados controlados por exportação são os mesmos globalmente no Azure. É importante que não haja "tiques da ITAR/EAR", pois não há uma estrutura formal de avaliação e certificação para dados de exportação controlada.

Para o Azure governamental e o governo dos EUA do Office 365 para defesa, colocamos medidas contratuais e processos adicionais em vigor para dar suporte a clientes sujeitos a controles de exportação. Essas cláusulas contratuais adicionais e o suporte Nacional garantido dos EUA e a administração das regiões do Azure não estão em vigor para a Austrália.

Isso não significa que o Azure na Austrália não possa ser usado para ITAR/EAR, mas você precisa entender claramente as restrições impostas por meio de sua licença de exportação e deve implementar proteções adicionais para atender a essas obrigações antes de usar o Azure para armazenar esses dados. Por exemplo, talvez seja necessário criar a nacionalidade como um atributo em Azure Active Directory, usar a proteção de informações do Azure para impor regras de criptografia nos dados e limitá-la apenas a nós e quaisquer outras nacionalidades incluídas na licença de exportação, Criptografe todos os dados locais antes de armazenar no Azure, usando a chave do cliente ou mantenha sua própria chave para dados do ITAR, e a lista continua......

Como o ITAR não é uma certificação formal, você precisa entender quais são as restrições e limitações associadas à licença de exportação e, em seguida, trabalhar com controles suficientes no Azure para atender a esses requisitos. Nesse caso, um dos problemas a considerar com atenção é o acesso por nossos engenheiros que talvez não sejam uma nacionalidade aprovada na licença de exportação.

## <a name="next-steps"></a>Passos Seguintes

Examine o artigo sobre o [Gateway de VPN do Azure](vpn-gateway.md) para configuração em conformidade com o ISM e implementação de conectividade VPN para o Azure Austrália
