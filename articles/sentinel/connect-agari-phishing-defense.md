---
title: Ligue as suas soluções de Defesa e Proteção de Marcas Agari a Azure Sentinel | Microsoft Docs
description: Aprenda a usar o conector de Defesa e Proteção de Marcas de Phishing Agari para puxar os seus registos para o Azure Sentinel. Veja os dados do Agari nos livros, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3684b5a8069a6b683ae562c527af89814362a7f9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806991"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Ligue as suas soluções de Defesa e Proteção de Marcas Agari phishing ao Azure Sentinel

> [!IMPORTANT]
> O conector de defesa e proteção de marca Agari Phishing está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector de Defesa e Proteção de Marcas Agari Phishing permite-lhe ligar facilmente os registos das suas soluções de Proteção de Marcas e Defesa de Phishing ao Azure Sentinel, para que possa ver os dados em livros, questioná-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. As soluções da Agari integram-se com o Azure Sentinel utilizando funções Azure e REST API.

Além disso, os clientes de Proteção de Marcas e Resposta a Phishing podem aproveitar a partilha de Inteligência de Ameaça através da API de Gráfico de Segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

São necessárias as seguintes soluções de defesa e proteção de marcas de phishing da Agari ao Azure Sentinel:

- Leia e escreva permissões no espaço de trabalho Azure Sentinel.

- Leia permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

- Leia e escreva permissões sobre Funções Azure, para criar uma App de Função. [Saiba mais sobre as Funções Azure](../azure-functions/index.yml).

- Certifique-se de que tem o seu **ID do Cliente** Agari e **as teclas Secret** (idênticas em todas as soluções Agari). Consulte o [site de desenvolvedores Agari](https://developers.agari.com/agari-platform/docs/quick-start) para obter instruções.

## <a name="configure-and-connect-agari-solutions"></a>Configure e ligue as soluções Agari 

As soluções Agari podem integrar e exportar registos diretamente para o Azure Sentinel usando uma App de Função Azure.

> [!NOTE]
> Este conector utiliza funções Azure para ligar às soluções da Agari para puxar os seus troncos para o Azure Sentinel. Isto pode resultar em custos adicionais de ingestão de dados. Consulte a página [de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para obter mais detalhes.

1. **Colete as suas credenciais Agari API:** 

    Certifique-se de que tem o seu **ID do Cliente** Agari e **as chaves Secretas.** As instruções podem ser encontradas no [site de desenvolvedores Agari](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **(Opcional) Ativar a API do Gráfico de Segurança:** 

    A App de Função Agari permite-lhe partilhar informações de ameaça com O Azure Sentinel através da API do Gráfico de Segurança. Para utilizar esta funcionalidade, terá de ativar o [conector de Plataformas de Inteligência de Ameaças sentinelas](connect-threat-intelligence.md) e também [registar uma aplicação](/graph/auth-register-app-v2) no Azure Ative Directory.

    Este processo irá dar-lhe três peças de informação para utilização ao implementar a App de Função abaixo: o ID do **inquilino gráfico,** o **ID** do cliente gráfico e o segredo do **cliente gráfico.**

1. **Implementar o conector e a app de função Azure associada:** 

    1. No portal Azure Sentinel, selecione **Conectores de Dados**. Selecione **Agari Phishing Defense and Brand Protection (Preview)** e, em seguida, **Abra a página do conector**.

    1. Em **Configuração**, copie o **ID** do espaço de trabalho Azure Sentinel e **a chave primária** e colá-los de lado.

    1. Selecione **Implementar para Azure**. (Pode ter de rolar para baixo para encontrar o botão.)

    1. Aparecerá o ecrã **de implementação personalizado.**

        - Insira o seu **ID do Cliente** Agari e **o Segredo do Cliente** (chaves secretas)

        - Introduza o **ID** do espaço de trabalho Azure Sentinel e **a chave workspace** (chave primária) que copiou e colocou de lado.

        - Selecione **True** or **False** para as soluções Agari para as quais tem subscrições ativas.

        - Se criou uma Aplicação Azure para partilhar IoCs com O Azure Sentinel utilizando a API do Gráfico de Segurança, selecione **True** for **Enable Security Graph Sharing** e insira o ID do inquilino **gráfico,** o **ID** do cliente gráfico e o segredo do **cliente gráfico.**

    1. Selecione **Rever + criar**. Quando a validação estiver concluída, clique em **Criar**.

1. **Atribua as permissões necessárias à sua App de Função:**

    O conector Agari utiliza uma variável ambiental para armazenar os sinais de tempo de acesso ao registo. Para que o pedido escreva para esta variável, as permissões devem ser atribuídas à identidade atribuída ao sistema.

    1. No portal Azure, navegue para **a App de Função.**

    1. Na lâmina **'App' de função,** selecione a sua App de Função a partir da lista e, em seguida, selecione **Identidade** em **Definições** no menu de navegação da App de Função.

    1. No separador **Designado sistema,** desa um **conjunto de estados** para **On**. 

    1. Selecione **Guardar** e aparecerá um botão **de atribuições de funções Azure.** Clica nele.

    1. No ecrã de **atribuições de funções Azure,** selecione **Adicionar a atribuição de funções**. Definir **Âmbito** para **Subscrição**, selecione a sua subscrição a partir do drop-down de **subscrição** e desaprote **a função** para **o Proprietário de Dados de Configuração de Aplicação.** 

    1. Selecione **Guardar**.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos** em *CustomLogs,* nas seguintes tabelas: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Para consultar os dados das soluções Agari, introduza um dos nomes de tabela acima na janela de consulta.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar as soluções de Defesa e Proteção de Marcas Agari phishing ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
