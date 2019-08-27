---
title: Integração na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019011"
---
# <a name="on-board-azure-sentinel-preview"></a>Visualização do Azure Sentinel integrada

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neste início rápido, você aprenderá como integrar o Azure Sentinel. 

Para o Azure Sentinel integrado, primeiro você precisa habilitar o Azure Sentinel e, em seguida, conectar suas fontes de dados. O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecimento de integração em tempo real, incluindo soluções de proteção contra ameaças da Microsoft, Microsoft 365 fontes, incluindo o Office 365, o Azure AD, o Azure ATP e o Microsoft Cloud App Security e muito mais. Além disso, há conectores internos para o ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato de evento comum, syslog ou REST-API para conectar suas fontes de dados com o Azure Sentinel.  

Depois de conectar suas fontes de dados, escolha em uma galeria de painéis criados de especialistas que insights de superfície com base em seus dados. Esses painéis podem ser facilmente personalizados para suas necessidades.


## <a name="global-prerequisites"></a>Pré-requisitos globais

- Assinatura ativa do Azure, se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Espaço de trabalho Log Analytics. Saiba como [criar um espaço de trabalho log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Para habilitar o Azure Sentinel, você precisa de permissões de colaborador para a assinatura na qual reside o espaço de trabalho do Azure Sentinel. 
- Para usar o Azure Sentinel, você precisa de permissões de colaborador ou leitor no grupo de recursos ao qual o espaço de trabalho pertence
- Permissões adicionais podem ser necessárias para conectar fontes de dados específicas
 
## Habilitar o Azure Sentinel<a name="enable"></a>

1. Vá para a portal do Azure.
2. Verifique se a assinatura na qual o Azure Sentinel foi criado está selecionada. 
3. Pesquise pelo Azure Sentinel. 
   ![procurando](./media/quickstart-onboard/search-product.png)

1. Clique em **+ Adicionar**.
1. Selecione o espaço de trabalho que você deseja usar ou crie um novo. Você pode executar o Azure Sentinel em mais de um espaço de trabalho, mas os dados são isolados em um único espaço de trabalho.

   ![procurar](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os espaços de trabalho padrão criados pela central de segurança do Azure não serão exibidos na lista; Você não pode instalar o Azure Sentinel neles.
   > - O Azure Sentinel pode ser executado em espaços de trabalho implantados em qualquer uma das seguintes regiões: Leste da Austrália, sudeste da Austrália, Canadá central, Índia central, leste dos EUA, leste dos Estados Unidos 2 EUAP (canário), leste do Japão, sudeste da Ásia, Sul do Reino Unido, Europa Ocidental, oeste dos EUA 2, Oeste EUA Central, França central, Coreia central, Europa Setentrional, leste dos EUA 2, Ásia Oriental, oeste dos EUA, EUA Central, EUA Central do Sul.


6. Clique em **Adicionar Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ligar a origens de dados

O Azure Sentinel cria a conexão com serviços e aplicativos conectando-se ao serviço e encaminhando os eventos e logs para o Azure Sentinel. Para computadores e máquinas virtuais, você pode instalar o agente do Azure Sentinel que coleta os logs e os encaminha para o Azure Sentinel. Para firewalls e proxies, o Azure Sentinel utiliza um servidor syslog Linux. O agente é instalado nele e do qual o agente coleta os arquivos de log e os encaminha para o Azure Sentinel. 
 
1. Clique em **coleta de dados**.
2. Há um bloco para cada fonte de dados que você pode conectar.<br>
Por exemplo, clique em **Azure Active Directory**. Se você conectar essa fonte de dados, transmitirá todos os logs do Azure AD para o Azure Sentinel. Você pode selecionar o tipo de logs de sua WAN para obter logs de entrada e/ou logs de auditoria. <br>
Na parte inferior, o Azure Sentinel fornece recomendações para quais painéis você deve instalar para cada conector, para que você possa obter informações interessantes em seus dados imediatamente. <br> Siga as instruções de instalação ou [consulte o guia de conexão relevante](connect-data-sources.md) para obter mais informações. Para obter informações sobre conectores de dados, consulte [conectar serviços da Microsoft](connect-data-sources.md).

Depois que as fontes de dados estiverem conectadas, seus dados começarão a transmitir para o Azure Sentinel e estarão prontos para começar a trabalhar com o. Você pode exibir os logs nos [painéis internos](quickstart-get-visibility.md) e começar a criar consultas no log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a conectar fontes de dados ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- Transmita dados de [dispositivos de formato de erro comuns](connect-common-event-format.md) para o Azure Sentinel.
