---
title: Troubleshoot Azure Synapse Studio (pré-visualização)
description: Troubleshoot Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431322"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio (pré-visualização) resolução de problemas

Este guia de resolução de problemas fornece instruções sobre que informações fornecer ao abrir um bilhete de suporte sobre problemas de conectividade da rede. Com a informação adequada, podemos resolver o problema mais rapidamente.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problema de conectividade do serviço sQL on-demand (pré-visualização)

### <a name="symptom-1"></a>Sintoma 1

A opção "SQL on-demand" está cinzenta na queda "Connect to".

![sintoma1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintoma 2

Executar a consulta com "SQL on-demand" dá-lhe a mensagem de erro "Falhade estabelecer a ligação ao servidor".

![sintoma2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

> [!NOTE] 
>    Os seguintes passos de resolução de problemas são para Chromium Edge e Chrome. Pode utilizar outros navegadores (como o FireFox) com os mesmos passos de resolução de problemas, mas a janela "Developer Tool" pode ter um layout diferente das imagens deste TSG. Se possível, NÃO utilize o Edge clássico para resolução de problemas, pois pode apresentar informações imprecisas em determinadas situações.

Abra o painel "Informações de Diagnóstico", selecione o botão "Download Diagnostic". Mantenha as informações descarregadas para reporte de erros. Em vez disso, pode copiar o "ID da sessão" e anexá-lo ao abrir o bilhete de apoio.

![diagnóstico-informação](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Para começar a resolução de problemas, tente novamente a operação que realizou no Estúdio Azure Synapse.

- Para o sintoma 1, selecione o botão "Refresh" à direita da desvendação da "base de dados de utilização" no separador "Script SQL" e verifique se consegue ver "SQL on-demand".
- Para o sintoma 2, tente executar a consulta novamente para ver se executa com sucesso.

Se o problema ainda existir, prima F12 no seu navegador para abrir "Ferramentas de Desenvolvimento" (DevTools).

Na janela "Ferramentas de Desenvolvimento", mude para o painel "Rede". Se necessário, selecione o botão "Limpar" na barra de ferramentas no painel "Rede".
Certifique-se de que "Desativar a cache" no painel "Rede" é verificado.

Retry a operação que realizou no Estúdio Azure Synapse. Pode ver novos itens apresentados na lista "Network" em "Ferramentas de Desenvolvimento". Note o tempo atual do sistema para fornecer no bilhete de apoio.

![painel de rede](media/troubleshooting-synapse-studio/network-panel.png)

Encontre o item cuja coluna Url corresponde ao seguinte padrão:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Onde [*A]* é o seu nome de espaço de trabalho, e "ondemand" poderia ser "-sqlod" e onde *[B]* deve ser um nome de base de dados, como "mestre". Deve haver, no máximo, dois itens com o mesmo valor URL, mas valores de método diferentes; OPÇÕES e POST. Verifique se estes dois itens têm "200" ou "20x" sob a coluna de estado, onde "x" pode ser qualquer dígito.

Se um deles tiver algo diferente de "20x" e:

- o estado começa com "(falhado)", ou alargar a coluna "Status" ou pairar sobre o texto de estado para ver o texto completo. Inclua o texto e/ou a imagem ao abrir o bilhete de apoio.

    ![estatuto-texto](media/troubleshooting-synapse-studio/status-text.png)

    - Se vir ERR_NAME_NOT_RESOLVED e criar o seu espaço de trabalho dentro de 10 minutos, espere 10 minutos e tente novamente para ver se o problema ainda existe.
    - Se vir ERR_INTERNET_DISCONNECTED ou ERR_NETWORK_CHANGED, pode indicar que a ligação à rede para PC está a ter problemas. Verifique a ligação de rede e volte a tentar a operação.
    - Se vir ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR ou outros códigos de erro que contenham "SSL", pode indicar que a configuração SSL local está a ter problemas, ou o seu administrador de rede bloqueou o acesso ao servidor on-demand SQL. Abra um bilhete de apoio e fixe o código de erro na descrição.
    - Se vir ERR_NETWORK_ACCESS_DENIED, poderá ter de verificar com o administrador se a sua política local de firewall bloqueou o acesso a *.database.windows.net domínio ou à porta remota 1443.
    - Opcionalmente, experimente imediatamente a mesma operação num ambiente de máquinas e/ou rede diferente para excluir um problema de configuração da rede no seu PC.

- o estado é "40x", "50x", ou outros números, selecionados no ou no item(s) para ver os detalhes. Devia ver os detalhes do artigo à direita. Encontre a secção "Cabeçalho de Resposta"; em seguida, verifique se existe um item denominado "access-control-allow-origin". Em caso afirmativo, verifique se tem um dos seguintes valores:

    - `*`(único asterisco)
    - https://web.azuresynapse.net/(ou outro valor que o texto na barra de endereços do seu navegador começa com)

Se o cabeçalho de resposta contiver um dos valores acima referidos, significa que já deveríamos ter recolhido as informações de falha. Pode abrir um bilhete de apoio se necessário e, opcionalmente, anexar a imagem dos detalhes do item.

Se não conseguir ver o cabeçalho, ou se o cabeçalho não tiver um dos valores acima indicados, prenda uma imagem dos detalhes do item quando abrir o bilhete.

![detalhes de itens](media/troubleshooting-synapse-studio/item-details.png)

Se os passos acima não resolverem o seu problema, poderá ter de abrir um bilhete de apoio. Ao submeter o seu bilhete de apoio, inclua o "ID da sessão" ou "Informação de Diagnóstico" descarregado no início deste guia.

Ao reportar o problema, pode, opcionalmente, tirar uma imagem do separador "Consola" nas "Ferramentas de Desenvolvimento" e anexá-lo ao bilhete de suporte. Percorra o conteúdo e tire mais de uma imagem, se necessário, para capturar toda a mensagem.

![developer-tool-console](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se estiver a anexar imagens, forneça o tempo (ou um intervalo de tempo estimado) de quando tirou as imagens. Ajudar-nos-á a analisar o problema.

Certos navegadores suportam mostrar carimbos de tempo no separador "Consola". Para o Chromium Edge/Chrome, abra o diálogo "Definições" em "Ferramentas de Desenvolvimento", e verifique "Mostrar selos de tempo" no separador "Preferências".

![definições de consola de ferramentas de desenvolvimento](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-stamp](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Passos seguintes
Se os passos anteriores não ajudarem a resolver o problema [Criar um bilhete](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) de apoio
