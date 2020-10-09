---
title: Ligue a Zimperium Mobile Threat Defense a Azure Sentinel Microsoft Docs
description: Saiba como ligar a Zimperium Mobile Threat Defense ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587945"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Ligue a sua Defesa de Ameaça Móvel Zimperium a Azure Sentinel


> [!IMPORTANT]
> O conector de dados de defesa da ameaça móvel zimperium em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector de defesa da ameaça móvel Zimperium dá-lhe a capacidade de ligar o registo de ameaça zimperium com o Azure Sentinel para visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre o cenário de ameaça móvel da sua organização e melhora as suas capacidades de operação de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configure e ligue a Defesa da Ameaça Móvel zimperium

A Zimperium Mobile Threat Defense pode integrar e exportar registos diretamente para **o Azure Sentinel.**

1. No portal Azure Sentinel, clique nos conectores de dados e selecione **a Defesa de Ameaças Móveis zimperium**.
2. Selecione **Abrir a página do conector**.
3. Siga as instruções na página do conector **de ameaça móvel zimperium,** resumida da seguinte forma.
 1. Em zConsole, clique em **Gerir** a barra de navegação.
 2. Clique no separador **Integrações.**
 3. Clique no botão **Relatório de Ameaças** e, em seguida, no botão Adicionar **Integrações.**
 4. Crie a Integração selecionando o **Microsoft Azure Sentinel** a partir das integrações disponíveis e introduza iD do espaço de trabalho e chave primária para ligar ao Azure Sentinel.
 5. A opção de selecionar um nível de filtro para os dados de ameaça para empurrar para a Azure Sentinel também está disponível. 

4. Para mais informações, consulte o portal de apoio ao [cliente da Zimperium.](https://support.zimperium.com)


## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics em ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Para utilizar o esquema relevante no Log Analytics para a Defesa de Ameaças Móveis do Zimperium, procure ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Zimperium Mobile Threat Defense ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)

- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

Para saber mais sobre Zimperium, veja o seguinte:

- [Zimperium](https://zimperium.com)

- [Blog de Segurança Móvel Zimperium](https://blog.zimperium.com)

- [Portal de Apoio ao Cliente da Zimperium](https://support.zimperium.com)

