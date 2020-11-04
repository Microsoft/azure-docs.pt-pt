---
title: Troubleshoot Synapse Studio (pré-visualização)
description: Troubleshoot Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fe31f83ccc0dcbd2d61a7c70d40a64da08d13a1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321010"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio (pré-visualização) resolução de problemas

Este guia de resolução de problemas fornece instruções sobre as informações a fornecer ao abrir um bilhete de apoio sobre problemas de conectividade de rede. Com a informação adequada, podemos resolver o problema mais rapidamente.

## <a name="serverless-sql-pool-preview-service-connectivity-issue"></a>Problema de conectividade de serviço de pool SQL sem servidor (pré-visualização)

### <a name="symptom-1"></a>Sintoma 1

A opção "Piscina SQL sem servidor" está acinzentada no dropdown "Connect to".

![sintoma1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintoma 2

Executar a consulta com "pool SQL sem servidor" dá-lhe a mensagem de erro "Falhou em estabelecer a ligação ao servidor".

![sintoma 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

> [!NOTE] 
>    Os seguintes passos de resolução de problemas são para Chromium Edge e Chrome. Pode utilizar outros navegadores (como o FireFox) com os mesmos passos de resolução de problemas, mas a janela "Developer Tool" pode ter um layout diferente das imagens deste TSG. Se possível, NÃO utilize a Borda Clássica para resolução de problemas, pois pode apresentar informações inexatas em determinadas situações.

Abra o painel "Informação de Diagnóstico", selecione o botão "Baixar diagnóstico". Mantenha as informações descarregadas para reportar erros. Em vez disso, pode copiar o "Session ID" e anexá-lo ao abrir o bilhete de apoio.

![informação de diagnóstico](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Para começar a resolução de problemas, relemisça a operação que realizou no Azure Synapse Studio.

- Para o sintoma 1, selecione o botão "Refresh" à direita da "Use database" no separador "SCRIPT SQL" e verifique se consegue ver "pool SQL sem servidor".
- Para o sintoma 2, tente executar a consulta novamente para ver se executa com sucesso.

Se o problema ainda existir, prima F12 no seu browser para abrir "Developer Tools" (DevTools).

Na janela "Ferramentas de Desenvolvimento", mude para o painel "Rede". Selecione o botão "Limpar" na barra de ferramentas no painel "Rede", se necessário.
Certifique-se de que "Desative a cache" no painel "Rede" está verificado.

Releminar a operação que realizou no Azure Synapse Studio. Pode ver novos itens apresentados na lista "Rede" em "Ferramentas de Desenvolvimento". Note o tempo atual do seu sistema para fornecer no bilhete de apoio.

![painel de rede 1](media/troubleshooting-synapse-studio/network-panel.png)

Encontre o item cuja coluna url corresponde ao seguinte padrão:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Onde [ *A* ] é o seu nome de espaço de trabalho, e "-ondemand" poderia ser "-sqlod" e onde [ *B* ] deveria ser um nome de base de dados, como "mestre". Deve haver no máximo dois itens com o mesmo valor DE URL, mas valores de método diferentes; OPÇÕES E POST. Verifique se estes dois itens têm "200" ou "20x" debaixo da coluna de estado, onde "x" pode ser qualquer dígito.

Se um deles tiver algo diferente de "20x" e:

- O estado começa com "(falhado)", ou alarga a coluna "Status" ou paira sobre o texto de estado para ver o texto completo. Inclua o texto e/ou a imagem ao abrir o bilhete de apoio.

    ![texto de estado](media/troubleshooting-synapse-studio/status-text.png)

    - Se vir ERR_NAME_NOT_RESOLVED e criar o seu espaço de trabalho em 10 minutos, aguarde 10 minutos e reforce para ver se o problema ainda existe.
    - Se vir ERR_INTERNET_DISCONNECTED ou ERR_NETWORK_CHANGED, pode indicar que a ligação à rede do PC está a ter problemas. Verifique a sua ligação à rede e volte a tentar a operação.
    - Se vir ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR ou outros códigos de erro que contenham "SSL", poderá indicar que a configuração SSL local está a ter problemas, ou o administrador de rede bloqueou o acesso ao servidor de piscina SQL sem servidor. Abra um bilhete de apoio e anexe o código de erro na descrição.
    - Se vir ERR_NETWORK_ACCESS_DENIED, poderá ter de verificar com o administrador se a sua política de firewall local bloqueou o acesso ao domínio *.database.windows.net ou à porta remota 1443.
    - Opcionalmente, tente a mesma operação imediatamente num ambiente de máquina e/ou rede diferente para excluir um problema de configuração de rede no seu PC.

- O estado é "40x", "50x", ou outros números, selecione no(s) item(s) para ver os detalhes. Devia ver os detalhes do artigo à direita. Encontre a secção "Cabeçalho de resposta"; em seguida, verifique se existe um item chamado "acesso-control-allow-origin". Em caso afirmativo, verifique se tem um dos seguintes valores:

    - `*` (asterisco único)
    - https://web.azuresynapse.net/ (ou outro valor que o texto na barra de endereços do seu navegador começa com)

Se o cabeçalho de resposta contiver um dos valores acima, significa que já devíamos ter recolhido a informação de falha. Pode abrir um bilhete de apoio, se necessário, e anexar opcionalmente a imagem dos detalhes do artigo.

Se não conseguir ver o cabeçalho, ou se o cabeçalho não tiver um dos valores acima indicados, anexe uma imagem dos detalhes do item quando abrir o bilhete.

 
![detalhes do item](media/troubleshooting-synapse-studio/item-details.png)
 
Se os passos acima não resolverem o seu problema, poderá ter de abrir um bilhete de apoio. Ao submeter o seu bilhete de apoio, inclua o "Session ID" ou "Informação de Diagnóstico" descarregado no início deste guia.

Ao reportar o problema, pode, opcionalmente, tirar uma imagem do separador "Consola" no separador "Ferramentas do Desenvolvedor" e anexá-lo ao bilhete de suporte. Percorra o conteúdo e tire mais de uma imagem, se necessário, para capturar toda a mensagem.

![consola de ferramentas de desenvolvedor](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se estiver a anexar imagens, forneça o tempo (ou um intervalo estimado de tempo) de quando tirou as imagens. Ajudar-nos-á a analisar o problema.

Certos navegadores suportam os sinais de tempo no separador "Consola". Para o Chromium Edge/Chrome, abra o diálogo "Definições" em "Ferramentas do Desenvolvedor", e verifique "Mostrar os intervalos de tempo" no separador "Preferências".

![definições de consola de ferramentas de desenvolvedor](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![mostrar carimbo de tempo](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Passos seguintes
Se os passos anteriores não ajudarem a resolver o seu problema [Crie um bilhete de apoio](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
