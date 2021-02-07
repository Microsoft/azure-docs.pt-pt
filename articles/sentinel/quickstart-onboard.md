---
title: 'Quickstart: A bordo em Azure Sentinel'
description: Neste quickstart, aprenda a embarcar a Azure Sentinel, primeiro permitindo-o e, em seguida, conectando fontes de dados.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 7cb8cb4d99ca8a63195eba21d82814fcd7820f5c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807772"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Quickstart: A bordo Azure Sentinel

Neste arranque rápido, aprenda a embarcar Azure Sentinel. 

Para embarcar no Azure Sentinel, primeiro tem de ativar o Azure Sentinel e, em seguida, ligar as suas fontes de dados. O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e fornecendo integração em tempo real, incluindo soluções Microsoft 365 Defender (anteriormente Microsoft Threat Protection), Microsoft 365 sources (incluindo Office 365), Azure AD, Microsoft Defender for Identity (anteriormente Azure ATP), Microsoft Cloud App Security, alertas Azure Defender do Azure Security Center, e muito mais. Além disso, existem conectores incorporados no ecossistema de segurança mais amplo para soluções não Microsoft. Também pode utilizar o Formato Comum de Eventos (CEF), Syslog ou REST-API para ligar as suas fontes de dados ao Azure Sentinel. 

Depois de ligar as suas fontes de dados, escolha entre uma galeria de livros criados por especialistas que superem insights com base nos seus dados. Estes livros de trabalho podem ser facilmente personalizados às suas necessidades.

>[!IMPORTANT] 
> Para obter informações sobre as acusações incorridos ao utilizar o Azure Sentinel, consulte [os preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Pré-requisitos globais

- Subscrição Ative Azure, se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Log Analytics espaço de trabalho. Saiba como [criar um espaço de trabalho Log Analytics.](../azure-monitor/learn/quick-create-workspace.md) Para obter mais informações sobre os espaços de trabalho do Log Analytics, consulte a conceção da sua implementação de [Registos de Monitores Azure.](../azure-monitor/platform/design-logs-deployment.md)

- Para ativar o Azure Sentinel, precisa de permissões de colaboradores para a subscrição em que reside o espaço de trabalho Azure Sentinel. 
- Para utilizar o Azure Sentinel, precisa de permissões de colaborador ou leitor no grupo de recursos a que pertence o espaço de trabalho.
- Podem ser necessárias permissões adicionais para ligar fontes de dados específicas.
- O Azure Sentinel é um serviço pago. Para obter informações sobre preços consulte [Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Disponibilidade geográfica e residência de dados

- O Azure Sentinel pode funcionar em espaços de trabalho na maioria [das regiões de GA de Log Analytics,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) exceto as regiões da China e Alemanha (Soberana). Por vezes, as regiões new log analytics podem demorar algum tempo a embarcar no serviço Azure Sentinel. 

- Os dados gerados pelo Azure Sentinel, tais como incidentes, marcadores e regras de análise, podem conter alguns dados do cliente provenientes dos espaços de trabalho do Log Analytics do cliente. Estes dados gerados pelo Azure Sentinel são guardados na geografia listada na tabela seguinte, de acordo com a geografia em que se encontra o espaço de trabalho:

    | Geografia do espaço de trabalho | Geografia de dados gerada por Azure Sentinel |
    | --- | --- |
    | Estados Unidos da América<br>Índia<br>Brasil<br>África<br>Coreia | Estados Unidos da América |
    | Europa<br>França<br>Suíça | Europa |
    | Austrália | Austrália |
    | Reino Unido | Reino Unido |
    | Canadá | Canadá |
    | Japão | Japão |
    |

## <a name="enable-azure-sentinel"></a>Ativar sentinela Azure <a name="enable"></a>

1. Inicie sessão no Portal do Azure. Certifique-se de que a subscrição na qual o Azure Sentinel é criado está selecionada.

1. Procure e selecione **Azure Sentinel.**

   ![Pesquisa de serviços](./media/quickstart-onboard/search-product.png)

1. Selecione **Adicionar**.

1. Selecione o espaço de trabalho que pretende utilizar ou crie um novo. Você pode executar Azure Sentinel em mais de um espaço de trabalho, mas os dados são isolados para um único espaço de trabalho.

   ![Escolha uma área de trabalho](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os espaços de trabalho predefinidos criados pelo Azure Security Center não aparecerão na lista; não se pode instalar o Azure Sentinel neles.
   >

   >[!IMPORTANT]
   >
   > - Uma vez implantado num espaço de trabalho, o Azure Sentinel **não suporta atualmente** a deslocação desse espaço de trabalho para outros grupos de recursos ou subscrições. 
   >
   >   Se já moveu o espaço de trabalho, desative todas as regras ativas de acordo com **o Analytics** e reative-as após cinco minutos. No entanto, isto deve ser eficaz na maioria dos casos, para reiterar, não é apoiado e empreendido por sua conta e risco.

1. **Selecione Add Azure Sentinel**.

## <a name="connect-data-sources"></a>Ligar a origens de dados

O Azure Sentinel ingere dados de serviços e apps ligando-se ao serviço e reencaminhando os eventos e registos para a Azure Sentinel. Para máquinas físicas e virtuais, pode instalar o agente Log Analytics que recolhe os registos e os encaminha para o Azure Sentinel. Para Firewalls e proxies, o Azure Sentinel instala o agente Log Analytics num servidor Linux Syslog, a partir do qual o agente recolhe os ficheiros de registo e os encaminha para o Azure Sentinel. 
 
1. A partir do menu principal, selecione **Conectores de dados.** Isto abre a galeria de conectores de dados.

1. A galeria é uma lista de todas as fontes de dados que pode ligar. Selecione uma fonte de dados e, em seguida, o botão **de página do conector Open.**

1. A página do conector mostra instruções para configurar o conector e quaisquer instruções adicionais que possam ser necessárias.<br>
Por exemplo, se selecionar a fonte de dados do **Azure Ative Directory,** que permite transmitir registos do Azure AD para o Azure Sentinel, pode selecionar o tipo de registos que pretende obter - registos de inscrição e/ou registos de auditoria. <br> Siga as instruções de instalação ou [consulte o guia de ligação relevante](connect-data-sources.md) para obter mais informações. Para obter informações sobre os conectores de dados, consulte [os serviços da Connect Microsoft](connect-data-sources.md).

1. O separador **passos seguintes** na página do conector mostra livros incorporados relevantes, consultas de amostra e modelos de regras de análise que acompanham o conector de dados. Pode usar estes como-is ou modificá-los - de qualquer forma você pode obter imediatamente insights interessantes através dos seus dados. <br>

Depois de as suas fontes de dados estarem ligadas, os seus dados começam a fluir para o Azure Sentinel e estão prontos para começar a trabalhar. Pode ver os registos nos [livros incorporados](quickstart-get-visibility.md) e começar a criar consultas no Log Analytics para [investigar os dados.](tutorial-investigate-cases.md)

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu sobre o embarque e a ligação de fontes de dados ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- Transmita os dados dos [aparelhos common Event Format](connect-common-event-format.md) para o Azure Sentinel.
