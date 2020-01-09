---
title: 'Início rápido: integração no Azure Sentinel'
description: Saiba como coletar dados no Azure Sentinel seguindo este documento de início rápido.
services: sentinel
author: rkarlin
ms.author: rkarlin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: e8625b1d81db5b3ec90a177f426d05bd84bd10dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426957"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Início rápido: Sentinela do Azure integrado

Neste guia de início rápido, saiba como integrar o Azure Sentinel. 

Para o Azure Sentinel integrado, primeiro você precisa habilitar o Azure Sentinel e, em seguida, conectar suas fontes de dados. O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecimento de integração em tempo real, incluindo soluções de proteção contra ameaças da Microsoft, Microsoft 365 fontes, incluindo o Office 365, o Azure AD, o Azure ATP e o Microsoft Cloud App Security e muito mais. Além disso, há conectores internos para o ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar o formato de evento comum, syslog ou REST-API para conectar suas fontes de dados com o Azure Sentinel.  

Depois de conectar suas fontes de dados, escolha de uma galeria de pastas de trabalho criadas de especialistas que insights de superfície com base em seus dados. Essas pastas de trabalho podem ser facilmente personalizadas para suas necessidades.

>[!IMPORTANT] 
> Para obter informações sobre os encargos incorridos ao usar o Azure Sentinel, consulte [preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Pré-requisitos globais

- Assinatura ativa do Azure, se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Espaço de trabalho Log Analytics. Saiba como [criar um espaço de trabalho log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Para obter mais informações sobre espaços de trabalho Log Analytics, consulte [projetando sua implantação de logs de Azure monitor](../azure-monitor/platform/design-logs-deployment.md).

- Para habilitar o Azure Sentinel, você precisa de permissões de colaborador para a assinatura na qual reside o espaço de trabalho do Azure Sentinel. 
- Para usar o Azure Sentinel, você precisa de permissões de colaborador ou leitor no grupo de recursos ao qual o espaço de trabalho pertence.
- Permissões adicionais podem ser necessárias para conectar fontes de dados específicas.
- O Azure Sentinel é um serviço pago. Para obter informações sobre preços, consulte [sobre o Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## Habilitar o Azure Sentinel<a name="enable"></a>

1. Inicie sessão no Portal do Azure. Verifique se a assinatura na qual o Azure Sentinel foi criada está selecionada.

1. Pesquise e selecione **Azure Sentinel**.

   ![procurar](./media/quickstart-onboard/search-product.png)

1. Selecione **Adicionar**.

1. Selecione o espaço de trabalho que você deseja usar ou crie um novo. Você pode executar o Azure Sentinel em mais de um espaço de trabalho, mas os dados são isolados em um único espaço de trabalho.

   ![procurar](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os espaços de trabalho padrão criados pela central de segurança do Azure não serão exibidos na lista; Você não pode instalar o Azure Sentinel neles.
   > - O Azure Sentinel pode ser executado em espaços de trabalho em qualquer [região GA de log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) , exceto nas regiões China, Alemanha e Azure governamental. Os dados gerados pelo Azure Sentinel (como incidentes, indicadores e regras de alerta, que podem conter alguns dados de clientes provenientes desses espaços de trabalho) são salvos em Europa Ocidental (para espaços de trabalho localizados na Europa) ou leste dos EUA (para todos os espaços de trabalho baseados nos EUA, bem como qualquer outra região, exceto Europa).

1. Selecione **Adicionar Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ligar a origens de dados

O Azure Sentinel cria a conexão com serviços e aplicativos conectando-se ao serviço e encaminhando os eventos e logs para o Azure Sentinel. Para computadores e máquinas virtuais, você pode instalar o agente do Azure Sentinel que coleta os logs e os encaminha para o Azure Sentinel. Para firewalls e proxies, o Azure Sentinel utiliza um servidor syslog Linux. O agente é instalado nele e do qual o agente coleta os arquivos de log e os encaminha para o Azure Sentinel. 
 
1. Clique em **coleta de dados**.
2. Há um bloco para cada fonte de dados que você pode conectar.<br>
Por exemplo, clique em **Azure Active Directory**. Se você conectar essa fonte de dados, transmitirá todos os logs do Azure AD para o Azure Sentinel. Você pode selecionar o tipo de logs de sua WAN para obter logs de entrada e/ou logs de auditoria. <br>
Na parte inferior, o Azure Sentinel fornece recomendações para quais pastas de trabalho você deve instalar para cada conector, para que você possa obter informações interessantes em seus dados imediatamente. <br> Siga as instruções de instalação ou [consulte o guia de conexão relevante](connect-data-sources.md) para obter mais informações. Para obter informações sobre conectores de dados, consulte [conectar serviços da Microsoft](connect-data-sources.md).

Depois que as fontes de dados estiverem conectadas, seus dados começarão a transmitir para o Azure Sentinel e estarão prontos para começar a trabalhar com o. Você pode exibir os logs nos [painéis internos](quickstart-get-visibility.md) e começar a criar consultas no log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar fontes de dados ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- Transmita dados de [dispositivos de formato de erro comuns](connect-common-event-format.md) para o Azure Sentinel.
