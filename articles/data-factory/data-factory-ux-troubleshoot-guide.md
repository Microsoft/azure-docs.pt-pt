---
title: Resolução de problemas Azure Data Factory UX
description: Saiba como resolver problemas com os problemas da Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426232"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problemas de resolução de dados Azure Data Factory UX Problemas

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para a Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>ADF UX não está a carregar

> [!NOTE]
> O Azure Data Factory UX suporta oficialmente o Microsoft Edge e o Google Chrome. A utilização de outros navegadores web pode levar a comportamentos inesperados ou indocumentais.

### <a name="third-party-cookies-blocked"></a>Cookies de terceiros bloqueados

O ADF UX utiliza cookies de navegador para persistir na sessão do utilizador e permitir experiências de desenvolvimento e monitorização interativas. É possível que o seu navegador bloqueie cookies de terceiros porque está a usar uma sessão incógnita ou tem um bloqueador de anúncios ativado. Bloquear cookies de terceiros pode causar problemas ao carregar o portal, como ser redirecionado para uma página em branco, https://adf.azure.com/accesstoken.html ou receber uma mensagem de aviso dizendo que os cookies de terceiros estão bloqueados. Para resolver este problema, ative as opções de cookies de terceiros no seu navegador utilizando os seguintes passos:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Permitir todos os cookies

1. Visite **chrome://settings/cookies** no seu browser.
1. Selecione Permitir a opção **de todos os cookies** 

    ![Permitir todos os cookies no Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Refresque a ADF UX e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Apenas permitir que a ADF UX utilize cookies
Se não quiser permitir todos os cookies, pode opcionalmente apenas permitir ADF UX:
1. Visite **chrome://settings/cookies.**
1. Selecione **adicionar** em Sites que podem sempre usar a opção **cookies** 

    ![Adicione ADF UX para sites permitidos no Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adicione **adf.azure.com** site, verifique **todas as opções de cookies** e guarde. 

    ![Permitir todos os cookies do site da ADF UX](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Refresque a ADF UX e tente novamente.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Visite **edge://settings/content/cookies** no seu browser.
1. Certifique-se **de que os sites para guardar e ler dados de cookies** estão ativados e que a opção de **cookies de terceiros do Bloco está desativada** 

    ![Permitir todos os cookies em Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Refresque a ADF UX e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Apenas permitir que a ADF UX utilize cookies

Se não quiser permitir todos os cookies, pode opcionalmente apenas permitir ADF UX:

1. Visite **edge://settings/content/cookies.**
1. Na secção **'Permitir',** **selecione Adicionar** e adicione **adf.azure.com** site. 

    ![Adicione ADF UX para sites permitidos em Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Refresque a ADF UX e tente novamente.

## <a name="connection-failed-on-adf-ux"></a>A ligação falhou no ADF UX

Por vezes, veria erros de "Ligação falhada" no ADF UX semelhantes à imagem abaixo depois de clicar em **Test Connection**, **Preview**, etc.

![A ligação falhou](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Neste caso, pode primeiro experimentar a mesma operação com o modo de navegação InPrivate no seu navegador.

Se ainda não estiver a funcionar, no browser, prima F12 para abrir **ferramentas de desenvolvimento.** Vá ao separador **'Rede',** verifique **a Cache de Desativação,** volte a tentar a operação falhada e encontre o pedido falhado (a vermelho).

![Pedido falhado](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Em seguida, encontre o **nome do anfitrião** (neste caso, **dpnortheurope.svc.datafactory.azure.com**) a partir do URL de **pedido** do pedido falhado.

Digite o nome do **anfitrião** diretamente na barra de endereço do seu navegador. Se vir 404 no navegador, isto normalmente significa que o lado do cliente está bem e o problema está no lado do serviço ADF. Arquivar um bilhete de suporte com o **ID** de atividade a partir da mensagem de erro ADF UX.

![Recurso não encontrado](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Se não ou vir um erro semelhante abaixo no navegador, isto geralmente significa que você tem algum problema do lado do cliente. Siga mais a frente os passos de resolução de problemas.

![Erro do lado do cliente](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Abrir **o comando rápido** e escrever **nslookup dpnortheurope.svc.datafactory.azure.com**. Uma resposta normal deve parecer abaixo:

![Resposta de comando 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Se vir uma resposta normal do DNS, contacte o suporte de TI local para verificar se a ligação HTTPS a este nome de anfitrião está ou não bloqueada. Se o problema não puder ser resolvido, preencha um bilhete de apoio com o **ID** de atividade a partir da mensagem de erro ADF UX.

Se vir algo mais do que isto, isto normalmente significa que há algo de errado com o seu servidor DNS ao resolver o nome DNS. Normalmente, mudar o ISP (Fornecedor de Serviços de Internet) ou DNS (por exemplo, para o Google DNS 8.8.8.8) pode ser uma possível solução para tentar. Se o problema persistir, poderá tentar ainda mais **datafactory.azure.com** e **nslookup azure.com** para ver em que nível a sua resolução de DNS está falhada e submeter todas as informações ao seu suporte de TI local ou ao seu ISP para resolução de problemas. Se acreditarem que o problema ainda está do lado da Microsoft, arquive um bilhete de suporte com o **ID** de atividade a partir da mensagem de erro ADF UX.

![Resposta de comando 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Alterar tipo de serviço ligado em conjuntos de dados

O conjunto de dados do formato de ficheiro pode ser utilizado com todos os conectores baseados em ficheiros, por exemplo, pode configurar um conjunto de dados parquet no Azure Blob ou no Azure Data Lake Storage Gen2. Note que cada conector suporta diferentes conjuntos de definições relacionadas com a loja de dados na atividade, e com diferentes modelos de aplicações. 

Na UI de autoria da ADF, quando utiliza um conjunto de dados de formato de ficheiro numa atividade - incluindo copy, Lookup, GetMetadata, Delete - e no conjunto de dados pretende apontar para um serviço ligado de diferente tipo da corrente (por exemplo, mudar de Sistema de Ficheiros para ADLS Gen2), veria a seguinte mensagem de aviso. Para se certificar de que é um interruptor limpo, após o seu consentimento, os oleodutos e atividades, que referenciam este conjunto de dados também serão modificados para utilizar o novo tipo, e quaisquer configurações existentes de loja de dados, que são incompatíveis com o novo tipo serão apuradas uma vez que já não se aplica.

Para saber mais sobre as definições de armazenamento de dados suportados para cada conector, pode ir ao artigo de conector correspondente - > copiar propriedades de atividade para ver a lista de propriedades detalhadas. Consulte a [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage,](connector-azure-file-storage.md) [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md), [HTTP](connector-http.md), e [SFTP](connector-sftp.md).

![Mensagem de aviso](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

* [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
