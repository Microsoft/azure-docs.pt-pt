---
title: Ferramenta de dados de cópia de resolução de problemas na Fábrica de Dados Azure
description: Saiba como resolver problemas com a ferramenta de dados de cópia na Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550675"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Ferramenta de dados de cópia de resolução de problemas na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para a ferramenta De Dados de Cópia na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Código de erro: Incapaz de validar na ferramenta Dados de Cópia

- **Sintomas** : No primeiro passo da ferramenta Dados de Cópia, encontra a mensagem de aviso de "Incapaz de Validar".
- **Causas** : Isto pode acontecer quando todos os cookies de terceiros estiverem desativados.
- **Resolução:** 
    - Utilize o internet Explorer ou o navegador Microsoft Edge.
    - Se estiver a utilizar o navegador Chrome, siga as instruções abaixo para adicionar a exceção cookies para *microsoftonline.com* e *windows.net* .
        1.  Abra o navegador Chrome.
        2.  Clique na chave inglesa ou três linhas à direita (Personalize e controle o Google Chrome).
        3.  Clique em **Definições** .
        4.  Pesse **cookies** ou vá para **a Privacidade** em Definições Avançadas.
        5.  Selecione **Definições de conteúdo** .    
        6.  Os cookies devem ser definidos para **permitir a fixação de dados locais (recomendado)** .
        7.  Clique **Em Gerir exceções** . Sob **o padrão de nome hospedeiro** introduza o seguinte, e **certifique-se de que Allow** é o conjunto de comportamento.
            - login.microsoftonline.com
            - login.windows.net
        8.  Feche o navegador e relançe.
    - Se estiver a utilizar o navegador Firefox, siga as instruções abaixo para adicionar a exceção cookies.
        1. A partir do menu **Tools** Firefox, aceda às  >  **Opções de Ferramentas.**
        2. Em **Privacy**  >  **Histórico de Privacidade,** pode ver que a definição atual é **Utilizar configurações personalizadas para o histórico** .
        3. Em **Aceitar cookies de terceiros** , a sua configuração atual pode ser **Nunca** , então deve clicar em Exceções no direito de adicionar os **seguintes** sites.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Feche o navegador e relançe. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Código de erro: Não é possível abrir a página de login e introduzir senha

- **Sintomas** : A ferramenta De dados de cópia redireciona-o para a página de login, mas a página de login não aparece com sucesso.
- **Causas** : Este problema pode acontecer se alterar o ambiente de rede de escritórios para rede doméstica. Existem algumas caches nos navegadores. 
- **Resolução:** 
    1.  Feche o navegador e tente novamente. Vá para o próximo passo se a questão ainda existir.   
    2.  Se estiver a utilizar o navegador Internet Explorer, tente abri-lo em modo privado (Prima "Ctrl" + "Shift" + "P"). Se estiver a utilizar o navegador Chrome, tente abri-lo em modo incógnito (Prima "Ctrl" + "shift" + "N"). Vá para o próximo passo se a questão ainda existir. 
    3.  Tente usar outro navegador. 


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Dados de mapeamento ADF fluem Guia de Desempenho](concepts-data-flow-performance.md)
