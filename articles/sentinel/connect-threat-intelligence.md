---
title: Recolher dados de inteligência de ameaças na pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba mais sobre como ligar a dados de inteligência de ameaças ao Azure sentinela.
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6ac93b99b1e4eb8c0b42992c813cb491834b1693
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993273"
---
# <a name="collect-data-from-threat-intelligence-providers"></a>Recolher dados de fornecedores de inteligência de ameaças 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois de transmitir os dados em sentinela do Azure, pode enriquecê-lo com as informações sobre ameaças feed que utilizar na sua organização. 

Para ativar cruzada verificar os alertas e as regras de inteligência de ameaças verdadeiro, por exemplo, se receber um alerta de um endereço IP específico, a integração de fornecedor de inteligência de ameaças poderá informá-lo a se esse endereço IP foi recentemente encontrado por serem mal-intencionados , Permite a integração com o azure Sentinel [fornecedores de inteligência de ameaças](https://aka.ms/graphsecuritytips). 

Pode transmitir em fluxo registos de fornecedores de inteligência de ameaças em sentinela do Azure com um único clique. Esta ligação permite-lhe incorporar indicadores que contém vários tipos de observables, como o endereço IP, domínio, URL e hash de ficheiro para pesquisar e criar regras no Azure sentinela de alertas.  

## <a name="prerequisites"></a>Pré-requisitos  

- Utilizador com permissões de administrador de segurança ou de administrador global 

- Aplicação de inteligência de ameaças integrada com o Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Ligar a informações sobre ameaças 

1. Se já estiver a utilizar um fornecedor de inteligência de ameaças, certifique-se de que navegar para a aplicação de sugestão e conceder permissão para enviar indicadores para a Microsoft e especificar o serviço como sentinela do Azure.  

2. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **informações sobre ameaças** mosaico.

3. Clique em **Ligar**. 

4. Para utilizar o esquema relevante no Log Analytics para feeds de inteligência de ameaças, procure **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a ligar o seu fornecedor de informações sobre ameaças a sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes.

- Para começar a utilizar com o Azure sentinela, terá de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [carregar os dados para o Azure sentinela](quickstart-onboard.md), e [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
